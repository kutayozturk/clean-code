Kodunuzun düzgün formatta (biçimde) olmasına dikkat etmelisiniz. Kodunuzun formatını belirleyen bir takım kurallar benimsemeli ve o kurallara her zaman uymalısınız. Eğer bir takımda çalışıyorsanız, takım olarak belli kuralları kabul etmeli ve tüm üyeler bu konuda hem fikir olmalıdır.

## Formatlamanın Amacı
Açık olalım; formatlama yani kodun biçimsel düzeni önemlidir. Kodu formatlamak iletişimle alakalıdır ve iletişim profesyonel bir geliştiricinin işinin ilk adımıdır. Belki de profesyonel bir geliştiricinin en önemli işinin “kodun çalışması” olduğunu düşündünüz. Umuyorum bu kitap, bu düşüncenizi değiştirmiştir.

Bugün eklediğiniz yeni bir fonksiyonalite, muhtemelen bir sonraki sürümde tekrar değişecektir ve kodunuzun okunabilirliği, yapacağınız tüm değişiklikler için köklü bir etkiye sahip olacaktır. Kodlama tarzı ve okunabilirliği, orijinal kod değiştikten çok çok sonra da bakımını etkileyecektir. Disiplininiz ve kodlama tarzınız orada durmaya devam edecektir. Peki, iletişim kurmamıza yardım eden en iyi formatlama konuları neler?

## Dikey Formatlama
Dikey boyut ile başlayalım. Bir kaynak dosyası ne kadar büyük olmalı? Örneğin Java kaynak dosyaları en fazla ne kadar büyük olabiliyorlar? Aşağıdaki grafikte 7 farklı proje gösteriliyor: JUnit, Fitnesse, TestNG, Time and Money, JDepend, Ant ve Tomcat. Kutuların etrafındaki çizgiler, her projedeki minimum ve maksimum dosya boyutlarını gösteriyor. Kutu, dosyaların yaklaşık olarak üçte birini, kutunun ortası ise yaklaşık ortalamayı gösteriyor.

Yani Fitnesse projesindeki dosyalar ortalama 65 satır civarı ve dosyaların üçte biri 40 ve 100+ satır arası. Fitness’taki en büyük dosya 400 ve en küçüğü 6 satır.

JUnit, Fitnesse ve Time and Money küçük dosyalardan oluşmuş. Hiçbiri 500 satırın üzerinde değil ve bu dosyaların bir çoğu 200 satır. Diğer bir taraftan Tomcat ve Ant binlerce satırlık dosyalara sahip ve yarısından fazlası 200 satırın üzerinde.

Bu grafik bize ne anlatıyor? Çok önemli sistemleri, projeleri (maksimum limitimizi 500 tutarak) 200 satırlık dosyalardan oluşturabiliriz. Çünkü küçük dosyalar büyük dosyalardan her zaman daha anlaşılırdır.

## Gazete Metaforu
İyi yazılmış bir makale düşünelim. Onu dikey şekilde okursunuz. En başında yazının ne hakkında olduğunu söyleyen bir başlık beklersiniz ve ona göre yazıyı okuyup okumayacağınıza karar verirsiniz. İlk paragraf tüm hikayenin özetini verir. Aşağılara devam ettikçe detaylar artar.

Kaynak kodun gazete makalesi gibi okunmasını isteriz. İsmi basit ama açıklayıcı olmalı. İsmin kendisi, bize doğru modülde olup olmadığımızı söyleyebilecek kadar net olmalı. Detaylar aşağılarda olmalı.

Bir gazete de bir çok makaleden oluşur ve bu makalelerin çoğu küçüktür. Bir sürü makale sadece bir sayfaya sığabilir. Eğer gazeteler sayfalarca yazılmış tek bir makaleden oluşsaydı, muhtemelen kimse o gazeteyi okumazdı.

## Kavramlar Arası Dikey Açıklık
Hemen hemen tüm kodları soldan sağa ve yukarıdan aşağıya okuruz. Her satır bir açıklamayı ve satırlar bütünü bir düşünceyi temsil eder. Bu düşünceler birbirlerinden boşluk satırlarıyla ayrılmalıdırlar.

Aşağıdaki örneğe bakalım; paket tanımını, import ifadesini ve her bir fonksiyonu birbirinden ayıran boşluklar var. Kodda görselliğin en temel kuralı budur. Her boş satır yeni ve ayrı bir kavramın işaretidir.

Örneğin şu şekildedir:
```
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
        Pattern.MULTILINE + Pattern.DOTALL
    );
    
    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
    
    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```
