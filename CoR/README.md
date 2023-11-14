#  Sorumluluk Zinciri (Chain of Responsibility) Tasarım Deseni

> Diğer Adıyla: CoR, Komut Zinciri (Chain of Command)

##  💬 Amaç

Sorumluluk Zinciri, istekleri bir işleyici zinciri boyunca aktarmanıza olanak tanıyan davranışsal (behavioral) bir tasarım modelidir. Bir istek aldıktan sonra her işleyici, isteği işlemeye veya zincirdeki bir sonraki işleyiciye iletmeye karar verir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/chain-of-responsibility/chain-of-responsibility-2x.png)

</div>


##  🙁 Problem

Çevrimiçi bir sipariş sistemi üzerinde çalıştığınızı hayal edin. Yalnızca kimliği doğrulanmış kullanıcıların sipariş oluşturabilmesi için sisteme erişimi kısıtlamak istiyorsunuz. Ayrıca yönetici izinlerine sahip kullanıcıların tüm siparişlere tam erişime sahip olması gerekir.

Biraz planlama yaptıktan sonra bu kontrollerin sıralı olarak yapılması gerektiğini fark ettiniz. Uygulama, kullanıcının kimlik bilgilerini içeren bir istek aldığında, sistemde kullanıcının kimliğini doğrulamayı deneyebilir. Ancak bu kimlik bilgileri doğru değilse ve kimlik doğrulama başarısız olursa kalan kontrollere devam etmenin bir anlamı yoktur.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/problem1-en-2x.png)

*Sipariş sisteminin bunu gerçekleştirebilmesi için isteğin bir dizi kontrolden geçmesi gerekir.*

</div>

Önümüzdeki birkaç ay boyunca bu sıralı kontrollerden birkaçını daha uygulamaya devam ettiğinizi düşünelim.

- Meslektaşlarınızdan biri, ham verileri doğrudan sipariş sistemine aktarmanın güvenli olmadığını öne sürdü. Yani bir istekteki verileri temizlemek için fazladan bir doğrulama adımı eklediniz.
- Daha sonra birisi sistemin kaba kuvvetle şifre kırmaya karşı savunmasız olduğunu fark etti. Bunu geçersiz kılmak için hemen aynı IP adresinden gelen tekrarlanan başarısız istekleri filtreleyen bir kontrol eklediniz.
- Başka biri, aynı verileri içeren tekrarlanan isteklerde önbelleğe alınmış sonuçları döndürerek sistemi hızlandırabileceğinizi önerdi. Bu nedenle, isteğin yalnızca önbelleğe alınmış uygun bir yanıt olmadığında sisteme geçmesine izin veren başka bir kontrol eklediniz.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/problem2-en-2x.png)

*Kod büyüdükçe daha karmaşık hale geldi.*

</div>

Zaten karmakarışık görünen çeklerin kodu, her yeni özellik eklendikçe daha da şişirildi. Bir kontrolün değiştirilmesi bazen diğerlerini de doğal olarak etkiler. Hepsinden kötüsü, sistemin diğer bileşenlerini korumak için kontrolleri yeniden kullanmaya çalıştığınızda, kodun bir kısmını kopyalamak zorunda kalırsınız, çünkü bu bileşenler kontrollerin bazılarını gerektirir ama bütün kontrolleri gerektirmez.

Sistemin anlaşılması çok zor ve bakımı pahalı hale geldi. Bir gün her şeyi yeniden düzenlemeye karar verene kadar kodla bir süre uğraşmak tabiri yerindeyse savaşmak zorunda kalırsınız.


##  😊 Çözüm

Diğer birçok davranışsal tasarım modeli gibi, Sorumluluk Zinciri de belirli davranışları işleyici (handler) adı verilen bağımsız nesnelere dönüştürmeye dayanır. Bizim durumumuzda her kontrolün, kontrolü gerçekleştiren tek bir yöntemle birlikte kendi sınıfına çıkarılması gerekir. İstek, verileriyle birlikte bu yönteme argüman olarak iletilir.

