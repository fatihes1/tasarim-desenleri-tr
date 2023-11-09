# SOLID Prensipleri

Şimdi temel tasarım prensiplerini bildiğinize göre, SOLID prensipleri olarak bilinen beş prensibe bir göz atalım. Robert Martin, bunları "Agile Software Development, Principles, Patterns, and Practices" kitabında tanıttı.

SOLID, yazılım tasarımlarını daha anlaşılır, esnek ve bakımı daha kolay hale getirmeyi amaçlayan beş tasarım prensibini temsil eden bir kısaltmadır.

Her şeyde olduğu gibi, bu prensipleri düşünmeden kullanmak, işe yarar vermekten daha çok zarar verebilir. Bu prensiplerin bir programın mimarisine uygulanmasının maliyeti, programı olması gerekenden daha karmaşık hale getirebilir. Tahmin ediyorum ki, tüm bu prensiplerin aynı anda uygulandığı bir başarılı yazılım ürünü olduğu düşünülemez. Bu prensiplere ulaşmayı hedeflemek iyidir, ancak her zaman pragmatik olmaya çalışın ve burada yazılan her şeyi kutsal kural olarak almayın.


## Single Responsibility Principle

> Türkçe Adıyla: Tek Sorumluluk Prensibi

*Bir sınıfın değişmesi gereken yalnızca bir nedeni olmalıdır.*

Her sınıfı, yazılım tarafından sağlanan işlevselliğin yalnızca bir bölümünden sorumlu olacak şekilde yapmaya çalışın ve bu sorumluluğun tamamen sınıf içinde kapsanmasını sağlayın (sınıf içinde gizlenmiş de denebilir).

Bu prensibin temel amacı karmaşıklığı azaltmaktır. Sadece yaklaşık 200 satır kodu olan bir program için sofistike bir tasarım icat etmeniz gerekmez. Bir düzine yöntemi düzgün hale getirin ve sorun yaşamazsınız.

Gerçek sorunlar, programınızın sürekli büyüdüğü ve değiştiği noktada ortaya çıkar. Bir noktada sınıflar o kadar büyük hale gelir ki ayrıntılarını artık hatırlayamazsınız. Kod gezinmesi yavaşlar ve belirli şeyleri bulmak için tüm sınıfları veya hatta tüm programı taramanız gerekebilir. Programdaki varlıkların sayısı beyninizin kapasitesini aşar ve kod üzerinde kontrolü kaybetmeye başladığınızı hissedersiniz.

Dahası, bir sınıf çok fazla iş yaparsa, her bir iş değiştiğinde sınıfı her seferinde değiştirmeniz gerekir. Bunu yaparken, değiştirmeyi niyetlemedik diğer sınıf parçalarını bozma riski altındasınız.

Eğer programın belirli yönlerine odaklanmakta zorlandığınızı hissediyorsanız, tek sorumluluk ilkesini hatırlayın ve bazı sınıfları parçalara ayırmanın zamanı geldiğini fark edin.

## Open/Closed Principle

> Türkçe Adıyla: Açıklık/Kapalılık Prensibi

*Bu ilkenin ana fikri, yeni özellikler uyguladığınızda mevcut kodun bozulmasını önlemektir.* 

Bir sınıf, onu genişletebilir, bir alt sınıf üretebilir ve istediğiniz her şeyi yapabilirse açıktır. Bazı programlama dilleri özel anahtar kelimelerle (örneğin, `final`) bir sınıfın daha fazla genişlemesini kısıtlamanıza izin verir. Bu noktadan sonra sınıf artık açık değildir. Aynı zamanda sınıf, başka sınıflar tarafından kullanılmak üzere %100 hazır durumdaysa ve arayüzü net bir şekilde tanımlanmışsa, kapalıdır (veya tamdır) ve gelecekte değiştirilmeyecektir.

Bu ilkeyi öğrendiğimde, açık ve kapalı kelimelerinin birbirine zıt gibi göründüğünden ilk başta kafam karıştı. Ancak bu ilkenin terimleri bağlamında bir sınıf aynı zamanda hem açık (genişletilebilir) hem de kapalı (değiştirilemez) olabilir.