Şurada ise boşlukları silinmiş versiyonu; okunabilirliği ciddi derecede etkiliyor:
```
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
        Pattern.MULTILINE + Pattern.DOTALL
    );
    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```
## Dikey Yoğunluk
Eğer açıklık (openness), kavramların ayırt edilmelerini sağlıyorsa; dikey yoğunluk da yakın ilişkiyi temsil ediyor olmalı. Öyle ki; birbirine sıkıca bağlı kod satırları dikey olarak yoğun şekilde görünür. Aşağıdaki gereksiz yorumların, 2 örnek değişken arasındaki ilişkiyi nasıl böldüğüne bakalım:
```
public class ReporterConfig {
    
    /**
     * The class name of the reporter listener
     */
    private String m_className;
    
    /**
     * The properties of the reporter listener
     */
    private List < Property > m_properties = new ArrayList < Property > ();
    
    public void addProperty(Property property) {
        m_properties.add(property);
    }
Şu kodun ise okunması çok daha kolay. Koda bakarız ve bunun iki değişkeni ve bir metodu olan bir sınıf olduğunu anlarız:

public class ReporterConfig {
    private String m_className;
    private List < Property > m_properties = new ArrayList < Property > ();
    
    public void addProperty(Property property) {
        m_properties.add(property);
    }
```
    
## Dikey Uzaklık
Birbirine yakın kavramlar, dikey olarak birbirlerine yakın şekilde tutulmalıdırlar. Sonrasında bu ilişkili kavramlar -çok iyi bir sebebimiz olmadıkça- ayrı parçalara ayrılmamalıdırlar. Okuyucuların kodumuzu okurken sayfadan sayfaya geçmelerini istemeyiz.

Değişkenler, kullanıldıkları yere en yakın şekilde tanımlanmalıdır, şu şekilde:
```
private static void readPreferences() {
    InputStream is = null;
    try {
        is = new FileInputStream(getPreferencesFile());
        setPreferences(new Properties(getPreferences()));
        getPreferences().load(is);
    } catch (IOException e) {
        try {
            if (is != null)
                is.close();
        } catch (IOException e1) {}
    }
}
```
Döngüler için kullandığımız kontrol değişkenleri ise döngüyle beraber tanımlanmalıdır. Şu fonksiyonda olduğu gibi:
```
public int countTestCases() {
    int count = 0;
    for (Test each: tests)
        count += each.countTestCases();
    return count;
}
```
## Örnek Değişkenler (Instance Variables)
Sınıfın en tepesinde tanımlı olmalıdır, ancak gene de bu durum örnek değişkenlerin dikey uzaklığını artırmamalıdır. Çünkü iyi tasarlanmış bir sınıfta örnek değişkenler sınıfın metotları tarafından defalarca kullanılırlar.

Örnek değişkenlerin nereye konulması gerektiğine dair çok tartışmalar yapıldı. C++’ta, sözde makas (scissors) kuralını takip ettik, bu kural tüm örnek değişkenlerin alta tanımlanması gerektiğini söyler. Java’daki genel kullanım şekli ise sınıfın üst kısmına koymaktır. Aslına bakarsanız kullanım şekillerinden birini diğerine tercih etmek için bir sebep göremiyorum. Önemli olan örnek değişkenlerin iyi bilinen bir yere tanımlanmasıdır. Böylece herkes tanımları bulabilmek için nereye bakması gerektiğini bilir.

Aşağıdaki TestSuite sınıfında tanımlanmış iki değişken göreceksiniz. Onları saklamak için daha iyi bir yer bulunamazdı:
```
public class TestSuite implements Test {
    static public Test createTest(Class << ? extends TestCase > theClass,
        String name) {
        ...
    }
    getTestConstructor(Class << ? extends TestCase > theClass)
    throws NoSuchMethodException {
        ...
    }
    public static Test warning(final String message) {
        ...
    }
    private static String exceptionToString(Throwable t) {
        ...
    }
    private String fName;
    private Vector < Test > fTests = new Vector < Test > (10);
    public TestSuite() {}
    public TestSuite(final Class << ? extends TestCase > theClass)                                                          {
        ...
    }
    public TestSuite(Class << ? extends TestCase > theClass, String name) {
            ...
        }
        ...............
}
```
## Bağımlı (Dependent) Fonksiyonlar
Eğer bir fonksiyon başka bir fonksiyonu çağırıyorsa, bu fonksiyonlar birbirine yakın olmalı ve eğer mümkünse çağıran çağrılanın üstünde olmalıdır. Bu durum programa doğal bir akış sağlar.