Desen, bu işleyicileri bir zincire bağlamanızı önerir. Her bağlantılı işleyicinin, zincirdeki bir sonraki işleyiciye referansı depolamak için bir alanı vardır. İşleyiciler, bir isteği işlemenin yanı sıra, isteği zincir boyunca daha da iletir. İstek, tüm işleyicilerin onu işleme şansı olana kadar zincir boyunca ilerler.

İşte işin en iyi kısmı: Bir işleyici, isteği zincirin daha aşağısına iletmemeye ve daha sonraki işlemleri etkili bir şekilde durdurmamaya karar verebilir.

Sipariş sistemleriyle ilgili örneğimizde; bir işleyici işlemi gerçekleştirir ve ardından isteği zincirin daha aşağılarına iletip iletmeyeceğine karar verir. İsteğin doğru verileri içerdiğini varsayarsak, tüm işleyiciler kimlik doğrulama kontrolleri veya önbelleğe alma gibi birincil davranışlarını yürütebilir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/solution1-en-2x.png)

*İşleyiciler bir zincir oluşturacak şekilde tek tek dizilir.*

</div>

Bununla birlikte, bir istek alındığında işleyicinin onu işleyip işleyemeyeceğine karar verdiği biraz farklı bir yaklaşım ve kuralları vardır. Yapabiliyorsa isteği daha fazla iletmez. Yani ya isteği işleyen tek bir işleyicidir ya da hiç işlemez. Bu yaklaşım, grafiksel bir kullanıcı arayüzü içindeki yığınlarındaki (stack) olaylarla uğraşırken çok yaygındır.

Örneğin, bir kullanıcı bir düğmeyi tıklattığında olay, düğmeyle başlayan, kapsayıcıları (formlar veya paneller gibi) boyunca ilerleyen ve ana uygulama penceresiyle biten GUI öğeleri zinciri boyunca yayılır. Olay, zincirde onu idare edebilen ilk öge tarafından işlenir. Bu örnek aynı zamanda dikkat çekicidir çünkü bir nesne ağacından her zaman bir zincirin çıkarılabileceğini gösterir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/solution2-en-2x.png)

*Bir nesne ağacının dalından bir zincir oluşturulabilir.*

</div>

Tüm işleyici sınıflarının aynı arayüzü uygulaması çok önemlidir. Her beton işleyicisi yalnızca yürütme (`execute`) yöntemine sahip olan aşağıdakiyle ilgilenmelidir. Bu şekilde, kodunuzu sınıflara bağlamadan çeşitli işleyicileri kullanarak çalışma zamanında zincirler oluşturabilirsiniz.


## 🚙 Gerçek Dünya Örneği

<div align="center">

