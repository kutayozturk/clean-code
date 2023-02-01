İyi tasarıma sahip sistemler oluşturabilmek için takip edebileceğiniz 4 kural olduğunu söylesem? Bu kuralları izleyerek, kodunuzun yapısını ve tasarımını anlayarak, SRP ve DIP gibi ilkelerin uygulanmasını kolaylaştıracak olsanız?

Kent Beck’in dört basit tasarım kuralı, iyi tasarlanmış yazılımlar yaratmada önemli bir etkendir. Ve Kent Beck’e göre eğer bir tasarım basitse, şu kuralları takip etmelidir:

Tüm testleri çalıştırın
Tekrarlanmış kodlar yazmayın
Açıklayıcı olun
Sınıf ve metot sayısını en aza indirin
Kurallar önem sırasına göredir. Şimdi sırasıyla bu kuralları inceleyelim:

## Kural #1: Tüm testleri çalıştırın
Bir tasarım, amaçlandığı gibi hareket eden bir sistem üretmelidir. Bir sistem kağıt üzerinde mükemmel bir tasarıma sahip olabilir ancak sistemin amaçlandığı gibi çalıştığını doğrulamanın basit bir yolu yoksa, kağıt üstündeki tüm çabalar boşa olabilir.

Kapsamlı bir şekilde test edilen ve tüm testlerini her zaman geçiren bir sistem test edilebilir bir sistemdir. Bir sistemin test edilebilir olması önemlidir. Test edilebilir olmayan sistemler doğrulanabilir de değildir. Doğrulanamayan bir sistem asla dağıtılmamalıdır (deploy).

Sistemlerimizi test edilebilir hale getirmek, sınıflarımızın küçük ve tek amacının olduğu bir tasarıma sahip olmamıza yardımcı olur. SRP’ye uyan sınıfları test etmek daha kolaydır. Daha çok test yazdıkça, test etmesi daha kolay olan sistemler ortaya çıkarmaya devam ederiz. Dolayısıyla sistemimizin test edilebilir olduğundan emin olmak, daha iyi tasarımlar oluşturmamıza yardımcı olur.

Sıkı bağımlılıklar da (tight coupling) test yazmayı zorlaştırır. Daha çok test yazdıkça; DIP gibi ilkeleri ve bağımlılık enjeksiyonu (dependency injection), arayüzler ve soyutlamalar gibi araçları daha çok kullanırız. Tasarımlarımız daha da gelişir.

Bir kere testlerimizi yazdıktan sonra, kodumuzu ve sınıflarımızı temiz tutmak için kendimizi yetkin hissederiz. Bunu, kodumuzu sürekli yeniden yapılandırarak yaparız. Eklediğimiz birkaç kod satırı için durup yeni tasarım üzerine düşünürüz. Testlerimiz geçtiğindeyse hiçbir yeri bozmadığımızdan emin olabiliriz. Testlere sahip olmamız, kodu temizlerken onu bozma korkumuzu ortadan kaldırır.

Düzenlemelerimizi yaparken, temiz tasarım hakkında tüm bildiklerimizi uygularız: birbirine bağlılığı (cohesion) artırırız, bağımlılığı (coupling) azaltırız, meselelerin ayrımını (seperation of concerns) sağlarız, sistemlerimizi modülarize ederiz, fonksiyon ve sınıflarımızı küçültür ve daha iyi isimler seçeriz. Burası ayrıca basit tasarımın son üç kuralını uyguladığımız yerdir: tekrarları kaldır, kodunun açıklayıcı olduğundan emin ol, sınıf ve metot sayılarını en aza indir.

## Kural #2: Tekrarlanmış kodlar yazmayın
Tekrarlanmış kodlar, iyi tasarlanmış sistemlerin birinci düşmanıdır. Tekrarlanmış kodlar, ek iş, ek risk ve gereksiz karmaşa demektir. Örneğin bir liste sınıfındaki şu iki metota bakalım:
```
int size() {}
boolean isEmpty() {}
Şu şekilde buradaki tekrarlanmış kodu eleyebiliriz:

boolean isEmpty() {
    return 0 == size();
}
```
Temiz bir sistem yaratmak, bir kaç satır kod için bile olsa tekrarı azaltma isteği gerektirir. Örneğin:
```
public void scaleToOneDimension(
    float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
        return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01 f);
    RenderedOp newImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);
    image.dispose();
    System.gc();
    image = newImage;
}
public synchronized void rotate(int degrees) {
    RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
    image.dispose();
    System.gc();
    image = newImage;
}
```
Bu sistemi temiz tutabilmek için, scaleToOneDimension ve rotate metotları arasındaki tekrarlanmış kodları elemeliyiz:
```
public void scaleToOneDimension(
    float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)            return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01 f);
    replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
}
public synchronized void rotate(int degrees) {
    replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}
private void replaceImage(RenderedOp newImage) {
    image.dispose();
    System.gc();
    image = newImage;
}
```
Bu küçük ortak metodu çıkararak, SRP ilkesini ihlal ediyor olduğumuzu farkettik. Böylece yeni çıkarılan metodu başka bir sınıfa taşıyabiliriz. Ve böylece ekipten başka birisine yeni metodu soyut bir sınıfa alma ve onu başka yerlerde kullanma fırsatı vermiş oluruz. Yaptığımız bu küçük değişiklik sistem karmaşıklığının ciddi şekilde azalmasına yardımcı olur.

