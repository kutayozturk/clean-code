> Kötü koda yorum yazmayın, onu yeniden yazın.

Yorumları yazmamızın amacı, kodda kendimizi iyi ifade edemediğimiz noktaları telafi etmektir. Yorumlar her zaman koddaki kusurlarımızdır, çünkü kendimizi onlarsız nasıl ifade edebileceğimizi bilmiyoruz.

Bu yüzden eğer kendimizi yorum yazarken buluyorsak, durup tekrar düşünelim. Kendimizi açıklayabileceğimiz başka yollar olup olmadığına bakalım. Yorumlara neden bu kadar kötü bakıyorum? Çünkü yorumlar yalan söylerler. Her zaman ve bilerek değil, ancak çoğunlukla söylerler.

> Bir yorum ne kadar eski olursa, yazıldığı koddan bir o kadar uzak ve bir o kadar yanlış oluyor. Sebebi basit, yazılımcılar yorumları güncellemezler.

Örneğin şu yoruma ve ifade etmek istediği satıra bakalım:

```
MockRequest request;
private final String HTTP_DATE_REGEXP =
        "[SMTWF][a-z]{2}\\,\\s[0-9]{2}\\s[JFMASOND][a-z]{2}\\s" +
                "[0-9]{4}\\s[0-9]{2}\\:[0-9]{2}\\:[0-9]{2}\\sGMT";
private Response response;
private FitNesseContext context;
private FileResponder responder;
private Locale saveLocale;
// Example: "Tue, 02 Apr 2003 22:18:49 GMT"
```

Açıklayıcı satır ve HTTP_DATE_REGEXP sabiti arasına muhtemelen daha sonradan değişkenler sokulmuş. Ancak yorum satırı güncellenmeden orada kalmış.

Yanlış yorumlar, hiç yorum olmamasından da kötüdür. Aldatıcılar ve yanlış yönlendiriyorlar. Asla başarılamayacak şeyleri vadediyorlar. Gerçek sadece bir yerdedir, kodda. Sadece kod size gerçekte ne yaptığını anlatabilir. Sadece kod gerçek ve doğru bilginin kaynağıdır.

## Yorumlarla Kötü Kodu Süslemeyin
Yorum yazmanın en yaygın motivasyonlarından biri de kötü koddur. Bir modül yazar ve onun kafa karıştırıcı, dağınık olduğunu biliriz. Bu yüzden kendimize “Yorum yazsam iyi olacak.” deriz. Aslında, temiz yazsak iyi olacak.

Bir kaç yorumu olan temiz ve açıklayıcı kod, bir sürü yorumu olan karmaşık ve darmadağın bir koddan daha iyidir. Yarattığın karmaşayı açıklamak için yazacağın yoruma harcadığın enerjiyi, bu karmaşayı temizlemek için harcamalısın.

Örneğin bu mu:
```
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```
Yoksa şu mu:
```
if (employee.isEligibleForFullBenefits())
```
Koddaki asıl niyeti açıklayabilmek sadece bir kaç saniye alıyor, ancak çoğu durumda, yazmak istediğin yorumdaki aynı şeyi söyleyebilen bir metot yazmak meseledir.

> Gerçekten iyi bir yorum, onu yazmamanın bir yolunu bulduğunuz yorumdur.

## İyi Yorumlar
Bazı yorumlar zorunludur ve faydalıdır.

## Yasal Yorumlar
Bazen bizim kurumsal kodlama standartlarımız yasal sebelerden ötürü bizi kesin yorumlar yazmaya zorlar. Örneğin telif hakkı (copyright) ve yazarlık (authorship) durumları gereklidir ve her kaynak dosyanın başına böyle bir yorum koymak mantıklıdır.

Burada örneğin, zamanında çalıştığım bir projede her kaynak dosyasının başına koyduğumuz standart bir yorum başlığı var:
```
// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
 // Released under the terms of the GNU General Public License version 2 or later.
 ```
Tüm şartlar ve koşulları yorum içine koymaktansa, mümkünse standart bir lisans ya da dış bir dokümana referans tercih edilmelidir.

