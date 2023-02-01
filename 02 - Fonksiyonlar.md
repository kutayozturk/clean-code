### Fonksiyonlar bir programın içindeki organizasyonun ilk satırlarıdır.

Aşağıdaki koda bir bakalım. Sadece uzun değil, içinde tekrarlanmış (duplicate) kodlar, bir sürü ne olduğu belirsiz metin sabitleri ve net olmayan veri tipleri var. 3 dakika içinde metodu ne kadar anlayabileceğinize bir bakın:
```
public static String testableHtml(PageData pageData, boolean includeSuiteSetup) throws Exception {
    WikiPage wikiPage = pageData.getWikiPage();
    StringBuffer buffer = new StringBuffer();
    if (pageData.hasAttribute("Test")) {
        if (includeSuiteSetup) {
            WikiPage suiteSetup =                PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME, wikiPage);
            if (suiteSetup != null) {
                WikiPagePath pagePath = suiteSetup.getPageCrawler().getFullPath(suiteSetup);
                String pagePathName = PathParser.render(pagePath);
                buffer.append("!include -setup .")
                      .append(pagePathName).append("\n");
            }
        }
        WikiPage setup = PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
        if (setup != null) {
            WikiPagePath setupPath =
                    wikiPage.getPageCrawler().getFullPath(setup);
            String setupPathName = PathParser.render(setupPath);
            buffer.append("!include -setup .")
                    .append(setupPathName).append("\n");
        }
    }
    buffer.append(pageData.getContent());
    if (pageData.hasAttribute("Test")) {
        WikiPage teardown = PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
        if (teardown != null) {
            WikiPagePath tearDownPath = wikiPage.getPageCrawler().getFullPath(teardown);
            String tearDownPathName = PathParser.render(tearDownPath);
            buffer.append("\n")
                    .append("!include -teardown .")
                    .append(tearDownPathName).append("\n");
        }
        if (includeSuiteSetup) {
            WikiPage suiteTeardown = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage);
            if (suiteTeardown != null) {
                WikiPagePath pagePath = suiteTeardown.getPageCrawler().getFullPath(suiteTeardown);
                String pagePathName = PathParser.render(pagePath);
                buffer.append("!include -teardown .")
                        .append(pagePathName).append("\n");
            }
        }
    }
    pageData.setContent(buffer.toString());
    return pageData.getHtml();
}
```
Muhtemelen 3 dakika içinde neler döndüğünü çözemediniz.

Bir kaç metot sadeleştirmesi, bir kaç yeniden adlandırma ve yeniden yapılandırmadan (refactoring) sonra kodu şu şekle getirebildim:

public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception {
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage) {
        WikiPage testPage = pageData.getWikiPage();
        StringBuffer newPageContent = new StringBuffer();
        includeSetupPages(testPage, newPageContent, isSuite);
        newPageContent.append(pageData.getContent());
        includeTeardownPages(testPage, newPageContent, isSuite);
        pageData.setContent(newPageContent.toString());
    }
    return pageData.getHtml();
}
Son dokunuşlardan sonra ise şu hale geldi:
```
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception {
    if (isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
    return pageData.getHtml();
}
```
## Bir Metot Ne Kadar Kısa Olmalı?
Fonksiyonların ilk kuralı küçük, kısa olmaları gerektiğidir. İkinci kural ise daha da kısa olmaları gerektiğidir.

80’lerde bir fonksiyonun tüm ekrandan daha büyük olmaması gerektiği gibi bir kural vardı. Bunu söylediğimiz zamanlarda VT100 ekranlarımız 80 kolona 24 satırdı.

Bugünlerde normal bir yazı tipiyle (font) ve güzel büyük bir monitörle bir satıra 150 karakter ve bir ekrana 100 satırdan fazlasını sığdırabilirsiniz. Satırlar 150 karakter, fonksiyonlar da 100 satır filan olmamalı.
Aslına bakarsanız, fonksiyonlar 20 satırı zar zor bulmalı.

