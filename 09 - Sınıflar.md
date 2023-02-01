## Sınıf Düzeni
Java kodlama standartlarına (code conventions) göre, bir sınıf değişkenlerle başlamalıdır. Eğer varsa, public static sabitler önce gelmelidir. Ardından private static değişkenler ve onu takip eden private instance (örnek) değişkenleri. public bir değişkene sahip olmak için gerçekten iyi bir sebebiniz olmalıdır.

Değişkenlerden sonra public fonksiyonlar gelmelidir. public fonksiyonlardan hemen sonra ise public bir fonksiyon tarafından çağrılmış yardımcı private metotlar gelebilir.

## Kapsülleme (Encapsulation)
Değişkenlerimizi ve util fonksiyonlarımızı gizli tutmak isteriz, ancak bazen bir değişkeni veya util metodu testlerden erişilebilmesi için protected yapmamız gerekebilir. (Uncle Bob, bir metodun testinin olmasının o metodun kapsüllenmesinden daha önemli olduğuna vurgu yapıyor.)

## Sınıflar Küçük Olmalıdır
Sınıf tasarımı konusundaki ilk kural sınıfların küçük olmaları gerektiğidir. Buradaki sorumuz, ne kadar küçük?

Fonksiyonların boyutuna, satır sayılarına bakarak karar verdik; ancak sınıfların boyut ölçümünün daha farklı bir birimi vardır: sorumluluklarının sayısı.

Aşağıdaki örneğe ait sınıfta 70 metot var, çok çok büyük:
```
public class SuperDashboard extends JFrame implements MetaDataUser
public String getCustomizerLanguagePath()
public void setSystemConfigPath(String systemConfigPath)
public String getSystemConfigDocument()
public void setSystemConfigDocument(String systemConfigDocument)
public boolean getGuruState()
public boolean getNoviceState()
public boolean getOpenSourceState()
public void showObject(MetaObject object)
public void showProgress(String s)
public boolean isMetadataDirty()
public void setIsMetadataDirty(boolean isMetadataDirty)
public Component getLastFocusedComponent()
public void setLastFocused(Component lastFocused)
public void setMouseSelectState(boolean isMouseSelected)
public boolean isMouseSelected()
public LanguageManager getLanguageManager()
public Project getProject()
public Project getFirstProject()
public Project getLastProject()
public String getNewProjectName()
public void setComponentSizes(Dimension dim)
public String getCurrentDir()
public void setCurrentDir(String newDir)
public void updateStatus(int dotPos, int markPos)
public Class[] getDataBaseClasses()
public MetadataFeeder getMetadataFeeder()
public void addProject(Project project)
public boolean setCurrentProject(Project project)
public boolean removeProject(Project project)
public MetaProjectHeader getProgramMetadata()
public void resetDashboard()
public Project loadProject(String fileName, String projectName)
public void setCanSaveMetadata(boolean canSave)
public MetaObject getSelectedObject()
public void deselectObjects()
public void setProject(Project project)
public void editorAction(String actionName, ActionEvent event)
public void setMode(int mode)
public FileManager getFileManager()
public void setFileManager(FileManager fileManager)
public ConfigManager getConfigManager()
public void setConfigManager(ConfigManager configManager)
public ClassLoader getClassLoader()
public void setClassLoader(ClassLoader classLoader)
public Properties getProps()
public String getUserHome()
public String getBaseDir()
public int getMajorVersionNumber()
public int getMinorVersionNumber()
public int getBuildNumber()
public MetaObject pasting(MetaObject target, MetaObject pasted, MetaProject project)
public void processMenuItems(MetaObject metaObject)
public void processMenuSeparators(MetaObject metaObject)
public void processTabPages(MetaObject metaObject)
public void processPlacement(MetaObject object)
public void processCreateLayout(MetaObject object)
public void updateDisplayLayer(MetaObject object, int layerIndex)
public void propertyEditedRepaint(MetaObject object)
public void processDeleteObject(MetaObject object)
public boolean getAttachedToDesigner()
public void processProjectChangedState(boolean hasProjectChanged)
public void processObjectNameChanged(MetaObject object)
public void runProject()
public void setAçowDragging(boolean allowDragging)
public boolean allowDragging()
public boolean isCustomizing()
public void setTitle(String title)
public IdeMenuBar getIdeMenuBar()
public void showHelper(MetaObject metaObject, String propertyName)
    // ... many non-public methods follow ...
}
```
Peki SuperDashboard sınıfınının sadece aşağıdaki 5 metoda sahip olabileceğini söyleseydik?
```
public class SuperDashboard extends JFrame implements MetaDataUser {
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
}
```
Bu durumda bile, az sayıda metoduna rağmen SuperDashboard’un sorumlulukları çok fazladır.