Template Method deseni, daha üst düzey tekrarları kaldırmak için kullanılan yaygın bir yöntemdir. Örneğin:
```
public class VacationPolicy {
    public void accrueUSDivisionVacation() {
        // code to calculate vacation based on hours worked to date      
        // code to ensure vacation meets US minimums
        // ...
        // code to apply vaction to payroll record
        // ...
    }
    public void accrueEUDivisionVacation() {
        // code to calculate vacation based on hours worked to date
        // code to ensure vacation meets EU minimums
        // code to apply vaction to payroll record
        // ...
    }
}
```
accrueUSDivisionVacation ve accrueEUDivisionVacation metotlarının çoğu, yasal minimumları hesaplamak dışında aynı. Algoritmanın bu kısmı çalışan türüne göre değişiyor. Template Method desenini uygulayarak bariz tekrarlanmışlığı ortadan kaldırabiliriz:
```
abstract public class VacationPolicy {
    public void accrueVacation() {
        calculateBaseVacationHours();
        alterForLegalMinimums();
        applyToPayroll();
    }
    
    private void calculateBaseVacationHours() { /* ... */ };
    abstract protected void alterForLegalMinimums();
    private void applyToPayroll() { /* ... */ };
}
public class USVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums() {
        // US specific logic
    }
}
public class EUVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums() {
        // EU specific logic
    }
}
```
Alt sınıflar, tekrarlanmış olmayan diğer bilgiyi sağlayarak accrueVacation algoritmasındaki “boşluğu” doldurur.

## Kural #3: Açıklayıcı olun
Bir yazılım projesinin maliyetinin çoğu uzun dönem bakımıdır. Kodumuzu değiştirirken hata potansiyelini en aza indirgemek için, sistemin ne yaptığını anlamamız önemlidir. Sistemler daha karmaşık hale geldikçe, yazılımcılar olarak anlamamız zorlaşır ve hata yapma riskimiz artar. Bu nedenle, kodumuz yazanın niyetini açıkça belli etmelidir. Yazar kodu daha da açık hale getirdikçe, diğerlerinin anlaması için geçen süre ve bakım süresi daha da azalır.

İyi isimler seçerek kendinizi daha iyi ifade edebilirsiniz. Ayrıca küçük fonksiyon ve sınıfların isimlendirmesi, anlaşılması ve yazılması daha kolaydır.

Standart bir terminoloji kullanarak da kendinizi ifade edebilirsiniz. Örneğin tasarım desenleri (design patterns) büyük ölçüde iletişim ve ifade etme ile alakalıdır. Bu desenleri gerçekleştiren sınıfların isimlerinde bu tür standart desen isimleri kullanarak, Command veya Visitor gibi, tasarımınızı diğer geliştiricilere kısaca açıklayabilirsiniz.

İyi yazılmış birim testleri de açıklayıcıdır. Testlerin öncelikli amacı, örnek dokümantasyon sağlamaktır. Testlerimizi okuyan biri sınıfın neyle ilgili olduğunu hızlı bir şekilde anlayabilmelidir.

Çoğu zaman kodumuzun çalışmasını sağlıyor ve bir sonraki kişinin kodu okumasını kolaylaştırmak için hiç çabalamadan bir sonraki soruna geçiyoruz.

Unutmayın, bu kodu okuyacak bir sonraki kişi büyük ihtimalle siz olacaksınız.

Her bir fonksiyon ve sınıfınızla biraz zaman geçirin. Daha iyi isimler seçin, büyük fonksiyonları daha küçük fonksiyonlara bölün ve yarattığınız şeylere özen gösterin.

## Kural #4: Sınıf ve metot sayısını en aza indirin
Sınıflarımızı ve metotlarımızı küçültmek için çabalarken, küçük küçük bir çok sınıf ve metot yaratabiliriz. Bu kural ise bu sayıyı minimumda tutmamız gerektiğini söylüyor.

Yüksek sayıda sınıf ve metot bazen anlamsız dogmatikliğin bir sonucudur. Örneğin, her sınıf için bir arayüz oluşturmayı ısrarla vurgulayan bir kodlama standardını veya alanların (fields) ve davranışların her zaman veri sınıflarına ve davranış sınıflarına ayrılması gerektiğinde ısrarcı olan geliştiricileri düşünelim. Bu tür dogmalara karşı direnilmeli ve daha pragmatik bir yaklaşım benimsenmelidir.

Bu kuralın, 4 kuraldan en düşük öncelikli olanı olduğunu unutmayın. Bu yüzden sınıf ve fonksiyon sayısını düşük tutmak önemli olsa da, testler yazmak, tekrarları ortadan kaldırmak ve kendimizi açıkça ifade etmek daha önemlidir.