1991'de Kent Beck’i evinde ziyarete gitmiştim. Oturduk ve birlikte biraz kod yazdık. Bir ara bana Sparkle adında küçük bir Java/Swing programı gösterdi. Her fonksiyon 2, 3 ya da 4 satır uzunluğundaydı. Her biri çok şeffaftı, her biri bir şeyler anlatıyordu. İşte fonksiyonların ne kadar kısa olmaları gerektiğinin cevabı buydu!

## Sadece Bir Şey Yapmalı
Yukarıdaki kodun ilk halinin birden fazla şey yaptığı aşikar. Buffer’lar oluşturuyor, sayfayı indiriyor ve HTML dokümanı oluşturuyor. Diğer taraftan, son hali ise sadece bir şey yapıyor. Çünkü fonksiyonlar bir şey yapmalı. Onu çok iyi yapmalı ve sadece onu yapmalı. Buradaki problem “bir şey”i tanımlamanın zor oluşu. Örnek kodumuzun son versiyonu sadece bir şey mi yapıyor?
```
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception {
    if (isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
    return pageData.getHtml();
}
```
Aslında şu 3 şeyi yaptığını söyleyebiliriz:

- Sayfanın bir test sayfası olup olmadığına bakıyor.
- Eğer öyleyse includeSetupAndTeardownPages metodunu çağırıyor.
- HTML dokümanını oluşturuyor.

Peki hangisi? Metot sadece 1 iş mi yoksa 3 iş mi yapıyor? Metodu aşağıdaki şu tek cümle ile tanımlayabiliriz:

*“Bir sayfanın test sayfası olup olmadığını kontrol ederiz ve eğer öyleyse Setup ve Teardown sayfalarını dahil ederiz. Her iki durumda da dokümanı oluştururuz.”*

> Eğer bir fonksiyon sadece isminde belirtilen adımları yapıyorsa, o fonksiyon bir şey yapıyordur.

Örnek kodumuzun ilk versiyonu kesinlikle birden fazla iş yapıyordu. İkinci versiyonunda bile 2 seviye soyutlama (two-level abstraction) yapıyordu, ki kodu küçülterek son haline getirebiliyor oluşumuz bile bunun kanıtı. Son halinde kodu daha fazla küçültemiyoruz. if ifadesini alıp yeni bir metot içerisine yazabilirdik. Ancak bu, koddaki soyutlama seviyesini değiştirmeden kodu yeniden yazmak olurdu. Fonksiyonlarımızın bir şeyi yaptığından emin olmak için fonksiyonumuzun içindeki ifadelerin fonksiyonumuzun adında ifade edilen ile aynı soyutlama seviyesinde olması gerekir.

## Switch İfadeleri
Kısa bir switch ifadesi yazmak zordur. Bir şey yapan switch de yazmak zordur. Doğaları gereği switch ifadeleri N tane şey yaparlar. Ve ne yazık ki, switch ifadelerinden her zaman kaçamıyoruz, ancak her switch ifadesinin alt seviye bir sınıfa gömüldüğünden ve tekrarlanmadığından emin olabiliriz. Elbette bunu polimorfizmle (çok biçimlilik) yaparız.

Şu koda bir bakalım, Employee’nin tipine bağlı işlemlerden sadece birini gösteriyor:
```
public Money calculatePay(Employee e) throws InvalidEmployeeType {
    switch (e.type) {
        case COMMISSIONED:
            return calculateCommissionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
        default:
            throw new InvalidEmployeeType(e.type);
    }
}
```
Bu fonksiyonla ilgili bir sürü problem var:

- Çok büyük ve Employee tipleri eklendikçe de büyümeye devam edecek.
- Kesinlikle birden fazla şey yapıyor.
- Tek Sorumluluk Kuralı’nı (SRP — Single Responsibility Rule) ihlal ediyor.
- Açık/Kapalı Prensibi’ni (OCP — Open/Closed Principle) de ihlal ediyor.