Bir sınıf zaten geliştirilmiş, test edilmiş, gözden geçirilmiş ve bir çerçevede kullanılmışsa veya bir uygulamada kullanılmışsa, koduyla oynamak risklidir. Sınıfın kodunu doğrudan değiştirmek yerine bir alt sınıf oluşturabilir ve farklı davranmasını istediğiniz kısmını geçersiz kılabilirsiniz. Amaçladığınız sonuca ulaşacaksınız ancak orijinal sınıfın mevcut istemcilerini bozmamış olacaksınız.

Bu ilke, bir sınıfa yapılan tüm değişiklikler için uygulanacak bir ilke olarak düşünülmemelidir. Sınıfta bir hata olduğunu biliyorsanız, sadece düzeltin; bunun için bir alt sınıf oluşturmayın. Bir alt sınıf, ana sınıfın sorunlarından sorumlu olmamalıdır.

## Liskov Substitution Principle

*Bir sınıfı genişletirken, alt sınıf nesnelerini üst sınıf nesnelerinin yerine kullanabilmelisiniz ve istemci kodunu bozmadan bu nesneleri geçirebilmelisiniz.*

Bu, alt sınıfın, üst sınıfın davranışı ile uyumlu olmaya devam etmesi gerektiği anlamına gelir. Bir yöntemi geçersiz kılarken, temel davranışı başka bir şeyle tamamen değiştirmek yerine genişletmelisiniz.

Değiştirme ilkesi, bir alt sınıfın, üst sınıf nesneleriyle çalışabilen kodla uyumlu kalmaya yardımcı olacak bir dizi kontrolü içerir. Bu kavram, kütüphane ve çerçeveler geliştirirken kritik bir öneme sahiptir çünkü sınıflarınız, kodlarına doğrudan erişemeyeceğiniz ve değiştiremeyeceğiniz başka insanlar tarafından kullanılacaktır.

Diğer tasarım prensipleri geniş yorumlamalara açıkken, değiştirme ilkesi, alt sınıflar ve özellikle yöntemleri için belirli bir dizi resmi gereksinimi içerir. Bu kontrol listesini detaylı bir şekilde ele alalım.


- Bir alt sınıfın bir yönteminde, süper sınıfın yöntemindeki parametre türleriyle eşleşmelidir veya daha soyut olmalıdır. Kulağa karışık mı geliyor? Bir örnek verelim.
	- Bir yöntemle beslemesi gereken kedileri olan bir sınıf olduğunu varsayalım: `feed(Cat c)` . Müşteri kodu her zaman bu yönteme kedi nesneleri geçirir.
	- İyi: Diyelim ki bu yöntemi yeniden tanımlayan bir alt sınıf oluşturdunuz, böylece herhangi bir hayvanı (kedilerin bir üst sınıfı) besleyebilir: `feed(Animal c)` . Şimdi, müşteri koduna üst sınıfın nesnesi yerine bu alt sınıfın bir nesnesini geçirirseniz, her şey yolunda çalışacaktır. Yöntem tüm hayvanları besleyebilir, bu nedenle müşteri tarafından iletilen herhangi bir kedi de beslenebilir.
	- Kötü: Başka bir alt sınıf oluşturdunuz ve besleme yöntemini sadece Bengal kedilerini (kedilerin bir alt sınıfı) kabul edecek şekilde sınırladınız: `feed(BengalCat c)` . Müşteri kodunu, orijinal sınıf yerine bu gibi bir nesneyle ilişkilendirirseniz ne olur? Yöntem yalnızca belirli bir kedi türünü besleyebilir, bu nedenle müşteri tarafından iletilen genel kedilere hizmet etmeyecek ve ilgili tüm işlevselliği bozacaktır.

- Bir alt sınıfın bir yönteminde, süper sınıfın yöntemindeki geri dönüş türüyle eşleşmelidir veya geri dönüş türü, süper sınıfın yöntemdeki geri dönüş türünün bir alt türü olmalıdır.
Parametre türleri için gereksinimlerin tersine olduğunu gördüğünüz gibi, geri dönüş türü için gereksinimler aynıdır.
	- Bir sınıfınızın `buyCat(): Cat` adında bir yöntemi olduğunu varsayalım. Müşteri kodu bu yöntemi çalıştırmanın bir sonucu olarak herhangi bir kedi almayı bekler.
	- İyi: Bir alt sınıf, yöntemi şu şekilde yeniden tanımlar: `buyCat(): BengalCat` . Müşteri bir Bengal kedi alır, bu hala bir kedi olduğundan her şey yolundadır.
	- Kötü: Bir alt sınıf, yöntemi şu şekilde yeniden tanımlar: `buyCat(): Animal` . Şimdi, müşteri kodu bozulur çünkü bilinmeyen genel bir hayvan (bir timsah? bir ayı?) alır ve bir kedi için tasarlanmış bir yapıya uymaz.