## Bilgilendirici Yorumlar
Şu yoruma bakalım; abstract bir metodun dönüş değerini açıklıyor. Fonksiyon ismini responderBeingTested şeklinde değiştirerek yorumu gereksiz hale getirebilirdik:
```
// Returns an instance of the Responder being tested.
protected abstract Responder responderInstance();
```
Daha iyi bir örnek:
```
// format matched kk:mm:ss EEE, MMM dd, yyyy
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```
Yorum yazmak yerine eğer kod, tarihlerin ve zamanların formatını dönüştüren bir sınıfa taşındaydı çok daha açık ve temiz olabilirdi.

Bazen diğer programcıları kesin sonuçlar hakkında uyarmak iyi olabilir. Örneğin burada, belli bir test metodunun neden kapatıldığını açıklayan bir yorum var:
```
// Don't run unless you have some time to kill.
public void _testWithReallyBigFile() {
    writeLinesToFile(10000000);
    response.setBody(testFile);
    response.readyToSend(this);
    String responseString = output.toString();
    assertSubString("Content-Length: 1000000000", responseString);
    assertTrue(bytesSent > 1000000000);
}
```
Bugünlerde bir testi, açıklama da ekleyerek @Ignore ile kapatabiliriz. Ancak JUnit 4'ten öncesinde, metodun başına bir _ koymak yaygın bir adetti.

## TODO Yorumları
Kodda yapmak istediklerimizi //TODO şeklinde yorum olarak yazabiliriz.

Sıradaki örnekte TODO yorumu, fonksiyonun neden bozuk bir gerçekleştirimi olduğunu ve fonksiyonun gelecekte nasıl olması gerektiğini söylüyor:
```
//TODO-MdM these are not needed
// We expect this to go away when we do the checkout model
protected VersionInfo makeVersion() throws Exception {
    return null;
}
```
TODO’lar programcıların yapmaları gereken ancak o anda bazı sebeplerden ötürü yapmadıkları işler içindir. Kullanımdan kaldırılan (deprecated) bir özelliği silmek için hatırlatıcı olabilir, birilerinin daha iyi bir isim düşünmesini isteyebiliriz ya da başka birilerinin probleme göz atması için bir rica olabilir. TODO ne olursa olsun, sistemde kötü kod bırakmak için bahane değildir.

## Javadoc’lar
İyi tanımlanmış bir API kadar yardımcı olan başka bir şey yoktur. Eğer başkalarının kullanımına sunacağınız bir API yazıyorsanız, kesinlikle iyi Javadoc’lar da yazmalısınız. Ancak Javadoc’lar da diğer tüm yorumlar gibi yanıltıcı ve aldatıcı olabilir.

## Kötü Yorumlar
Çoğu yorum bu kategoriye girer. Eğer gerçekten yorum yazmaya karar verdiyseniz, zamanınızı yazabileceğiniz en iyi yorumu yazmak için harcadığınızdan emin olun.

Zamanında çalıştığım bir projeden bulduğum bir örnek, bir yorumun faydalı olabildiği bir örnek. Ancak yazarın acelesi varmış ya da çok dikkatini vermemiş:
```
public void loadProperties() {
    try {
        String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
        FileInputStream propertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
    } catch (IOException e) {
        // No properties files means all defaults are loaded
    }
}
```
catch bloğundaki yorum ne demek? Yazara bir şey ifade ettiği belli. Eğer bir IOException alırsak, bu demektir ki *.properties dosyası yok ve bu durumda tüm varsayılanlar (default) yüklenmiş.

Peki tüm varsayılanları kim yükledi? loadProperties.load çağrılmadan önce de yüklenmişler miydi? Ya da loadProperties.load istisnayı yakaladı, varsayılanları yükledi ve bizim için görmezden mi geldi? Ya da loadProperties.load, dosyayı yüklemeye başlamadan önce varsayılanları yükledi mi? Yoksa yazar catch bloğunu boş bıraktığı için kendini mi rahatlatmaya çalışıyordu? Ya da en kötüsü, yazar buraya tekrar geleceğini ve varsayılanları yükleyen kodu yazacağını söyleyen notu mu bıraktı kendine?

Bize yardım edebilecek tek şey, bu kodu test etmektir.

> Ne anlama geldiğini anlamamız için diğer modüllere bakmanızı gerektiren hiçbir yorum, tükettiği bitlere değmeyecektir.