Bu kodun sorununun çözümü, switch ifadesini şu şekilde bir “Abstract Factory” tasarım deseni (design pattern) temeline gömmek ve başka hiç kimsenin görmesine izin vermemektir:
```
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}
-----------------------------------------------------------------
public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
------------------------------------------------------------------
public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommissionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmploye(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```
EmployeeFactoryImpl, Employee türevlerinin uygun örneklerini (instances) oluşturmak için switch ifadesini kullanacak ve calculatePay, isPayday ve deliverPay gibi çeşitli fonksiyonlar, Employee arayüzünden polimorfik olarak gönderilecektir.

Bence switch ifadeleri yalnızca şu durumlar için tolere edilebilirler:

- Yalnızca bir kez görüneceklerse
- Sistemin geri kalanının göremeyeceği şekilde bir arayüz ilişkisi arkasına gizleneceklerse
- Polimorfik nesneleri yaratmak için kullanılacaklarsa

## Açıklayıcı İsimler Kullanın
Yukarıdaki örneğimizde testableHtml fonksiyonumuzun ismini SetupTeardownIncluder.render olarak değiştirdim. Bu isim çok daha iyi, çünkü fonksiyonun yaptığı işi daha iyi tanımlıyor. private metotlarımızın isimlerine de aynı denklikte tanımlayıcı isimler verdim; isTestable ya da includeSetupAndTeardownPages gibi…

Ward’ın prensibini hatırlayın: “Programın her parçası beklediğiniz gibi çıktığında, temiz kod üzerinde çalıştığınızı anlarsınız.” Bu prensibi uygulamanın ilk adımı, tek bir şey yapan küçük fonksiyonlar için iyi isimler seçmektir. Küçük ve daha odaklı bir fonksiyon için açıklayıcı bir isim seçmek daha kolaydır.

Uzun isimler kullanmaktan kaçınmayın. Uzun tanımlayıcı isimler, kısa bilmece gibi isimlerden veya uzun bir yorumdan daha iyidir.

İsim seçmek için zaman harcamaktan korkmayın. Bir sürü farklı isim deneyin ve her biri ile kodu tekrar tekrar okuyun. Açıklayıcı isimler seçmek zihninizdeki tasarımı ortaya çıkaracak ve onu geliştirmenize yardım edecektir.

İsimlerinizde tutarlı olun. Fonksiyon isimlerinizde aynı isimleri, kelime gruplarını, sıfatları ve fiilleri kullanın.

## Argümanlar
Bir fonksiyon için ideal argüman sayısı sıfırdır. StringBuffer örneğimizi düşünelim. Onu bir örnek değişken (instance variable) olarak yazmak yerine argüman olarak da geçebilirdik. Ancak okuyucularımız değişkeni her gördüğünde tekrar tekrar yorumlamak zorunda kalacaklardı.

Argüman fonksiyon adından daha farklı bir soyutlama seviyesidir ve o noktada çok önemli olmayan bazı detayları bilmeye zorlar.

Argümanlar test açısından daha da zorlar. Hiç argüman yoksa önemsizdir. Bir tane varsa çok zor değildir. İki argüman biraz daha zorlayıcıdır ancak iki argüman sonrası yıldırıcı olabilir.

Bir fonksiyona tek bir argüman geçmenin iki yaygın sebebi vardır. Bir argüman ile bir soru soruyor olabilirsiniz; boolean fileExists(“My File”) gibi… Ya da belki bu argümanı işleme sokuyor, onu bir şeylere dönüştürüyor ve return ediyorsunuzdur. Bu iki kullanım şekli kullanıcıların bir fonksiyonda görmek isteyebilecekleri kullanım şekilleridir.

Tek argümanın biraz daha az kullanım şekli ise event’tir. Bu kullanım şeklinde girdi var ancak çıktı yoktur. Tüm program bu fonksiyonun çağrısını bir event (olay) olarak yorumlar ve argümanı sistemin durumunu değiştirmek için kullanır. Örneğin; void passwordAttemptFailedNtimes(int attempts). Bu kullanımda oldukça dikkatli olunmalıdır ve okuyucuya bunun bir event olduğu açıkça belli edilmelidir. İsimler ve bağlamlar dikkatli seçilmelidir.

