# İyi Tasarımın Özellikleri

Gerçek desenlere devam etmeden önce, yazılım mimarisi tasarlama sürecini, hedeflenmesi gereken şeyleri ve kaçınılması gereken şeylere odaklanalım.

## ♻️ Kodun Yeniden Kullanımı (Code Reuse)

Maliyet ve zaman, herhangi bir yazılım ürünü geliştirirken en değerli ölçütlerden ikisidir. Geliştirmenin daha az zaman alması, rakiplerden önce piyasaya girmeniz anlamına gelir. Daha düşük geliştirme maliyetleri, pazarlama için daha fazla para anlamına gelir ve potansiyel müşterilere daha geniş bir ulaşım sağlar.

Kodun yeniden kullanılması, geliştirme maliyetlerini azaltmanın en yaygın yollarından biridir. Hedef oldukça nettir: baştan başlayarak sürekli bir şey geliştirmek yerine, neden yeni projelerde var olan kodu yeniden kullanmayalım?

Fikir kağıt üzerinde harika görünüyor, ancak mevcut kodu yeni bir bağlamda çalıştırmak genellikle ekstra çaba gerektirir. Bileşenler arasındaki sıkı bağlar, arayüzler yerine somut sınıflara bağımlılıklar, sabitlenmiş işlemler - tüm bunlar kodun esnekliğini azaltır ve onu yeniden kullanmayı zorlaştırır.

Tasarım desenlerinin kullanılması, yazılım bileşenlerinin esnekliğini artırmak ve onları daha kolay yeniden kullanılabilir hale getirmek için bir yol sağlar.

Ancak bu bazen bileşenleri daha karmaşık hale getirmesi bir dezavantaj olarak gelir. İşte tasarım desenlerinin kod yeniden kullanımındaki rolü hakkında tasarım desenlerinin kurucu babalarından biri olan Erich Gamma'dan bir alıntı:



*Üç farklı yeniden kullanım seviyesi görüyorum.*

*En düşük seviyede, sınıfları yeniden kullanırsınız: sınıf kütüphaneleri, konteynerler, belki de bazı sınıf takımları gibi (örneğin, konteyner/iterator).*

*En yüksek seviyede ise çerçeveler (frameworks) bulunur. Gerçekten tasarım kararlarınızı yoğunlaştırmaya çalışırlar. Bir sorunu çözmek için temel soyutlamaları tanımlarlar, bunları sınıflarla temsil ederler ve aralarındaki ilişkileri tanımlarlar. Örneğin, JUnit küçük bir çerçeve (framework) örneğidir. Bir çerçeve (framework) "Merhaba, dünya" seviyesindedir. `Test`, `TestCase`, `TestSuite` ve bu sınıflar arasındaki ilişkileri tanımlar.* 

*Bir çerçeve (framework) genellikle tek bir sınıftan daha büyük (grained) olur. Ayrıca, bir çerçeveye (framework) bir yerlerde miras alarak bağlanırsınız. Onlar, "bizi arama, biz seni ararız" prensibini kullanırlar. Çerçeve (framework), özel davranışınızı tanımlamanıza izin verir ve sıra size geldiğinde sizi arar. Bir yerlerden tanıdık değil mi, JUnit de öyle değil miydi? Size bir testi yürütmek istediğinde sizi arar, ancak geri kalan işlem çerçevede (framework) gerçekleşir.*

*Ayrıca, orta bir seviye daha vardır. İşte burada desenleri görüyorum. Tasarım desenleri, çerçevelerden daha küçük ve daha soyut olabilir. Gerçekten birkaç sınıfın birbirleriyle nasıl ilişkilenebileceği ve etkileşime girebileceği hakkında bir açıklamadır. Sınıflardan desenlere ve nihayet çerçevelere (frameworks) doğru ilerledikçe yeniden kullanım seviyesi artar. Bu orta katmanın güzel olmasının nedeni, desenlerin risk taşımayan bir şekilde yeniden kullanım sunmasıdır. Bir çerçeve (framework) oluşturmak yüksek risk taşır ve önemli bir yatırımdır. Desenler, somut kodlardan bağımsız olarak tasarım fikirlerini ve kavramları yeniden kullanmanıza olanak tanır.*

## 📚 Genişletilebilirlik (Extensibility)

Değişim, bir yazılım geliştiricisinin yaşamındaki tek sabit şeydir. 
- Windows için bir video oyunu yayınladınız, ancak şimdi insanlar macOS sürümünü istiyor. 
- Kare düğmelere sahip bir GUI çerçevesi oluşturdunuz, ancak birkaç ay sonra yuvarlak düğmeler trend haline geldi. 
- Parlak bir e-ticaret web sitesi mimarisi tasarladınız, ancak sadece bir ay sonra müşteriler, telefon siparişlerini kabul etmelerine olanak tanıyan bir özellik istiyorlar. 

Her yazılım geliştiricisinin onlarca benzer hikayesi vardır. Bunun neden olduğu birkaç neden vardır.