## Gereksiz Yorumlar
Aşağıdaki örnekte kodun kendisini okumak, yorumu okumaktan daha kısa sürecektir:
```
// Utility method that returns when this.closed is true.
// Throws an exception if the timeout is reached.
public synchronized void waitForClose(final long timeoutMillis)
        throws Exception {
    if (!closed) {
        wait(timeoutMillis);
        if (!closed)
            throw new Exception("MockResponseSender could not be closed");
    }
}
```
Burada ise Apache Tomcat’in kaynak kodlarından alınmış, tamamıyla gereksiz ve faydasız bir yorum örneği var. Bu yorumlar sadece karmaşaya ve belirsizliğe sebep olur, belgelemeye ise hiçbir faydası olmaz:
```
public abstract class ContainerBase implements Container, Lifecycle,     Pipeline, MBeanRegistration, Serializable {
    /**
     * The processor delay for this component.
     */
    protected int backgroundProcessorDelay = -1;
    /**
     * The lifecycle event support for this component.
     */
    protected LifecycleSupport lifecycle = new LifecycleSupport(this);
    /**
     * The container event listeners for this Container.
     */
    protected ArrayList listeners = new ArrayList();
```

## Zorunlu Yorumlar
Her fonksiyonun bir Javadoc’u ya da her değişkenin bir yorumu olması gerektiğini söyleyen kurallar saçmadır. Örneğin şu koddaki yorumlar mevcut koda hiçbir şey katmaz:
```
/**
 * @param title             The title of the CD
 * @param author            The author of the CD
 * @param tracks            The number of tracks on the CD
 * @param durationInMinutes The duration of the CD in minutes
 */
public void addCD(String title, String author,
                  int tracks, int durationInMinutes) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cd.tracks = tracks;
    cd.duration = duration;
    cdList.add(cd);
}
```
## Günlük Gibi Yazılan Yorumlar
Bazı kişiler bir modülü her düzenlemeye başladıklarında, başladıkları tarihi yorum olarak eklerler. Bu yorumlar bir günlük gibi birikir:

Uzun bir zaman önce bu logları yaratmak için iyi bir sebebimiz vardı; çünkü bunu bizim için yapabilecek kaynak kod kontrol sistemlerimiz (source kod management systems) yoktu. Ancak bugünlerde bu yorumlar sadece kalabalık yapıyorlar.

Aşağıdaki ilk yorum yerinde görünüyor, catch bloğunun neden yok sayıldığını açıklıyor. Ancak ikinci yorum tamamen etkisiz. Görünen o ki programcı bu fonksiyonda try/catch yazmaktan o kadar yılmış ki, ara vermeye ihtiyaç duymuş :-)
```
private void startSending() {
    try {
        doSending();
    } catch (SocketException e) {
        // normal. someone stopped the request.
    } catch (Exception e) {
        try {
            response.add(ErrorResponder.makeExceptionString(e));
            response.closeAll();
        } catch (Exception e1) {
            //Give me a break!
        }
    }
}
```
Değersiz ve etkisiz bir yorum yazmaktansa, programcı problemi kodun yapısını geliştirerek çözebileceğinin farkına varabilmeliydi. Şu şekilde try/catch bloğunu ayrı metoda çıkarmalıydı:
```
private void startSending() {
    try {
        doSending();
    } catch (SocketException e) {
        // normal. someone stopped the request.
    } catch (Exception e) {
        addExceptionAndCloseResponse(e);
    }
}

private void addExceptionAndCloseResponse(Exception e) {
    try {
        response.add(ErrorResponder.makeExceptionString(e));
        response.closeAll();
    } catch (Exception e1) {
     }
}
```
Javadoc’lar da etkisiz olabilir. Aşağıdaki Javadoc’lar çok iyi bilinen açık kaynak bir kütüphaneden alındı:
```
/**
 * The name.
 */
private String name;
/**
 * The version.
 */
private String version;
/**
 * The licenceName.
 */
private String licenceName;
/**
 * The version.
 */
private String info;
```
Yorumlardaki kes-yapıştır hatasını görebiliyor musunuz? Eğer yazar, yorumlar yazılırken (ya da yapıştırılırken), dikkatini vermiyorsa, okuyucunun bundan fayda sağlaması nasıl beklenebilir?

Bir Fonksiyon ya da Değişken Kullanabilecekken Yorum Yazmayın
Aşağıdaki koda bir bakalım:
```
// does the module from the global list <mod> depend on the
// subsystem we are part of?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem())
```
Yorum olmadan şu şekilde olabilirdi:
```
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```
Yazar benim yaptığım gibi kodu yeniden yapılandırmalıydı. Böylece yorum kaldırılabilirdi.