## Boolean Argümanlar
Boolean argümanlar çirkindir. Fonksiyonlara parametre olarak geçmek ise korkunç bir pratiktir. Metodun imzasını karmaşıklaştırır ve “Bu fonksiyon birden fazla şey yapıyor.” diye bağırır. Argüman true ise bir şey yap, false ise başka bir şey yap.

## Nesne Argümanlar
Eğer bir fonksiyon 2 ya da 3 argümandan fazlasına ihtiyaç duyuyorsa, bu argümanlardan birkaçı bir sınıf ile sarmalanmalıdır.

Şu iki örnek çağırıma bakalım. Nesneler yaratarak argümanların sayısını azaltmak hile yapmak gibi görünebilir ama değildir:
```
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```
## Fiiller ve İsimler
Bir fonksiyon için iyi isim seçmek, fonksiyonun ve değişkenlerin niyetlerini açıklamak konusunda güzel bir başlangıç olabilir. Tek argümanlı (Monad) durumunda, fonksiyon ve argüman çok iyi bir fiil ve isim ikilisi olacak şekilde seçilmelidir. Örneğin write(name) imzasına sahip bir fonksiyon oldukça açıklayıcıdır. Hatta writeField(name) bize name’in bir alan (field) olduğunu ve bir yerlere yazılacağını söyler.

İkinci bir örnek olarak assertEquals metodu, assertExpectedEqualsActual (expected, actual) olarak yazılsaydı çok daha iyi olabilirdi. Bu yazım şekli, argümanların sırasını hatırlamanın zorunluluğunu ortadan kaldırır.

## Yan Etkiler Oluşturma (Side Effects)
Yan etkiler bizi kandırır. Fonksiyonunuz bir şeyi yapmaya söz verir ama yanında başka gizli şeyler de yapar. Bazen kendi sınıfının değişkenlerinde beklenmedik değişiklikler yapar. Bazen bunu fonksiyonlara geçilen parametreler ile ya da globaller ile yapar.

Şu zararsız koda bir bakalım:
```
public class UserValidator {
    private Cryptographer cryptographer;

    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```
Bu fonksiyon bir userName’i bir password’e eşlemek için standart bir algoritma kullanıyor. Eşleşirse true, eşleşmezse false dönüyor. Ancak bir yan etkisi var, o da Session.initialize() çağrısıdır. checkPassword, isminde söylendiği gibi şifreyi kontrol eder. Ancak Session’ın initialize edileceğini söylemiyor. Bu nedenle bu fonksiyonu çağıran, mevcut oturum verisini silme riski taşıyor.

Fonksiyonlar ya bir şeyler yaparlar ya da bir şeylere cevap verirler; ancak ikisini birden yapmazlar. Ya bir nesnenin yapısını değiştirirler ya da o nesne hakkında bilgi dönerler.

Şu örneği inceleyelim:
```
public boolean set(String attribute, String value);
```
Bu fonksiyon attribute isimli bir değişkene bir value atar ve başarılı ise true döner ya da attribute diye bir değişken yoksa false döner. Bu, şu tür tuhaf durumlara sebep olur:
```
if (set("username", "unclebob"))...
```
Okuyucunun açısıdan bir düşünelim. Ne anlama geliyor? username’in daha öncesinde unclebob olarak set edilip edilmediğini mi sorguluyor yoksa username’in başarılı bir şekilde unclebob olarak set edilip edilmediğini mi Anlamı buradan çıkarmak çok zor, çünkü net değil. Yazar fiil gibi kullanmak istemiş ancak if içerisinde bir sıfatmış hissi veriyor.

