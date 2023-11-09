# OOP'nin Temelleri

Nesne yönelimli programlama (**O**bject-**o**riented **p**rogramming), nesneye dayalı programlamanın bir paradigmasıdır. Veri parçalarını ve bu veriye ilişkin davranışları özel paketler olarak adlandırılan nesnelerde sarmalama kavramına dayanır. Bu nesneler, bir programcı tarafından tanımlanan sınıflar (classes) adı verilen bir dizi plan (blueprints) ile oluşturulur.


## Nesneler & Sınıflar

Kedileri sever misin? Umarım seversiniz; çünkü bu repo'da OOP kavramlarını çeşitli kedi örnekleri kullanarak açıklamaya çalışacağım.

![](https://github.com/fatihes1/tasarim-desenleri-tr/assets/54971670/caefdda0-4b10-4717-9929-a9934f1c6198)

*Bu bir UML sınıfı diyagramıdır. Bu repoda bu tür birçok diyagram göreceksiniz.*

Diyelim ki Oscar adında bir kediniz var. Oscar bir nesnedir, `Cat` sınıfının bir örneğidir (instance). Her kedinin birçok standart özelliği vardır: isim, cinsiyet, yaş, kilo, renk, en sevdiği yiyecek vb. 
Bunlar sınıfın alanlarını (fields) tanımlar.

Bütün kediler de benzer şekilde davranır: nefes alır, yer, koşar, uyur ve miyavlarlar. Bunlar sınıfın yöntemleridir (methods). Tüm bu alanlar ve yöntemlere sınıflarının üyeleri (members) olarak atıfta bulunulabilir.

💡 *Nesnenin alanlarında depolanan verilere genellikle durum (state) olarak ulaşılır ve nesnenin tüm yöntemleri davranışını tanımlar.*

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281370473-f11f5454-a99d-45e9-8596-7caacc8f202f.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T104612Z&X-Amz-Expires=300&X-Amz-Signature=d486072c642bdbf3c52f483814fa0062a9f20bce34f9aa77d7d1ebbd6b13a7e4&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*Nesneler, sınıfların örnekleridir (instance).*

Arkadaşınızın kedisi Luna da `Cat` sınıfının bir örneğidir. Oscar'la aynı özelliklere sahiptir. Aralarındaki fark, bu özelliklerin değerlerindedir: cinsiyeti kadındır, farklı bir rengi ve daha hafif olabilir.

Kısaca bir sınıf, o sınıfın somut örnekleri olan nesnelerin yapısını tanımlayan bir plan (blueprint) gibidir.

## Sınıf Hiyerarşileri

Bir sınıf hakkında konuştuğumuzda her şey yolunda görünebilir. Doğal olarak, gerçek bir program tek bir sınıftan fazlasını içermektedir. Bu sınıflardan bazıları sınıf hiyerarşileri halinde düzenlenebilir. Bunun ne anlama geldiğine gelin beraber bakalım.

Komşunuzun Fido adında bir köpeği olduğunu düşünelim. Görünüşe göre, köpeklerin ve kedilerin çok ortak noktası var: isim, cinsiyet, yaş ve renk gibi özellikler hem köpeklerin hem de kedilerin ortak nitelikleridir. Köpekler de, kedilerin yaptığı gibi nefes alabilir, uyuyabilir ve koşabilir. O halde, ortak nitelikleri ve davranışlar barındıracak temel `Animal` sınıfını tanımlayabiliriz.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281372173-faf6bf7a-f777-42bc-9594-02986bfc63be.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T105242Z&X-Amz-Expires=300&X-Amz-Signature=c8b5acea2dfe868120cd166cb75df7bc3a7d449c252d1339a7f2feab6ea55485&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*Bir sınıf hiyerarşisinin UML diyagramı. Bu diyagramdaki tüm sınıflar Hayvan (Animal) sınıfı hiyerarşisinin bir parçasıdır.*

Dilerseniz bir adım daha da ileri gidebilir ve Hayvanlar (`Animals`) ve Bitkiler (`Plants`) için bir üst sınıf haline gelecek olan tüm canlı Organizmalar (`Organisms`) için daha genel bir sınıf oluşturabiliriz. Böyle bir sınıf piramidi bir hiyerarşidir. Böyle bir hiyerarşide Kedi (`Cat`) sınıfı, hem Hayvan (`Animals`)  hem de Organizma (`Organisms`) sınıflarından her şeyi miras alır.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281373064-c99c819a-6fac-430a-af7e-b26e20aeaff5.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T105611Z&X-Amz-Expires=300&X-Amz-Signature=55b53b4e3372626f3101daec3c065a8810af97d2f9f76fbfb28ff04ed8f1fbf0&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*UML diyagramındaki sınıflar aralarındaki ilişkiyi göstermek, içeriklerini göstermekten daha önemliyse basitleştirilebilir.*


# OOP Prensipleri

Nesne yönelimli programlama, onu diğer programlama paradigmalarından ayıran kavramlar olarak dört prensibe dayanır.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281374180-fd3b52bf-1e93-4bcc-8701-b18a42678d5e.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T110001Z&X-Amz-Expires=300&X-Amz-Signature=6dc8add4b843679f258d09bdb9899619429f403f6b50e8a2c30abc9632bbaf15&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

## Soyutlama (Abstraction)

Çoğu zaman, OOP kullanarak bir program oluştururken, programın nesnelerini gerçek dünya nesnelerine dayalı olarak oluşturursunuz. Bununla birlikte, programın nesneleri, orijinallerini %100 doğrulukla temsil etmez. Aslında bakarsanız bu nadiren gereklidir Bunun yerine, nesneleriniz yalnızca belirli bir bağlamdaki gerçek nesnelerin özelliklerini ve davranışlarını modellemektedir, geri kalan kısmını göz ardı edebiliriz.

Örneğin, bir Uçak (`Airplane`) sınıfı muhtemelen hem bir uçuş simülatöründe hem de bir uçuş rezervasyon uygulamasında var olabilir. Ancak birinci durumda, gerçek uçuşa ilişkin ayrıntıları içerecektir, oysa ikinci durumda sadece koltuk düzenini ve hangi koltukların kullanılabilir olduğunu ele almanız yeterli olacaktır.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281374994-8c973724-f452-4636-81ee-2402137bb5cf.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T110301Z&X-Amz-Expires=300&X-Amz-Signature=f309cd123bacd2c4855257b166dbe7e4d39cb1182f46edcf8639b7cf939c956f&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*Aynı gerçek dünya nesnesinin farklı modelleri.*

Soyutlama (Abstraction) belirli bir bağlama sınırlı, bu bağlamla ilgili tüm ayrıntıları yüksek doğrulukla temsil eden ve geri kalanları atlayan gerçek dünya nesnesi olarak düşünülebilir.

## Kapsülleme (Encapsulation)

Araba motorunu çalıştırmak için sadece bir anahtar çevirmeniz veya bir düğmeye basmanız yeterlidir. Motorun kaputunun altındaki telleri bağlamak, krank milini ve silindirleri döndürmek veya motorun güç döngüsünü başlatmak gibi ayrıntılarla uğraşmanıza gerek yoktur. Bu ayrıntılar arabanın kaputunun altında gizlenmiştir. Oldukça basit bir arayüze sahipsinizdir: bir başlatma anahtarı, bir direksiyon ve bazı pedallar. Bu, her nesnenin bir arayüze (`interface`) sahip olduğunu gösterir - nesnenin diğer nesnelerle etkileşimlere açık olan **public** kısmı.

Kapsülleme, bir nesnenin durumunun ve davranışlarının diğer nesnelerden gizlenebilme yeteneğidir. Programın geri kalanına sınırlı bir arayüzü verir. 

Bir şeyi kapsüllenmiş hale getirmek, onu özel hale (private) getirmektir. Bu nedenle yalnızca kendi sınıfının yöntemlerinin içinden erişilebilir hale gelir. Daha az kısıtlayıcı bir mod olan korumalı (protected) olarak adlandırılan bir yapı daha vardır ve bu yapı, bir sınıfın üyelerini alt sınıflar için de erişilebilir yapar. 

Çoğu programlama dilinin arayüzleri ve soyut sınıflar/yöntemleri soyutlama ve kapsülleme kavramlarına dayanmaktadır. Modern nesne yönelimli programlama dillerinde, arayüz mekanizması (genellikle interface veya protocol anahtar kelimesi ile bildirilir), nesneler arasındaki etkileşim anlaşmalarını tanımlamanıza olanak tanır. Bu, arayüzlerin yalnızca nesnelerin davranışlarıyla ilgilendiği ve neden bir arayüzde bir alan bildiremeyeceğiniz nedenlerden biridir.

*"Arayüz" (interface) terimi programlamada gerçekten kafa karıştırıcı olabilir. Bu terim, bir nesnenin programın geri kalanı ile etkileşime giren public bir bölümünü ifade ederken bununla beraber birçok programlama dilinde "arayüz" olarak adlandırılan bir dil özelliği de bulunmaktadır. Bu terimin çift kullanımı, yazılım geliştirme alanındaki yeni başlayanlar için zorlayıcı olabilir.*

Düşünü ki `FlyingTransport` adında bir arayüzünüz var ve içinde `fly(origin, destination, passengers)` adında bir metot bulunuyor. Hava taşımacılığı simülatörü tasarlarken, Havalanı (`Airport`) sınıfını yalnızca `FlyingTransport` arayüzünü uygulayan nesnelerle çalışacak şekilde sınırlayabilirsiniz. Böylece bir havalimanı nesnesine iletilen herhangi bir nesnenin (`Airplane`, `Helicopter` veya `DomesticatedGryphon` gibi) bu tür bir havalimanından kullanılabileceğinden emin olabilirsiniz.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281381339-9b7a1907-c62a-44e1-8875-626ed122789b.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T112137Z&X-Amz-Expires=300&X-Amz-Signature=00e4ca5a6367aa39d4d1f114b96c502f3369faded5e71ad156566cfa6082c170&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*Bir arabirim uygulayan birkaç sınıfın UML diyagramı.*

Bu sınıflardaki `fly` yönteminin uygulanmasını istediğiniz şekilde değiştirebilirsiniz. Yöntemin imzası, arayüzde bildirildiği gibi olduğu sürece, Havalimanı (`Airport`) sınıfının tüm örnekleri uçan nesnelerinizle sorunsuz bir şekilde çalışabilir.

## Miras (Inheritance)

Miras, mevcut sınıfların üzerine yeni sınıflar oluşturma yeteneğidir. Mirasın başlıca faydası kodun yeniden kullanılabilmesidir. Mevcut bir sınıftan biraz farklı bir sınıf oluşturmak isterseniz, kodu çoğaltmanıza gerek yoktur. Bunun yerine mevcut sınıfı genişletirsiniz ve ekstra işlevselliği sonuçta oluşturulan bir alt sınıfa (subclass) koyarsınız, bu alt sınıf, üst sınıfın alanlarını ve yöntemlerini devralır.

Miras kullanmanın sonucu olarak alt sınıflar, üst sınıfın arayüzüne sahip olurlar. Bir yöntem, üst sınıfta bildirildiyse, alt sınıfta gizleyemezsiniz. Ayrıca, alt sınıfınız için anlamsız olsa bile soyut yöntemleri uygulamak zorundasınız.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281382971-b71c0a84-86f7-4fa7-8d1c-4e18966e860f.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T112810Z&X-Amz-Expires=300&X-Amz-Signature=93288468ffd6fd8689bb5a98874f5f6b10562c03d795498bc08d9973bbe58d6b&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*Aynı anda birden çok arabirimi uygulamaya karşı tek bir sınıfı genişletmenin UML diyagramı.*

Çoğu programlama dilinde bir alt sınıf yalnızca bir üst sınıfı genişletebilir. Öte yandan, herhangi bir sınıf aynı anda birden fazla arayüzü uygulayabilir. Ancak, daha önce belirttiğim gibi, bir üst sınıf bir arayüzü uygularsa, tüm alt sınıflar da uygulamak zorundadır.

## Polimorfizm (Polymorphism)

Hayvan örneklerine geri dönelim. Çoğu Hayvan (`Animal`) ses çıkarabilir. Tüm alt sınıfların doğru sesi çıkarabilmesi için temel `makeSound` yöntemini geçersiz kılması gerekeceğini tahmin edebiliriz; bu nedenle onu hemen soyut (abstract) olarak tanımlayabiliriz. Bu, süper sınıfta yöntemin varsayılan uygulamasını atlayabilmemize ve tüm alt sınıfların kendi uygulamalarını oluşturmasını zorunlu kılmamıza olanak tanır.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281384189-909f8e0e-a3aa-4cd0-aeb8-a5aa834e401e.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T113254Z&X-Amz-Expires=300&X-Amz-Signature=ee715bb3ad9968b452bb466fa7e148c4643aed6e33167b0146e5057ad92d483d&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

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

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281385507-a38caaa9-929c-4f6c-be83-5299c0f0a12e.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T113816Z&X-Amz-Expires=300&X-Amz-Signature=6bfc961ec3410eda7b899d5e5625aba6d2f763af134b8de02efcbe9714b94698&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*UML Association: Profesör öğrencilerle iletişim kurar.*

**Bağlantı (Association)**, bir nesnenin başka bir nesneyi kullanmasına veya onunla etkileşimde bulunmasına dayalı bir ilişki türüdür. UML diyagramlarında, bir nesnenin diğerini kullandığını gösteren bir okla gösterilen bir ilişki ile gösterilir. Ayrıca, iki yönlü bir ilişkiye sahip olmak tamamen normaldir. Bu durumda, ok her iki ucunda bir sivriliğe sahiptir. 

Genel olarak, bir bağlantıyı bir sınıfta bir alan gibi temsil etmek için kullanırsınız. Bağlantı her zaman orada bulunur, bir siparişten müşterisini isteyebilirsiniz. Gerçekten bir alan olması gerekmez, daha çok bir arabirim perspektifinden modellemeniz gerekiyorsa, sadece siparişin müşterisini döndürecek bir yöntemin varlığını gösterir.


![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281386317-d44c9b2f-0643-4ebe-828f-2f89a6a6b3bc.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T114144Z&X-Amz-Expires=300&X-Amz-Signature=00aa3d42364973f58d8ab5df096233bda1acc16c5147932371a10907bae469d6&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*UML Dependency. Profesör maaşa bağlıdır.*

**Bağımlılık (Dependency)**, genellikle nesneler arasında kalıcı bir bağlantı olmadığını ima eden bir birliktelik türüdür. Bağımlılık, genellikle (ancak her zaman değil) bir nesnenin bir başka nesneyi bir yöntem parametresi olarak kabul ettiği, başlattığı veya kullandığı anlamına gelir. Sınıflar arasında bir bağımlılığı nasıl belirleyebilirsiniz? Cevap oldukça basit: Bir sınıfın tanımındaki değişikliklerin başka bir sınıfta değişikliklere neden olması durumunda iki sınıf arasında bir bağımlılık vardır.

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281386907-ee1be8b1-e029-44bd-87ee-337aaeed9b40.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T114415Z&X-Amz-Expires=300&X-Amz-Signature=76626f75a3ace4189f72ebf28c3df5372d2580b24fc359cea35f69518c15f27b&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*UML Composition. Üniversite bölümlerden oluşmaktadır.*

**Kompozisyon (Composition)**, iki nesne arasındaki "bütün-bölüm" ilişkisidir, bunlardan biri diğerinin bir veya daha fazla örneğinden oluşur. Bu ilişki ile diğer ilişkiler arasındaki ayrım, bileşenin yalnızca bir konteynerin parçası olarak var olabileceğidir. UML'de kompozisyon ilişkisi, konteynerin sonunda doldurulmuş bir elmas şekli ve bileşenin tarafında bir ok ile gösterilir.

*Nesneler arasındaki ilişkilerden bahsederken, UML'nin sınıflar arasındaki ilişkileri temsil ettiğini unutmayın. Bu, bir üniversite nesnesinin, diyagramdaki her varlık için sadece bir "blok" görseniz bile birden fazla bölümden oluşabileceği anlamına gelir. UML notasyonu ilişkilerin her iki tarafındaki miktarları temsil edebilir, ancak miktarlar bağlamdan açıkça görünüyorsa bunları göz ardı etmekte uygundur.*

![](https://github-production-user-asset-6210df.s3.amazonaws.com/54971670/281389562-d9d77130-dcf1-4594-9e0d-09c92b3ca0b0.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231108T115506Z&X-Amz-Expires=300&X-Amz-Signature=c8c87bf5ae2888d5fffc0b8135ea9ec9b42e25fdef0c0424504a528e5d6ce885&X-Amz-SignedHeaders=host&actor_id=54971670&key_id=0&repo_id=699041305)

*UML Aggregation. Bölüm profesörlerden oluşmaktadır.*

**Birleştirme/Toplama (Aggregation)**, sadece bir nesnenin başka bir nesneye bir başvuru içerdiği daha az katı bir kompozisyon türüdür. Konteyner, bileşenin yaşam döngüsünü kontrol etmez. Bileşen, konteyner olmadan var olabilir. Bununla beraber, aynı anda birkaç konteynerle ilişkilendirilebilir. UML'de, birleştirme ilişkisi, birleşme ile aynı şekilde çizilir, ancak okun tabanında boş bir elmas ile işaretlenir.


















