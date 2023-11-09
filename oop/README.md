# OOP'nin Temelleri

Nesne yönelimli programlama (**O**bject-**o**riented **p**rogramming), nesneye dayalı programlamanın bir paradigmasıdır. Veri parçalarını ve bu veriye ilişkin davranışları özel paketler olarak adlandırılan nesnelerde sarmalama kavramına dayanır. Bu nesneler, bir programcı tarafından tanımlanan sınıflar (classes) adı verilen bir dizi plan (blueprints) ile oluşturulur.


## Nesneler & Sınıflar

Kedileri sever misiniz? Umarım seversiniz; çünkü bu repo'da OOP kavramlarını çeşitli kedi örnekleri kullanarak açıklamaya çalışacağım.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/caefdda0-4b10-4717-9929-a9934f1c6198)

*Bu bir UML sınıfı diyagramıdır. Bu repoda bu tür birçok diyagram göreceksiniz.*

Diyelim ki Oscar adında bir kediniz var. Oscar bir nesnedir, `Cat` sınıfının bir örneğidir (instance). Her kedinin birçok standart özelliği vardır: isim, cinsiyet, yaş, kilo, renk, en sevdiği yiyecek vb. 
Bunlar sınıfın alanlarını (fields) tanımlar.

Bütün kediler de benzer şekilde davranır: nefes alır, yer, koşar, uyur ve miyavlarlar. Bunlar sınıfın yöntemleridir (methods). Tüm bu alanlar ve yöntemlere sınıflarının üyeleri (members) olarak atıfta bulunulabilir.

💡 *Nesnenin alanlarında depolanan verilere genellikle durum (state) olarak ulaşılır ve nesnenin tüm yöntemleri davranışını tanımlar.*

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/1d15cc58-6064-4f67-95b0-a549527d1e18)

*Nesneler, sınıfların örnekleridir (instance).*

Arkadaşınızın kedisi Luna da `Cat` sınıfının bir örneğidir. Oscar'la aynı özelliklere sahiptir. Aralarındaki fark, bu özelliklerin değerlerindedir: cinsiyeti kadındır, farklı bir rengi ve daha hafif olabilir.

Kısaca bir sınıf, o sınıfın somut örnekleri olan nesnelerin yapısını tanımlayan bir plan (blueprint) gibidir.

## Sınıf Hiyerarşileri

Bir sınıf hakkında konuştuğumuzda her şey yolunda görünebilir. Doğal olarak, gerçek bir program tek bir sınıftan fazlasını içermektedir. Bu sınıflardan bazıları sınıf hiyerarşileri halinde düzenlenebilir. Bunun ne anlama geldiğine gelin beraber bakalım.

Komşunuzun Fido adında bir köpeği olduğunu düşünelim. Görünüşe göre, köpeklerin ve kedilerin çok ortak noktası var: isim, cinsiyet, yaş ve renk gibi özellikler hem köpeklerin hem de kedilerin ortak nitelikleridir. Köpekler de, kedilerin yaptığı gibi nefes alabilir, uyuyabilir ve koşabilir. O halde, ortak nitelikleri ve davranışlar barındıracak temel `Animal` sınıfını tanımlayabiliriz.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/016410fc-a35a-4a53-abd0-d49cbf18f2a7)

*Bir sınıf hiyerarşisinin UML diyagramı. Bu diyagramdaki tüm sınıflar Hayvan (Animal) sınıfı hiyerarşisinin bir parçasıdır.*

Dilerseniz bir adım daha da ileri gidebilir ve Hayvanlar (`Animals`) ve Bitkiler (`Plants`) için bir üst sınıf haline gelecek olan tüm canlı Organizmalar (`Organisms`) için daha genel bir sınıf oluşturabiliriz. Böyle bir sınıf piramidi bir hiyerarşidir. Böyle bir hiyerarşide Kedi (`Cat`) sınıfı, hem Hayvan (`Animals`)  hem de Organizma (`Organisms`) sınıflarından her şeyi miras alır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/ce136619-5a11-4252-8e9c-df454f94f938)

*UML diyagramındaki sınıflar aralarındaki ilişkiyi göstermek, içeriklerini göstermekten daha önemliyse basitleştirilebilir.*