Bir sınıfın adı, hangi sorumlulukları yerine getirdiğini tanımlamalıdır. Aslında bir bakıma, adlandırma muhtemelen sınıf boyutunu belirlemeye yardımcı olan ilk şey olacaktır. Bir sınıf için kısa bir isim bulamıyorsak, muhtemelen sınıf çok fazla iş yapıyordur. Sınıf ismi ne kadar belirsiz olursa, sorumlulukları o kadar çok olur. Örneğin, Processor, Manager veya Super gibi kelimeleri içeren sınıflara muhtemelen gereğinden çok sorumluluk yüklenmiştir.

Ayrıca sınıfın kısa bir açıklamasını eğer, ya da veya fakat kelimelerini kullanmadan 25 kelimeyle yazabilmeliyiz. Bu kelimeleri kullanmamız demek, sorumlulukların fazlalaşması demektir.

Single Responsibility Prensibi (SRP), bir sınıf veya modülün değiştirilmesi için gereken tek bir sebebi olması gerektiğini söyler. Bu ilke bize hem bir sorumluluk tanımı verir hem de sınıf büyüklüğü için bir rehber olur. Sınıfların sadece bir sorumluluğu olmalıdır.

5 metotlu SuperDashboard sınıfı iki sorumluluğa sahiptir. İlki, sürüm bilgisini kontrol etmek; ikincisi ise Java Swing bileşenlerini kontrol etmektir.

Swing kodunu değiştirirsek sürüm numarasını güncellemek isteyeceğiz, ancak tersi pek de doğru değil. Sürüm bilgisini sistemdeki diğer kod değişikliklerine dayanarak değiştirebiliriz. Sınıftaki sorumlulukları tanımlamaya çalışmak, kodumuzdaki soyutlamaları daha iyi tanımamıza ve oluşturmamıza yardımcı olur.

Sürüm bilgisi ile uğraşan her üç SuperDashboard metodunu Version adlı ayrı bir sınıfa kolayca çıkarabiliriz. Version sınıfı, diğer uygulamalarda yeniden kullanılabilme potansiyeline sahip bir yapıdır:
```
public class Version {
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
}
```
SRP, nesne yönelimli programlamada en önemli kavramlardan biridir. Anlaşılması ve uygulanması da kolaydır. Ancak bu kavram en çok istismar edilen ilkelerden biridir. Sürekli çok fazla şey yapan sınıflarla karşılaşırız. Peki neden?

Yazılımın çalışması ve yazılımın temiz kalması çok farklı iki etkinliktir. Çoğumuz, kodumuzun organizasyonundan ve temizliğinden daha çok çalışmasına odaklanıyoruz. Sorun şu ki, birçoğumuz program çalıştıktan sonra işimizin bittiğini düşünüyor. Organizasyon ya da temizlik kaygısı duymuyoruz. Geriye dönüp aşırı doldurulmuş sınıfları tek sorumlulukları olan ayrı birimler haline getirmek yerine bir sonraki soruna geçiyoruz. Aynı zamanda birçok geliştirici çok sayıda küçük, tek amaçlı sınıfın daha büyük resmin anlaşılmasını zorlaştırmasından korkuyor. Büyük resmin nasıl tamamlandığını anlamak için sınıftan sınıfa geçmek konusunda endişe duyuyorlar. Birçok küçük sınıfa sahip bir sistem, birkaç büyük sınıfa sahip bir sistemden daha fazla hareketli parçaya sahip değildir.

