Mesleğimiz son yıllarda uzun bir yol kat etti. 1997'de hiç kimse Test Driven Development (TDD) kavramını bilmiyordu. Büyük çoğunluğumuzun birim testleri, programlarımızın çalışıp çalışmadığından emin olmak için yazdığı kodlardı. Özenle sınıflarımızı ve metotlarımızı yazar ve sonra bunları test etmek için bazı özel kodlar yazardık.

Bugünlerde, yazdığım her satır kodun beklediğim gibi çalıştığından emin olmak için test yazıyorum. Geçen testlerimi yazdıktan sonra ise, yazdığım kodla sonradan çalışacak herkesin de çalıştırmasına uygun olacak şekilde olduklarından emin oluyorum.

Agile ve TDD hareketleri birçok programcıyı otomatik birim testleri yazmaya teşvik etti ve her geçen gün daha fazla programcı bu hareketlere katılıyorlar. Ancak daha gidilecek çok yol var. Kodlama disiplinimize test yazmayı eklemekte acele edince, birçok programcı iyi testler yazmanın incelikli ve önemli noktalarından bazılarını kaçırdılar.

![](https://miro.medium.com/max/720/1*55o6cKQQUMFM4k304JqMrw.webp)

## TDD’nin 3 Kuralı
Herkes TDD’nin üretim kodundan önce birim testleri yazmamızı istediğini bilir. Ancak bu kural buzdağının sadece görünen kısmıdır:

- Geçmeyen bir birim testi yazmadan, üretim (uygulama) kodu yazmamalısın.
- Aynı anda birden fazla geçmeyen birim testi yazmamalısın. Derleme hatası da geçmeyen test demektir.
- O andaki geçmeyen testi geçirecek üretim kodundan başka üretim kodu yazmamalısın.
Bu üç yasa sizi otuz saniye uzunluğunda bir döngüye sokar. Testler ve üretim kodu birlikte yazılmışlardır. Sadece, testler üretim kodundan bir kaç saniye öncedir.

Bu şekilde çalışırsak, her gün düzinelerce, her ay yüzlerce ve her yıl binlerce test yazmış oluruz. Ve testlerimiz hemen hemen tüm üretim kodumuzu kapsar. Üretim kodunun boyutuna rakip olabilecek test kodu, yıldırıcı bir yönetim problemi oluşturabilir.

Testlerimizi Temiz Tutmak
Birkaç yıl önce, test koduna üretim koduyla aynı kalite ve standartlarda bakım yapılması gerekmediğine karar veren bir ekibe koçluk yapmam istendi. Değişkenlerin iyi adlandırılması, test metotlarının kısa ve açıklayıcı olması gerekmiyordu. Test kodlarının iyi tasarlanmış ve üzerinde düşünülmüş bir şekilde bölünmesine de ihtiyaç yoktu. Testler çalıştığı ve üretim kodunu kapsadığı sürece sorun yoktu.

Hiç test olmamasındansa kirli testlerin olmasının daha iyi olduğunu söyleyebilirsiniz. Ancak bu ekibin farkında olmadığı şey, kirli testlerin bulunmasının -daha kötü olmasa da- hiç test olmaması ile eşdeğer olmasıydı. Sorun şuydu ki; üretim kodları geliştikçe testler de değişmeliydi. Ve testler ne kadar kirliyse, değiştirmesi de o kadar zor oluyordu. Üretim kodunu değiştirirken eski testler geçmemeye başlar, ve test kodundaki karışıklık bu testlerin tekrar geçirilmesini zorlaştırır. Böylece testler gittikçe artan bir yükümlülük gibi görülür.

Sürüm çıkardıkça testleri yazma maliyeti arttı ve nihayetinde geliştiricilerin tek şikayet ettiği şey haline geldi. Yöneticiler tahminlerin (estimations) neden bu kadar arttığını sorduğunda, testleri suçluyorlardı. Sonunda testleri tamamen çıkarmaya zorlandılar. Ancak testler olmadan, sistemlerinin bir kısmındaki değişikliklerin diğer kısımları bozmadığından emin olamazlardı. Bu nedenle hata (defect/bug) oranı artmaya başladı. Hata sayısı arttıkça da, üretim kodunda değişiklikler yapmaya çekinmeye başladılar. Çünkü iyilikten çok zararı olmasından korkuyorlardı. Üretim kodları çürümeye başladı. Sonunda elimize hiç testi olmayan, karışık ve hatalarla dolu üretim kodu, sinirli müşteriler ve test çabalarının başarısız olduğuna dair hisler kaldı.

Bir bakıma haklılardı. Onların test yazma çabaları başarısız olmuştu. Ancak bu başarısızlığın sebebi, testlerin dağınık olmasına izin verme kararlarıydı. Testlerini temiz tutmuş olsaydılar, test yazma çabaları başarısız olmazdı.

Hikayenin özü basit:
> Test kodu, üretim kodu kadar önemlidir ve üretim kodu kadar da temiz tutulmalıdır. Testler ikinci sınıf vatandaş mualemesi görmemelidir. Test yazmak düşünce, tasarım ve dikkat gerektirir.

Testlerinizi temiz tutmazsanız, onları kaybedersiniz. Ve onlar olmaksızın, üretim kodunu esnek tutan şeyleri de kaybedersiniz. Evet, kodumuzu esnek, bakımlı ve tekrar kullanılabilir tutan şey birim testlerimizdir. Testleriniz varsa, kodda değişiklikler yapmaktan korkmazsınız. Testleriniz olmadan, her değişiklik muhtemel bir hatadır ve mimariniz ne kadar esnek olursa olsun değişiklik yapmaktan korkarsınız.

Testlerinizi yazdıkça ve test kapsam yüzdesi yükseldikçe değişiklik yapmaktan daha az korkarsınız. Hatta korkmadan mimarinizi ve tasarımınızı bile geliştirebilirsiniz. Tasarımınızı ve mimarinizi olabildiğince temiz tutmanın anahtarı üretim kodunu kapsayan otomatik (automated) birim testlere sahip olmaktır.

## Temiz Testler
Bir testi şu 3 şey temiz yapar:

- Okunabilirlik
- Okunabilirlik
- Okunabilirlik


Okunabilirliğin birim testlerde olması belki de üretim kodunda olmasından daha önemlidir. Peki testleri ne okunabilir yapar? Tüm kodu okunabilir kılan aynı şeyler: açıklık, basitlik ve ifade yoğunluğu.

Şu koda bir bakalım. Üç test de anlaması zor. Öncelikle addPage ve assertSubString metoduna yapılan çağrılarda çok sayıda tekrarlanmış kod var. Daha da önemlisi, bu kod testin ifade edilebilirliğine müdahale eden ayrıntılarla dolu:
```
public void testGetPageHieratchyAsXml() throws Exception {
    crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    request.setResource("root");
    request.addInput("type", "pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(
            new FitNesseContext(root), request);
    String xml = response.getContent();
    assertEquals("text/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>ChildOne</name>", xml);
}
public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks()
throws Exception {
    WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    PageData data = pageOne.getData();
    WikiPageProperties properties = data.getProperties();
    WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
    symLinks.set("SymPage", "PageTwo");
    pageOne.commit(data);
    request.setResource("root");
    request.addInput("type", "pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(
            new FitNesseContext(root), request);
    String xml = response.getContent();
    assertEquals("text/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>ChildOne</name>", xml);
    assertNotSubString("SymPage", xml);
}
public void testGetDataAsHtml() throws Exception {
    crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");
    request.setResource("TestPageOne");
    request.addInput("type", "data");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(
            new FitNesseContext(root), request);
    String xml = response.getContent();
    assertEquals("text/xml", response.getContentType());
    assertSubString("test page", xml);
    assertSubString("<Test", xml);
}
Örneğin, PathParser çağrılarına bakalım. Stringleri, tarayıcılar tarafından kullanılan PagePath örneklerine (instance) dönüştürüyorlar. Bu dönüşüm, testle tamamen alakasız. Responder’ı yaratmak ve cevabı toplama ve aktarma ile ilgili ayrıntılar da sadece gürültü. Sonuç olarak, kod okunmak için tasarlanmamış.

Şimdi ise yeniden düzenlediğim, çok daha temiz ve açıklayıcı olan şu versiyonuna bakalım:

public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    submitRequest("root", "type:pages");
    assertResponseIsXML();
    assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
}
public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
    WikiPage page = makePage("PageOne");
    makePages("PageOne.ChildOne", "PageTwo");
    addLinkTo(page, "PageTwo", "SymPage");
    submitRequest("root", "type:pages");
    assertResponseIsXML();
    assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
    assertResponseDoesNotContain("SymPage");
}
public void testGetDataAsXml() throws Exception {
    makePageWithContent("TestPageOne", "test page");
    submitRequest("TestPageOne", "type:data");
    assertResponseIsXML();
    assertResponseContains("test page", "<Test");
}
```
BUILD-OPERATE-CHECK deseni, testlerin bu yapısıyla açıkça görülüyor. Testlerin her biri üç kısma ayrılmış. İlk kısım test verisini oluşturuyor, ikinci kısım bu test verisini işliyor ve üçüncü kısım ise işlemin beklenen sonuçları verip vermediğine bakıyor.

## Çifte Standart
Test API’si içindeki kod, üretim kodundan farklı mühendislik standartlarına sahiptir. Basit, öz ve açıklayıcı olmalıdır, ancak üretim kodu kadar verimli olmasına gerek yoktur. Sonuçta, üretim ortamında değil test ortamında çalışır ve bu iki ortamın çok farklı ihtiyaçları vardır.

Aşağıdaki koda bakalım. Bu testi, prototipini yaptığım bir çevre kontrol sisteminin bir parçası olarak yazmıştım. Ayrıntılara girmeden, bu testin, sıcaklık “çok soğuk” olduğunda düşük sıcaklık alarmının, ısıtıcının ve körüğün hepsinin açık olup olmadığını kontrol ettiğini söyleyebiliriz:
```
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
    hw.setTemp(WAY_TOO_COLD);
    controller.tic();
    assertTrue(hw.heaterState());
    assertTrue(hw.blowerState());
    assertFalse(hw.coolerState());
    assertFalse(hw.hiTempAlarm());
    assertTrue(hw.loTempAlarm());
}
```
Bir sürü detay var. Örneğin tic() metodu ne yapıyor? Ya da örneğin gözleriniz önce heaterState() metoduna ardından assertTrue()’ya bakıyor, veya coolerState() metoduna bakıyor ve assertFalse() metodunu kontrol ediyor. Bu durum testi okumayı zorlaştırıyor.

Kodu geliştirerek testin okunabilirliğini şu şekilde arttırdım:
```
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```
Bir wayTooCold() fonksiyonu yazarak tic() fonksiyonu çağrısını gizledim. Fakat dikkat etmemiz gereken asıl nokta, assertEquals’daki garip String. Bu String’de büyük harf “açık”, küçük harf ise “kapalı” anlamına geliyor ve harfler daima şu sırada bulunuyor: heater, blower, cooler, hi-temp-alarm, lo-temp-alarm. Anlamını bildikten sonra, gözleriniz o String’i geçer ve sonuçları çabucak yorumlayabilirsiniz.

Şu koda bakalım ve testleri anlamanın ne kadar kolay olduğunu görelim:
```
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
    tooHot();
    assertEquals("hBChl", hw.getState());
}
@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
    tooCold();
    assertEquals("HBchl", hw.getState());
}
@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
    wayTooHot();
    assertEquals("hBCHl", hw.getState());
}
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
getState metodu şu şekilde:

public String getState() {
    String state = "";
    state += heater ? "H" : "h";
    state += blower ? "B" : "b";
    state += cooler ? "C" : "c";
    state += hiTempAlarm ? "H" : "h";
    state += loTempAlarm ? "L" : "l";
    return state;
}
```
Çok verimli bir kod değil. Kodu verimli hale getirebilmek için bir StringBuffer kullanmış olmalıydım. StringBuffer’lar biraz çirkindir ve maliyeti az olsa bile kullanmaktan kaçınırım. Evet, buradaki maliyetin de çok az olduğunu söyleyebilirsiniz. Bu uygulama gömülü gerçek zamanlı bir sistem ve kaynaklarının çok kısıtlı olması da muhtemel. Ancak test ortamı kaynaklarının kısıtlı olması mümkün değil.

Çifte standardın doğası budur. Üretim ortamında asla yapmayacağımız fakat test ortamında uygulayabileceğimiz şeyler vardır. Genellikle bu şeyler bellek veya CPU verimliliği meseleleridir.

## Test Başına Bir Doğrulama (Assert)
Her testin sadece bir assert ifadesi olması çok zorlu görünebilir ancak kodun anlaşılabilirliğini ne kadar kolaylaştırdığını yukarıdaki örneğimizde gördük.

Örneğimizin ilk düzenlenmiş versiyonunu şu şekilde yeniden düzenledim:
```
public void testGetPageHierarchyAsXml() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldBeXML();
}
public void testGetPageHierarchyHasRightTags() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldContain(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
}
```
Fonksiyonların adlarını given-when-then olarak standardize ettim. Testlerin okunmasını daha da kolaylaştırıyor. Ancak testleri bu şekilde bölmek de bir çok kodun tekrarlanmasına sebep oluyor.

Template Method desenini kullanarak ve given/when kısmını temel (base) bir sınıfa, then kısmını da farklı türev sınıflara koyarak kod tekrarlanmasını ortadan kaldırabiliriz. Veya tamamen ayrı bir test sınıfı oluşturabilir ve given/when kısmını @Before fonksiyonuna ve then kısımlarının her birini ayrı birer @Test fonksiyonu içine koyabiliriz. Ancak bu, küçük bir mesele için çok büyük değişiklik yapmak demek oluyor. Günün sonunda birden fazla assert olan versiyonunu tercih ediyorum.

Bence tek assert kuralı iyi bir pratik. Testlerime genellikle tek sayıda assert koymaya çalışsam da, bazı zamanlar birden fazlasını koymaktan da çekinmiyorum. Bence, söyleyebileceğimiz en iyi şey; bir testteki assert sayısının en aza indirilmesi gerektiğidir.

## Test Başına Tek Bir Konsept
Her test fonksiyonunda tek bir konsepti test etmek istiyoruz. Birinin ardından bir diğer şeyi test eden testler istemiyoruz.

Aşağıda böyle bir teste ait örnek var. Bu test üç kısma ayrılmalı çünkü üç bağımsız şeyi test ediyor. Hepsini aynı fonksiyonda birleştirmek, okuyucuyu, her bölümün neden orada olduğunu ve o bölüm tarafından nelerin test edildiğini anlamaya zorlar:
```
/**
 * Miscellaneous tests for the addMonths() method.
 */
public void testAddMonths() {
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);
    SerialDate d2 = SerialDate.addMonths(1, d1);
    assertEquals(30, d2.getDayOfMonth());
    assertEquals(6, d2.getMonth());
    assertEquals(2004, d2.getYYYY());
    SerialDate d3 = SerialDate.addMonths(2, d1);
    assertEquals(31, d3.getDayOfMonth());
    assertEquals(7, d3.getMonth());
    assertEquals(2004, d3.getYYYY());
    SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1));
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```
Ayırmak istediğimiz üç test fonksiyonu muhtemelen şu şekilde olmalıdır:

Elimizde 31 günü olan aylardan birinin son günü olsun (örneğin 31 Mayıs);

1) 30 günü olan bir ay eklediğimizde (Haziran gibi), tarih ayın 31'i değil 30'u olmalıdır.

