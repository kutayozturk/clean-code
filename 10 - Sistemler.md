Bir şehir yapmak isteseniz, tüm ayrıntıları kendiniz yönetebilir miydiniz? Muhtemelen hayır. Mevcut bir şehri yönetmek bile bir kişi için çok fazladır. Şehirler yine de çalışmaya devam eder, çünkü kentlerin belirli kısımlarını, su sistemlerini, güç sistemlerini, trafiğini, kolluk kuvvetlerini, bina kodlarını vb. yöneten bir takım insanlar vardır. Bazıları bu büyük resmin sorumluluğunu üstlenirken diğerleri ayrıntılara odaklanır. Bireylerin ve yönettikleri bileşenlerin büyük resmi anlamaksızın etkili bir şekilde çalışmasını mümkün kılan, soyutlama ve modülerlik düzeyleridir.

Temiz kod da, soyutlamanın daha düşük seviyelerinde bunu başarmamıza yardımcı olur.

## Oluşturma Aşamalarının Kullanımdan Ayrılması
Oluşturma (construction), kullanımdan çok farklı bir süreçtir. Uygulama nesneleri oluşturulurken ve birbiri ile ilişkilendirilirken, nesnelerin oluşturulma süreci çalışma zamanı mantığından (runtime logic) ayrılmalıdır.

Meselelerin ayrılması (seperation of concerns), zanaatımızın (craft) en eski ve en önemli tasarım tekniklerinden biridir. Maalesef çoğu uygulama bu çok faydalı tasarım tekniğini kullanmıyor. Tipik bir örnek:
```
public Service getService() {
    if (service == null)
        service = new MyServiceImpl(...); // Good enough default for most cases?
        return service;
}
```
Bu, ihtiyaç halinde oluşturma (lazy initialization/evaluation) deyimidir ve bir çok faydası vardır: Nesneyi kullanmadıkça yapımının (construction) üstesinden gelmek zorunda değiliz ve başlatma sürecimiz çok daha hızlı olabilir. Ayrıca hiçbir zaman null dönmediğinden de emin oluruz.

Faydaları olmasına rağmen olumsuz yanları da vardır. Örneğin; MyServiceImpl ve yapıcısının (constructor) gerektirdiği her şeye bir bağımlılığımız var. Bağımlılıkları çözmeden kodu derlememiz imkansız. İş mantığı (business logic) ile yapım aşaması birbirine geçmiş olduğundan, testlerimizde hem null durumunu, hem de çalışacak olan bloğu test etmeliyiz. Bu sorumlulukların her ikisine birden sahip olması metodun birden fazla şey yaptığını gösterir, bu yüzden bir bakıma Single Responsibility prensibini ihlal ediyoruz.

İhtiyaç halinde oluşturma (lazy initialization) deyiminin bir kez kullanımı sorun teşkil etmez ancak uygulamalarda bir çok örneği vardır. Güçlü sistemler oluşturmaya gayret gösteriyorsak, küçük, kullanışlı deyimlerin modülerliği bozmasına asla izin vermemeliyiz. Nesnenin oluşturulması ve birbiri ile ilişkilendirilmesi süreci de bir istisna değildir. Bu işlemleri iş mantığından ayrı olarak modülerize etmeli ve bağımlılıklarımızı çözmek için tutarlı bir stratejimiz olduğundan emin olmalıyız.

“main”in Ayrılması
main fonksiyonu, sistem için gerekli olan tüm nesneleri üretir, ardından oluşturduğu nesneleri onları kullanacak olan uygulamaya geçer. Akış tek yönlü, main’den uygulamaya doğrudur. Bu demektir ki, uygulama main hakkında hiçbir şey bilmez ve sadece tüm nesnelerin düzgün şekilde oluşturulup oluşturulmadığına bakar.

## Fabrikalar
Bazen bir nesne yaratılmasından uygulamanın sorumlu olmasını isteriz. Örneğin, bir sipariş alma sisteminde uygulama, Order nesnesine ekleyebilmek için LineItem örneklerini (instances) oluşturmalıdır. Bu durumda LineItem örneklerinin oluşturulmasının kontrolünü uygulamaya vermek için Abstract Factory desenini kullanabiliriz. Kontrol uygulamada ancak detaylar uygulama kodundan tamamen ayrıdır:

