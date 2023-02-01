Sistemlerimizdeki yazılımları nadiren kontrol ederiz. Bazen üçüncü taraf paketler satın alırız veya açık kaynak kullanırız. Diğer zamanlarda da şirketimizin diğer ekiplerinin bizler için yazdığı bileşenlere bağımlıyızdır. Ve bu yabancı kodları kodumuzla temiz bir şekilde birleştirmek zorunda kalırız.
Üçüncü Taraf Yazılım (Kod) Kullanmak
Bir arayüz kullanıcısı ile arayüz sağlayıcısı arasında doğal bir gerginlik vardır. Üçüncü taraf paketlerin ve çatıların (framework) sağlayıcıları, yazılımlarının geniş kitlelerce kullanılabilir olmaları ve bir çok ortamda çalışabilir olmaları için çabalarlar. Diğer bir taraftan kullanıcılar ise belirli ihtiyaçlara odaklanmış arayüzlerle çalışmak isterler. İşte gerginlik burada başlar.

Bir örnek olması açısından java.util.Map sınıfına bakalım:

![](https://miro.medium.com/max/640/1*bGJixq6btffOUpPd5DCrQg.webp)

(Not: Java 6'da putAll() ve toString() metotları kaldırılmış. Java 8'deki Map sınıfı için ise şuraya bakabilirsiniz. Bir çok metot eklenmiş ve putAll() geri gelmiş. toString() ise, özyinelemeli (recursive) işlemlerde Map kendi referansını içerdiğinden ve bu sebeple bir istisna fırlatılabileceğinden eklenmemiş.)

Map bir çok şey yapabilen metotlarla dolu bir arayüzdür. Bu güç ve esneklik yeri geldiğinde kullanışlıdır ancak bir çok sorumluluğu da beraberinde getirir. Örneğin uygulamamızda bir Map kullanıyor ve onu bir yerlere argüman olarak geçiyor olalım. Map’imizi kullananların içerisinden hiçbir şey silmemesi gerekebilir. Ancak Map arayüzü clear() metoduna sahiptir ve Map’i kullanan herkes bu güce sahiptir. Ya da tasarımımıza göre Map üzerinde sadece belirli tipte nesneleri tutuyor olabiliriz. Fakat Map, içindeki nesnelerin tiplerini güvenilir şekilde sınırlandırmaz. Herhangi bir kullanıcı, herhangi tipte bir nesneyi Map’e ekleyebilir.

Uygulamamızın sensörlerden oluşan bir Map’e ihtiyacı olsaydı, muhtemelen şu şekilde tanımlayacaktık:
```
Map sensors = new HashMap();
```
Daha sonra bir yerlerde tek bir sensöre erişmek isteseydik, şu şekilde erişecektik:
```
Sensor s = (Sensor)sensors.get(sensorId);
```
Bu çözüm işe yarar ancak kod temiz değildir. Bu kod bir Map üzerinden bir Object’e erişmenin ve onu doğru tipe dönüştürmenin (type casting) sorumluluğunu taşıyor. Kodun okunabilirliğini Generic’leri kullanarak büyük oranda geliştirebiliriz:
```
Map<Sensor> sensors = new HashMap<Sensor>();
.....
Sensor s = sensors.get(sensorId);
```
Map<Sensor> yapması gerekenden ya da istediğimizden daha fazla şey yapıyor ve bu çözüm de problemimizi çözmüyor.

Map<Sensor> örneğini (instance) serbestçe bir yerlere argüman olarak geçirmek demek; Map arayüzü her değişiklik yaptığında, düzeltilecek çok yerin olacağı anlamına gelir. Böyle bir değişikliğin pek olası olmadığını düşünebiliriz ancak Java 5'te Generic’lere destek geldiği zamanı hatırlayın. Map’lerin özgür kullanımını telafi etmek için gereken büyük değişiklik nedeniyle Generic kullanımını engelleyen sistemler gördük.

Map’in daha temiz bir kullanımı aşağıdaki gibidir.
```
public class Sensors {
    private Map sensors = new HashMap();
    public Sensor getById(String id) {
        return (Sensor) sensors.get(id);
    }
 
}
```
İşte burada Map arayüzü Sensors sınırının (boundary) ardına gizlenmiştir. Uygulamanın geri kalanı üzerinde çok az etki bırakarak değiştirilebilir. Generic kullanımı artık büyük bir sorun değil çünkü tip dönüşümü ve tip yönetimi, Sensors sınıfının içinde halledildi. Bu arayüz sadece uygulamanın ihtiyaçlarını karşılaması için oluşturuldu.

Map’in her kullanımında bu şekilde kapsüllenmesini önermiyoruz. Bunun yerine, Map’leri (veya sınırdaki başka herhangi bir arayüzü) sisteminizde bir yerlere geçirmemenizi tavsiye ediyoruz. Map gibi bir sınır arayüzü kullanıyorsanız, onu sınıfın içinde tutun. Map’leri return etmekten ve public API’lere argüman olarak geçmekten kaçının.

## Sınırları Keşfetmek ve Öğrenmek
Üçüncü taraf yazılımlar/çatılar daha kısa sürede daha fazla işlevsellik elde etmemize yardımcı olur. Ancak bu paketleri kullanmak istediğimizde test yazmak bizim için en iyi yol olabilir.

Üçüncü taraf kütüphanemizi nasıl kullanacağımızın net olmadığını varsayalım. Doküman okumak ve onu nasıl kullanacağımıza karar vermek için bir veya iki gün (veya daha fazla) harcayabiliriz. Ardından, kodumuzu üçüncü taraf kodu kullanacak şekilde yazabilir ve düşündüğümüz şekilde çalışıp çalışmayacağını görebiliriz. Hatta çoğu zaman kendimizi, aldığımız hataların kodumuzda mı yoksa onların hataları mı olup olmadığını anlamaya çalışırken buluruz.

Üçüncü taraf kodu öğrenmek ve entegre etmek zordur. İkisini birlikte yapmak daha da zordur. Üretim (production) kodumuzda yeni şeyler denemek yerine, üçüncü taraf kodu keşfetmek için bazı testler yazabiliriz. Jim Newkirk bu testleri, öğrenme testleri (learning tests) olarak nitelendiriyor.

Öğrenme testlerinde, üçüncü taraf API’yi uygulamamızda kullanmayı umduğumuz şekilde çağırırız. Aslında, bu API hakkındaki anlayışımızı kontrol eden kontrollü deneyler yapıyoruz. Testler, API’den ne istediğimiz üzerine odaklanır.

## Log4j Öğrenmek
Diyelim ki kendi yazdığımız loggerımız yerine, Apache Log4j paketini kullanmak istiyoruz. Biraz doküman okuduktan sonra ilk testimizi yazarız ve konsola bir “hello” yazmasını bekleriz:
```
@Test
public void testLogCreate() {
    Logger logger = Logger.getLogger("MyLogger");
    logger.info("hello");
}
  ```
Testi çalıştırdığımızda logger, Appender adı verilen bir şeye ihtiyacımız olduğunu belirten bir hata üretir. Biraz daha okuduktan sonra bir ConsoleAppender olduğunu farkediyoruz. ConsoleAppender da ekledikten sonra kod şu şekile geliyor:
```
@Test
public void testLogAddAppender() {
    Logger logger = Logger.getLogger("MyLogger");
    ConsoleAppender appender = new ConsoleAppender();
    logger.addAppender(appender);
    logger.info("hello");
}
  ```
Bu kez, Appender’ın çıktı üretmediğini görüyoruz. Google’dan küçük bir yardım aldıktan sonra aşağıdakileri deneyelim:
```
@Test
public void testLogAddAppender() {
    Logger logger = Logger.getLogger("MyLogger");
    logger.removeAllAppenders();
    logger.addAppender(new ConsoleAppender(
        new PatternLayout("%p %t %m%n"),
        ConsoleAppender.SYSTEM_OUT));
    logger.info("hello");
}
  ```
İşe yaradı ve konsola “hello” yazdırdık. ConsoleAppender’a konsola yazdığını söylemek zorundayız.

İlginçtir ki, ConsoleAppender.SYSTEM_OUT bağımsız değişkenini kaldırdığımızda, “hello” ifadesinin yine de yazdırıldığını görüyoruz. Fakat PatternLayout’u çıkardığımızda, gene hata alıyoruz. Bu çok garip bir davranış. Dokümantasyona biraz daha dikkatli baktığımızda, varsayılan
ConsoleAppender kurucusunun “yapılandırılmamış” olduğunu görüyoruz. Bu Log4j’de hata veya en azından bir tutarsızlık gibi görünüyor.

En sonunda aşağıdaki basit birim testini kodladık:
```
public class LogTest {
    private Logger logger;
    @Before
    public void initialize() {
        logger = Logger.getLogger("logger");
        logger.removeAllAppenders();
        Logger.getRootLogger().removeAllAppenders();
    }
    @Test
    public void basicLogger() {
        BasicConfigurator.configure();
        logger.info("basicLogger");
    }
    @Test
    public void addAppenderWithStream() {
        logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n"),
            ConsoleAppender.SYSTEM_OUT));
        logger.info("addAppenderWithStream");
    }
    @Test
    public void addAppenderWithoutStream() {
        logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n")));
        logger.info("addAppenderWithoutStream");
    }
}
  ```
Artık basit bir konsol loggerını nasıl başlatacağımızı biliyoruz. Bu bilgiyi de kendi logger sınıfımıza koyarak, uygulamanın geri kalanının Log4j sınır arayüzünden izole edilmesini sağlayabiliriz.

> Öğrenme testlerinin maliyeti yoktur. API’yi zaten öğrenmek zorundayızdır ve bu testleri yazmak bu bilgiyi elde etmenin kolay ve izole bir yoludur. Öğrenme testleri uzun vadede de pozitif bir getiri sağlar; üçüncü taraf yazılımların yeni sürümleri çıktığında, davranış farklılıklarının olup olmadığını görmek için öğrenme testlerini kullanırız.

Entegre olduktan sonra, üçüncü taraf kodun ihtiyaçlarımızla uyumlu kalacağına dair bir garantimiz yoktur. Yazarları, kodlarını kendi ihtiyaçlarını karşılamak üzere değiştirmek zorunda kalırlar, hataları düzeltirler veya yeni yetenekler eklerler. Kullananlara ise her release ile birlikte yeni riskler gelecektir. Testlerimize uyumlu olmayan değişiklikleri öğrenme testlerimiz sayesinde hemen bulabiliriz.

Öğrenme testlerinin sağladığı bilgiye ihtiyacınız olsun veya olmasın, üretim kodunun yaptığını yapan testleriniz tarafından uygulamanızda temiz bir sınır çizilmelidir.

Birkaç yıl önce, bir radyo iletişim sistemi için yazılım geliştiren bir takım içindeydim. Hakkında çok az bildiğimiz Transmitter isimli bir alt sistem vardı ve bu alt sistemden sorumlu kişiler arayüzünü tanımlama noktasına gelmemişlerdi. Engellenmek istemediğimizden, kodun bilinmeyen kısmından uzaklaşmaya başladık.

Dünyamızın nerede bittiğini ve yenisinin nerede başladığını oldukça iyi biliyorduk. Çalışırken bazen bu sınıra karşı koyduk. Sisler ve cehalet bulutları sınırların ardını gizlese de, çalışmalarımız sınır arayüzünün nasıl olmasını gerektiğini farketmemizi sağladı. Transmitter’dan şöyle bir beklentimiz vardı:

“Vericiyi verilen frekansta tuşla ve bu akıştan gelen verilerin analog bir temsilini yayınla.”

API’nin henüz tasarlanmamış olması nedeniyle nasıl yapılacağını bilmiyorduk. Bu yüzden detayları daha sonra çözmeye karar verdik.

Kendi arayüzümüzü tanımladık, adına Transmitter benzeri bir şey koyduk. Bu arayüze, sıklığı (frequency) ve veri akışı (data stream) alan bir metot ekledik. İstediğimiz arayüz buydu.

Bu arayüz bizim kontrolümüz altındaydı. İstemci kodunu daha okunaklı tutmaya ve sadece yapmak istediğiniz şeye odaklanmanıza yardımcı oluyordu.

Aşağıdaki şekilde, kontrolümüz dışında olan ve tanımlanmamış olan API’den CommunicationsController sınıflarını yalıttığımızı görebilirsiniz. Kendi uygulamamıza özgü arayüzü kullanarak, CommunicationsController sınıfımızı daha temiz ve verimli hale getirmiş olduk. Transmitter API’si tanımlandıktan sonra, boşluğu kapatmak için TransmitterAdapter yazdık. ADAPTER2, API ile olan etkileşimi sarmalıyor ve API geliştikçe değiştirmemiz gereken tek yer burası olmuş oluyordu:

![](https://miro.medium.com/max/720/1*97ejCiGAsbvfBKTQUiINpQ.webp)
  
Bu tasarım aynı zamanda test kodunda da uygun bağlantı noktası sağlar. Uygun bir FakeTransmitter kullanarak, CommunicationsController sınıflarını test edebiliriz. Ayrıca, API’yi doğru kullandığımızdan emin olduğumuz Transmitter API’ye sahip olduğumuzda, sınır testleri de oluşturabiliriz.

## Temiz Sınırlar
Sınırlarda değişimler olur. İyi yazılımlar, büyük yatırımlar ve yeniden çalışmalar olmadan değişikliklere uyum sağlarlar. Kontrolümüz dışında olan kodları kullandığımızda, yatırımımızı korumak için özel bir dikkat göstermeli ve gelecekte yapılacak değişikliklerin çok pahalı olmayacağından emin olmalıyız.

Sınırlardaki kod, beklentileri tanımlayan kesin ayırımlara ve testlere ihtiyaç duyar. Kodumuzun üçüncü taraf yazılımların ayrıntılarıyla ilgili çok fazla şey bilmesini önlemeliyiz. Kontrol etmediğimiz bir şeyden çok, kontrol ettiğimiz bir şeye güvenmek daha iyidir, çünkü sonunda o bizi kontrolü altına alacaktır.
  
  
  