2) İki ay eklediğimizde, ikinci ayın 31 günü varsa , tarih ayın 31'i olmalıdır.

Elimizde 30 günü olan aylardan birinin son günü olsun;

3) 31 günü olan bir ay eklediğimizde tarih ayın 31'i değil 30'u olmalıdır.

Yani soruna neden olan şey birden fazla assert ifadesi değil. Aksine, test edilen birden fazla konseptin olmasıdır. Bu nedenle en iyi kural, konsept başına assert sayısını en aza indirgemek ve test fonksiyonu başına sadece bir konsept test etmektir.

## F.I.R.S.T Kuralı
Temiz testler, F.I.R.S.T kısaltmasını oluşturan şu beş kuralı takip eder:

- Fast: Testler hızlı olmalıdır. Testler yavaş çalıştıklarında, sık sık çalıştırmak istemezsiniz. Testleri sık sık çalıştırmazsanız, problemleri kolayca giderecek kadar erkenden fark edemezsiniz.

- Independent: Testler birbirine bağlı olmamalıdır. Bir test, bir sonraki testin koşullarını belirlememelidir. Her bir testi bağımsız olarak ve istediğiniz herhangi bir sırada çalıştırabilmelisiniz. Testler birbirine bağımlı olduğunda, hata alan ilki, hiyerarşide aşağılara doğru hatalara sebep olarak ilk hata alınan yerin tespitini zorlaştıracaktır.