![](https://miro.medium.com/max/720/1*eIiyFx28080y6CWckzQiuA.webp)

Tüm bağımlılıklar main’den OrderProcessing uygulamasına doğrudur. Bu, uygulamanın bir LineItem nesnesinin nasıl oluşturulacağı ile ilgili detaylardan kopuk olduğu anlamına gelir. Bu detaylar, main’in tarafında olan LineItemFactoryImplementation sınıfında tutulur. Uygulama, LineItem örneklerinin ne zaman oluşturulacağı üzerinde tam kontrole sahiptir, ve hatta uygulamaya özel yapıcı argümanları da sağlayabilir.

## Bağımlılık Enjeksiyonu
Kontrolü tersine çevirme (Inversion of Control — IoC) yönteminin bağımlılıkların yönetimi (dependency management) için bir uygulaması olan Bağımlılık Enjeksiyonu (Dependency Injection — DI), nesnelerin oluşumunu kullanımdan ayırmada güçlü bir mekanizmadır. IoC ikincil sorumlulukları bir nesneden -o işe adanmış- başka nesnelere taşır, ve böylece Single Responsibility prensibi desteklenmiş olur. Bağımlılıkların yönetimi konusu özelinde düşünecek olursak, bir nesne kendi bağımlılıklarının örneklerini oluşturma sorumluluğunu almamalıdır. Bunun yerine, bu sorumluluğu başka bir yetkili mekanizmaya, IoC / DI mekanizmasına bırakmalıdır.

İyi bir bağımlılık enjeksiyonu mekanizmasında; sınıf, bağımlılıklarını gidermek için doğrudan hiçbir adım atmaz, tamamen pasiftir. Bunun yerine, bağımlılıkları enjekte etmek için kullanılan setter metotlar veya yapıcı argümanlar (veya her ikisini) sağlar. Nesnelerin oluşturulma süreçlerinde, DI konteyneri gerekli nesneleri örnekler ve bağımlılıkları ilişkilendirmek için sağlanan yapıcı değişkenlerini veya setter metotları kullanır. Bağımlılıklar genellikle bir konfigürasyon dosyası aracılığıyla belirtilir. Spring Framework, Java için en iyi bilinen DI konteynerini sağlar.

## Big Design Up Front
Big Design Up Front yaklaşımına göre, yazılımın gerçekleştirimi başlamadan önce tasarım tamamlanmalı ya da mükemmelleştirilmelidir. Genellikle yazılım geliştirmedeki şelale (waterfall) modeli ile ilişkilendirilir. BDUF kulağa hoş gelse de aslında zararlı bir yaklaşımdır. Önceki eforu çöpe atma konusunda oluşacak olan psikolojik direnç nedeniyle değişime uyum sağlamayı engeller. Bu direnç bazen kişisel, ama çoğu zaman ekonomik sebeplerden kaynaklanır. Eğer uygulama mimarisinde Seperation of Concerns sağlanmışsa, radikal değişiklikler dahi ekonomik olarak yapılabilir (feasible) olacaktır. Uygulamaya ait mimari kararların tümünün en başta alınması yerine, olabildiğince basit ancak ayrılmış bir mimari (decoupled architecture) ile başlayarak, çalışan uygulama özelliklerini kısa aralıklarla teslim etmeye odaklanılmalı, mimari iyileştirmeler buna paralel ilerlemelidir.

Modülerite ve Seperation of Concern dağıtılmış (decentralized) yönetim ve karar vermeyi mümkün kılar. Büyük bir sistemde, bir şehirde ya da bir yazılım projesinde, hiç kimse tüm kararları vermeye yetkin değildir. En iyisi, sorumlulukları en kalifiye kişilere vermek ve kararları mümkün olan son ana kadar ertelemektir. Bu ikisi çelişiyor gibi görünebilir, ancak kararları ertelemek mümkün olan en iyi ve en çok bilgi ile karar vermemizi sağlar. Erken bir karar, optimal olmayan bilgi ile verilmiş bir karardır. Gerçekleştirim kararlarımızı ne kadar erken verirsek, müşteri geri bildirimi ve bu kararlardaki deneyimimiz o kadar az olacaktır.

Sistemler de temiz olmalıdır. Kötü bir mimari iş mantığını (business logic) belirsizleştirir ve çevikliği (agility) olumsuz etkiler. İş mantığı gizlendiğinde, hataların bulunması ve yeni özelliklerin eklenmesi zorlaşır. Çeviklik azalırsa, üretkenlik azalır ve TDD’nin faydaları kaybolur.

Soyutlamanın her seviyesinde niyet açık olmalıdır. Sistemler veya bağımsız modüller tasarlarken, çalışabilecek en basit çözümü uygulamayı unutmayın.
