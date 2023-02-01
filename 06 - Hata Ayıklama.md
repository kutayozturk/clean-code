Hata işleme (Error handling), kod yazarken yapmak zorunda olduğumuz şeylerden sadece biri. Bir şeyler yanlış gidebilir ve programımız patlayabilir. Biz programcılar olarak kodumuzu böyle durumlara karşı hazırlamaktan sorumluyuz.

## Dönüş Kodları Yerine İstisnaları Kullanın
Eskiden yazdığımız bazı dillerde istisnaları (exception) işleyebileceğimiz ve raporlayabileceğimiz teknikler sınırlıydı. Bunun yerine flagler ya da hata kodları dönüyorduk. Bu yaklaşıma örnek:
```
public class DeviceController {
    ...
    public void sendShutDown() {
            DeviceHandle handle = getHandle(DEV1);
            // Check the state of the device
            if (handle != DeviceHandle.INVALID) {
                // Save the device status to the record field
                retrieveDeviceRecord(handle);
                // If not suspended, shut down
                if (record.getStatus() != DEVICE_SUSPENDED) {
                    pauseDevice(handle);
                    clearDeviceWorkQueue(handle);
                    closeDevice(handle);
                } else {
                    logger.log("Device suspended. Unable to shut down");
                }
            } else {
                logger.log("Invalid handle for: " + DEV1.toString());
            }
        }
        ...
}
```
Bu tür yaklaşımlar çağıranın kafasını karıştırır. Çağıran, bu çağırımdan hemen sonra hataları kontrol etmelidir, ancak maalesef bu kontroller çok çabuk unutulabiliyor. Bu sebeple, bir hata ile karşılaşıldığında istisna fırlatmak (throw) daha iyidir. Çağıranın kodunu daha temiz hale getirir.

Aşağıdaki kod, istisna fırlatmayı seçtiğimiz versiyonu:
```
public class DeviceController {
    ...
    public void sendShutDown() {
        try {
            tryToShutDown();
        } catch (DeviceShutDownError e) {
            logger.log(e);
        }
    }
    private void tryToShutDown() throws DeviceShutDownError {
        DeviceHandle handle = getHandle(DEV1);
        DeviceRecord record = retrieveDeviceRecord(handle);
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
    }
    private DeviceHandle getHandle(DeviceID id) {
            ...
            throw new DeviceShutDownError("Invalid handle for: " + id.toString());
            ...
        }
        ...
}
```
Kod daha temiz çünkü birbirine geçmiş iki işlem de (cihazın kapanması ve hata işleme) şimdi birbirinden ayrı. Bu 2 işleme bakabilir ve onları birbirinden bağımsız olarak algılayabiliriz.

## İlk Önce Try-Catch-Finally Bloklarını Yazın
try-catch yazmak programınıza bir kapsam sağlar ve uygulamanın try bloğunda bir yerlerde hata alabileceğini ve catch’de duracağını belirtirsiniz. catch blokları programınızı tutarlı bir durumda bırakmak zorundadırlar. Bu sebeple istisnalar fırlatabileceğiniz bir koda, try-catch-finally bloklarını yazmakla başlayın. Kullanıcının koddan ne beklemesi gerektiğini anlamasına yardım edecektir.

Şu örneğe bakalım. Bir dosyaya erişen ve bazı serialize edilmiş nesneleri okuyan kodu yazmamız gerekiyor. Dosya bulunamazsa istisna fırlatılacağını söyleyen bir birim test ile başlayalım:
```
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName() {
    sectionStore.retrieveSection("invalid - file");
}
Test, şu taklit gerçekleştirimi de yazmamızı gerektirecektir:

public List<RecordedGrip> retrieveSection(String sectionName) {
    // dummy return until we have a real implementation
    return new ArrayList<RecordedGrip>();
}
```
Testimiz patlayacak çünkü bir istisna fırlatmıyor. Şimdi ise kodumuzu geçersiz bir dosyaya erişecek şekilde değiştirelim ve bir istisna fırlattığını görelim:
```
public List<RecordedGrip> retrieveSection(String sectionName) {
    try {
        FileInputStream stream = new FileInputStream(sectionName)
    } catch (Exception e) {
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```
Testimiz şimdi geçecek çünkü istisnayı yakaladık. Bu noktada kodu yeniden yapılandırabiliriz. İstisnayı, aslında fırlatılması gereken istisna tipine daraltacağız:
```
public List<RecordedGrip> retrieveSection(String sectionName) {
    try {
        FileInputStream stream = new FileInputStream(sectionName);
        stream.close();
    } catch (FileNotFoundException e) {
        throw new StorageException("retrieval error”, e);
    }
    return new ArrayList<RecordedGrip>();
}
```
İhtiyacımız olan mantığı inşa edebilmek için TDD (Test Driven Development) kullanabiliriz. İstisnaları zorlayan testler yazmaya çalışmalı ve ardından testlerimize cevap verecek kodu yazmalıyız. Bu, try bloğunu önce yazmamızı sağlayacak ve bakım yapmamızı kolaylaştıracaktır.