Aşağıdaki koda bakalım; en üstteki fonksiyonun aşağıdaki fonksiyonu nasıl çağırdığına ve onun da diğerlerini nasıl çağırdığına bakın. Bu akış, çağırılan fonksiyonu bulmayı ve tüm modülün okunuşunu kolaylaştırıyor:
```
public class WikiPageResponder implements SecureResponder {
    protected WikiPage page;
    protected PageData pageData;
    protected String pageTitle;
    protected Request request;
    protected PageCrawler crawler;
    
    public Response makeResponse(FitNesseContext context, Request request)  throws Exception {
        String pageName = getPageNameOrDefault(request, "FrontPage");
        if (page == null)
            return notFoundResponse(context, request);
        else
            return makePageResponse(context);
    }
    
    private String getPageNameOrDefault(Request request, String defaultPageName) {
        String pageName = request.getResource();
        if (StringUtil.isBlank(pageName))
            pageName = defaultPageName;
        return pageName;
    }
    protected void loadPage(String resource, FitNesseContext context) throws Exception {
        WikiPagePath path = PathParser.parse(resource);
        crawler = context.root.getPageCrawler();
        crawler.setDeadEndStrategy(new VirtualEnabledPageCrawler());
        page = crawler.getPage(context.root, path);
        if (page != null)
            pageData = page.getData();
    }
    
    private Response notFoundResponse(FitNesseContext context, Request request) throws Exception {
        return new NotFoundResponder().makeResponse(context, request);
    }
    
    private SimpleResponse makePageResponse(FitNesseContext context) throws Exception {
        pageTitle = PathParser.render(crawler.getFullPath(page));
        String html = makeHtml(context);
        SimpleResponse response = new SimpleResponse();
        response.setMaxAge(0);
        response.setContent(html);
        return response;
    }
```

## Bir satır ne kadar geniş olmalı?
Programcılar genelde kısa satırları tercih ederler. Benim kuralım ise bir satırda asla sağa kaydırma (scroll) yapma gereksinimi duymamak. Ancak bugünlerde monitörlerimiz çok geniş ve genç programcılarımız yazı tipini öyle küçük tutuyor ki, ekrana 200 karakter sığdırabiliyorlar. Bu iyi değil. Bir satır 120 karakterden uzun olmamalıdır.

Yatay Açıklık ve Yoğunluk
Yatay boşluğu, güçlü ilişki içerisinde olanları birleştirmek ve daha zayıf olanları ayırt etmek için kullanırız.

Şu fonksiyona bakalım:
```
private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}
```
Atama ifadelerinin iki ayrı tarafı olur: sağ ve sol. İşte boşluklar bu ayrımı net kılar. İşlemcileri (operators) vurgulamak için onlardan önce bir boşluk bıraktım. Diğer bir taraftan fonksiyon isimleri ile açılış parantezi arasına ise boşluk koymadım. Çünkü fonksiyon ve onun argümanları birbiriyle çok yakından alakalıdır. Ancak argümanların ayrı olduğunu vurgulamak için onları fonksiyon açılış parantezinden ayırdım.

## Girintileme
Bir sınıf hiyerarşilerden oluşur. Sınıf içerisinde metotlar, metotlar içerisinde bloklar… Bu hiyerarşinin her bir seviyesi isimlerini tanımlayabildiğimiz kapsamlardır ve kapsamların bu hiyerarşilerini görünür yapabilmek için konumlarına göre satırları girintilendirmemiz gerekir.

Bir sınıf içerisindeki metotlar sınıfın bir seviye sağına, metotların gerçekleştirimleri bu metot tanımlarının bir seviye sağına, metot içerisindeki bloklar ise her bir bloğun sağına girintilidir. Girintileme olmadan programlar insanlar tarafından neredeyse okunmaz olurdu.

## Girintilemeyi Bozmak

Kısa if ifadeleri, kısa while’lar ya da kısa fonksiyonlar için girintilemeyi bozmak bazen cazip gelir. Ancak bunu ne zaman yapsam, tekrar girintiliyorum ve ifadelerimi tek satıra indirgemekten kaçınıyorum. Şunun gibi hizalamayı ve genişletmeyi tercih ediyorum:
```
public class CommentWidget extends TextWidget {
    public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";
    
    public CommentWidget(ParentWidget parent, String text) {
        super(parent, text);
    }
    
    public String render() throws Exception {
        return "";
    }
}
```
## Takım Kuralları
Her programcının kendi formatlama kuralları vardır. Ancak bir takımda çalışıyorsa, orada takımın kuralları geçerli olur. Geliştiricilerden oluşan bir takım kendi basit formatlama tarzı üzerinde anlaşmalıdır ve takımın her bir üyesi bu tarzı kullanmalıdır. Yazılımımızın anlaşmazlık yaşamış bir grup insan tarafından yazılmış gibi değil de, tutarlı görünmesini isteriz.