Büyük sınıflar içerilerinde çok fazla bilgi barındırır. Burada soru şudur: Her biri iyi tanımlanmış ve güzel etiketleri olan bileşenleri içeren birçok küçük çekmeceye mi sahip olmak istersiniz? Yoksa her şeyi içine attığınız birkaç büyük çekmeceye mi?

Her büyük sistem aynı büyüklükte mantık ve karmaşıklık içerecektir. Yeniden ifade etmek gerekirse; sistemlerimizin birkaç büyük sınıftan değil, birçok küçük sınıftan oluşmasını istiyoruz. Her küçük sınıfın, tek bir sorumluluğu ve değiştirilmesi için tek bir nedeni olmalıdır. Ve istenileni verebilmesi için başka sınıflarla birlikte çalışır.

## Birbirine Bağlılık (Cohesion)
Sınıflar az sayıda örnek değişkene (instance variable) sahip olmalıdır. Bir sınıfın her bir metodu, bu değişkenlerden bir veya daha fazlasını değiştirmelidir. Her bir değişkenin her bir metot tarafından kullanıldığı bir sınıf, maksimum düzeyde birbirine bağlılığa sahiptir.

Bu derece bütünleşebilen sınıflar yaratmak ne tavsiye edilir ne de mümkündür. Ancak yine de birbirine bağlılığın yüksek olmasını isteriz.

Aşağıda Stack’in gerçekleştirimine ait bir örnek var. Bağımlılığı oldukça yüksek bir sınıf. Sadece size() metodu her iki değişkeni kullanmıyor:
```
public class Stack {
    private int topOfStack = 0;
    List < Integer > elements = new LinkedList < Integer > ();
    public int size() {
        return topOfStack;
    }
    public void push(int element) {
        topOfStack++;
        elements.add(element);
    }
    public int pop() throws PoppedWhenEmpty {
        if (topOfStack == 0)
            throw new PoppedWhenEmpty();
        int element = elements.get(--topOfStack);
        elements.remove(topOfStack);
        return element;
    }
}
```
Fonksiyonları ve parametre listelerini kısa tutma stratejisi, bazen bir dizi metot tarafından kullanılan örnek değişkenlerin çoğalmasına sebep olabilir. Değişkenleri ve metotları, birbirine daha bağlı olacak şekilde iki veya daha fazla sınıfa ayırmaya çalışmalıyız.

Büyük fonksiyonları daha küçük fonksiyonlara dönüştürme eylemi sınıfların çoğalmasına neden olur. İçinde birçok değişken tanımlanmış büyük bir fonksiyon düşünelim. Diyelim ki, bu fonksiyonun küçük bir bölümünü ayrı bir fonksiyona çıkarmak istiyorsunuz. Bir şekilde ayıklamak istediğiniz kod, fonksiyonda tanımlanan dört değişken kullanıyor olsun. Bu değişkenlerin hepsini argüman olarak yeni fonksiyona aktarmamız gerekir mi?

Hayır. Bu dört değişkeni örnek değişken olarak tanımlamış olsaydık, herhangi bir argüman geçirmeden kodu ayırabilirdik. Ve o zaman fonksiyonun küçük parçalara bölünmesi daha kolay olurdu. Ne yazık ki bu da sınıflarımızın birbirine bağlılığı kaybetmesi anlamına geliyor; çünkü sadece birkaç fonksiyonun paylaşmasına izin vermek için daha fazla örnek değişken eklemek gerekecektir. Ancak bu değişkenleri paylaşmak isteyen sadece birkaç fonksiyon varsa, bu onları belirli bir sınıf yapmaz mı? Elbette yapar. Sınıflar birbirine bağlılığı kaybettiğinde, onları bölün.

