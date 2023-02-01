Değişkenlerimizi private yapmamızın bir sebebi var: kimsenin onlara bağımlı (dependent) olmasını istemiyoruz. Gerçekleştirimlerini ya da tiplerini değiştirme özgürlüğünü elimizde tutmak istiyoruz. Peki, programcılar neden getter ve setter metotlarını otomatik olarak ekler ve değişkenlerini sanki public değişkenlermiş gibi açığa çıkarır?

## Veri Soyutlama
Aşağıdaki iki kodun farkına bakalım. Her ikisi de kartezyen çarpımındaki nokta (point) kavramını temsil ediyor. Ancak birisi gerçekleştirimini açığa çıkarırken, diğeri onu tamamen saklıyor:

1.
```
public class Point {
    public double x;
    public double y;
}
```
2.
```
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```
İkinci örneğimizde gerçekleştirimin bir dikdörtgen ya da polar koordinatlar içinde olup olmadığını anlamanın bir yolu yok. İkisi de olabilir. Ve birden fazla veri yapısını temsil ediyor. İçindeki metotlar ise bir erişim politikasını (access policy) zorluyor. Burada koordinatları atomik şekilde düşünmek zorundayız.

İlk örneğimiz ise çok açık şekilde bir dikdörtgenin koordinatları ve bizi bu koordinatları bağımsız şekilde manipüle etmeye zorluyor. Değişkenler private olsaydı ve biz de getter/setter metotları kullanıyor olsaydık, bu durum gene de gerçekleştirimi açığa çıkarırdı.

Gerçekleştirimi gizlemek, yalnızca değişkenler ile fonksiyonlar arasına bir katman koyma meselesi değildir. Gerçekleştirimleri gizlemek, soyutlama ile ilgilidir. Bir sınıf, değişkenlerini getter ve setter metotlar aracılığı ile dışarı açmaz; aksine gerçekleştirimi bilmelerine gerek olmadan veriyi değiştirmelerine izin veren arayüzleri açar.

Şimdi aşağıdaki şu iki koda bakalım:

1.
```
public interface Vehicle {
    double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();
}
```
2.
```
public interface Vehicle {
    double getPercentFuelRemaining();
}
```
Burada ikincisini tercih etmeliyiz. Verimizin detaylarını vermek istemeyiz. Aksine verilerimizi soyut ifadelerle ifade etmek istiyoruz. Bu sadece arayüzleri veya getter/setter metotları kullanarak gerçekleşmez. Gerçek niyetimizi, nesnenin içerdiği veriyi en iyi şekilde temsil edebilecek şekle sokmalıyız. En kötü seçenek ise getter/setter metotları eklemektir.

## Veri/Nesne Anti-Simetrisi
Nesneler verilerini soyutlamalar arkasında saklarlar ve bu verileri işleyecek fonksiyonları açarlar. Veri yapıları ise nesnelerini dışa açarlar ve anlamlı hiçbir fonksiyonları yoktur. Bu ikisi birbirine sanal olarak tamamen zıttır.

Aşağıdaki örneğe bakalım:
```
public class Square {
    public Point topLeft;
    public double side;
}
public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}
public class Circle {
    public Point center;
    public double radius;
}
public class Geometry {
    public final double PI = 3.141592653589793;
    public double area(Object shape) throws NoSuchShapeException {
        if (shape instanceof Square) {
            Square s = (Square) shape;
            return s.side * s.side;
        } else if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.height * r.width;
        } else if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeException();
    }
}
```
Geometry sınıfı 3 tane şekil (shape) sınıfı üzerinde işlemler yapıyor. Şekil sınıfları hiçbir davranış sergilemeyen basit veri yapılarıdır. Tüm davranış Geometry sınıfı içerisindedir.

Geometry sınıfına perimeter() isimli bir fonksiyon eklediğimizi düşünelim. Şekil sınıfları değişmemiş olacaktı. Bağlı olan diğer sınıflar da etkilenmemiş olacaktı. Diğer bir taraftan yeni bir şekil eklersem Geometry sınıfındaki tüm fonksiyonların değişmesi gerekecekti.

Şimdi aşağıdaki nesne yönelimli çözüme bakalım:
```
public class Square implements Shape {
    private Point topLeft;
    private double side;
    public double area() {
        return side * side;
    }
}
public class Rectangle implements Shape {
    private Point topLeft;
    private double height;
    private double width;
    public double area() {
        return height * width;
    }
}
public class Circle implements Shape {
    private Point center;
    private double radius;
    public final double PI = 3.141592653589793;
    public double area() {
        return PI * radius * radius;
    }
}
```
area() polimorfik bir metot. Geometry isimli bir sınıfa ihtiyaç yok. Yani eğer yeni bir şekil eklersek, mevcut fonksiyonların hiçbiri etkilenmeyecek. Ancak yeni bir fonksiyon eklersek, tüm şekiller değişmek zorunda kalacak.