![](https://refactoring.guru/images/patterns/content/chain-of-responsibility/chain-of-responsibility-comic-1-en-2x.png)

*Teknik destek çağrısı birden fazla operatör üzerinden yapılabilir.*

</div>

Bir senaryo düşünelim: Bilgisayarınıza yeni bir donanım parçası satın aldınız ve yüklediniz. Bir inek (geek) olduğunuzdan, bilgisayarda yüklü birkaç işletim sistemi vardır. Donanımın desteklenip desteklenmediğini görmek için hepsini önyüklemeye çalışırsınız. Windows donanımı otomatik olarak algılar ve etkinleştirir. Ancak, sevgili Linux'unuz yeni donanımla çalışmayı reddediyor. Küçük bir umutla, kutunun üzerinde yazılı olan teknik destek telefon numarasını aramaya karar verdiniz.

İlk duyduğunuz şey otomatik cevaplayıcının robotik sesi olacaktır. Hiçbiri sizin durumunuzla ilgili olmayan çeşitli sorunlara dokuz popüler çözüm önerir. Bir süre sonra robot sizi canlı bir operatöre bağlar.

Tahmin edeceğiniz üzere operatör de spesifik bir şey öneremiyor. Yorumlarınızı dinlemeyi reddederek kılavuzdan uzun alıntılar yapıp duruyor. "Bilgisayarı kapatıp açmayı denediniz mi?" Birkaç kez kez düzgün bir mühendise bağlanmayı talep ediyorsunuz.

Sonunda operatör çağrınızı, bir ofis binasının karanlık bodrumundaki yalnız sunucu odasında otururken muhtemelen saatlerce canlı insan sohbeti özlemi çeken mühendislerden birine iletir. Mühendis size yeni donanımınız için uygun sürücüleri nereden indireceğinizi ve bunları Linux'ta nasıl kuracağınızı anlatır. Sonunda probleminiz çözüldü! Aramayı sevinçten uçarak bitirebilirsiniz.

##  ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/structure-2x.png)

</div>

1. **İşleyici (Handler)**, tüm beton işleyicileri için ortak olan arayüzü (interface) tanımlar. Genellikle istekleri işlemek için tek bir yöntem içerir, ancak bazen zincirdeki bir sonraki işleyiciyi ayarlamak için başka bir yöntem de barındırabilir.
2. **Temel İşleyici (Base Handler)**, tüm işleyici sınıflarında ortak olan standart kodu barındırabileceğiniz isteğe bağlı bir sınıftır.
Genellikle bu sınıf, bir sonraki işleyiciye referansı depolamak için bir alan bulundurur. İstemciler bir işleyiciyi önceki işleyicinin yapıcısına veya ayarlayıcısına ileterek bir zincir oluşturabilirler. Sınıf ayrıca varsayılan işleme davranışını da uygulayabilir: Varlığını kontrol ettikten sonra yürütmeyi bir sonraki işleyiciye aktarabilir.
3. **Concrete Handlers** istekleri işlemek için asıl kodu içerir. Bir talep alındığında, her işleyicinin talebi işleyip işlemeyeceğine ve ek olarak onu zincire aktarıp aktarmayacağına karar vermesi gerekir.
İşleyiciler genellikle bağımsızdır ve değişmezdir; gerekli tüm verileri yapıcı (constructor) aracılığıyla yalnızca bir kez kabul eder.
4. **İstemci (Client)**, uygulamanın mantığına bağlı olarak zincirleri ya yalnızca bir kez oluşturabilir ya da dinamik olarak oluşturabilir. Bir isteğin zincirdeki herhangi bir işleyiciye gönderilebileceğini unutmayın; bunun ilk işleyici olması gerekmez.



##  💻 Sözde Kod (Pseudocode)

Bu örnekte Sorumluluk Zinciri modeli, etkin GUI öğeleri için bağlamsal yardım bilgilerinin görüntülenmesinden sorumludur.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/example-en-2x.png)

*GUI sınıfları Bileşik (Composite) desenle oluşturulmuştur. Her öge kendi kapsayıcı öğesine bağlıdır. Herhangi bir noktada, öğenin kendisiyle başlayan ve kapsayıcı öğelerin tümüne uzanan bir öge zinciri oluşturabilirsiniz.*

</div>

Uygulamanın GUI'si genellikle bir nesne ağacı olarak yapılandırılmıştır. Örneğin, uygulamanın ana penceresini oluşturan `Dialog` sınıfı, nesne ağacının kökü olacaktır. İletişim kutusunda, diğer `Panels` , `Buttons` ve `TextFields` gibi basit düşük düzeyli öğeleri içerebilecek Paneller (`Panels`) bulunur.

Basit bir bileşen, bileşene atanmış bir yardım metni olduğu sürece, kısa bağlamsal araç ipuçlarını gösterebilir. Ancak daha karmaşık bileşenler, kılavuzdan bir alıntı göstermek veya tarayıcıda bir sayfa açmak gibi bağlamsal yardımı göstermenin kendi yollarını tanımlar.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/chain-of-responsibility/example2-en-2x.png)

*Bir yardım isteği GUI nesnelerinden bu şekilde geçer.*

</div>

Kullanıcı fare imlecini bir öğenin üzerine getirip `F1` tuşuna bastığında, uygulama işaretçinin altındaki bileşeni algılar ve ona bir yardım isteği gönderir. İstek, yardım bilgilerini görüntüleyebilen öğeye ulaşana kadar öğenin tüm kapsayıcılarında kabarır.