Bu nedenle, büyük bir fonksiyonu daha küçük fonksiyonlara dönüştürmek bize genellikle birkaç küçük sınıfa bölme fırsatı verir. Bu, programımıza daha iyi bir organizasyon ve daha şeffaf bir yapı kazandırır. Ancak, daha uzun ve tanımlayıcı isimler kullanacağımız ve programı okunabilir tutmak için boşluk ve biçimlendirme teknikleri uygulayacağımız için programımız çok daha uzun hale gelmiş olur.

## Değişim İçin Düzenleme
Çoğu sistem için değişim süreklidir. Her değişiklik, sistemin geri kalanının artık amaçlandığı gibi çalışmaması riskini bize yükler. Temiz bir sistemde, değişim riskini azaltmak için sınıflarımızı sürekli yeniden düzenleriz.

Aşağıdaki Sql sınıfı SQL cümleleri oluşturmak için kullanılıyor:
```
public class Sql {
    public Sql(String table, Column[] columns)
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String pattern)
    public String select(Criteria criteria)
    public String preparedInsert()
    private String columnList(Column[] columns)
    private String valuesList(Object[] fields, final Column[] columns)
    private String selectWithCriteria(String criteria)
    private String placeholderList(Column[] columns)
}
```
Şimdilik update işlemini desteklemiyor ancak update işlevini de eklememiz gerektiğinde, sınıfı değiştirmemiz gerekecek. Hatta subselectleri desteklemesi için select ifadesini değiştirmemiz gerektiğinde de değişmesi gerekecektir. Sınıftaki herhangi bir değişiklik, diğer kodları bozma potansiyeline sahip olduğundan, sınıfın en baştan tekrar test edilmesi gerekiyor. Bu sebeple bu sınıf Single Responsibility prensibini ihlal ediyor.

SRP ihlalini basit bir bakış açısıyla tespit edebiliriz: selectWithCriteria gibi yalnızca select ifadesine ait private metotlar vardır.

Aşağıdaki gibi bir çözüm uyguladım. Koddaki her public metodu, Sql sınıfının kendi türevinden olan sınıflara, valuesList gibi özel metotları ise doğrudan erişilebilecek yerlere taşıdım:
```
abstract public class Sql {
    public Sql(String table, Column[] columns)
    abstract public String generate();
}
public class CreateSql extends Sql {
    public CreateSql(String table, Column[] columns)
    @Override public String generate()
}
public class SelectSql extends Sql {
    public SelectSql(String table, Column[] columns)
    @Override public String generate()
}
public class InsertSql extends Sql {
    public InsertSql(String table, Column[] columns, Object[] fields)
    @Override public String generate()
    private String valuesList(Object[] fields, final Column[] columns)
}
public class SelectWithCriteriaSql extends Sql {
    public SelectWithCriteriaSql(String table, Column[] columns, Criteria criteria)
    @Override public String generate()
}
public class SelectWithMatchSql extends Sql {
    public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern)
    @Override public String generate()
}
public class FindByKeySql extends Sql {
    public FindByKeySql(String table, Column[] columns, String keyColumn, String  keyValue)
    @Override public String generate()
}
public class PreparedInsertSql extends Sql {
    public PreparedInsertSql(String table, Column[] columns)
    @Override public String generate()
    private String placeholderList(Column[] columns)
}
public class Where {
    public Where(String criteria)
    public String generate()
}
public class ColumnList {
    public ColumnList(Column[] columns)
    public String generate()
}
```
Kod inanılmaz basit bir hale geldi. Çok kısa sürede neler olup bittiğini anlayabiliyoruz. Bir fonksiyonun bir başkasını bozabilme riski neredeyse ortadan kalktı.

update eklememiz gerektiğinde de, mevcut sınıfların hiçbirinin değiştirilmesi gerekmez. UpdateSql adlı yeni bir Sql alt sınıfı ekleyerek orada güncelleme işlemlerini gerçekleştirebiliriz. Ve bu değişiklik yüzünden sistemdeki başka hiçbir kod kesintiye uğramaz.

