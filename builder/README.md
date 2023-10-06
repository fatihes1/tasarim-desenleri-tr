#  İnşaatçı/Oluşturucu (Builder) Tasarım Deseni

  

##  💬 Amaç

İnşaatı (Builder), karmaşık nesneleri adım adım oluşturmanızı sağlayan bir yaratıcı (creational) tasarım desenidir. Bu desen, aynı oluşturma kodunu kullanarak bir nesnenin farklı türlerini ve temsillerini üretmenize olanak tanır.

![](https://refactoring.guru/images/patterns/content/builder/builder-en-2x.png)

  

##  🙁 Problem

Birçok alanın ve iç içe geçmiş nesnelerin zahmetli, adım adım başlatılmasını gerektiren karmaşık bir nesne düşünün. Bu tür başlatma kodu genellikle çok sayıda parametreye sahip korkunç bir yapıcı (constructor) içine gömülür. Veya daha da kötüsü: istemci kodunun her tarafına dağılmış durumdadır.

![](https://refactoring.guru/images/patterns/diagrams/builder/problem1-2x.png)

*Bir nesnenin olası her yapılandırması için bir alt sınıf oluşturarak programı fazla karmaşık hale getirebilirsiniz.*

 Örneğin, bir Ev (`Home`) nesnesi oluşturmanın nasıl yapılacağını düşünelim. Basit bir ev inşa etmek için dört duvar ve bir zemin inşa etmeniz, bir kapı takmanız, bir çift pencere takmanız ve bir çatı inşa etmeniz gerekiyor. Ancak daha büyük, daha parlak bir ev isterseniz, arka bahçe gibi daha karmaşık başka özellikler (ısıtma sistemi, su tesisatı ve elektrik tesisatı gibi) isterseniz ne olur?

En basit çözüm, temel `Home` sınıfını genişletmek ve tüm parametre kombinasyonlarını kapsayan bir alt sınıf kümesi oluşturmaktır. Ancak bu işlemin sonunda çok sayıda alt sınıf ile karşı karşıya kalacaksınız. Veranda eklemek için  yeni bir parametre, bu hiyerarşiye daha da genişletmeyi neden olacaktır.

Alt sınıfların oluşturulmasını gerek duymayan başka bir yaklaşım bulunmaktadır. Tüm ev nesnesini kontrol eden tüm olası parametreleri içeren büyük bir yapıcıyı (constructor) temel `Home` sınıfında oluşturabilirsiniz. Bu yaklaşım alt sınıflara ihtiyacı ortadan kaldırırken, başka bir sorun yaratır. Peki bu sorun nedir?

![](https://refactoring.guru/images/patterns/diagrams/builder/problem2-2x.png)

*Çok sayıda parametreye sahip yapıcının dezavantajı vardır: Parametrelerin tümüne her zaman ihtiyaç duyulmaz.*

Çoğu durumda, parametrelerin çoğu kullanılmayacak, bu da yapıcı çağrılarını oldukça karmaşık hale getirecektir. Örneğin, yüzme havuzları sadece evlerin bir kısmında bulunur. Örneğin on evin sadece birinde havuz olması durumunda, yüzme havuzları ile ilgili parametreler bu on evin dokuzunda gereksiz olacaktır.


##  😊 Çözüm

İnşaatçı (Builder) modeli, nesne oluşturma kodunu kendi sınıfından çıkarmanızı ve bunu oluşturucular (`Builders`) adı verilen ayrı nesnelere taşımanızı önerir.

![](https://refactoring.guru/images/patterns/diagrams/builder/solution1-2x.png)

*İnşaatçı (Builder) modeli, karmaşık nesneleri adım adım oluşturmanıza olanak tanır. Builder, ürün oluşturulurken diğer nesnelerin ürüne erişmesine izin vermez.*

Bu desen, nesne oluşturmayı bir dizi adıma (duvarları inşa etme (`buildWalls`), kapıyı inşa etme (`buildDoor`) vb.) ayırır ve organize eder. Bir nesne oluşturmak için bu adımlardan bazılarını bir oluşturucu (builder) nesne üzerinde çalıştırırsınız. Önemli olan şudur ki tüm adımları çağırmak zorunda değilsiniz. Bir nesnenin belirli bir yapılandırmasını üretmek için sadece gereken adımları çağırabilirsiniz.

Nesnenin çeşitli temsillerini oluşturmanız gerektiğinde, inşaat adımlarının bazıları farklı bir uygulama gerektirebilir. Örneğin, bir kulübenin duvarları ahşaptan oluşabilir, ancak kale duvarları taştan inşa edilmelidir.

Bu durumda, aynı inşaat adımlarını uygulayan ancak farklı bir şekilde uygulayan bir dizi farklı oluşturucu (builder) sınıfı oluşturabilirsiniz. Ardından bu oluşturuculara inşaat sürecinde (yani inşaat adımlarına çağrıların sıralı bir kümesi) farklı türde nesneler üretmek için kullanabilirsiniz.

![](https://refactoring.guru/images/patterns/content/builder/builder-comic-1-en-2x.png)

*Farklı oluşturucular (builders) aynı görevi çeşitli şekillerde yürütürler.*

Örneğin, her şeyi ahşap ve camdan inşa eden bir oluşturucu (builder) hayal edin, her şeyi taş ve demirden inşa eden ikincisi ve altın ve elmas kullanan üçüncü bir oluşturucu (builder). Aynı adımları çağırsanız da, ilk oluşturucudan normal bir ev, ikincisinden küçük bir kale ve üçüncüsünden ise bir saray oluşturduğunu görürsünüz. Ancak, bu yalnızca inşaat adımlarını çağıran istemci kodunun ortak bir arayüz (interface) kullanarak yapımcılarla etkileşimde bulunabilmesi durumunda bu şekilde olur.

#### Yönetici (Director)

Daha da ileri gidebilir ve ürünü oluşturmak için kullandığınız yapıcı adımlarına dair bir dizi çağrıyı yönetici (`Director`) olarak adlandırılan ayrı bir sınıfa çıkarabilirsiniz. Yönetici sınıfı, inşaat adımlarını hangi sırayla gerçekleştireceğini tanımlar, yapıcı ise bu adımların uygulanmasını sağlar.

![](https://refactoring.guru/images/patterns/content/builder/builder-comic-2-en-2x.png)

*Yönetici, çalışan bir ürün elde etmek için hangi yapım adımlarının uygulanması gerektiğini bilir*

Programınızda bir yönetici `Director` sınıfına sahip olmak kesinlikle zorunlu değildir. İnşaat adımlarını her zaman doğrudan istemci kodundan belirli bir sırayla çağırabilirsiniz. Ancak, yönetici sınıfı, programınızın genelinde yeniden kullanabileceğiniz çeşitli inşaat rutinlerini yerleştirmek için iyi bir yer olabilir.

Ayrıca, yönetici sınıfı, ürün oluşturmanın ayrıntılarını tamamen istemci kodundan gizler. İstemci, sadece bir oluşturucuyu (builder) bir yöneticiyle (director) ilişkilendirmelidir. Yönetici ile inşaatı başlatmalı ve sonucu oluşturucudan (builder) almalıdır.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/builder/structure-2x.png)
  
1. Oluşturucu arayüzü (Builder interface), tüm inşaatçı türleri için ortak olan ürün oluşturma adımlarını bildirir.
2. Concrete builders, inşaat aşamalarının farklı uygulamalarını sağlar. Concrete builders ortak arayüzü takip etmeyen ürünler üretebilirler.
3. Ürünler (Products) sonuçta ortaya çıkan nesnelerdir. Farklı geliştiriciler tarafından oluşturulan ürünlerin aynı sınıf hiyerarşisine veya arayüze ait olması gerekmez.
4. Director sınıfı, yapım adımlarının çağrılacağı sırayı tanımlar, böylece belirli ürün konfigürasyonlarını oluşturabilir ve yeniden kullanabilirsiniz.
5. İstemci, oluşturucu (builder) nesnelerden birini yöneticiyle (director) ilişkilendirmelidir. Genellikle yöneticinin kurucusunun (constructor) parametreleri aracılığıyla yalnızca bir kez yapılır. Daha sonra yönetici, daha sonraki tüm inşaatlar için bu inşaatçı nesnesini kullanır. Ancak müşterinin inşaatçı nesnesini yönetmenin üretim yöntemine aktarması durumunda alternatif bir yaklaşım var. Bu durumda yöneticiyle her bir ürün ürettiğinizde farklı bir oluşturucu kullanabilirsiniz.

  
##  💻 Sözde Kod (Pseudocode)

Oluşturucu (Builder) modelinin bu örneği, arabalar gibi farklı türdeki ürünleri oluştururken aynı nesne yapım (constructor) kodunu nasıl yeniden kullanabileceğinizi ve bunlara karşılık gelen kılavuzları nasıl oluşturabileceğinizi gösterir.


![](https://refactoring.guru/images/patterns/diagrams/builder/example-en-2x.png)

*Arabaların adım adım yapım örneği ve bu araba modellerine uygun kullanıcı kılavuzları.*


Araç, yüzlerce farklı şekilde inşa edilebilen/oluşturulabilen karmaşık bir nesnedir. Araba (`Car`) sınıfını büyük bir yapılandırıcıyla (constructor) şişirmek yerine, araba montaj kodunu ayrı bir araba oluşturucu (builder) sınıfına çıkardık. Bu sınıf, bir arabanın çeşitli parçalarını yapılandırmak için bir dizi yönteme sahiptir.

İstemci kodu özel, ince ayarlı bir araba modeli monte etmesi gerekiyorsa, doğrudan yapımcı ile çalışabilir. Öte yandan istemci, montajı, en popüler araba modellerinden bazılarını inşa etmek için bir oluşturucu (builder) nasıl kullanılacağını bilen yönetici (director) sınıfına devredebilir.

Şaşırabilirsiniz, ama her arabaya bir kılavuz gerekir (ciddi misiniz, kim okur?). Kılavuz, arabanın her özelliğini açıklar. Bu nedenle kılavuzlardaki ayrıntılar farklı modeller arasında değişiklik gösterecektir. Bu yüzden gerçek arabalar ve ilgili kılavuzlar için mevcut bir oluşma (construction) sürecini yeniden kullanmak mantıklıdır. Elbette, bir kılavuz oluşturmak, bir arabayı oluşturmakla aynı şey değildir, bu nedenle hem araba inşa eden hem de onların kardeşi olan kılavuzları oluşturmak için özdeş oluşturucu (builder) yöntemlerini uygulayan başka bir oluşturucu (builder) sınıfı sağlamamız gerekmektedir. Bu sınıf, kardeş araba parçalarını oluşturmak yerine, onları açıklamakla yükümlüdür. Bu oluşturucuları (builder) aynı yönetici (director) nesnesine ileterek bir araba veya bir kılavuz oluşturabiliriz.

Son parça sonucu almakla ilgilidir. Metal bir araba ve kağıt bir kılavuz, ilişkili olsalar da hala çok farklı şeylerdir. Yöneticiyi ürün sınıflarına bağlamadan, yöneticiye sonuçları getirmek için bir yöntem yerleştiremeyiz. Dolayısıyla inşaatın sonucunu işi yapan oluşturucudan (builder) alıyoruz.

```java
// Builder deseni, ürünleriniz oldukça karmaşıksa ve kapsamlı yapılandırmaya ihtiyaç duyuyorsa mantıklıdır. 
// Aşağıdaki iki ürün birbiriyle ilişkilidir, ancak ortak bir arabirime (interface) sahip değillerdir. 
class Car is 
	// Bir arabanın bir GPS, yol bilgisayarı ve bazı koltuk sayıları olabilir. 
	// Arabaların farklı modelleri (spor araba, SUV, cabriolet) farklı özelliklere sahip olabilir.

class Manual is 
	// Her arabada, arabayla uyumlu bir kullanım kılavuzu olmalıdır ve tüm özelliklerini açıklamalıdır.
	
	// Builder arabirimleri (interface), ürün nesnelerinin farklı parçalarını oluşturmak için yöntemleri belirtir. 
interface Builder is 
	method reset() 
	method setSeats(...) 
	method setEngine(...) 
	method setTripComputer(...) 
	method setGPS(...)

// Builder sınıfları, builder arabirimini takip eder ve 
// yapı adımlarının özgül uygulamalarını sağlar. Programınızın 
// birkaç farklı builder varyasyonu olabilir, her biri farklı 
// şekilde uygulanır. 
class CarBuilder implements Builder is 
	private field car:Car

	// Yeni bir builder örneği, daha sonraki montajda kullanacağı boş bir ürün nesnesi içermelidir.
	constructor CarBuilder() is
	    this.reset()

	// Sıfırlama yöntemi oluşturulan nesneyi temizler.
	method reset() is
	    this.car = new Car()

	// Tüm üretim adımları aynı ürün örneğiyle çalışır.
	method setSeats(...) is
	    // Arabadaki koltuk sayısını ayarla.

	method setEngine(...) is
	    // Verilen motoru takın.

	method setTripComputer(...) is
	    // Yol bilgisayarını kurun.

	method setGPS(...) is
	    // Bir GPS kurun.

	// Builder'lar sonuçları almak için kendi yöntemlerini sağlaması gerekir.
	// Bu, farklı türde builder'ların aynı arabirim takip etmeyen tamamen farklı ürünler oluşturabileceği için
	// builder arabirimine bildirilemeyen bir işlemdir (en azından bir tip güdümlü bir programlama dili değilse).
	//
	// Genellikle sonucu istemciye döndükten sonra bir builder örneğinin yeni bir ürün üretmeye hazır olması beklenir.
	// Bu nedenle, `getProduct` yöntemi gövdesinin sonunda sıfırlama yöntemini çağırmak alışılmış bir uygulamadır.
	// Bununla birlikte, bu davranış zorunlu değildir ve builder'ınızın önceki sonucu atmadan önce müşteri kodundan
	// açık bir sıfırlama çağrısı beklemesini sağlayabilirsiniz.
	method getProduct():Car is
	    product = this.car
	    this.reset()
	    return product
	    
// Diğer yaratıcı (creational) desenlerinin aksine, builder size ortak bir arabirim takip etmeyen ürünler oluşturmanıza izin verir. 
class CarManualBuilder implements Builder is 
	private field manual:Manual

	constructor CarManualBuilder() is
	    this.reset()

	method reset() is
	    this.manual = new Manual()

	method setSeats(...) is
	    // Koltuk özelliklerini belgeleyin.

	method setEngine(...) is
	    // Motor talimatlarını ekleyin.

	method setTripComputer(...) is
	    // Yol bilgisayarı talimatlarını ekleyin.

	method setGPS(...) is
	    // GPS talimatlarını ekleyin.

	method getProduct():Manual is
	    // Kılavuzu döndürün ve builder'ı sıfırlayın.

// Yönetici, yapı adımlarını belirli bir sırayla yürütmekten sorumlu tek şeydir. 
// Belirli bir düzen veya yapılandırmaya göre ürünler üretmek yararlıdır. 
// Sıkı bir tanım yaparsak, yönetici sınıfı isteğe bağlıdır, çünkü 
// istemci builder'ları doğrudan kontrol edebilir. 

class Director is 
	// Yönetici, müşteri kodunun ona ilettiği herhangi bir builder örneğiyle çalışır. 
	// Bu şekilde, müşteri kodu yeni monte edilen ürünün son türünü değiştirebilir. 
	// Yönetici, aynı yapı adımlarını kullanarak birkaç ürün varyasyonu oluşturabilir. 
	method constructSportsCar(builder: Builder) is
	 		builder.reset() 
	 		builder.setSeats(2) 
	 		builder.setEngine(new SportEngine())
	 		builder.setTripComputer(true)
	 		builder.setGPS(true)

	method constructSUV(builder: Builder) is
		// ...
		
	    
// İstemci kodu bir builder nesnesi oluşturur, onu yöneticiye ileterek 
// ardından inşa sürecini başlatır. Sonuç, builder nesnesinden alınır.
class Application is
	method makeCar() is
	    director = new Director()

	    CarBuilder builder = new CarBuilder()
	    director.constructSportsCar(builder)
	    Car car = builder.getProduct()

	    CarManualBuilder builder = new CarManualBuilder()
	    director.constructSportsCar(builder)

	    // Son ürün genellikle bir builder nesnesinden alınır,
	    // çünkü yönetici, builder'ları ve ürünleri tanımaz ve bağımlı değildir.
	    Manual manual = builder.getProduct()

```

  

##  💡Uygulanabilirlik

**🐞 "İç içe geçen kuruculardan (constructor)" (telescoping constructor) kurtulmak için İnşaatçı/Oluşturucu (Builder) modelini kullanın.**

⚡️ On isteğe bağlı parametreye sahip bir kurucunuz (constructor) olduğunu varsayalım. Böyle bir canavarı çağırmak çok sakıncalıdır,  kurucuyu aşırı yükler. Bu nedenle daha az parametreyle birkaç kısa sürüm oluşturursunuz. Bu kurucular, bazı varsayılan değerleri atlanmış parametrelere geçirerek hala ana parametreye atıfta bulunur.

```java
class Pizza {
    Pizza(int size) { ... }
    Pizza(int size, boolean cheese) { ... }
    Pizza(int size, boolean cheese, boolean pepperoni) { ... }
    // ...
```

*Böyle bir canavar oluşturmak yalnızca C# veya Java gibi yöntem aşırı yüklemeyi destekleyen dillerde mümkündür.*

Oluşturucu/İnşaatçı deseni, yalnızca gerçekten ihtiyacınız olan adımları kullanarak nesneleri adım adım oluşturmanıza olanak tanır. Deseni uyguladıktan sonra, artık kurucularınıza düzinelerce parametre tıkmak zorunda kalmazsınız.
  
----------------

**🐞 Kodunuzun bazı ürünlerin (örneğin taş ve ahşap evler) farklı temsillerini oluşturabilmesini istediğinizde Oluşturucu/İnşaatçı modelini kullanın.**

⚡️ Oluşturucu deseni, ürünün çeşitli temsillerinin inşası, yalnızca ayrıntılarda farklılık gösteren benzer adımlar içerdiğinde uygulanabilir.
Temel oluşturucu arayüzü, olası tüm üretim adımlarını tanımlar ve üreticiler, ürünün belirli temsillerini oluşturmak için bu adımları uygular. Bu arada, yönetici sınıfı üretim üretici sınıfları yönlendirir.

-----------------

**🐞 Bileşik ağaçlar veya diğer karmaşık nesneler oluşturmak için Oluşturucuyu/İnşaatçıyı kullanın.**

⚡️ Oluşturucu/İnşaatçı (Builder) deseni, ürünleri adım adım oluşturmanıza olanak tanır. Nihai ürünü bozmadan bazı adımların yürütülmesini erteleyebilirsiniz. Adımları yinelemeli olarak bile çağırabilirsiniz, bu da bir nesne ağacı oluşturmanız gerektiğinde kullanışlı olur.
Bir inşaatçı (builder), üretim adımlarını atarken bitmemiş ürünü açığa çıkarmaz. Böylelikle bu durum, istemci kodunun eksik bir sonuç almasını engeller.


##  📝 Nasıl Uygulanır?

1. Mevcut tüm ürün gösterimlerini oluşturmak için ortak yapım adımlarını açıkça tanımlayabildiğinizden emin olun. Aksi takdirde modeli uygulamaya devam edemezsiniz.

2. Bu adımları temel oluşturucu arayüzünde (base builder interface) bildirin

3. Ürün temsillerinin her biri için bir oluşturucu sınıfı (concrete builder class) oluşturun ve yapım adımlarını uygulayın.
İnşaatın sonucunu almak için bir yöntem uygulamayı (implement etmeyi) unutmayın. Bu yöntemin oluşturucu arayüzünde bildirilememesinin nedeni, çeşitli geliştiricilerin ortak bir arayüze sahip olmayan ürünler oluşturabilmesidir. Dolayısıyla böyle bir yöntemin dönüş türünün ne olacağını bilmiyorsunuz. Ancak tek bir hiyerarşideki ürünlerle çalışıyorsanız getirme yöntemi güvenli bir şekilde temel arayüze eklenebilir.

4. Bir yönetici sınıfı oluşturmayı düşünün. Aynı oluşturucu nesneyi kullanarak bir ürün oluşturmanın çeşitli yollarını kapsayabilir.

5. İstemci kodu hem oluşturucu (builder) hem de yönetici (director) nesnelerini oluşturur. Üretim başlamadan önce müşterinin bir inşaatçı (builder) nesnesini yöneticiye iletmesi gerekir. Genellikle müşteri bunu yöneticinin sınıf kurucusunun parametreleri aracılığıyla yalnızca bir kez yapar. Yönetici, inşaatçının nesnesini sonraki tüm inşaatlarda/üretimlerde kullanır. İnşaatçının, yöneticinin belirli bir ürün inşa yöntemine aktarıldığı alternatif bir yaklaşım da var.

6. İnşaat/Üretim sonucu, yalnızca tüm ürünlerin aynı arayüzü takip etmesi durumunda doğrudan yöneticiden elde edilebilir. Aksi takdirde, müşteri sonucu oluşturucudan/inşaatçıdan (builder) almalıdır.
  

##  ⚖️ Artıları ve Eksileri

✅ Nesneleri adım adım oluşturabilir, yapım adımlarını erteleyebilir veya adımları yinelemeli olarak çalıştırabilirsiniz.

✅ Ürünlerin çeşitli temsillerini oluştururken aynı yapı kodunu yeniden kullanabilirsiniz.

✅ *Tek Sorumluluk İlkesi (Single Responsibility Principle)*. Karmaşık yapı kodunu ürünün iş mantığından ayırabilirsiniz.
  

❌ Desen birden fazla yeni sınıf oluşturmayı gerektirdiğinden kodun genel karmaşıklığı artar
  

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Birçok tasarım, Fabrika Yöntemi (Factory method) (daha az karmaşık ve alt sınıflar yoluyla daha fazla özelleştirilebilir) kullanılarak başlar ve Soyut Fabrika (Abstract Factory), Prototip (Prototype) veya Oluşturucuya (Builder) (daha esnek, ancak daha karmaşık) gibi tasarım desenlerine doğru gelişir.

- Builder, karmaşık nesneleri adım adım oluşturmaya odaklanır. Abstract Factory, ilişkili nesnelerin ailelerini oluşturmayı özelleştirir. Abstract Factory, ürünü hemen döndürürken, Builder size ürünü almadan önce bazı ek inşaat adımlarını çalıştırma imkanı sunar.

- Karmaşık Composite ağaçları oluştururken Builder'ı kullanabilirsiniz çünkü yapısını rekürsif (yineleyici) olarak çalışacak şekilde programlayabilirsiniz.

- Builder'ı Bridge ile birleştirebilirsiniz: direktör sınıfı soyutlamayı oynar, farklı yapı işlemcileri ise uygulamaları temsil eder.

- Abstract Factory, Builder ve Prototype desenleri Singleton deseni olarak uygulanabilir.
  
##  👾 Kod Örnekleri

  

###  Java

**Kullanım örneği:** Builder modeli Java dünyasında iyi bilinen bir kalıptır. Pek çok olası yapılandırma seçeneğine sahip bir nesne oluşturmanız gerektiğinde özellikle kullanışlıdır.

  

Desen çekirdek Java kitaplıklarında mevcuttur:

-   [`java.lang.StringBuilder#append()`](http://docs.oracle.com/javase/8/docs/api/java/lang/StringBuilder.html#append-boolean-)  (`unsynchronized - senkronize edilmemiş`)
-   [`java.lang.StringBuffer#append()`](http://docs.oracle.com/javase/8/docs/api/java/lang/StringBuffer.html#append-boolean-)  (`synchronized - senkronize`)
-   [`java.nio.ByteBuffer#put()`](http://docs.oracle.com/javase/8/docs/api/java/nio/ByteBuffer.html#put-byte-)  (Ayrıca [`CharBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/CharBuffer.html#put-char-),  [`ShortBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/ShortBuffer.html#put-short-),  [`IntBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/IntBuffer.html#put-int-),  [`LongBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/LongBuffer.html#put-long-),  [`FloatBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/FloatBuffer.html#put-float-)  ve  [`DoubleBuffer`](http://docs.oracle.com/javase/8/docs/api/java/nio/DoubleBuffer.html#put-double-))
-   [`javax.swing.GroupLayout.Group#addComponent()`](http://docs.oracle.com/javase/8/docs/api/javax/swing/GroupLayout.Group.html#addComponent-java.awt.Component-)
-   Tüm implementation'ları  [`java.lang.Appendable`](http://docs.oracle.com/javase/8/docs/api/java/lang/Appendable.html)


**Tanımlama:** Oluşturucu modeli, tek bir oluşturma yöntemine ve elde edilen nesneyi yapılandırmak için çeşitli yöntemlere sahip olan bir sınıfta tanınabilir. Oluşturucu yöntemleri genellikle zincirlemeyi destekler. (Örneğin, `someBuilder.setValueA(1).setValueB(2).create()`)

 
####  Adım Adım Araba Üretimi

Bu örnek, Builder deseninin farklı otomobil modellerinin aşamalı olarak oluşturulmasına olanak tanıdığını gösteriyor.

Örnek ayrıca, Builder'ın aynı inşa adımlarını kullanarak farklı türde ürünler (otomobil kılavuzu) ürettiğini göstermektedir.

Yönetici, inşa sırasını kontrol eder. Hangi inşa adımlarını çağırarak bu veya şu otomobil modelini üreteceğini bilir. Yönetici, yalnızca ortak arayüzleri üzerinden inşaatçılarla çalışır. Bu, yöneticiye farklı türde inşaatçıları (builder) iletebilme olanağı sağlar.

Sonuç olarak, yönetici nesnesinden alınır çünkü yönetici sonucun türünü bilemez. Yalnızca Builder nesnesi neyi tam olarak inşa ettiğini bilir.

**📁 builders**

⤵️ 📄 `builders/Builder.java`: Ortak builder arabirimi/aarayüzü 

```java

package fatihes1.builder.example.builders;

import fatihes1.builder.example.cars.CarType;
import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Builder interface defines all possible ways to configure a product.
 */
public interface Builder {
    void setCarType(CarType type);
    void setSeats(int seats);
    void setEngine(Engine engine);
    void setTransmission(Transmission transmission);
    void setTripComputer(TripComputer tripComputer);
    void setGPSNavigator(GPSNavigator gpsNavigator);
}
```

⤵️ 📄 `builders/CarBuilder.java:`: Araba (Car) builder'ı

```java
package fatihes1.builder.example.builders;

import fatihes1.builder.example.cars.Car;
import fatihes1.builder.example.cars.CarType;
import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Concrete builders implement steps defined in the common interface.
 */
public class CarBuilder implements Builder {
    private CarType type;
    private int seats;
    private Engine engine;
    private Transmission transmission;
    private TripComputer tripComputer;
    private GPSNavigator gpsNavigator;

    public void setCarType(CarType type) {
        this.type = type;
    }

    @Override
    public void setSeats(int seats) {
        this.seats = seats;
    }

    @Override
    public void setEngine(Engine engine) {
        this.engine = engine;
    }

    @Override
    public void setTransmission(Transmission transmission) {
        this.transmission = transmission;
    }

    @Override
    public void setTripComputer(TripComputer tripComputer) {
        this.tripComputer = tripComputer;
    }

    @Override
    public void setGPSNavigator(GPSNavigator gpsNavigator) {
        this.gpsNavigator = gpsNavigator;
    }

    public Car getResult() {
        return new Car(type, seats, engine, transmission, tripComputer, gpsNavigator);
    }
}
```

⤵️ 📄 `builders/CarManualBuilder.java**` Manuel Araba (Car) Builder'ı

```java
package fatihes1.builder.example.builders;

import fatihes1.builder.example.cars.Manual;
import fatihes1.builder.example.cars.CarType;
import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Unlike other creational patterns, Builder can construct unrelated products,
 * which don't have the common interface.
 *
 * In this case we build a user manual for a car, using the same steps as we
 * built a car. This allows to produce manuals for specific car models,
 * configured with different features.
 */
public class CarManualBuilder implements Builder{
    private CarType type;
    private int seats;
    private Engine engine;
    private Transmission transmission;
    private TripComputer tripComputer;
    private GPSNavigator gpsNavigator;

    @Override
    public void setCarType(CarType type) {
        this.type = type;
    }

    @Override
    public void setSeats(int seats) {
        this.seats = seats;
    }

    @Override
    public void setEngine(Engine engine) {
        this.engine = engine;
    }

    @Override
    public void setTransmission(Transmission transmission) {
        this.transmission = transmission;
    }

    @Override
    public void setTripComputer(TripComputer tripComputer) {
        this.tripComputer = tripComputer;
    }

    @Override
    public void setGPSNavigator(GPSNavigator gpsNavigator) {
        this.gpsNavigator = gpsNavigator;
    }

    public Manual getResult() {
        return new Manual(type, seats, engine, transmission, tripComputer, gpsNavigator);
    }
}
```

**📁 cars** 

⤵️ 📄 `cars/Car.java`: Araba (Car) Ürünü

```java
package fatihes1.builder.example.cars;

import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Car is a product class.
 */
public class Car {
    private final CarType carType;
    private final int seats;
    private final Engine engine;
    private final Transmission transmission;
    private final TripComputer tripComputer;
    private final GPSNavigator gpsNavigator;
    private double fuel = 0;

    public Car(CarType carType, int seats, Engine engine, Transmission transmission,
               TripComputer tripComputer, GPSNavigator gpsNavigator) {
        this.carType = carType;
        this.seats = seats;
        this.engine = engine;
        this.transmission = transmission;
        this.tripComputer = tripComputer;
        if (this.tripComputer != null) {
            this.tripComputer.setCar(this);
        }
        this.gpsNavigator = gpsNavigator;
    }

    public CarType getCarType() {
        return carType;
    }

    public double getFuel() {
        return fuel;
    }

    public void setFuel(double fuel) {
        this.fuel = fuel;
    }

    public int getSeats() {
        return seats;
    }

    public Engine getEngine() {
        return engine;
    }

    public Transmission getTransmission() {
        return transmission;
    }

    public TripComputer getTripComputer() {
        return tripComputer;
    }

    public GPSNavigator getGpsNavigator() {
        return gpsNavigator;
    }
}
```

⤵️ 📄 `cars/Manual.java`: Manuel Ürünü

```java
package fatihes1.builder.example.cars;

import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Car manual is another product. Note that it does not have the same ancestor
 * as a Car. They are not related.
 */
public class Manual {
    private final CarType carType;
    private final int seats;
    private final Engine engine;
    private final Transmission transmission;
    private final TripComputer tripComputer;
    private final GPSNavigator gpsNavigator;

    public Manual(CarType carType, int seats, Engine engine, Transmission transmission,
                  TripComputer tripComputer, GPSNavigator gpsNavigator) {
        this.carType = carType;
        this.seats = seats;
        this.engine = engine;
        this.transmission = transmission;
        this.tripComputer = tripComputer;
        this.gpsNavigator = gpsNavigator;
    }

    public String print() {
        String info = "";
        info += "Type of car: " + carType + "\n";
        info += "Count of seats: " + seats + "\n";
        info += "Engine: volume - " + engine.getVolume() + "; mileage - " + engine.getMileage() + "\n";
        info += "Transmission: " + transmission + "\n";
        if (this.tripComputer != null) {
            info += "Trip Computer: Functional" + "\n";
        } else {
            info += "Trip Computer: N/A" + "\n";
        }
        if (this.gpsNavigator != null) {
            info += "GPS Navigator: Functional" + "\n";
        } else {
            info += "GPS Navigator: N/A" + "\n";
        }
        return info;
    }
}
```

⤵️ 📄 `cars/CarType.java` 

```java
package fatihes1.builder.example.cars;

public enum CarType {
    CITY_CAR, SPORTS_CAR, SUV
}
```

**📁 components**

⤵️ 📄 `components/Engine.java` : Ürün Özelliği 1

```java
package fatihes1.builder.example.components;

/**
 * Just another feature of a car.
 */
public class Engine {
    private final double volume;
    private double mileage;
    private boolean started;

    public Engine(double volume, double mileage) {
        this.volume = volume;
        this.mileage = mileage;
    }

    public void on() {
        started = true;
    }

    public void off() {
        started = false;
    }

    public boolean isStarted() {
        return started;
    }

    public void go(double mileage) {
        if (started) {
            this.mileage += mileage;
        } else {
            System.err.println("Cannot go(), you must start engine first!");
        }
    }

    public double getVolume() {
        return volume;
    }

    public double getMileage() {
        return mileage;
    }
}
```

⤵️ 📄 `components/GPSNavigator.java` : Ürün Özelliği 2

```java
package fatihes1.builder.example.components;

/**
 * Just another feature of a car.
 */
public class GPSNavigator {
    private String route;

    public GPSNavigator() {
        this.route = "221b, Baker Street, London  to Scotland Yard, 8-10 Broadway, London";
    }

    public GPSNavigator(String manualRoute) {
        this.route = manualRoute;
    }

    public String getRoute() {
        return route;
    }
}
```

⤵️ 📄 `components/Transmission.java` : Ürün Özelliği 3

```java
package fatihes1.builder.example.components;

/**
 * Just another feature of a car.
 */
public enum Transmission {
    SINGLE_SPEED, MANUAL, AUTOMATIC, SEMI_AUTOMATIC
}
```

⤵️ 📄 `components/TripComputer.java` : Ürün Özelliği 4

```java
package fatihes1.builder.example.components;

import fatihes1.builder.example.cars.Car;

/**
 * Just another feature of a car.
 */
public class TripComputer {

    private Car car;

    public void setCar(Car car) {
        this.car = car;
    }

    public void showFuelLevel() {
        System.out.println("Fuel level: " + car.getFuel());
    }

    public void showStatus() {
        if (this.car.getEngine().isStarted()) {
            System.out.println("Car is started");
        } else {
            System.out.println("Car isn't started");
        }
    }
}

```


**📁 director**

⤵️ 📄 `director/Director.java` : Yönetici (Directors), İnşaatçıları (Builder) yönetir

```java
package fatihes1.builder.example.director;

import fatihes1.builder.example.builders.Builder;
import fatihes1.builder.example.cars.CarType;
import fatihes1.builder.example.components.Engine;
import fatihes1.builder.example.components.GPSNavigator;
import fatihes1.builder.example.components.Transmission;
import fatihes1.builder.example.components.TripComputer;

/**
 * Director defines the order of building steps. It works with a builder object
 * through common Builder interface. Therefore it may not know what product is
 * being built.
 */
public class Director {

    public void constructSportsCar(Builder builder) {
        builder.setCarType(CarType.SPORTS_CAR);
        builder.setSeats(2);
        builder.setEngine(new Engine(3.0, 0));
        builder.setTransmission(Transmission.SEMI_AUTOMATIC);
        builder.setTripComputer(new TripComputer());
        builder.setGPSNavigator(new GPSNavigator());
    }

    public void constructCityCar(Builder builder) {
        builder.setCarType(CarType.CITY_CAR);
        builder.setSeats(2);
        builder.setEngine(new Engine(1.2, 0));
        builder.setTransmission(Transmission.AUTOMATIC);
        builder.setTripComputer(new TripComputer());
        builder.setGPSNavigator(new GPSNavigator());
    }

    public void constructSUV(Builder builder) {
        builder.setCarType(CarType.SUV);
        builder.setSeats(4);
        builder.setEngine(new Engine(2.5, 0));
        builder.setTransmission(Transmission.MANUAL);
        builder.setGPSNavigator(new GPSNavigator());
    }
}
```

⤵️ 📄 `Demo.java` : İstemci Kodu

```java
package fatihes1.builder.example;

import fatihes1.builder.example.builders.CarBuilder;
import fatihes1.builder.example.builders.CarManualBuilder;
import fatihes1.builder.example.cars.Car;
import fatihes1.builder.example.cars.Manual;
import fatihes1.builder.example.director.Director;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {

    public static void main(String[] args) {
        Director director = new Director();

        // Director gets the concrete builder object from the client
        // (application code). That's because application knows better which
        // builder to use to get a specific product.
        CarBuilder builder = new CarBuilder();
        director.constructSportsCar(builder);

        // The final product is often retrieved from a builder object, since
        // Director is not aware and not dependent on concrete builders and
        // products.
        Car car = builder.getResult();
        System.out.println("Car built:\n" + car.getCarType());


        CarManualBuilder manualBuilder = new CarManualBuilder();

        // Director may know several building recipes.
        director.constructSportsCar(manualBuilder);
        Manual carManual = manualBuilder.getResult();
        System.out.println("\nCar manual built:\n" + carManual.print());
    }

}
```

**🖨️ Çıktılar**


⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu (HtmlDialog)

```
Car built:
SPORTS_CAR

Car manual built:
Type of car: SPORTS_CAR
Count of seats: 2
Engine: volume - 3.0; mileage - 0.0
Transmission: SEMI_AUTOMATIC
Trip Computer: Functional
GPS Navigator: Functional
```