# OOP Prensipleri

Nesne yönelimli programlama, onu diğer programlama paradigmalarından ayıran kavramlar olarak dört prensibe dayanır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/daf39393-1522-40ea-b453-9327a6e91c4a)

## Soyutlama (Abstraction)

Çoğu zaman, OOP kullanarak bir program oluştururken, programın nesnelerini gerçek dünya nesnelerine dayalı olarak oluşturursunuz. Bununla birlikte, programın nesneleri, orijinallerini %100 doğrulukla temsil etmez. Aslında bakarsanız bu nadiren gereklidir Bunun yerine, nesneleriniz yalnızca belirli bir bağlamdaki gerçek nesnelerin özelliklerini ve davranışlarını modellemektedir, geri kalan kısmını göz ardı edebiliriz.

Örneğin, bir Uçak (`Airplane`) sınıfı muhtemelen hem bir uçuş simülatöründe hem de bir uçuş rezervasyon uygulamasında var olabilir. Ancak birinci durumda, gerçek uçuşa ilişkin ayrıntıları içerecektir, oysa ikinci durumda sadece koltuk düzenini ve hangi koltukların kullanılabilir olduğunu ele almanız yeterli olacaktır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/112eaf18-7cb4-40f7-b2d8-b6d79c13aa60)

*Aynı gerçek dünya nesnesinin farklı modelleri.*

Soyutlama (Abstraction) belirli bir bağlama sınırlı, bu bağlamla ilgili tüm ayrıntıları yüksek doğrulukla temsil eden ve geri kalanları atlayan gerçek dünya nesnesi olarak düşünülebilir.

## Kapsülleme (Encapsulation)

Araba motorunu çalıştırmak için sadece bir anahtar çevirmeniz veya bir düğmeye basmanız yeterlidir. Motorun kaputunun altındaki telleri bağlamak, krank milini ve silindirleri döndürmek veya motorun güç döngüsünü başlatmak gibi ayrıntılarla uğraşmanıza gerek yoktur. Bu ayrıntılar arabanın kaputunun altında gizlenmiştir. Oldukça basit bir arayüze sahipsinizdir: bir başlatma anahtarı, bir direksiyon ve bazı pedallar. Bu, her nesnenin bir arayüze (`interface`) sahip olduğunu gösterir - nesnenin diğer nesnelerle etkileşimlere açık olan **public** kısmı.

Kapsülleme, bir nesnenin durumunun ve davranışlarının diğer nesnelerden gizlenebilme yeteneğidir. Programın geri kalanına sınırlı bir arayüzü verir. 

Bir şeyi kapsüllenmiş hale getirmek, onu özel hale (private) getirmektir. Bu nedenle yalnızca kendi sınıfının yöntemlerinin içinden erişilebilir hale gelir. Daha az kısıtlayıcı bir mod olan korumalı (protected) olarak adlandırılan bir yapı daha vardır ve bu yapı, bir sınıfın üyelerini alt sınıflar için de erişilebilir yapar. 

Çoğu programlama dilinin arayüzleri ve soyut sınıflar/yöntemleri soyutlama ve kapsülleme kavramlarına dayanmaktadır. Modern nesne yönelimli programlama dillerinde, arayüz mekanizması (genellikle interface veya protocol anahtar kelimesi ile bildirilir), nesneler arasındaki etkileşim anlaşmalarını tanımlamanıza olanak tanır. Bu, arayüzlerin yalnızca nesnelerin davranışlarıyla ilgilendiği ve neden bir arayüzde bir alan bildiremeyeceğiniz nedenlerden biridir.

*"Arayüz" (interface) terimi programlamada gerçekten kafa karıştırıcı olabilir. Bu terim, bir nesnenin programın geri kalanı ile etkileşime giren public bir bölümünü ifade ederken bununla beraber birçok programlama dilinde "arayüz" olarak adlandırılan bir dil özelliği de bulunmaktadır. Bu terimin çift kullanımı, yazılım geliştirme alanındaki yeni başlayanlar için zorlayıcı olabilir.*