Bu problemi, fonksiyon ismini setAndCheckIfExists şeklinde yeniden düzenleyerek çözebiliriz. Ancak bu da if’in okunabilirliği için çok da yardımcı olmuyor. if’in okunabilirlik problemini de kodu şu şekilde düzenleyerek halledebiliriz:
```
if (attributeExists("username")) {
    setAttribute("username", "unclebob"); ...
}
```
## try/catch Bloklarını Ayırın
try/catch blokları doğaları gereği çirkindir. Normal işleyişi ve kodun yapısını karıştırır. Bu nedenle try ve catch bloklarının gövdelerini kendi fonksiyonlarına ayırmak daha iyidir.
```
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    } catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```
Bu örnekte delete fonksiyonu tamamen hata işleme (error processing) ile ilgilidir. Anlaması kolaydır. deletePageAndAllReferences fonksiyonu ise tamamen bir sayfayı silmenin süreçleri ile alakalıdır. Hata işleme yok sayılabilir. Bu kullanım, kodun daha kolay anlaşılması ve düzenlenebilmesi için güzel bir ayrım sağlar.

> Fonksiyonlar bir şeyi yapmalıdır, hata işleme ise başlı başına bir şeydir. Bu yüzden hataları işleyen bir metot başka bir şey yapmamalıdır.

## Error.java
```
public enum Error {
    OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT;
}
```
Bunun gibi sınıflar bağımlılık magneti gibidirler; diğer çoğu sınıf da import (dahil etmek/ithal etmek) etmeli ve kullanmalıdır. Error enum’ı değiştiği zaman, diğer tüm sınıflar da tekrar derlenmeli ve tekrar deploy (dağıtım) edilmelidir. Yazılımcılar yeniden build (inşa) etmek ve her şeyi yeniden deploy etmek istemediklerinden yeni hata kodları eklemezler; mevcut olanlardan kullanırlar.

Hata kodlarındansa, türetilmiş Error sınıfları kullanmak daha iyidir. Hiçbir yeri yeniden derlenmeye, deploy etmeye zorlamadan eklenip çıkartılabilirler.

Bazı yazılımcılar Edsger Dijkstra’nın yapısal programlama kurallarını takip ederler. Dijkstra her fonksiyonun bir giriş ve bir çıkışı olması gerektiğini söyler. Yani, bir fonksiyonun sadece bir return ifadesi olmalı, bir döngüde break, continue ya da goto ifadeleri asla ve asla olmamalıdır. Fonksiyonlar küçük olduğunda, bu kurallar küçük faydalar sağlar. Sadece büyük metotlarda bu ifadeler büyük faydalar getirir. Yani fonksiyonlarınızı küçük tutarsanız, birden fazla return, continue ya da break ifadesi kodunuza zarar veremez; hatta tek giriş - tek çıkış kuralından (single-entry, single-exit) bile daha açıklayıcı olabilir. Diğer bir taraftan goto ifadesi sadece çok büyük fonksiyonlarda bir anlam ifade eder bu nedenle goto kullanımından kaçınılmalıdır.

## Peki, Böyle Metotları Nasıl Yazabilirsiniz?
Yazılım yapmak, yazmanın bir diğer türevidir. Bir makale hakkında bir şeyler yazdığınızda; önce düşüncelerinizi toplarsınız, daha sonra kulağa iyi gelene kadar düzenleme yaparsınız. İlk taslak acemi ve dağınık olabilir.

Ben kod yazıyorken fonksiyonlarımın ilk halleri uzun ve karışık olur. Bir sürü girintilemeler ve iç içe döngüleri olur. Uzun argüman listeleri olur. Verdiğim isimler keyfidir ve tekrarlanmış (duplicate) kodlar vardır. Ancak bu acemi satırların her birini kapsayan testlerim de vardır.

Daha sonra kodu fonksiyonlara bölerek, isimleri değiştirerek, tekrarlanmış kodları çıkartarak (extract) rafine ederim. Metotları küçültürüm ve tekrar sıraya koyarım. Sonunda fonksiyonlarım, anlattığım tüm bu kurallara uygun olurlar.
