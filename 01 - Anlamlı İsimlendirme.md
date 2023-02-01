# Clean Code (Temiz Kod)
> İyi isim seçmek zaman alır, ancak uzun vadede daha çok zaman kazandırır.

Her şeye bir isim veririz; değişkenlerimize, fonksiyonlarımıza, argümanlarımıza, sınıflarımıza ve paketlerimize. O kadar çok isimlendirme yaparız ki, bunu iyi yapsak iyi olur aslında.

İlk örneğimizle başlayalım:
```
int d; //elapsed time in days (gün cinsinden geçen süre)
```
d burada hiçbir şeyi açıklamıyor, günlerle ya da zaman ile alakalı hiçbir şey uyandırmıyor. Daha anlamlı isimler seçmeliyiz, şunlar gibi:
```
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
Peki aşağıdaki kodun amacı nedir? Ne yaptığını açıklamak neden bu kadar zor? Karışık işlemler yok, boşluklar ve girintiler (indentation) de mantıklı. Burada problem kodun basitliği değil, kapalı oluşu.
```
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList)
        if (x[0] == 4)
            list1.add(x);
    return list1;
}
```
- theList ne içeriyor?
- theList’in ilk elemanın önemi nedir?
- 4 değerinin önemi nedir?
- Dönen list1 listesini nasıl kullanmalıyım?
Bu soruların cevapları kodda değil, yazan programcının zihninde. Ancak şöyle olsa:
```
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int[] cell : gameBoard)
        if (cell[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell);
    return flaggedCells;
}
```
Kodun basitliğinin değişmediğini farkedin. Hala aynı sayıda işlemci ve sabit var. Ama kod daha açık hale geldi. Daha da ileri giderek, int[] dizisi kullanmak yerine, cell’leri içeren bir Cell sınıfı yapabiliriz.
Yaptığımız bu basit değişikliklerle kodda neler olduğunu anlamak hiç de zor değil.

Kötü isim örneklerinden biri de küçük l (Lüleburgaz’ın L’si) ya da büyük O kullanmaktır. Çünkü görünüşte bir ve sıfıra benzerler.
```
int a = l;
if (O == l)
    a = O1;
else
    l = 01;