## Kontrolsüz (Unchecked) İstisnalar Kullanın
Java programcıları yıllarca kontrollü (checked) istisnaların faydalarını tartıştılar. Java’nın ilk versiyonunda kontrollü istisnalar tanıtıldığında bize harika bir fikir gibi geldi. Her metodun imzası, çağıranın geçebileceği tüm istisnaları listeleyecekti. Daha da fazlası, bu istisnalar metodun tipinin bir parçasıydı.

C#’ın -cesur teşebbüslerine rağmen- kontrollü istisnaları yok, C++’ın da, Python ya da Ruby’nin de. Kontrollü istisnaların bedeline değip değmeyeceğine karar vermemiz gerek.

Bedel nedir? Bedel, Açık/Kapalı Kuralı’nın (OCP — Gelişime Açık Değişime Kapalılık Kuralı) ihlalidir. Metodunuzdan kontrollü bir istisna fırlatırsanız ve catch üç seviyeden fazlaysa, o istisnayı sizinle catch arasındaki her metodun imzasında belirtmeniz gerekir. Bu demektir ki, düşük seviyede bir değişiklik, imza değişikliklerini daha üst seviyelerde zorlayabilir. Onları ilgilendiren hiçbir şey olmadığı halde, değiştirilen modüller yeniden derlenmeli ve dağıtılmalıdır.

Büyük sistemlerdeki çağırma hiyerarşilerini düşünün. En alt seviye metotlardan birisinin bir istisna fırlatacak şekilde düzenlenmesi durumunda, çağıran tüm metotlar da imzasına bir throws eklemek zorundadır. Bu durumda kapsülleme de (encapsulation) bozulmuştur çünkü değişen metotlar da artık bu istisnanın detaylarını biliyor olacaktır.

## İstisnalarla Bağlam Sağlayın
Fırlattığınız her istisna, kaynağa ve hatanın yerine ait yeterli bilgi sağlamalıdır. Java’da herhangi bir istisnadan bir iz bulabilirsiniz ya da bazen bu izler bize hiçbir şey söylemez. Bu nedenle fırlattığınız istisnalarda bilgilendirici hata mesajları verin. Başarısız olan işlemden, hatanın tipinden bahsedin. Eğer uygulamanızda loglama yapıyorsanız, catch bloğunuza bu hatayı loglayabilecek yeterli bilgiyi geçin.

Çağıranın İhtiyaçlarına Uygun İstisna Sınıfları Tanımlayın
Hataları sınıflandırabilmenin bir sürü yolu vardır; kaynaklarına göre, türlerine göre sınıflandırabiliriz. Cihaz hatası mı, ağ hatası mı ya da programlama hataları mı?

Şu sınıflandırma örneğine bakalım. Üçüncü taraf bir kütüphane çağrımı için try-catch-finally yazılmış. Çağrılardan fırlatılabilecek tüm istisnaları kapsıyor:
```
ACMEPort port = new ACMEPort(12);
try {
    port.open();
} catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlock exception", e);
} catch (GMXError e) {
    reportPortError(e);
    logger.log("Device response exception");
} finally {

}
```
Bu ifadede çokça tekrarlanmış kod var. Burada yaptığımız işin kabaca aynı olduğunu bildiğimizden çağırdığımız API’yi sararak (wrapping) ve ortak bir istisna tipi döndüğünden emin olarak kodumuzu önemli ölçüde basitleştirebiliriz:
```
LocalPort port = new LocalPort(12);
try {
    port.open();
} catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
} finally {
    ...
}
```
LocalPort sınıfımız, ACMEPort sınıfından atılmış istisnaları yakalayan ve çeviren basit bir sarmalayıcıdır (wrapper):

```
public class LocalPort {
    private ACMEPort innerPort;
    public LocalPort(int portNumber) {
        innerPort = new ACMEPort(portNumber);
    }
    public void open() {
        try {
            innerPort.open();
        } catch (DeviceResponseException e) {
            throw new PortDeviceFailure(e);
        } catch (ATM1212UnlockedException e) {
            throw new PortDeviceFailure(e);
        } catch (GMXError e) {
```

Sarmalayıcı sınıflar üçüncü taraf API’lerin detaylarını gizlemek için en iyi pratiktir. Belli bir tedarikçinin API’sine bağlı olmazsınız ve rahat hissedebileceğiniz bir API tanımlayabilirsiniz. Ve ileride farklı bir kütüphaneye geçmek istediğinizde, sarmalayarak minimize ettiğiniz bağımlılıklar ile geçiş yapmak çok daha kolaydır.