## Kapama Parantezlerine Koyduğumuz Yorumlar
Bazen yazılımcılar kapama parantezlerine özel yorumlar eklerler, aşağıdaki örnekte olduğu gibi. Uzun fonksiyonlarda bir anlam ifade etse de, küçük fonksiyonlarda sadece kalabalık sağlar. Bu nedenle, eğer bir kapama parantezine yorum ekliyorsanız, bunun yerine fonksiyonunuzu kısaltmayı denemelisiniz:
```
public class WC {
    public static void main(String[] args) {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        String line;
        int lineCount = 0;
        int charCount = 0;
        int wordCount = 0;
        try {
            while ((line = in.readLine()) != null) {
                lineCount++;
                charCount += line.length();
                String words[] = line.split("\\W");
                wordCount += words.length;
            } //while
            System.out.println("wordCount = " + wordCount);
            System.out.println("lineCount = " + lineCount);
            System.out.println("charCount = " + charCount);
        } // try
        catch (IOException e) {
            System.err.println("Error:" + e.getMessage());
        } //catch
    } //main
}
```
## Kapatılmış Kodlar
Aşağıdaki şu kodu görenler onu silmeye cesaret edemeyecek, orada bir sebeple durduğunu ve çok önemli olduğunu düşüneceklerdir:
```
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(),formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
```
Apache Commons’tan alınan şu örneğe bakalım:
```
this.bytePos = writeBytes(pngIdBytes, 0);
//hdrPos = bytePos;
writeHeader();
writeResolution();
//dataPos = bytePos;
if(writeImageData()) {
    writeEnd();
    this.pngBytes = resizeByteArray(this.pngBytes, this.maxPos);
} else {
    this.pngBytes = null;
}
return this.pngBytes;
```
Neden o iki satır kapalı? Önemliler mi? Ya da sadece birileri yıllar önce kapatmış ve temizlemeye değer mi görmemişler?

Kodları kapatmanın önemli olduğu zamanlar vardı, 60'lar. Ancak şimdi versiyon kontrol sistemlerimiz (SVN, Git vb.) var ve bu sistemler kodu bizim için akıllarında tutuyorlar.

## Yersiz Bilgi
Eğer bir yorum yazmanız gerekiyorsa, onun yakınında olduğu kodu tanımladığından emin olun. Kısmi bir yorum içerisinde, tüm sisteme ait bir bilgi vermeyin.

Aşağıdaki Javadoc yorumuna bakalım örneğin:
```
/**
 * Port on which fitnesse would run. Defaults to <b>8082</b>.
 *
 * @param fitnessePort
 */
public void setFitnessePort(int fitnessePort) {
    this.fitnessePort = fitnessePort;
}
Yorum kesinlikle fonksiyonu tanımlamıyor. Gereksiz bir bilgiyi, varsayılan port ayarını bize söylüyor. Ve elbette ayar değiştiğinde, bu yorumun da değişeceğinin hiçbir garantisi yok.

Bir yorum ve tanımladığı kod arasındaki bağlantı net olmalıdır. Eğer bir yorum yazmak için sıkıntı çekecekseniz, en azından okuyucunun yoruma ve koda baktığında ne demek istediğini anlayabilmesini istersiniz:

/*
 * start with an array that is big enough to hold all the pixels
 * (plus filter bytes), and an extra 200 bytes for header info
 */
this.pngBytes =new byte[((this.width +1)*this.height *3)+200];
```
Bir yorumun amacı kendi kendisini açıklamakta yetersiz olan kodu açıklamaktır. Ancak burada yorumun kendisi de bir açıklamaya ihtiyaç duyuyor.