Başka bir karşı örnek, dinamik yazılım dillerinin dünyasından gelir: Temel yöntem bir dize döndürür, ancak üzerine yazılan yöntem bir sayı döndürür.

- Alt sınıfın bir yöntemi, temel yöntemin atmadığı istisna türlerini fırlatmamalıdır. Başka bir deyişle, istisna türleri eşleşmeli veya temel yöntemin zaten atabileceği türlerin alt türleri olmalıdır. Bu kural, istemci kodundaki `try-catch` bloklarının temel yöntemin muhtemelen fırlatacağı belirli istisna türlerini hedef aldığı gerçeğinden kaynaklanır. Bu nedenle, beklenmeyen bir istisna, istemci kodunun savunma hatlarından kayarak tüm uygulamayı çökerebilir.

*Çoğu modern programlama dilinde, özellikle statik yazılanlar (Java, C# ve diğerleri), bu kurallar dilin içine yerleştirilir. Bu kurallara aykırı bir programı derleyemezsiniz.*

- Bir alt sınıf ön koşulları güçlendirmemelidir. Örneğin, temel yöntemin parametresi int türünde ise ve bir alt sınıf bu yöntemi geçersiz kılarsa ve yönteme iletilen bir argümanın pozitif olması gerektiğini belirtiyorsa (değer negatifse bir istisna fırlatarak), bu ön koşulları güçlendirir. İstemci kodu, yönteme negatif sayılar ile çalıştığında sorunsuz çalışan kod, bu alt sınıfın bir nesnesiyle çalışmaya başladığında artık bozulur.

- Bir alt sınıf, son koşulları zayıflatmamalıdır. Diyelim ki bir veritabanı ile çalışan bir sınıfınız var. Sınıfın bir yöntemi, bir değer döndükten sonra her zaman açık olan tüm veritabanı bağlantılarını kapatmalıdır.
Bir alt sınıf oluşturdunuz ve bağlantıları yeniden kullanabilmek için veritabanı bağlantılarını açık bırakacak şekilde değiştirdiniz. Ancak istemci, sizin niyetleriniz hakkında hiçbir şey bilmiyor olabilir. Çünkü istemciler, bağlantıların kapatılmasını bekliyor, yöntemi çağırdıktan hemen sonra programı sonlandırarak sistemde hayalet veritabanı bağlantılarıyla kirlenmesine neden olabilir.


- Üst sınıfın sabitleri korunmalıdır. Bu muhtemelen en az resmi kuraldır. Sabitler, bir nesnenin anlam kazandığı koşullardır. Örneğin, bir kedinin sabitleri, dört bacağa, bir kuyruğa, miyavlayabilme yeteneğine sahip olmaktır. Sabitler hakkında kafa karıştırıcı olan şey, bunların, arayüz sözleşmeleri veya yöntemler içinde bir dizi belirleme veya birim testi ve istemci kodunun beklentileri aracılığıyla dolaylı olarak belirlenebilmesidir.
Sabitlerle ilgili kuralı ihlal etmek en kolay olandır çünkü karmaşık bir sınıfın tüm sabitlerini yanlış anlayabilir veya fark edemeyebilirsiniz. Bu nedenle, bir sınıfı genişletmenin en güvenli yolu, yeni alanlar ve yöntemler tanıtmak ve üst sınıfın mevcut üyeleriyle uğraşmamaktır. Tabii ki, bu her zaman gerçek hayatta mümkün olmayabilir.

- Alt sınıf, üst sınıfın özel alanlarının değerlerini değiştirmemelidir. Ne? Bunu nasıl mümkün olabilir? Birçok programlama dili, bazı yansıma mekanizmaları aracılığıyla bir sınıfın özel üyelerine erişmenize izin verir. Diğer diller (Python, JavaScript gibi), özel üyeler için hiçbir koruma sağlamazlar.

## Interface Segregation Principle

> Türkçe Adıyla: Arayüz Ayırma Prensibi

*İstemci, kullanmadıkları yöntemlere bağlı kalmaya zorlanmamalıdır.*

Arayüzlerinizi, müşteri sınıflarının ihtiyaç duymadıkları davranışları uygulamak zorunda kalmayacakları kadar dar yapmaya çalışın.
Arayüz ayrım ilkesine göre, "şişman" arayüzleri daha ayrıntılı ve belirli olanlara bölmelisiniz. Müşteriler sadece gerçekten ihtiyaçları olan yöntemleri uygulamalıdır. Aksi takdirde, "şişman" bir arayüzün değiştirilmesi, değiştirilen yöntemleri kullanmayan müşterileri bile etkileyebilir.

Sınıf kalıtımı bir sınıfın yalnızca bir üst sınıfa sahip olmasına izin verir, ancak sınıfın aynı anda uygulayabileceği arayüzlerin sayısını sınırlamaz. Bu nedenle, birden fazla ilgisiz yöntemi tek bir arayüze sıkıştırmanıza gerek yoktur. Onları daha fazla rafine edilmiş arayüzlere bölebilirsiniz, ihtiyacınız varsa hepsini aynı sınıfta uygulayabilirsiniz. Ancak, bazı sınıflar sadece birini uygulamakla yetinebilir.


## Dependency Inversion Principle

> Türkçe Adıyla: Bağımlılığı Ters Çevirme Prensibi

*Yüksek seviyeli sınıflar, düşük seviyeli sınıflara bağlı olmamalıdır. Her ikisi de soyutlamalara bağlı olmalıdır. Soyutlamalar, detaylara bağlı olmamalıdır. Detaylar, soyutlamalara bağlı olmalıdır.*

Genellikle yazılım tasarlarken, iki seviyeli sınıf türü arasında bir ayrım yapabilirsiniz.

- Düşük seviyeli sınıflar, diskle çalışma, veri aktarma, ağ üzerinden veri aktarma, veritabanına bağlanma gibi temel işlemleri uygular.
- Yüksek seviyeli sınıflar, düşük seviyeli sınıflara bir şey yapmaları için talimat veren karmaşık iş mantığını içerir.

Bazı insanlar düşük seviyeli sınıfları önce tasarlar ve ardından yüksek seviyeli sınıflar üzerinde çalışmaya başlar. Bu, yeni bir sistem üzerinde bir prototip geliştirmeye başladığınızda ve yüksek seviyeli işler daha henüz uygulanmamış veya net olmadığında çok yaygın bir yaklaşımdır. Bu yaklaşımla iş mantığı sınıfları, primitif düşük seviyeli sınıflara bağımlı hale gelir.

Bağımlılık tersine çevirme ilkesi, bu bağımlılığın yönünü değiştirmeyi önerir.

1. İlk olarak, yüksek seviyeli sınıfların bağımlı olduğu düşük seviyeli işlemler için tercihen iş terimleriyle açıklamalar yapmanız gerekecektir. Örneğin, iş mantığı bir yöntemi şu şekilde çağırmalıdır: `openReport(dosya)` , `openFile(x)` , `readBytes(n)` ve `closeFile(x)` gibi bir dizi yöntem yerine. Bu arayüzler yüksek seviyeli olarak kabul edilir.

2. Şimdi yüksek seviyeli sınıfları bu arayüzler yerine somut düşük seviyeli sınıflar yerine bağımlı hale getirebilirsiniz. Bu bağımlılık, orijinaline göre çok daha esnek olacaktır.

3. Düşük seviyeli sınıflar bu arayüzleri uygulamaya başladığında, orijinal bağımlılığın yönünü tersine çevirerek iş mantığı seviyesine bağımlı hale gelirler.

Bağımlılık tersine çevirme ilkesi genellikle açık/kapalı ilkesi ile birlikte kullanılır: düşük seviyeli sınıfları mevcut sınıfları bozmadan farklı iş mantığı sınıfları ile kullanmak için genişletebilirsiniz.