```java
// İşlem yapmak için bir yöntem bildiren işlemci arabirim.
interface ComponentWithContextualHelp is
    method showHelp()

// Basit bileşenler için temel sınıf.
abstract class Component implements ComponentWithContextualHelp is
    field tooltipText: string

    // Bileşenin konteyneri, işlem zincirindeki bir sonraki bağıntıyı oluşturur.
    protected field container: Container

    // Bileşen, üzerine atanmış yardım metni varsa bir ipucu gösterir.
    // Aksi halde, varsa çağrıyı konteyner'e iletilir.
    method showHelp() is
        if (tooltipText != null)
            // İpucu göster.
        else
            container.showHelp()

// Konteynerler, basit bileşenlerin yanı sıra diğer konteynerları da
// içerebilir. Zincir ilişkileri burada oluşturulur. Sınıf, üst sınıfından
// showHelp davranışını devralır.
abstract class Container extends Component is
    protected field children: dizi Bileşen

    method add(child) is
        children.add(child)
        child.container = this

// İlkel bileşenler, varsayılan yardım uygulamasıyla iyi olabilir...
class Button extends Component is
    // ...

// Ancak karmaşık bileşenler varsayılan uygulamayı geçersiz kılabilir.
// Eğer yardım metni yeni bir şekilde sağlanamıyorsa, bileşen her zaman
// temel uygulamayı çağırabilir (bkz. Component sınıfı).
class Panel extends Container is
    field modalHelpText: string

    method showHelp() is
        if (modalHelpText != null)
            // Yardım metni ile mod bir pencere göster.
        else
            super.showHelp()

// ...yukarıdakiyle aynı...
class Dialog extends Container is
    field wikiPageURL: string

    method showHelp() is
        if (wikiPageURL != null)
            // Wiki yardım sayfasını aç.
        else
            super.showHelp()

// İstemci kodu.
class Application is
    // Her uygulama zinciri farklı şekilde yapılandırır.
    method createUI() is
        dialog = new Dialog("Bütçe Raporları")
        dialog.wikiPageURL = "http://..."
        panel = new Panel(0, 0, 400, 800)
        panel.modalHelpText = "Bu panel..."
        ok = new Button(250, 760, 50, 20, "Tamam")
        ok.tooltipText = "Bu, tamam düğmesi..."
        cancel = new Button(320, 760, 50, 20, "İptal")
        // ...
        panel.add(ok)
        panel.add(cancel)
        dialog.add(panel)

    // Burada ne olduğunu hayal edin.
    method onF1KeyPress() is
        component = this.getComponentAtMouseCoords()
        component.showHelp()

```


##  💡Uygulanabilirlik


**🐞 Programınızın farklı türdeki istekleri çeşitli şekillerde işlemesi beklendiğinde, ancak isteklerin kesin türleri ve sıraları önceden bilinmediğinde Sorumluluk Zinciri modelini kullanın.**

⚡️ Desen, birkaç işleyiciyi tek bir zincire bağlamanıza ve bir istek aldığınızda her işleyiciye bunu işleyip işleyemeyeceğini "sormanıza" olanak tanır. Bu şekilde tüm işleyicilerin isteği işleme alma şansı olur.

----------------

**🐞 Birkaç işleyicinin belirli bir sırayla yürütülmesi gerekli olduğunda modeli kullanın.**

⚡️ Zincirdeki işleyicileri istediğiniz sırayla bağlayabildiğiniz için tüm istekler zincirden tam olarak planladığınız gibi iletilecektir.

----------------

**🐞 İşleyici kümesinin ve bunların sırasının çalışma zamanında değişmesi gerektiğinde CoR modelini kullanın.**

⚡️ İşleyici sınıflarının içindeki bir referans alanı için ayarlayıcılar sağlarsanız işleyicileri dinamik olarak ekleyebilir, kaldırabilir veya yeniden sıralayabilirsiniz.
  

##  📝 Nasıl Uygulanır?

1. Handler arayüzünü tanımlayın ve istekleri işlemek için bir yöntemin imzasını açıklayın.
İstemcinin isteği yönteme nasıl ileteceğine karar verin. En esnek yol, isteği bir nesneye dönüştürmek ve bunu işleme yöntemine bir argüman olarak iletmektir.