Aşağıdaki modülü ilk XP Immersion için yazmıştım. Nasıl kötü kod ve yorum yazılıra bir örnek gibi. Daha sonra Kent Beck bu kodu onlarca öğrencinin önünde çok daha hoş bir hale getirdi:
```
/**
 * This class Generates prime numbers up to a user specified
 * maximum. The algorithm used is the Sieve of Eratosthenes.
 * <p>
 * Eratosthenes of Cyrene, b. c. 276 BC, Cyrene, Libya --
 * d. c. 194, Alexandria. The first man to calculate the
 * circumference of the Earth. Also known for working on
 * calendars with leap years and ran the library at Alexandria.
 * <p>
 * The algorithm is quite simple. Given an array of integers
 * starting at 2. Cross out all multiples of 2. Find the next
 * uncrossed integer, and cross out all of its multiples.
 * Repeat untilyou have passed the square root of the maximum
 * value. 
 *
 * @author Alphonse
 * @version 13 Feb 2002 atp
 */
public class GeneratePrimes {

    /**
     *
     * @param maxValue is the generation limit.
     */
    public static int[] generatePrimes(int maxValue) {
        if (maxValue >= 2) {
            int s = maxValue + 1; // size of array
            boolean[] f = new boolean[s];
            int i;
            for (i = 0; i < s; i++)
                f[i] = true;
            f[0] = f[1] = false;
            int j;
            for (i = 2; i < Math.sqrt(s) + 1; i++) {
                if (f[i]) { // if i is uncrossed, cross its multiples.
                    for (j = 2 * i; j < s; j += i)
                        f[j] = false; // multiple is not prime
                }
            }

            // how many primes are there?
            int count = 0;
            for (i = 0; i < s; i++) {
                if (f[i])
                    count++; // bump count.
            }

            int[] primes = new int[count];

            // move the primes into the result

            for (i = 0, j = 0; i < s; i++) {
                if (f[i]) // if prime
                    primes[j++] = i;
            }

            return primes; // return the primes
        } else // maxValue < 2
            return new int[0]; // return null array if bad input.
    }
}
```
Aşağıdaki kod ise yeniden düzenlenmiş versiyonu. Yorumların kullanımının önemli ölçüde sınırlandırılmış olduğuna bakın; koca modülde sadece 2 tane yorum var:
```
/**
 * This class Generates prime numbers up to a user specified
 * maximum. The algorithm used is the Sieve of Eratosthenes.
 * Given an array of integers starting at 2:
 * Find the first uncrossed integer, and cross out all its
 * multiples. Repeat until there are no more multiples
 * in the array.
 */
public class PrimeGenerator {

    private static boolean[] crossedOut;
    private static int[] result;

    public static int[] generatePrimes(int maxValue) {
        if (maxValue < 2) return new int[0];
        else {
            uncrossIntegersUpTo(maxValue);
            crossOutMultiples();
            putUncrossedIntegersIntoResult();
            return result;
        }
    }

    private static void uncrossIntegersUpTo(int maxValue) {
        crossedOut = new boolean[maxValue + 1];
        for (int i = 2; i < crossedOut.length; i++)
            crossedOut[i] = false;
    }

    private static void crossOutMultiples() {
        int limit = determineIterationLimit();
        for (int i = 2; i <= limit; i++)
            if (notCrossed(i)) crossOutMultiplesOf(i);
    }

    private static int determineIterationLimit() {
        // Every multiple in the array has a prime factor that
        // is less than or equal to the root of the array size,
        // so we don't have to cross out multiples of numbers
        // larger than that root.
        double iterationLimit = Math.sqrt(crossedOut.length);
        return (int) iterationLimit;
    }

    private static void crossOutMultiplesOf(int i) {
        for (int multiple = 2 * i;
             multiple < crossedOut.length; multiple += i)
            crossedOut[multiple] = true;
    }

    private static boolean notCrossed(int i) {
        return crossedOut[i] == false;
    }

    private static void putUncrossedIntegersIntoResult() {
        result = new int[numberOfUncrossedIntegers()];
        for (int j = 0, i = 2; i < crossedOut.length; i++)
            if (notCrossed(i)) result[j++] = i;
    }

    private static int numberOfUncrossedIntegers() {
        int count = 0;
        for (int i = 2; i < crossedOut.length; i++)
            if (notCrossed(i)) count++;
        return count;
    }
    
}
```
İlk yorumun gereksiz olup olmadığını tartışabiliriz, çünkü tıpkı fonksiyonu okur gibi yazılmış. Gene de okuyucunun algoritmayı okumasına yardım edeceğini düşünüyorum, bu nedenle yorumu tutmakta kararlıyım.

İkincisi ise hemen hemen zorunlu bir açıklama. Karekökün döngü limiti gibi kullanılmasının ardındaki gerekçeyi açıklıyor. Ne daha basit bir değişken adı ne de bu kadar temiz bir kodlama stili bulamazdım.