Düşünü ki `FlyingTransport` adında bir arayüzünüz var ve içinde `fly(origin, destination, passengers)` adında bir metot bulunuyor. Hava taşımacılığı simülatörü tasarlarken, Havalanı (`Airport`) sınıfını yalnızca `FlyingTransport` arayüzünü uygulayan nesnelerle çalışacak şekilde sınırlayabilirsiniz. Böylece bir havalimanı nesnesine iletilen herhangi bir nesnenin (`Airplane`, `Helicopter` veya `DomesticatedGryphon` gibi) bu tür bir havalimanından kullanılabileceğinden emin olabilirsiniz.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/3571097a-e87f-4af1-a64a-6b174968c753)

*Bir arabirim uygulayan birkaç sınıfın UML diyagramı.*

Bu sınıflardaki `fly` yönteminin uygulanmasını istediğiniz şekilde değiştirebilirsiniz. Yöntemin imzası, arayüzde bildirildiği gibi olduğu sürece, Havalimanı (`Airport`) sınıfının tüm örnekleri uçan nesnelerinizle sorunsuz bir şekilde çalışabilir.

## Miras (Inheritance)

Miras, mevcut sınıfların üzerine yeni sınıflar oluşturma yeteneğidir. Mirasın başlıca faydası kodun yeniden kullanılabilmesidir. Mevcut bir sınıftan biraz farklı bir sınıf oluşturmak isterseniz, kodu çoğaltmanıza gerek yoktur. Bunun yerine mevcut sınıfı genişletirsiniz ve ekstra işlevselliği sonuçta oluşturulan bir alt sınıfa (subclass) koyarsınız, bu alt sınıf, üst sınıfın alanlarını ve yöntemlerini devralır.

Miras kullanmanın sonucu olarak alt sınıflar, üst sınıfın arayüzüne sahip olurlar. Bir yöntem, üst sınıfta bildirildiyse, alt sınıfta gizleyemezsiniz. Ayrıca, alt sınıfınız için anlamsız olsa bile soyut yöntemleri uygulamak zorundasınız.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/fd4ff5a5-c21c-4233-9326-1ad4dc9e31cd)

*Aynı anda birden çok arabirimi uygulamaya karşı tek bir sınıfı genişletmenin UML diyagramı.*

Çoğu programlama dilinde bir alt sınıf yalnızca bir üst sınıfı genişletebilir. Öte yandan, herhangi bir sınıf aynı anda birden fazla arayüzü uygulayabilir. Ancak, daha önce belirttiğim gibi, bir üst sınıf bir arayüzü uygularsa, tüm alt sınıflar da uygulamak zorundadır.

## Polimorfizm (Polymorphism)

Hayvan örneklerine geri dönelim. Çoğu Hayvan (`Animal`) ses çıkarabilir. Tüm alt sınıfların doğru sesi çıkarabilmesi için temel `makeSound` yöntemini geçersiz kılması gerekeceğini tahmin edebiliriz; bu nedenle onu hemen soyut (abstract) olarak tanımlayabiliriz. Bu, süper sınıfta yöntemin varsayılan uygulamasını atlayabilmemize ve tüm alt sınıfların kendi uygulamalarını oluşturmasını zorunlu kılmamıza olanak tanır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/9a5e89ce-2bd0-42e4-ab36-8dec134fe25e)

Hayvanları büyük bir çantaya koymuş olsak ve ardından gözlerimizi kapatsak ve birer birer hayvanları çantadan çıkarsak, çantadan bir hayvan aldıktan sonra onun kesin olarak ne olduğunu bilemeyiz. Ancak onu yeterince sıkıca seversek, hayvan, somut (concrete) sınıfına bağlı olarak belirli bir sevinç sesi çıkaracaktır.

```java
bag = [new Cat(), new Dog()];
foreach (Animal a : bag)
	a.makeSound()

// Meow!
// Woof!
```

Polimorfizm, bir programın bir nesnenin gerçek sınıfını tespit edebilme ve mevcut bağlamda gerçek türü bilinmese bile uygulanabilirliğini ortaya çıkarabilme yeteneğidir. Polimorfizmi, bir nesnenin genellikle miras aldığı bir sınıf veya uyguladığı bir arabirim gibi numara yapabilme yeteneği olarak da düşünebilirsiniz. Örneğimizde, çantadaki köpekler ve kediler, genel hayvanları taklit ediyordu.