İşte burada nesneler ve veri yapıları arasındaki ayrımı görebilirsiniz:

Prosedürel kod -veri yapılarını kullanan kod-, mevcut veri yapılarını değiştirmeden yeni fonksiyonlar eklemeyi kolaylaştırır. Nesne yönelimli kod ise mevcut fonksiyonları değiştirmeden yeni sınıflar eklemeyi kolaylaştırır.

Prosedürel kod yeni veri yapıları eklemeyi zorlaştırır çünkü tüm fonksiyonlar değişmelidir. Nesne yönelimli kod ise yeni fonksiyonlar eklemeyi zorlaştırır çünkü tüm sınıflar değişmelidir.

Yani, nesne yönelimli için zor olan şeyler prosedürel için kolay, prosedürel için zor olanlar da nesne yönelimli için kolaydır.

## Demeter Kuralı (Law of Demeter)
Demeter Kuralı der ki: Bir modül, değiştirdiği bir nesnenin içini bilmemelidir. Nesneler, verilerini saklar ve işlemlerini açarlar. Yani, bir nesne iç yapısını erişimciler aracılığıyla açmamalıdır.

C isimli bir sınıfımız ve bunun f isimli bir fonksiyonu olsun. Bu fonksiyon sadece şunların metotlarını çağırmalıdır:

- C’ye ait metotlar
- f tarafından yaratılmış bir nesnenin metotları
- f fonksiyonuna argüman olarak geçilen bir nesneye ait metotlar
- C içerisinde örnek değişken olarak tutulan bir nesneye ait metotlar
Fonksiyon, izin verilen fonksiyonlardan herhangi biri tarafından dönen nesneler üzerindeki metotları çağırmamalıdır. Diğer bir deyişle: “Arkadaşlarınla konuş, yabancılarla değil (talk to friends, not to strangers).”


Apache’de bir yerlerde bulduğum aşağıdaki kod bu kuralı ihlal ediyor. Çünkü getOptions()’dan dönen nesne ile getScratchDir() metodunu, getScratchDir()’den dönen değer ile de getAbsolutePath() metodunu çağırıyor.

final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
Tren Enkazları
Uç uca geçmiş bir dizi tren vagonu gibi birbirini çağıran fonksiyonlar için bu benzetme yapılır. Bu tür kodları parçalara ayırmak en iyisidir.

Şu koda bir bakalım:

Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
Bu koddaki ikinci ve üçünü satırlar Demeter Kuralı’nı ihlal ediyorlar mı? Kesinlikle. Bu kodun Demeter Kuralı’nı ihlal edip etmediği; ctxt, opts ve scratchDir’in nesne ya da veri yapısı olup olmamasına bağlıdır. Eğer nesneyseler, o zaman iç yapıları saklanmalıdır. Yani iç yapılarının biliniyor oluşu açıkça bir ihlaldir. Diğer bir taraftan hiçbir davranış sergilemeyen veri yapıları iseler, o zaman doğal olarak iç yapılarını açık ediyorlar yani Demeter Kuralı’nı ihlal ediyorlardır.

Erişimci fonksiyonların kullanımı meseleyi karıştırır. Eğer kod şu şekilde yazılmış olsaydı, muhtemelen Demeter Kuralı’nı sorgulamayacaktık:
```
final String outputDir = ctxt.options.scratchDir.absolutePath;
```
Ya ctxt, opts ve scratchDir gerçek davranışlı nesneler olsalardı? O zaman iç yapılarını gizlemek zorunda olduklarından, bu nesneler aracılığı ile başka yerlere yönlenemezdik. O halde scratchDir’e ait absolutePath nesnesini nasıl alabileceğiz?

Bu:
```
ctxt.getAbsolutePathOfScratchDirectoryOption();
```
Ya da şu:
```
ctx.getScratchDirectoryOption().getAbsolutePath()
```
İkinci seçenek getScratchDirectoryOption() metodunun bir nesne değil bir veri yapısı olduğunu varsayıyor ve burada iki seçenek de iyi durmuyor.