## Normal Bir Akış Tanımlayın
Şu örneğe bakalım; masrafları hesaplayan bir kod parçası:
```
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
} catch (MealExpensesNotFound e) {
    m_total += getMealPerDiem();
}
```
Eğer öğünler masraflandırılmışsa toplama ekliyor, değillerse çalışan o gün için o yemek tutarını alıyor. Burada istisna akışı karıştırıyor. Bu özel durum ile ilgilenmemiş olsak, kodumuz çok daha temiz görünürdü:
```
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```
ExpenseReportDAO sınıfını her zaman bir MealExpense nesnesi dönecek şekilde değiştirdiğimizde kodumuzu daha basit hale getirmiş olduk. Eğer hiç masraf yoksa da, günlük bir MealExpense nesnesi dönecektir:
```
public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
        // return the per diem default
    }
}
```
Buna Special Case Pattern (Özel Durum Deseni) denir. Özel durumlarla senin için başa çıkacak bir sınıf yaratır ya da bir nesne ayarlarsın. Bunu yaptığında, ön yüz kodu istisnai durumlarla uğraşmak zorunda kalmayacaktır. Bu davranış özel durum nesnesi ile kapsüllenmiş olur.

## Null Değerler Dönmeyin
Hata işlemeden bahsediyorken, hataları davet edercesine yaptığımız şeylerden de bahsetmeliyiz. null dönmek listedeki ilk şeydir. Örneğin:
```
public void registerItem(Item item) {
    if (item != null) {
        ItemRegistry registry = peristentStore.getItemRegistry();
        if (registry != null) {
            Item existing = registry.getItem(item.getID());
            if (existing.getBillingPeriod().hasRetailOwner()) {
                existing.register(item);
            }
        }
    }
}
```
Bu kod gözünüze normal gelebilir, ancak oldukça kötüdür. null döndüğümüzde aslında gene kendimize iş çıkarıyor ve topu fonksiyonumuzu çağıranlara atıyoruz. Uygulamanın kontrolden çıkması için tek bir eksik null kontrolü yeterli gibi görünüyor. İlk if’ten sonraki satırda null kontrolü yok. peristentStore nesnesi null olmuş olsaydı, çalışma anında çok minnoş bir NullPointerException alırdık.

Eğer bir metottan null dönecekseniz, onun yerine istisna fırlatmayı ya da bir Special Case nesnesi dönmeyi düşünün. Eğer kullandığınız bir API’den null dönebilecek bir metot çağırıyorsanız da, bu metodu özel durum nesnesi dönen ya da istisna fırlatan bir metot ile sarmalamayı düşünebilirsiniz.

Çoğu durumda özel durum nesneleri kolay çözümdür. Şunun gibi bir kodunuz olduğunu düşünelim:
```
List<Employee> employees = getEmployees();
if (employees != null) {
    for (Employee e: employees) {
        totalPay += e.getPay();
    }
}
```
getEmployees() null dönebilir ancak gerçekten de dönmek zorunda mı? Eğer getEmployees() metodunu boş liste dönecek şekilde düzenlersek, kodumuzu temizleyebiliriz:
```
List<Employee> employees = getEmployees();
for (Employee e: employees) {
    totalPay += e.getPay();
}
```
Neyse ki Java’nın Collections.emptyList() metodu var ve boş sabit bir liste dönüyor:
```
public List<Employee> getEmployees() {
    if (...there are no employees...)
        return Collections.emptyList();
}
```
Eğer bu şekilde kodlarsanız, NullPointerException alma şansınızı minimize edersiniz.

## Null Argümanlar Geçmeyin
Metotlardan null dönmek kötü bir pratiktir ancak metotlara null geçmek daha da kötü bir pratiktir. Sizden null bekleyen bir API ile çalışmadıkça, kodunuzda mümkün mertebe null geçmekten kaçınmalısınız.

Nedenini anlamak için şu örneğe bakalım; iki nokta için bir metrik hesaplıyor:
```
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        return (p2.x– p1.x) * 1.5;
    }
    ...
}
```
Birileri null bir parametre geçtiğinde ne olacak?
```
calculator.xProjection(null, new Point(12, 13));
```
NullPointerException alacağız elbette. Bunu nasıl düzeltebiliriz? Yeni bir istisna oluşturabilir ve bunu fırlatabiliriz:
```
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        if (p1 == null || p2 == null) {
            throw InvalidArgumentException(
                "Invalid argument for MetricsCalculator.xProjection");
        }
        return (p2.x– p1.x) * 1.5;
    }
}
```
Diğer bir alternatif:
```
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        assert p1 != null : "p1 should not be null";
        assert p2 != null : "p2 should not be null";
        return (p2.x– p1.x) * 1.5;
    }
}
```
Belgeme için iyi ancak problemimizi çözmüyor; birileri null geçtiğinde gene de hata alacağız.

Çoğu dilde istemeden gönderilmiş null değerler ile uğraşabilmenin bir yolu yoktur. Durum böyle olduğundan, burada en gerçekçi yaklaşım null değerlerin gelmesini önlemektir.

Temiz kod okunabilirdir ancak güçlü de olmalıdır. Bu ikisi çelişkili hedefler değildirler. Eğer hata işlemeyi bağımsız bir iş olarak görürsek, temiz ve güçlü kodlar yazabilir ve kodumuzun sürdürülebilirliği konusunda büyük adımlar atabiliriz.