```
Yazılımcılar kod yazarken problemleri kendileri yaratırlar. Örneğin aynı ismi aynı kapsamdaki (scope) 2 farklı objeye veremediğimizden, birini değiştirme yoluna gideriz. Sayı eklemek yeterli olmadığında, bunu birinin bir harfini eksilterek yaparız. Ancak, isimlerin farklı olması gerekiyorsa anlamları da farklı olmalıdır.

a1, a2, a3, … gibi isimlendirmeler kesinlikle anlamlı değildir. Bu tür isimler yazarın amacı hakkında en ufak bir ipucu bile vermezler. Burada a1 ve a2 yerine source (kaynak) ve destination (hedef) kullanılması çok daha anlamlıdır:
```
public static void copyChars(char a1[], char a2[]) {
    for (int i = 0; i < a1.length; i++) {
        a2[i] = a1[i];
    }
}
```
Product isimli bir sınıfınız olduğunu hayal edin. Bir de ProductInfo ve ProductData sınıflarımız olsun. “Info” ve “Data” birbirine yakın çağrışımlı, dolayısıyla etkisiz kelimelerdir ve aynı kapsamda kullanılmaları belirsizlik yaratır. Örneğin yeni eklenecek bir alanın (field) hangisine ekleneceği tamamen belirsizdir.

Şu şekilde metotlarımız olsun:
```
getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();
```
Geliştiriciler bu metotlardan hangisini çağıracağını nasıl bilebilir?

Benzer durum değişkenlerde de karşımıza çıkabilir. Örneğin moneyAmount değişkeni money değişkeninden, customerInfo customer’dan ya da accountData account’tan ayırt edilemez.

## Telaffuz Edilebilir İsimler Kullanın
Eğer bir değişkenin adını telafuz edemiyorsanız onu kullanmayın. Yakın zamanda karşılaştığım bir örnek: “yeterli” anlamında, adequate kelimesinin yerine, daha yaygın kullanılan ve dolayısıyla telaffuzu daha iyi bilinen enough kelimesi tercih edilmelidir.

## Aranabilir İsimler Kullanın
Tek harfli isimler ya da sayı sabitleri arama dostu değildir.
Örneğin MAX_CLASSES_PER_STUDENT sabitini çok rahatça bulabiliriz. Ancak 7'yi bulmak oldukça zahmetlidir. Aynı şekilde e de çok kötü bir seçimdir. İngilizce’deki en yaygın harftir ve neredeyse her yerde karşımıza çıkacaktır.

Kişisel tercihim, tek harfli isimleri yalnızca kısa metotlarda lokal değişken olarak kullanmaktır. Bir ismin uzunluğu, o değişkenin kapsamının genişliği ile eşdeğer olmalıdır. Eğer bir değişken ya da sabit bir kod bloğunda birden fazla kullanılacaksa, arama dostu bir isim vermek en iyisidir.

Şu iki kodu karşılaştıralım:
```
for (int j = 0; j < 34; j++) {
    s += (t[j] * 4) / 5;
}
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j = 0; j < NUMBER_OF_TASKS; j++) {
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks;
}
```
sum çok kullanışlı bir isim değil ancak en azından aranabilir. Benzer şekilde
WORK_DAYS_PER_WEEK sabitinin kullanımlarını bulmak, 5 sayısını bulmaktan kat be kat daha kolaydır.

## Arayüzler ve Gerçekleştirimler (Interfaces — Implementations)
Bir arayüz ve onu gerçekleştirecek somut (concrete) bir sınıf yazacağınızı düşünün. Bu iki sınıfa ne isim verirdiniz? IShapeFactory ve ShapeFactory mi? Ben arayüz sınıflarını sade bırakmayı tercih ediyorum. Başa I harfi eklemek yaygın bir pratik, ancak dikkat dağıtmakta bir numara ve bilgi vermek konusunda da sonuncu. Eğer arayüz ya da gerçekleştirim sınıflarından birini belirtmem gerekiyorsa, gerçekleştirim sınıfını seçiyorum; ShapeFactoryImpl şeklinde. Arayüz sınıfını belirtmekten çok daha iyi.

Sınıf isimleri Customer, WikiPage, Account ya da AddressParser gibi isimlerden/isim tamlamalarından oluşmalıdır. Sınıf isimleri asla bir fiil olmamalıdır.

Metot isimleri postPayment(), deletePage() ya da save() gibi fiillerden/fiil tamlamalarından oluşmalıdır. Erişimci (getters), mutatör (setters) ya da doğrulayıcı (predicate) (true/false dönen; isEmpty() gibi) metotların başlarına, Java standartlarına göre get, set, is eklenmelidir.
```
String name = employee.getName();
customer.setName("Mike");
if (paycheck.isPosted())
```
Farklı parametrelere sahip kurucular (constructors) oluşturmak yerine, “static” yapıcı (builder) metotlar kullanılmalıdır.

İlk satırdaki kod örneği, ikincisinden çok daha iyidir:

Complex fulcrumPoint = Complex.FromRealNumber(23.0);
Complex fulcrumPoint = new Complex(23.0);

## Kelime Oyunu Yapmaktan Kaçının
İki farklı amaç için aynı kelimeyi kullanmaktan ya da aynı amaçlar için farklı kelimeleri kullanmaktan kaçının. Örneğin Controller, Manager ya da Driver kelimelerini aynı kapsamda farklı sınıflar için kullanmak iyi bir kullanım örneği değildir. Birini seçin ve onunla devam edin.

Örneğin birileri sizden önce add metodu yazmış olsun ve bu metot da iki değeri birbirine birleştiriyor (concat) olsun. Bizim de bir listeye değer ekleyen bir metota ihtiyacımız olsun. Bu metoda add mi demeliyiz? Hayır. Bu durumda yeni metodumuza insert ya da append demeliyiz. Yeni bir add metodu yazmak, kelime oyunu yapmaktır.

## Gereksiz Bağlamlardan Kaçının
firstName, lastName, street, houseNumber, city, state ve zipcode isimli değişkenlerimiz olduğunu düşünelim. Birlikte alınca bir adresin detayları olduğunu çok çabuk anlayabiliyoruz. Ancak sadece state değişkenini görürsek, gene de adrese ait olduğunu düşünebilir miyiz?

Önekler (prefix) kullanarak bağlam (context) sağlayabilirsiniz; addrsFirstName, addrLastName, addrState vb. En azından okuyucular bu değişkenlerin daha büyük bir yapının parçası olduğunu anlayabileceklerdir. Elbette daha iyi bir çözüm Address isimli bir sınıf yaratmaktır.

Aşağıdaki örnekte ise metot oldukça uzun ve bir sürü değişkene sahiptir:
```
private void printGuessStatistics(char candidate, int count) {
    String number;
    String verb;
    String pluralModifier;
    if (count == 0) {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    } else if (count == 1) {
        number = "1";
        verb = "is";
        pluralModifier = "";
    } else {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    String guessMessage = String.format(
        "There %s %s %s%s", verb, number, candidate, pluralModifier);
    print(guessMessage);
}
```
Bu metodu daha küçük parçalara bölebilmek için GuessStatisticsMessage isimli bir sınıf oluşturmalı ve içine üç adet değişken koymalıyız:
```
public class GuessStatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;
    
    public String make(char candidate, int count) {
        createPluralDependentMessageParts(count);
        return String.format(
            "There %s %s %s%s",
            verb, number, candidate, pluralModifier);
    }
    private void createPluralDependentMessageParts(int count) {
        if (count == 0) {
            thereAreNoLetters();
        } else if (count == 1) {
            thereIsOneLetter();
        } else {
            thereAreManyLetters(count);
        }
    }
    private void thereAreManyLetters(int count) {
       number = Integer.toString(count);
       verb = "are";
       pluralModifier = "s";
    }
    private void thereIsOneLetter() {
       number = "1";
       verb = "is";
       pluralModifier = "";
    }
    private void thereAreNoLetters() {
       number = "no";
       verb = "are";
       pluralModifier = "s";
    }
}
```
Son örneğimize geçelim: Gas Station Deluxe isimli bir uygulamamız olsun. Bu uygulamada her sınıfın başına GSD öneki koymak kötü bir fikir. Örneğin GSD’nin hesap modülüne bir MailingAddres sınıfı eklediğinizi ve ismine GSDAccountAddres dediğinizi düşünelim. Daha sonra müşteri modülü için de bir MailingAddres sınıfına ihtiyaç duyduğunuzda GSDAccountAddres sınıfını kullanır mısınız? Doğru isim mi sizce?

Burada 10 karakter (GSDAccount) tamamen gereksizdir. Bu nedenle kısa isimler, açık ve net oldukları müddetçe uzun isimlerden her zaman daha iyidir.