2. Somut işleyicilerdeki yinelenen tekdüze kodu ortadan kaldırmak için, işleyici arayüzünden türetilmiş soyut bir temel işleyici sınırı oluşturmak faydalı olabilir.
Bu sınıf, zincirdeki bir sonraki işleyiciye bir referansı saklamak için bir alan içermelidir. Sınıfı değişmez (immutable) yapmayı düşünün. Ancak, zincirleri çalışma zamanında değiştirmeyi planlıyorsanız, referans alanının değerini değiştirmek için bir ayarlayıcı tanımlamanız gerekecektir.

3. İşleme yöntemi için kullanışlı varsayılan davranışı da uygulayabilirsiniz, bu da isteği bir sonraki nesneye iletme davranışını içerir, ancak başka işleyici kalmadığında. Somut işleyiciler, bu davranışı çağırarak kullanabilir ve işlemi bir sonraki nesneye iletmek için çağırabilir.

4. Birer birer işleyici alt sınıfları oluşturun ve işleme yöntemlerini uygulayın. Her işleyici, bir istek aldığında iki karar vermelidir:
	- İsteği işleyip işlemeyeceği. 
	- İsteği zincir boyunca iletip iletmeyeceği.

5. İstemci, zincirleri kendi başına oluşturabilir veya başka nesnelerden önceden oluşturulmuş zincirler alabilir. İkinci durumda, yapılandırmaya veya çevresel ayarlara göre zincirler oluşturmak için bazı fabrika sınıflarını uygulamanız gerekecektir.

6. İstemci, zincirdeki yalnızca ilk işleyiciyi değil, herhangi bir işleyiciyi tetikleyebilir. İstek, bir işleyici daha fazla iletmeyi reddedene veya zincirin sonuna ulaşana kadar zincir boyunca iletilir.

7. Zincirin dinamik yapısı nedeniyle, istemcinin aşağıdaki senaryoları ele almak için hazır olması gerekmektedir:
	- Zincir yalnızca tek bir bağlantıdan oluşabilir.
	- Bazı istekler zincirin sonuna ulaşmayabilir.
	- Diğerleri işlenmeden zincirin sonuna ulaşabilir.

##  ⚖️ Artıları ve Eksileri

✅ İstek işleme sırasını kontrol edebilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): İşlemleri çağıran sınıfları, işlemleri gerçekleştiren sınıflardan ayırabilirsiniz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle): Mevcut istemci kodunu bozmadan uygulamaya yeni işleyiciler tanıtabilirsiniz.

❌ Bazı istekler işlenmeyebilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Sorumluluk Zinciri**, **Command**, **Mediator** ve **Observer** desenleri, isteklerin göndericileri ve alıcıları arasında bağlantı kurmanın çeşitli yollarını ele alır:
	- **Sorumluluk Zinciri**, bir talebi potansiyel alıcılardan oluşan dinamik bir zincir boyunca, içlerinden biri bunu yerine getirene kadar sırayla iletir.
	- **Command**, gönderenler ve alıcılar arasında tek yönlü bağlantılar kurar.
	- **Mediator**, gönderenler ve alıcılar arasındaki doğrudan bağlantıları ortadan kaldırarak onları bir aracı nesne aracılığıyla dolaylı olarak iletişim kurmaya zorlar.
	- **Observer**, alıcıların dinamik olarak alma isteklerine abone olmalarını ve abonelikten çıkmalarını sağlar.

- **Sorumluluk Zinciri** sıklıkla **Kompozit** ile birlikte kullanılır. Bu durumda, bir yaprak bileşen bir istek aldığında, bunu tüm ana bileşenlerin zincirinden geçerek nesne ağacının köküne kadar iletebilir.

- **Sorumluluk Zincirindeki** işleyiciler **Command** olarak uygulanabilir. Bu durumda, bir istekle temsil edilen aynı bağlam nesnesi üzerinde birçok farklı işlemi yürütebilirsiniz. Ancak isteğin kendisinin bir Command nesnesi olduğu başka bir yaklaşım daha vardır. Bu durumda, aynı işlemi bir zincire bağlı bir dizi farklı bağlamda yürütebilirsiniz.