# Nesneler Arasındaki ilişkiler

İncelediğimiz kavramlardan olan kalıtım (inheritance) ve uygulamanın (implementation) yanı sıra, henüz konuşmadığımız nesneler arasındaki diğer ilişki türleri de bulunmaktadır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/5fd64b13-ea7e-467d-96d8-e96c4c86844b)

*UML Association: Profesör öğrencilerle iletişim kurar.*

**Bağlantı (Association)**, bir nesnenin başka bir nesneyi kullanmasına veya onunla etkileşimde bulunmasına dayalı bir ilişki türüdür. UML diyagramlarında, bir nesnenin diğerini kullandığını gösteren bir okla gösterilen bir ilişki ile gösterilir. Ayrıca, iki yönlü bir ilişkiye sahip olmak tamamen normaldir. Bu durumda, ok her iki ucunda bir sivriliğe sahiptir. 

Genel olarak, bir bağlantıyı bir sınıfta bir alan gibi temsil etmek için kullanırsınız. Bağlantı her zaman orada bulunur, bir siparişten müşterisini isteyebilirsiniz. Gerçekten bir alan olması gerekmez, daha çok bir arabirim perspektifinden modellemeniz gerekiyorsa, sadece siparişin müşterisini döndürecek bir yöntemin varlığını gösterir.


![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/3393526f-a288-4567-8a5b-0d34b679ff70)

*UML Dependency. Profesör maaşa bağlıdır.*

**Bağımlılık (Dependency)**, genellikle nesneler arasında kalıcı bir bağlantı olmadığını ima eden bir birliktelik türüdür. Bağımlılık, genellikle (ancak her zaman değil) bir nesnenin bir başka nesneyi bir yöntem parametresi olarak kabul ettiği, başlattığı veya kullandığı anlamına gelir. Sınıflar arasında bir bağımlılığı nasıl belirleyebilirsiniz? Cevap oldukça basit: Bir sınıfın tanımındaki değişikliklerin başka bir sınıfta değişikliklere neden olması durumunda iki sınıf arasında bir bağımlılık vardır.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/028fd5ef-45c9-45cf-b038-2f4f268722bc)

*UML Composition. Üniversite bölümlerden oluşmaktadır.*

**Kompozisyon (Composition)**, iki nesne arasındaki "bütün-bölüm" ilişkisidir, bunlardan biri diğerinin bir veya daha fazla örneğinden oluşur. Bu ilişki ile diğer ilişkiler arasındaki ayrım, bileşenin yalnızca bir konteynerin parçası olarak var olabileceğidir. UML'de kompozisyon ilişkisi, konteynerin sonunda doldurulmuş bir elmas şekli ve bileşenin tarafında bir ok ile gösterilir.

*Nesneler arasındaki ilişkilerden bahsederken, UML'nin sınıflar arasındaki ilişkileri temsil ettiğini unutmayın. Bu, bir üniversite nesnesinin, diyagramdaki her varlık için sadece bir "blok" görseniz bile birden fazla bölümden oluşabileceği anlamına gelir. UML notasyonu ilişkilerin her iki tarafındaki miktarları temsil edebilir, ancak miktarlar bağlamdan açıkça görünüyorsa bunları göz ardı etmekte uygundur.*

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/0db683da-d574-499a-8406-9f1d7a240e91)

*UML Aggregation. Bölüm profesörlerden oluşmaktadır.*

**Birleştirme/Toplama (Aggregation)**, sadece bir nesnenin başka bir nesneye bir başvuru içerdiği daha az katı bir kompozisyon türüdür. Konteyner, bileşenin yaşam döngüsünü kontrol etmez. Bileşen, konteyner olmadan var olabilir. Bununla beraber, aynı anda birkaç konteynerle ilişkilendirilebilir. UML'de, birleştirme ilişkisi, birleşme ile aynı şekilde çizilir, ancak okun tabanında boş bir elmas ile işaretlenir.


