Yeni Sql sınıfımız iki temel kuralı da destekler: SRP (Single Responsibility Principle) ve OCP (Open/Closed Principle).

> Sınıflar gelişime açık, değişime kapalı olmalıdır.

## Değişimden İzole Etme
İhtiyaçlar değişecek, bu nedenle kod da değişecektir. Gerçekleştirimleri içeren somut (concrete) sınıflarımız, gerçekleştirimleri sadece sunan soyut (abstract) sınıflarımız olduğunu biliyoruz. Somut sınıflarımıza bağımlı istemcilerimiz varsa, bu detaylar değiştiğinde bu istemciler de risk altındadır. Bu etkiyi yalıtmak için arayüzleri (interface) ve soyut sınıfları kullanabiliriz.

Somut sınıflarımızdaki detaylara olan bağımlılıklar, test sistemi için de zorluklar yaratır.

Örneğin bir Porfolio sınıfı yaratmak istesek ve o da harici bir TokyoStockExchange API’sine bağımlı olsa, testlerimiz her 5 dakikada bir değişen sonuçlardan etkilenecekti. Direkt olarak TokyoStockExchange API’sine bağımlı olan bir Portfolio sınıfı tasarlamak yerine, StockExchange isimli bir arayüz oluşturur ve içine basit bir metot tanımlayabiliriz:
```
public interface StockExchange {
    Money currentPrice(String symbol);
}
```
TokyoStockExchange sınıfını bu arayüzün bir gerçekleştirimi haline getirebilir ve Portfolio kurucusunun argüman olarak bir StockExchange referansı aldığından da şu şekilde emin olabiliriz:
```
public Portfolio {
    private StockExchange exchange;
    public Portfolio(StockExchange exchange) {
        this.exchange = exchange;
    }
    // ...
}
```
Artık TokyoStockExchange sınıfını taklit eden test edilebilir bir StockExchange arayüz gerçekleştirimi yaratabiliriz. Bu test gerçekleştirimi, testte kullandığımız anlık sembol değerini sabitleyecektir. Örneğin Microsoft’a karşılık gelen “MSFT” sembolü için her zaman $100 dönmesini sağlayıp, 5 hisselik bir satın alma işlemi için portföy değerimizin $500 olmasını bekleyebiliriz:
```
public class PortfolioTest {
    private FixedStockExchangeStub exchange;
    private Portfolio portfolio;
    
    @Before
    protected void setUp() throws Exception {
        exchange = new FixedStockExchangeStub();
        exchange.fix("MSFT", 100);
        portfolio = new Portfolio(exchange);
    }
    
    @Test
    public void GivenFiveMSFTTotalShouldBe500() throws Exception {
        portfolio.add(5, "MSFT");
        Assert.assertEquals(500, portfolio.value());
    }
}
```
Bir sistem bu şekilde test edilebilecek kadar bağımsızsa, aynı zamanda daha esnek olacak ve tekrar kullanılabilirliği daha fazla teşvik edecektir. Daha az bağlılık (coupling), sistemimizin değişimlerden daha izole olması demektir. Bu izolasyon, sistemin her öğesini anlamayı kolaylaştırır.

Bu şekilde bağlılığı en aza indirgediğimizde, sınıflarımız başka bir sınıf tasarım ilkesi olan Dependency Inversion Principle (DIP) ilkesine de bağlı kalırlar. DIP özünde, sınıflarımızın somut ayrıntılar yerine soyutlamalara bağlı olması gerektiğini söyler. TokyoStockExchange sınıfının uygulama ayrıntılarına bağımlı olmak yerine Portfolio sınıfımız şimdi StockExchange arayüzüne bağımlı. StockExchange, bir sembolün anlık fiyatını soran soyut konsepti temsil eder. Bu soyutlama, bu fiyatın elde edildiği yer de dahil olmak üzere böyle bir fiyat elde etme ile ilgili tüm ayrıntıları yalıtır.