- **Sorumluluk Zinciri** ve **Decorator** çok benzer sınıf yapılarına sahiptir. Her iki model de yürütmeyi bir dizi nesneden geçirmek için özyinelemeli kompozisyona dayanır. Ancak birkaç önemli farklılık vardır.
CoR işleyicileri isteğe bağlı işlemleri birbirlerinden bağımsız olarak yürütebilir. Ayrıca herhangi bir noktada isteğin iletilmesini de durdurabilirler. Öte yandan, çeşitli Dekoratörler nesnenin davranışını genişletirken onu temel arayüzle tutarlı tutabilir. Ayrıca dekoratörlerin isteğin akışını bozmasına izin verilmez.


  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Sorumluluk Zinciri Java'da oldukça yaygındır. Desenin en popüler kullanım durumlarından biri, olayların GUI sınıflarındaki ana bileşenlere aktarılmasıdır. Bir diğer dikkate değer kullanım durumu sıralı erişim filtreleridir.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`javax.servlet.Filter#doFilter()`](http://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html#doFilter-javax.servlet.ServletRequest-javax.servlet.ServletResponse-javax.servlet.FilterChain-)
-   [`java.util.logging.Logger#log()`](http://docs.oracle.com/javase/8/docs/api/java/util/logging/Logger.html#log-java.util.logging.Level-java.lang.String-)

**Tanımlama:** Desen, tüm nesneler ortak arayüzü takip ederken, diğer nesnelerde aynı yöntemleri dolaylı olarak çağıran bir grup nesnenin davranışsal yöntemleriyle tanınabilir.

 
####  Erişimi filtreleme

Bu örnek, kullanıcı verilerini içeren bir isteğin, kimlik doğrulama, yetkilendirme ve doğrulama gibi çeşitli işlemleri gerçekleştiren sıralı bir işleyiciler zincirinden nasıl geçtiğini gösterir.

Bu örnek, çeşitli yazarlar tarafından verilen modelin kanonik versiyonundan biraz farklıdır. Desen örneklerinin çoğu, doğru işleyiciyi arama, onu başlatma ve ardından zincirden çıkma fikri üzerine inşa edilmiştir. Ancak burada, bir isteği yerine getiremeyen bir işleyici bulunana kadar her işleyiciyi çalıştırırız. Akış biraz farklı olsa da bunun hala Sorumluluk Zinciri modeli olduğunu unutmayın.

**📁 middleware**

⤵️ 📄 `middleware/Middleware.java` : Basic validation interface

```java
package fatihes1.chain_of_responsibility.example.middleware;

/**
 * Base middleware class.
 */
public abstract class Middleware {
    private Middleware next;

    /**
     * Builds chains of middleware objects.
     */
    public static Middleware link(Middleware first, Middleware... chain) {
        Middleware head = first;
        for (Middleware nextInChain: chain) {
            head.next = nextInChain;
            head = nextInChain;
        }
        return first;
    }

    /**
     * Subclasses will implement this method with concrete checks.
     */
    public abstract boolean check(String email, String password);

    /**
     * Runs check on the next object in chain or ends traversing if we're in
     * last object in chain.
     */
    protected boolean checkNext(String email, String password) {
        if (next == null) {
            return true;
        }
        return next.check(email, password);
    }
}
```

⤵️ 📄 `middleware/ThrottlingMiddleware.java`: İstek sayısı sınırına ulaşılıp ulaşılmadığını kontrol edin

```java
package fatihes1.chain_of_responsibility.example.middleware;

/**
 * ConcreteHandler. Checks whether there are too many failed login requests.
 */
public class ThrottlingMiddleware extends Middleware {
    private int requestPerMinute;
    private int request;
    private long currentTime;

    public ThrottlingMiddleware(int requestPerMinute) {
        this.requestPerMinute = requestPerMinute;
        this.currentTime = System.currentTimeMillis();
    }

    /**
     * Please, not that checkNext() call can be inserted both in the beginning
     * of this method and in the end.
     *
     * This gives much more flexibility than a simple loop over all middleware
     * objects. For instance, an element of a chain can change the order of
     * checks by running its check after all other checks.
     */
    public boolean check(String email, String password) {
        if (System.currentTimeMillis() > currentTime + 60_000) {
            request = 0;
            currentTime = System.currentTimeMillis();
        }

        request++;
        
        if (request > requestPerMinute) {
            System.out.println("Request limit exceeded!");
            Thread.currentThread().stop();
        }
        return checkNext(email, password);
    }
}

```


⤵️ 📄 `middleware/UserExistsMiddleware.java`: Kullanıcının kimlik bilgilerini kontrol edilmesi

```java
package fatihes1.chain_of_responsibility.example.middleware;

import fatihes1.chain_of_responsibility.example.server.Server;

/**
 * ConcreteHandler. Checks whether a user with the given credentials exists.
 */
public class UserExistsMiddleware extends Middleware {
    private Server server;

    public UserExistsMiddleware(Server server) {
        this.server = server;
    }

    public boolean check(String email, String password) {
        if (!server.hasEmail(email)) {
            System.out.println("This email is not registered!");
            return false;
        }
        if (!server.isValidPassword(email, password)) {
            System.out.println("Wrong password!");
            return false;
        }
        return checkNext(email, password);
    }
}
```

⤵️ 📄 `middleware/RoleCheckMiddleware.java`: Kullanıcının rolünü kontrol edin

```java

package fatihes1.chain_of_responsibility.example.middleware;

/**
 * ConcreteHandler. Checks a user's role.
 */
public class RoleCheckMiddleware extends Middleware {
    public boolean check(String email, String password) {
        if (email.equals("admin@example.com")) {
            System.out.println("Hello, admin!");
            return true;
        }
        System.out.println("Hello, user!");
        return checkNext(email, password);
    }
}
```

**📁 server**

⤵️ 📄 `server/Server.java`: Yetkilendirme hedefi

```java
package fatihes1.chain_of_responsibility.example.server;

import fatihes1.chain_of_responsibility.example.middleware.Middleware;

import java.util.HashMap;
import java.util.Map;

/**
 * Server class.
 */
public class Server {
    private Map<String, String> users = new HashMap<>();
    private Middleware middleware;

    /**
     * Client passes a chain of object to server. This improves flexibility and
     * makes testing the server class easier.
     */
    public void setMiddleware(Middleware middleware) {
        this.middleware = middleware;
    }

    /**
     * Server gets email and password from client and sends the authorization
     * request to the chain.
     */
    public boolean logIn(String email, String password) {
        if (middleware.check(email, password)) {
            System.out.println("Authorization have been successful!");

            // Do something useful here for authorized users.

            return true;
        }
        return false;
    }

    public void register(String email, String password) {
        users.put(email, password);
    }

    public boolean hasEmail(String email) {
        return users.containsKey(email);
    }

    public boolean isValidPassword(String email, String password) {
        return users.get(email).equals(password);
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.chain_of_responsibility.example;

import fatihes1.chain_of_responsibility.example.middleware.Middleware;
import fatihes1.chain_of_responsibility.example.middleware.RoleCheckMiddleware;
import fatihes1.chain_of_responsibility.example.middleware.ThrottlingMiddleware;
import fatihes1.chain_of_responsibility.example.middleware.UserExistsMiddleware;
import fatihes1.chain_of_responsibility.example.server.Server;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    private static BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
    private static Server server;

    private static void init() {
        server = new Server();
        server.register("admin@example.com", "admin_pass");
        server.register("user@example.com", "user_pass");

        // All checks are linked. Client can build various chains using the same
        // components.
        Middleware middleware = Middleware.link(
            new ThrottlingMiddleware(2),
            new UserExistsMiddleware(server),
            new RoleCheckMiddleware()
        );

        // Server gets a chain from client code.
        server.setMiddleware(middleware);
    }

    public static void main(String[] args) throws IOException {
        init();

        boolean success;
        do {
            System.out.print("Enter email: ");
            String email = reader.readLine();
            System.out.print("Input password: ");
            String password = reader.readLine();
            success = server.logIn(email, password);
        } while (!success);
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: RAM kullanım istatistikleri

```
Enter email: admin@example.com
Input password: admin_pass
Hello, admin!
Authorization have been successful!


Enter email: wrong@example.com
Input password: wrong_pass
This email is not registered!
Enter email: wrong@example.com
Input password: wrong_pass
This email is not registered!
Enter email: wrong@example.com
Input password: wrong_pass
Request limit exceeded!

```