- Repeatable: Testler herhangi bir ortamda çalışabilir olmalıdır. Birim testlerini üretim ortamında, QA ortamında ve dizüstü bilgisayarınızda veya trende evinize gidiyorken çalıştırabilmelisiniz. Testleriniz herhangi bir ortamda tekrarlanabilir değilse, başarısız olmalarına hep bir mazeretiniz olacaktır.

- Self-Validating: Testler ya geçerler ya da başarısız olurlar. Testlerin geçip geçmediğini anlamak için bir log dosyasına bakmamıza gerek olmamalı veya elle iki farklı metin dosyasını karşılaştırmanız gerekmemelidir.

- Timely: Testlerin zamanında yazılması gerekir. Birim testleri, yazıldığı üretim kodundan hemen önce yazılmalıdır. Testlerinizi kodunuzu yazdıktan sonra yazarsanız, üretim kodununun test edilmesi zor olabilir. Kodunuzu test edilebilecek şekilde tasarlayamayabilirsiniz.

## Sonuç
Testler, bir projenin sağlığı açısından üretim kodu kadar önemlidir. Belki daha da önemlidir, çünkü testler üretim kodunun esnekliğini, bakımını ve tekrar kullanılabilirliğini korur ve geliştirir. Bu yüzden testlerinizi hep temiz tutun. Onları az ve öz hale getirmek için çalışın. Testlerinizin çürümesine izin verirseniz, kodunuz da çürür.