İlk olarak, bir sorunu çözmeye başladığınızda daha iyi anlarız. Genellikle bir uygulamanın ilk sürümünü bitirdiğinizde, birçok sorunun bazı yönlerini çok daha iyi anladığınız için sıfırdan yeniden yazmaya hazırsınız. Ayrıca profesyonelliğiniz arttıkça kendi kodlarınız kötü görünmeye başlar. 

Kontrolünüz dışındaki bir şeylerin değiştiğini düşünelim. Bu nedenle birçok geliştirici ekibi, orijinal fikirlerinden başka bir şeye dönüş yapar. Çevrimiçi bir uygulamada Flash'a güvenen herkes, tarayıcıların Flash desteğini bırakmasıyla kodlarını yeniden oluşturur veya taşıyor. 

Üçüncü neden ise sürüm değişimlerinden oluşabilir. Müşteriniz, mevcut uygulamanın sürümünden memnundu, ancak şimdi on bir "küçük" değişiklik görmek istiyor, böylece orijinal planlama oturumlarında bahsetmediği başka şeyler yapabilir. Bunlar lüzumsuz değişiklikler değildir: mükemmel ilk sürümünüz, ona daha fazlasının mümkün olduğunu göstermiştir.

*İyi tarafından bakmak gerekirse: Eğer biri uygulamanızda bir şeyi değiştirmenizi istiyorsa, bu hala uygulamanızın önemsendiği anlamına gelir.*

Bu nedenle tüm deneyimli geliştiriciler, bir uygulamanın mimarisini tasarlarken gelecekteki olası değişiklikleri ön görmeye çalışır ve mimariyi buna göre oluşturur.

# Değişenleri Kapsülleyin

Uygulamanızın değişen yönlerini belirleyin ve bu kodları aynı kalacak olan kısımlardan ayırın.

Bu ilkenin temel amacı, değişikliklerin neden olduğu etkiyi en aza indirmektir. Programınızın bir gemi olduğunu hayal edin ve değişikliklerin su altında bekleyen korkunç mayınlar olduğunu düşünün. Bir mayına çarpıldığında gemi batıyor. 

Bu bilgiye sahip olarak, geminin gövdesini değişik kompartmanlara bölebilirsiniz; böylece zararın sadece bir kompartmanla sınırlı kalması için güvenli bir şekilde kapatılabilirler. Şimdi, eğer gemi bir mayına çarparsa, gemi bütün olarak su üstünde kalır.

Aynı şekilde, programın bağımsız modüllerde değişen kısımlarını izole edebilirsiniz, böylece diğer kodları olumsuz etkilerden koruyabilirsiniz. Sonuç olarak, değişiklikleri uygulamak ve test etmek için harcanan süreyi azaltarak, programı çalışır duruma getirme, değişiklikleri uygulama ve test etme süresini azaltmış olursunuz. Değişiklikler yapmak için harcadığınız süre ne kadar az olursa, özellikleri uygulamak ve geliştirmek için o kadar fazla zamanınız olur.

### Yöntem düzeyinde kapsülleme

Örnek bir e-ticaret web sitesi yapıyorsunuz. Kodunuzun bir yerinde, vergiler dahil olmak üzere sipariş için toplam bir miktarı hesaplayan bir `getOrderTotal` adında bir yöntem bulunuyor. 

Gelecekte vergilerle ilgili kodun değişmesi gerekebileceğini öngörebiliriz. Vergi oranı, müşterinin ikamet ettiği ülke, eyalet veya hatta şehre bağlıdır. Bununla beraber gerçek formül, yeni yasalar veya düzenlemeler nedeniyle zaman içinde değişebilir. Bu nedenle `getOrderTotal` yöntemini oldukça sık değiştirmeniz gerekebilir. Ancak yöntemin adı bile, vergilerin nasıl hesaplandığıyla ilgilenmediğini göstermektedir.

```java
method getOrderTotal(order) is 
	total = 0
	foreach item in order.lineItems 
		total += item.price * item.quantity 
	if (order.country == "US") 
		total += total * 0.07 // US sales tax 
	else if (order.country == "EU"): 
		total += total * 0.20 // European VAT 10 11 return total
```

Vergi ile ilgili değişiklikler, artık tek bir yöntem içinde izole edilir. Dahası, vergi hesaplama mantığı çok karmaşıksa, onu ayrı bir sınıfa taşımak daha kolay hale gelir. Örneğin:

```java
method getOrderTotal(order) is
	total = 0
	foreach item in order.lineItems
		total += item.price * item.quantity
	
	total += total * getTaxRate(order.country)
	return total

method getTaxRate(country) is 
	if (country == "US") 
		return 0.07 // US sales tax 
	else if (country == "EU") 
		return 0.20 // European VAT 15 
	else 
		return 0
	
```


### Sınıf düzeyinde kapsülleme

Zamanla, önceden basit bir işlemi yapan bir yönteme daha fazla sorumluluk ekleyebilirsiniz. Bu eklenen davranışlar genellikle kendi yardımcı alanlarını ve yöntemlerini getirir. Nihayetinde içeren sınıfın asıl sorumluluğunu da bulandırabilir. Her şeyi yeni bir sınıfa çıkarmak, işleri çok daha açık ve basit hale getirebilir.