Eğer ctxt bir nesne ise, ona bir şeyler yapmasını söylemeliyiz, ona içindekileri sormamalıyız. (Tell Don’t Ask prensibi) O zaman neden scratchDirectory’den absolutePath’i istiyoruz? Onunla ne yapacağız?


Şu koda bakalım; noktalar, eğik çizgiler (slash), dosya uzantıları ve File nesneleri birlikte bu kadar dikkatsiz kullanılmamalıdırlar:
```
String outFile = outputDir + "/" + className.replace('.', '/') + ".class";
FileOutputStream fout = new FileOutputStream(outFile);
BufferedOutputStream bos = new BufferedOutputStream(fout);
```
Burada “absolute path”i almak istememizin sebebi, verilen isimde bir “scratch file” yaratmak istememiz. Yani, ctxt’ye bunu yapacağını söylemek isteseydik, şöyle olmalıydı:
```
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```
Bir nesnenin yapması makul olan bir iş bu. Bu durum ctxt’nin iç yapısını gizlemesine izin verir. Ve mevcut fonksiyonun bilmemesi gereken nesneler aracılığıyla yönlenerek Demeter Kuralı’nı ihlal etmesini de önler.

## Veri Aktarım Nesneleri (Data Transfer Objects)
Veri yapısını en kısa şekilde özetleyecek olursak, public değişkenleri olan ve hiç fonksiyonu olmayan sınıftır. Buna bazen DTO da deriz; ki bu nesneler özellikle veritabanı vb. yerlerle haberleşiyorken oldukça kullanışlıdır.

Biraz daha yaygını Bean formudur. Bean’lerin getter/setter’larca değiştirilmiş private değişkenleri vardır. Bazı Object Oriented püristlerini (pürist: dilin kullanımında aşırı dikkatli kişi) iyi hissettirebilir ancak başka hiçbir faydası yoktur. Örnek bir Bean:
```
public class Address {
    private String street;
    private String streetExtra;
    private String city;
    private String state;
    private String zip;
    public Address(String street, String streetExtra,
        String city, String state, String zip) {
        this.street = street;
        this.streetExtra = streetExtra;
        this.city = city;
        this.state = state;
        this.zip = zip;
    }
    public String getStreet() {
        return street;
    }
    public String getStreetExtra() {
        return streetExtra;
    }
    public String getCity() {
        return city;
    }
    public String getState() {
        return state;
    }
    public String getZip() {
        return zip;
    }
}
```
## Melez Yapılar (Hybrids)
Yarısı nesne yarısı veri yapısı olan melez yapılar bazen karışıklığa sebep olur. Önemli işler yapan fonksiyonları, private değişkenleri public yapan erişimcileri (getters/setters) ve public değişkenleri vardır. Bu tür karmaşık yapılar yeni fonksiyonlar eklemeyi zorlaştırır. Ve hatta yeni veri yapıları eklemeyi de zorlaştırır. Melez yapılar oluşturmaktan kaçınmalıyız. Bu tür yapılar, başka tiplerden ya da fonksiyonlardan korunma ihtiyacı olup olmadığından emin olunamayan sistemlerin göstergesidir.

## Aktif Kayıt (Active Record)
Aktif kayıtlar DTO’ların özel formlarıdır. Public değişkenleri olan veri yapılarıdır; ancak save ve find gibi yönlendirici metotları vardır. Bu aktif kayıtlar genellikle veritabanı tablolarından ya da diğer veri kaynaklarından doğrudan çeviridir. Fakat geliştiricilerin aktif kayıtları belli iş kuralları içerisine koyarak, onlara nesnelermiş gibi davranmaya çalıştıklarına sık sık denk geliyoruz. Çözüm ise elbette bir aktif kayda veri yapısıymış gibi davranmak ve iş kurallarını içeren ayrı nesneler yaratarak iç yapıyı saklamaktır.

Nesneler davranışını açığa vurur ve verisini saklar. Bu, hiçbir mevcut davranışı değiştirmeden yeni nesneler eklemeyi kolaylaştırır. Mevcut nesnelere ise yeni davranışlar eklemeyi zorlaştırır.

Veri yapıları ise verisini açığa vurur ve önemli bir davranışı yoktur. Bu, mevcut veri yapılarına yeni davranışlar eklemeyi kolaylaştırır ancak mevcut fonksiyonlara yeni veri yapıları eklemeyi zorlaştırır.

Bazen yeni veri tipleri bazen de yeni davranışlar ekleyebilme esnekliği isteriz. İyi yazılımcılar, bulunduğu kodun durumuna göre en iyi yaklaşımı seçeceklerdir.