2002'de bir projeme başladığımda, takımla beraber kodlama tarzımız üzerinde çalışmak için bir araya geldik. Ayraçları nereye koyacağımıza, sınıf, değişken, metot isimlerimizin nasıl olması gerektiğine karar verdik. Daha sonra bu kuralları geliştirme aracımızın (IDE) Code Formatter’ına (geliştirme araçlarının sunduğu otomatik kod biçimlendirme araçları) tanımladık ve hep ona bağlı kaldık. Sadece benim tercihim değil, takımın da kararını verdiği kurallardı bunlar. Takımın bir üyesi olarak, ben de hep bu kuralları takip ettim.

İyi bir yazılımın, iyi okunabilen dokümanlardan oluştuğunu unutmayın. Tutarlı ve akıcı tarzları olmalı. Okuyucu bir dosyada gördüğü formatlama tarzının, diğer tüm dosyalarda da olduğunu düşünebilmeli.

Uncle Bob’un Formatlama Kuralları
Burada Bob amcamız kişisel olarak kullandığı formatlama kurallarını koda dökerek anlatmış. Ancak sınıf isminden hemen sonra (değişkenlerden önce), sınıf değişkenleri arasında ve sınıf bitmeden hemen önce (en son metottan sonra) koymadığı boşlukların beni üzdüğünü belirtmeden geçemedim <:) Sahi o boşlukları neden koymamış, fikri olan var mı?
```
public class CodeAnalyzer implements JavaFileAnalysis {
    private int lineCount;
    private int maxLineWidth;
    private int widestLineNumber;
    private LineWidthHistogram lineWidthHistogram;
    private int totalChars;
    
    public CodeAnalyzer() {
        lineWidthHistogram = new LineWidthHistogram();
    }
    
    public static List < File > findJavaFiles(File parentDirectory) {
        List < File > files = new ArrayList < File > ();
        findJavaFiles(parentDirectory, files);
        return files;
    }
    
    private static void findJavaFiles(File parentDirectory, List < File > files) {
        for (File file: parentDirectory.listFiles()) {
            if (file.getName().endsWith(".java"))
                files.add(file);
            else if (file.isDirectory())
                findJavaFiles(file, files);
        }
    }
    
    public void analyzeFile(File javaFile) throws Exception {
        BufferedReader br = new BufferedReader(new FileReader(javaFile));
        String line;
        while ((line = br.readLine()) != null)
            measureLine(line);
    }
    
    private void measureLine(String line) {
        lineCount++;
        int lineSize = line.length();
        totalChars += lineSize;
        lineWidthHistogram.addLine(lineSize, lineCount);
        recordWidestLine(lineSize);
    }
    
    private void recordWidestLine(int lineSize) {
        if (lineSize > maxLineWidth) {
            maxLineWidth = lineSize;
            widestLineNumber = lineCount;
        }
    }
    
    public int getLineCount() {
        return lineCount;
    }
    
    public int getMaxLineWidth() {
        return maxLineWidth;
    }
    
    public int getWidestLineNumber() {
        return widestLineNumber;
    }
    
    public LineWidthHistogram getLineWidthHistogram() {
        return lineWidthHistogram;
    }
    
    public double getMeanLineWidth() {
        return (double) totalChars / lineCount;
    }
    
    public int getMedianLineWidth() {
        Integer[] sortedWidths = getSortedWidths();
        int cumulativeLineCount = 0;
        for (int width: sortedWidths) {
            cumulativeLineCount += lineCountForWidth(width);
            if (cumulativeLineCount > lineCount / 2)
                return width;
        }
        throw new Error("Cannot get here");
    }
    
    private int lineCountForWidth(int width) {
        return lineWidthHistogram.getLinesforWidth(width).size();
    }
    
    private Integer[] getSortedWidths() {
        Set < Integer > widths = lineWidthHistogram.getWidths();
        Integer[] sortedWidths = (widths.toArray(new Integer[0]));
        Arrays.sort(sortedWidths);
        return sortedWidths;
    }
}
```