# Programı Uygulamaya Değil, Arayüze Dönüştürün (Program to an Interface, not an Implementation)

*Program bir arayüze yöneliktir, bir uygulamaya değil. Somut sınıflara değil, soyutlamalara bağlı olun.*

Mevcut kodu bozmadan kolayca genişletebiliyorsanız, tasarımın yeterince esnek olduğunu söyleyebilirsiniz. Bu ifadenin doğru olduğundan emin olmak için başka bir kedi örneğine bakalım. Herhangi bir yiyeceği yiyebilen bir kedi, sadece sosisleri yiyebilen bir kediden daha esnektir. İlk kediye hala sosis verebilirsiniz çünkü sosisler "herhangi bir yiyeceğin" bir alt kümesidir; ancak bu kedinin menüsünü herhangi başka bir yiyecekle genişletemezsiniz.

İki sınıf arasında işbirliği yapmak istediğinizde, bunlardan birini diğerine bağımlı hale getirerek başlayabilirsiniz. Ama daha esnek bir nesneler arası işbirliği kurmanın başka bir yolu daha vardır:

1. Bir nesnenin diğer nesneden tam olarak neye ihtiyaç duyduğunu belirleyin: Hangi yöntemleri yürütüyor?
2. Bu yöntemleri yeni bir arayüzde veya soyut sınıfta tanımlayın.
3. Bağımlılık olan sınıfın bu arayüzü uygulamasını sağlayın.
4. Şimdi ikinci sınıfı bu arayüze bağımlı hale getirin, ancak somut sınıfa bağlı çalışmasını hala sağlayabilirsiniz, ancak bağlantı şimdi çok daha esnek hale gelir.


Bu değişiklikten sonra muhtemelen hemen herhangi bir faydasını hissetmeyeceksiniz. Aksine, kod, öncekinden daha karmaşık hale gelmiştir. Bununla birlikte, bu değişikliğin ekstra işlevsellik için iyi bir uzantı noktası olabileceğini veya kodunuzu kullanan başkalarının burayı genişletmek isteyebileceğini düşünüyorsanız, o zaman bu yolu izleyin.

# Miras yerine kompozisyonu tercih edin (Favor Composition Over Inheritance)

Inheritance, sınıflar arasında kodun en açık ve kolay şekilde yeniden kullanılmasının yolu olabilir. Aynı kodu içeren iki sınıfınız varsa, bu ikisi için ortak bir temel sınıf oluşturun ve benzer kodu taşıyın. Çok kolay!
Ne yazık ki, mirasın yalnızca programınızın zaten tonlarca sınıfa sahip olduğu ve herhangi bir değişikliğin oldukça zor olduğu durumların ardından belirgin hale gelen bazı sakıncaları vardır. İşte bu sorunların listesi.
- Bir alt sınıf, üst sınıfın arabirimini azaltamaz. Üst sınıfın soyut yöntemlerinin tümünü uygulamanız gerekecektir, onları kullanmasanız bile.
- Yöntemleri geçersiz kılarken, yeni davranışın temel davranışla uyumlu olduğundan emin olmanız gerekir. Bu önemlidir çünkü alt sınıf nesneleri, üst sınıf nesnelerini bekleyen herhangi bir kod tarafından kullanılabilir ve bu kodun bozulmasını istemezsiniz.
- Miras, üst sınıfın kapsüllemesini bozar çünkü üst sınıfın iç ayrıntıları alt sınıfa açık hale gelir. Tam tersi bir durum olabilir, bir programcı, daha fazla genişletmeyi kolaylaştırmak amacıyla üst sınıfın alt sınıfların bazı ayrıntılarından haberdar olmasını sağlar.
- Alt sınıflar, üst sınıflara sıkı bir şekilde bağlıdır. Üst sınıfta yapılan herhangi bir değişiklik, alt sınıfların işlevselliğini bozabilir.
- Miras yoluyla kodu yeniden kullanma girişimi, paralel miras hiyerarşileri oluşturmaya yol açabilir. Miras genellikle tek boyutta gerçekleşir. Ancak iki veya daha fazla boyut olduğunda birçok sınıf kombinasyonu oluşturmanız gerekebilir ve bu da sınıf hiyerarşisini absürt bir boyuta taşıyabilir.

Mirasın yerine "kompozisyon" adı verilen bir alternatif bulunmaktadır. Miras, sınıflar arasındaki "bir" ilişkisini temsil eder (bir araba bir taşımadır), kompozisyon ise "sahip olduğu" ilişkiyi temsil eder (bir araba bir motora sahiptir).
Bahsetmeliyim ki, bu ilkenin ayrıca "agregasyon" olarak adlandırılan bir türü de vardır; bu daha rahat bir kompozisyon türüdür, burada bir nesne diğerine bir referansa sahip olabilir ancak yaşam döngüsünü yönetmez. İşte bir örnek: bir arabanın bir sürücüsü vardır, ancak sürücü başka bir araba kullanabilir veya arabasız yürüyebilir.






