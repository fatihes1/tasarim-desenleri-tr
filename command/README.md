#  Komut (Command) Tasarım Deseni

> Diğer Adıyla: Action, Transaction

##  💬 Amaç

Komut, bir isteği, istekle ilgili tüm bilgileri içeren bağımsız bir nesneye dönüştüren davranışsal (behavioral) bir tasarım modelidir. Bu dönüşüm, istekleri yöntem bağımsız değişkenleri olarak aktarmanıza, bir isteğin yürütülmesini geciktirmenize, sıraya koymanıza ve geri alınamayan işlemleri desteklemenize olanak tanır.

![](https://refactoring.guru/images/patterns/content/command/command-en-2x.png)


##  🙁 Problem

Yeni bir metin düzenleyici uygulaması üzerinde çalıştığınızı hayal edin. Şu anki göreviniz, düzenleyicinin çeşitli işlemleri için bir dizi düğme içeren bir araç çubuğu oluşturmaktır. Araç çubuğundaki düğmelerin yanı sıra çeşitli iletişim kutularındaki genel düğmeler için kullanılabilecek normal bir `Button` sınıfı oluşturdunuz.

![](https://refactoring.guru/images/patterns/diagrams/command/problem1-2x.png)

*Uygulamanın tüm düğmeleri aynı sınıftan türetilmiştir.*

Bu düğmelerin tümü benzer görünse de hepsinin farklı şeyler yapması gerekiyor. Bu düğmelerin çeşitli tıklama eylemleri için oluşturduğunuz kodu nereye koyarsınız? En basit çözüm, butonun kullanıldığı her yer için tonlarca alt sınıf oluşturmaktır. Bu alt sınıflar, bir düğmeye tıklandığında yürütülmesi gereken kodu barındıracaktır.

![](https://refactoring.guru/images/patterns/diagrams/command/problem2-2x.png)

*Çok sayıda düğme alt sınıfı oluştu. Bir süre sonra kontrol edilemez hal alabilir.*

Çok geçmeden bu yaklaşımın son derece kusurlu olduğunu fark edeceksiniz. İlk olarak, çok sayıda alt sınıfınız oluşacaktır. Eğer temel `Button` sınıfını her değiştirdiğinizde bu alt sınıflardaki kodu kırma riskini almıyorsanız bu sorun değil. Basitçe söylemek gerekirse, GUI kodunuz iş mantığının geçici koduna garip bir şekilde bağımlı hale geldi.

![](https://refactoring.guru/images/patterns/diagrams/command/problem3-en-2x.png)

*Birkaç sınıf temelde aynı işlevi uygular.*

Ve işte işin en tatsız kısmı. Metni kopyalamak/yapıştırmak gibi bazı işlemlerin birden fazla yerden başlatılması gerekir. Örneğin, kullanıcı araç çubuğundaki küçük bir "Kopyala" düğmesini tıklayabilir veya içerik menüsü aracılığıyla bir şeyi kopyalayabilir veya klavyede `Ctrl+C` tuşlarına basabilir.

Başlangıçta, uygulamamızda yalnızca araç çubuğu bulunduğunda, çeşitli işlemlerin uygulanmasını düğme alt sınıflarına yerleştirmek sorun değildi. Başka bir deyişle, `CopyButton` alt sınıfının içinde metin kopyalamaya yönelik kodun bulunması sorun değildi. Ancak bağlam menüleri, kısayollar ve diğer şeyleri uyguladığınızda ya işlemin kodunu birçok sınıfta kopyalamanız ya da menüleri düğmelere bağımlı hale getirmeniz gerekir ki bu daha da kötü bir seçenektir.

##  😊 Çözüm

İyi yazılım tasarımı genellikle endişelerin ayrılması ilkesine dayanır; bu da genellikle bir uygulamanın katmanlara bölünmesiyle sonuçlanır. En yaygın örnek: grafiksel kullanıcı arayüzü için bir katman ve iş mantığı için başka bir katman oluşturulmasıdır. GUI katmanı ekranda güzel bir resim oluşturmaktan, her türlü girişi yakalamaktan ve kullanıcının ve uygulamanın yaptıklarının sonuçlarını göstermekten sorumludur. Ancak iş ayın yörüngesini hesaplamak veya yıllık bir rapor oluşturmak gibi önemli bir şey yapmaya geldiğinde, GUI katmanı işi altta yatan iş mantığı katmanına devreder.

Kodda şöyle görünebilir: Bir GUI nesnesi, bir iş mantığı nesnesinin yöntemini çağırır ve ona bazı argümanlar iletir. Bu süreç genellikle bir nesnenin diğerine istek göndermesi olarak tanımlanır.


![](https://refactoring.guru/images/patterns/diagrams/command/solution1-en-2x.png)

*GUI nesneleri, iş mantığı nesnelerine doğrudan erişebilir.*

Komut modeli, GUI nesnelerinin bu istekleri doğrudan göndermemesi gerektiğini önerir. Bunun yerine, çağrılan nesne, yöntemin adı ve argümanların listesi gibi tüm istek ayrıntılarını, bu isteği tetikleyen tek bir yöntemle ayrı bir komut sınıfına çıkarmalısınız.

Komut nesneleri, çeşitli GUI ve iş mantığı nesneleri arasında bağlantı görevi görür. Artık GUI nesnesinin, isteği hangi iş mantığı nesnesinin alacağını ve nasıl işleneceğini bilmesine gerek yok. GUI nesnesi yalnızca tüm ayrıntıları işleyen komutu tetikler.


![](https://refactoring.guru/images/patterns/diagrams/command/solution2-en-2x.png)

*İş mantığı katmanına bir komut aracılığıyla erişme.*

Önümüzdeki adım, komutlarınızı aynı arayüzü uygulamak olmalıdır. Genellikle yalnızca parametre almayan tek bir yürütme yöntemine sahiptir. Bu arayüz, betik sınıflarının somut sınıflarına bağlı olmadan çeşitli komutları aynı istek gönderici ile kullanmanıza olanak tanır. Bir artı olarak, şimdi komut nesnelerini gönderici ile ilişkilendirebilir ve böylece göndericinin davranışını çalışma zamanında değiştirebilirsiniz.

Belki de eksik parçayı fark etmişsinizdir, o da istek parametreleridir. Bir GUI nesnesi iş katmanı nesnesine bazı parametreler sağlamış olabilir. Ancak komut yürütme yönteminin herhangi bir parametresi olmadığı için, isteği alıcısına nasıl iletebiliriz? İşte komutun bu verilerle ya önceden yapılandırılması ya da kendi kendine alabilme yeteneğine sahip olması gerektiği ortaya çıkıyor.

![](https://refactoring.guru/images/patterns/diagrams/command/solution3-en-2x.png)

*GUI nesneleri, işi komutlara devreder.*

Metin editörümüze geri dönelim. Komut (Command) modelini uyguladıktan sonra, çeşitli tıklama davranışlarını uygulamak için artık tüm bu düğme alt sınıflarına ihtiyacımız yok. Bir komut nesnesine referansı saklayan temel `Button` sınıfına tek bir alan koymak ve düğmenin bu komutu bir tıklamayla yürütmesini sağlamak yeterlidir.

Mümkün olan her işlem için bir dizi komut sınıfı uygulayacak ve düğmelerin amaçlanan davranışına bağlı olarak bunları belirli düğmelere bağlayacaksınız.

Menüler, kısayollar veya diyalogların tamamı gibi diğer GUI öğeleri de aynı şekilde uygulanabilir. Bir kullanıcı GUI öğesiyle etkileşime girdiğinde yürütülen bir komuta bağlanacaklar. Tahmin edeceğiniz gibi, aynı işlemlerle ilgili öğeler aynı komutlara bağlanacak ve herhangi bir kod kopyası önlenecek.

Sonuç olarak komutlar, GUI ile iş mantığı katmanları arasındaki bağlantıyı azaltan kullanışlı bir orta katman haline gelir. Ve bu, Komut modelinin sunabileceği faydaların yalnızca küçük bir kısmı!


## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/content/command/command-comic-1-2x.png)

*Bir restoranda sipariş verme.*

Şehirde uzun bir yürüyüşten sonra güzel bir restorana gelip pencere kenarındaki masaya oturduğunuzu düşünelim. Dost canlısı bir garson yanınıza yaklaşıyor ve siparişinizi hızla alıp bir kağıda yazıyor. Garson mutfağa gider ve siparişi duvara yapıştırır. Bir süre sonra sipariş şefe ulaşır, şef de onu okur ve yemeği ona göre pişirir. Aşçı yemeği siparişle birlikte tepsiye yerleştirir. Garson tepsiyi teslim alır, her şeyin istediğiniz gibi olduğundan emin olmak için siparişi kontrol eder. Eğer her şey olması gerektiği gibi ise siparişinizi masanıza getirir.

Kağıt siparişi bir komut yani command görevi görür. Şef, siparişi servis etmeye hazır olana kadar kuyrukta kalır. Sipariş, yemeği pişirmek için gereken tüm bilgileri içerir. Bu, şefin sipariş ayrıntılarını doğrudan sizden öğrenmesi yerine hemen yemek pişirmeye başlamasını sağlar.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/command/structure-2x.png)

1. **Sender** sınıfı (diğer adıyla çağrıcı - invoker) istekleri başlatmadan sorumludur. Bu sınıfın, bir komut nesnesine referansı depolamak için bir alana sahip olması gerekir. Gönderen yani **Sender**, isteği doğrudan alıcıya göndermek yerine bu komutu tetikler. Gönderenin, komut nesnesinin oluşturulmasından sorumlu **olmadığını** unutmayın. Genellikle istemciden yapıcı aracılığıyla önceden oluşturulmuş bir komut alır.

2. **Komut** (Command) arayüzü (interface) genellikle komutun yürütülmesi için yalnızca tek bir yöntem bildirir.

3. **Concrete Commands** çeşitli türde istekleri uygular (implement). Bir komutun işi kendi başına gerçekleştirmesi değil, çağrıyı iş mantığı nesnelerinden birine iletmesi gerekir. Ancak kodu basitleştirmek adına bu sınıflar birleştirilebilir.
Alıcı nesnede bir yöntemi yürütmek için gereken parametreler, somut komutta alanlar olarak bildirilebilir. Bu alanların yalnızca yapıcı (constructor) aracılığıyla başlatılmasına izin vererek komut nesnelerini değişmez hale getirebilirsiniz.

4. **Alıcı (Receiver)** sınıfı bazı iş mantıklarını içerir. Hemen hemen her receiver görevi görebilir. Çoğu komut yalnızca bir isteğin alıcıya nasıl iletildiğinin ayrıntılarını ele alırken, asıl işi alıcının kendisi yapar.

5. **İstemci (Client)** somut komut nesneleri oluşturur ve yapılandırır. İstemci, alıcı örneği de dahil olmak üzere tüm istek parametrelerini komutun yapıcısına (constructor) aktarmalıdır. Bundan sonra ortaya çıkan komut bir veya daha fazla göndericiyle ilişkilendirilebilir.



##  💻 Sözde Kod (Pseudocode)

Bu örnekte **Komut** modeli, yürütülen işlemlerin geçmişinin izlenmesine yardımcı olur ve gerektiğinde bir işlemin geri alınmasını mümkün kılar.

![](https://refactoring.guru/images/patterns/diagrams/command/example-2x.png)

*Bir metin düzenleyicide geri alınamayan işlemler.*

Komutlar, düzenleyicinin durumunu değiştiren komutla ilişkilendirilmiş bir işlemi gerçekleştirmeden önce düzenleyicinin durumunun yedek bir kopyasını oluştururlar (örneğin kesme ve yapıştırma gibi). Bir komut gerçekleştirildikten sonra, komut geçmişi (komut nesnelerinin bir yığını) ile o andaki düzenleyicinin durumunun yedeği birlikte saklanır. Daha sonra, kullanıcı bir işlemi geri alması gerektiğinde, uygulama geçmişten en son komutu alabilir, düzenleyicinin durumunun ilişkilendirilen yedeğini okuyabilir ve onu geri yükleyebilir.

İstemci kodu (GUI öğeleri, komut geçmişi vb.), komut arayüzü üzerinden komutlarla çalıştığı için concrete komut sınıflarına bağlı değildir. Bu yaklaşım, mevcut kodu bozmadan uygulamaya yeni komutlar eklemenizi sağlar.


```java
// Temel komut sınıfı, tüm somut komutlar için ortak arabirimi tanımlar.
abstract class Command is
    protected field app: Application
    protected field editor: Editor
    protected field backup: metin

    constructor Command(app: Application, editor: Editor) is
        this.app = app
        this.editor = editor

    // Editörün durumunu yedekle.
    method saveBackup() is
        backup = editor.text

    // Editörün durumunu geri yükle.
    method undo() is
        editor.text = backup

    // Gerçekleştirme yöntemi, tüm somut komutlara kendi uygulamalarını
    // sağlamaları için zorlamak için soyut olarak tanımlanır.
    // Yöntem, komutun editörün durumunu değiştirip değiştirmediğine bağlı olarak
    // true veya false döndürmelidir.
    abstract method execute()

// Somut komutlar burada yer alır.
class CopyCommand extends Command is
    // Kopyalama komutu, editörün durumunu değiştirmediği için
    // geçmişe kaydedilmez.
    method execute() is
        app.clipboard = editor.getSelection()
        return false

class CutCommand extends Command is
    // Kesme komutu editörün durumunu değiştirir, bu nedenle
    // geçmişe kaydedilmelidir. Ve yöntem true döndüren sürece
    // kaydedilir.
    method execute() is
        saveBackup()
        app.clipboard = editor.getSelection()
        editor.deleteSelection()
        return true

class PasteCommand extends Command is
    method execute() is
        saveBackup()
        editor.replaceSelection(app.clipboard)
        return true

// Geri alma işlemi de bir komuttur.
class UndoCommand extends Command is
    method execute() is
        app.undo()
        return false

// Global komut geçmişi sadece bir yığındır.
class CommandHistory is
    private field history: array of Command

    // Son eklenen...
    method push(c: Command) is
        // Komutu geçmiş dizisinin sonuna ekler.

    // ...ilk çıkan
    method pop():Command is
        // En son komutu geçmişten alır.

// Editör sınıfı gerçek metin düzenleme işlemlerine sahiptir. 
// Bu, bir alıcı rolünü üstlenir: tüm komutlar sonunda
// editörün yöntemlerine uygulanır.
class Editor is
    field text: string

    method getSelection() is
        // Seçili metni döndür.

    method deleteSelection() is
        // Seçili metni sil.

    method replaceSelection(metin) is
        // Pano içeriğini mevcut pozisyona ekler.

// Uygulama sınıfı nesne ilişkilerini kurar. Bu, bir gönderici olarak
// işlev görür: bir şey yapılması gerektiğinde bir komut nesnesi oluşturur
// ve bunu yürütür.
class Application is
    field clipboard: string
    field editors: array of Editors
    field activeEditor: Editor
    field history: CommandHistory

    // Komutları UI nesnelerine atayan kod, böyle görünebilir.
    method createUI() is
        // ...
        copy = function() { executeCommand(
            new CopyCommand(this, activeEditor)) }
        copyButton.setCommand(copy)
        shortcuts.onKeyPress("Ctrl+C", copy)

        cut = function() { executeCommand(
            new CutCommand(this, activeEditor)) }
        cutButton.setCommand(cut)
        shortcuts.onKeyPress("Ctrl+X", cut)

        paste = function() { executeCommand(
            new PasteCommand(this, activeEditor)) }
        pasteButton.setCommand(paste)
        shortcuts.onKeyPress("Ctrl+V", paste)

        undo = function() { executeCommand(
            new UndoCommand(this, activeEditor)) }
        undoButton.setCommand(undo)
        shortcuts.onKeyPress("Ctrl+Z", undo)

    // Bir komutu yürütür ve geçmişe eklenip eklenmemesi gerektiğini kontrol eder.
    method executeCommand(command) is
        if (command.execute())
            history.push(command)

    // Geçmişten en son komutu alır ve geri alma yöntemini çalıştırır.
    // Unutulmaması gereken, komutun sınıfını bilmediğimizdir. Ancak bilmenize gerek yok,
    // çünkü komut, kendi eylemini nasıl geri alacağını bilir.
    method undo() is
        command = history.pop()
        if (command != null)
            command.undo()

```


##  💡Uygulanabilirlik


**🐞 Nesneleri işlemlerle parametreleştirmek istediğinizde Komut modelini kullanın.**

⚡️ Komut modeli, belirli bir yöntem çağrısını bağımsız bir nesneye dönüştürebilir. Bu değişiklik birçok ilginç kullanıma kapı açmaktadır: komutları yöntem bağımsız değişkenleri olarak iletebilir, bunları diğer nesnelerin içinde saklayabilir, bağlantılı komutları çalışma zamanında değiştirebilir gibi
Bir örnek vermek gerekirse: içerik menüsü gibi bir GUI bileşeni geliştiriyorsunuz ve kullanıcılarınızın, son kullanıcı bir öğeyi tıklattığında işlemleri tetikleyen menü öğelerini yapılandırabilmesini istiyorsunuz.

----------------

**🐞 İşlemleri sıraya koymak, yürütülmesini planlamak veya uzaktan yürütmek istediğinizde Komut modelini kullanın.**

⚡️ Herhangi bir nesnede olduğu gibi, bir komut da serileştirilebilir; bu, onu bir dosyaya veya veritabanına kolayca yazılabilen bir dizeye dönüştürmek için ideal bir yapıdır. Daha sonra dize, ilk komut nesnesi olarak geri yüklenebilir. Böylece komut yürütmeyi geciktirebilir ve planlayabilirsiniz. Sadece bu kadar değil elbette! Aynı şekilde ağ üzerinden komutları sıraya koyabilir, kaydedebilir veya gönderebilirsiniz.

----------------

**🐞 Tersine çevrilebilir işlemleri uygulamak istediğinizde Komut modelini kullanın.**

⚡️ Geri alma/yinelemenin birçok yolu olmasına rağmen, Komut modeli belki de bunların en popüler olanıdır.

İşlemleri geri alabilmek için gerçekleştirilen işlemlerin geçmişini implement etmeniz gerekir. Komut geçmişi, yürütülen tüm komut nesnelerinin yanı sıra uygulamanın durumunun ilgili yedeklerini içeren bir yığındır.

Bu yöntemin iki dezavantajı vardır. Birincisi, bir uygulamanın durumunu kaydetmek o kadar kolay değildir çünkü bir kısmı private olabilir. Bu sorun **Memento** tasarım deseniyle azaltılabilir.

İkincisi, durum yedeklemeleri oldukça fazla RAM tüketebilir. Bu nedenle bazen alternatif bir uygulamaya başvurabilirsiniz: komut, geçmiş durumu geri yüklemek yerine ters işlemi gerçekleştirir. Tersine operasyonun da bir bedeli vardır: Uygulanması zor, hatta imkansız olabilir.
  

##  📝 Nasıl Uygulanır?

1. Tek bir yürütme yöntemine sahip komut arayüzünü (interface) tanımlayın.

2. Komut arayüzünü uygulayan concrete komut sınıflarına istekleri sağlamaya başlayın. Her sınıf, istek argümanlarını saklamak için kullanılan alan kümesine sahip olmalı ve gerçek alıcı nesnesine bir başvuruya sahip olmalıdır. Tüm bu değerler komutun yapıcısı yani constructor aracılığıyla başlatılmalıdır.

3. Gönderen (Sender) olarak kullanılacak sınıfları tanımlayın. Bu sınıflara komutları saklamak için alanlar ekleyin. Göndericiler komutları yalnızca komut arayüzü üzerinden iletmelidir. Göndericiler genellikle komut nesnelerini kendi başlarına oluşturmaz, ancak onları istemci kodundan alabilirler.

4. Göndericileri, alıcıya doğrudan bir istek göndermek yerine komutu yürütecek şekilde değiştirin.

5. İstemci, nesneleri aşağıdaki sırayla başlatmalıdır:
	- Alıcıları (receivers) oluşturun.
	- Komutları oluşturun ve bunları gerektiğinde alıcılarla ilişkilendirin.
	- Göndericileri oluşturun ve belirli komutlarla ilişkilendirin.

##  ⚖️ Artıları ve Eksileri

✅ Geri alma/yineleme uygulayabilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): İşlemleri çağıran sınıfları, bu işlemleri gerçekleştiren sınıflardan ayırabilirsiniz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle): Mevcut istemci kodunu bozmadan uygulamaya yeni komutlar ekleyebilirsiniz.

✅ Operasyonların ertelenmiş yürütülmesini uygulayabilirsiniz.

✅ Bir dizi basit komutu karmaşık bir komutta birleştirebilirsiniz.


❌ Gönderenler ve alıcılar arasında tamamen yeni bir katman tanıttığınız için kod daha karmaşık hale gelebilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Sorumluluk Zinciri**, **Command**, **Mediator** ve **Observer** desenleri, isteklerin göndericileri ve alıcıları arasında bağlantı kurmanın çeşitli yollarını ele alır:
	- **Sorumluluk Zinciri**, bir talebi potansiyel alıcılardan oluşan dinamik bir zincir boyunca, içlerinden biri bunu yerine getirene kadar sırayla iletir.
	- **Command**, gönderenler ve alıcılar arasında tek yönlü bağlantılar kurar.
	- **Mediator**, gönderenler ve alıcılar arasındaki doğrudan bağlantıları ortadan kaldırarak onları bir aracı nesne aracılığıyla dolaylı olarak iletişim kurmaya zorlar.
	- **Observer**, alıcıların dinamik olarak alma isteklerine abone olmalarını ve abonelikten çıkmalarını sağlar.

- **Sorumluluk Zincirindeki** işleyiciler (handlers) **Command** olarak uygulanabilir. Bu durumda, bir istekle temsil edilen aynı bağlam nesnesi üzerinde birçok farklı işlemi yürütebilirsiniz.
Ancak isteğin kendisinin bir **Command** nesnesi olduğu başka bir yaklaşım daha vardır. Bu durumda, aynı işlemi bir zincire bağlı bir dizi farklı bağlamda yürütebilirsiniz.

- Geri alma işlemini gerçekleştirirken **Command** ve **Memento**'yu birlikte kullanabilirsiniz. Bu durumda, *komutlar* bir hedef nesne üzerinde çeşitli işlemlerin gerçekleştirilmesinden sorumludur; *mementos* ise bir komut yürütülmeden hemen önce o nesnenin durumunu kaydeder.

- **Command** ve **Strategy** benzer görünebilir çünkü her ikisini de bir nesneyi bazı eylemlerle parametreleştirmek için kullanabilirsiniz. Ancak hedefleri çok farklıdır:
	- Herhangi bir işlemi bir nesneye dönüştürmek için **Command**'i kullanabilirsiniz. İşlemin parametreleri o nesnenin alanları haline gelir. Dönüşüm (conversion), işlemin yürütülmesini ertelemenize, sıraya koymanıza, komut geçmişini saklamanıza, uzak hizmetlere komut göndermenize vb. olanak tanır.
	- Öte yandan, **Strategy** genellikle aynı şeyi yapmanın farklı yollarını açıklar ve bu algoritmaları tek bir bağlam sınıfı içinde değiştirmenize olanak tanır.

- **Prototype**, **Commands** kopyalarını geçmişe kaydetmeniz gerektiğinde yardımcı olabilir.

- **Visitor** desenini, **Command** modelinin güçlü bir versiyonu olarak değerlendirebilirsiniz. Nesneleri, farklı sınıflardaki çeşitli nesneler üzerinde işlemler gerçekleştirebilir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Komut modeli Java kodunda oldukça yaygındır. Çoğu zaman, UI öğelerini eylemlerle parametreleştirmek için geri aramalara alternatif olarak kullanılır. Ayrıca görevleri sıraya koymak, operasyon geçmişini takip etmek vb. için de kullanılır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   All implementations of  [`java.lang.Runnable`](http://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html)
    
-   All implementations of  [`javax.swing.Action`](http://docs.oracle.com/javase/8/docs/api/javax/swing/Action.html)

**Tanımlama:** Belirli eylemleri ("Kopyala", "Kes", "Gönder", "Yazdır" vb. gibi) temsil eden bir dizi ilgili sınıf görürseniz bu bir **Komut (Command)** modeli olabilir. Bu sınıflar aynı arayüzü/soyut sınıfı uygulamalıdır yani implement etmelidir. Komutlar, ilgili eylemleri kendi başlarına uygulayabilir veya işi, alıcılar olacak ayrı nesnelere devredebilir. Bulmacanın son parçası, bir çağırıcıyı tanımlamaktır; yöntemlerinin veya yapıcısının parametrelerindeki komut nesnelerini kabul eden bir sınıf aramaktır.

 
####  Metin düzenleyici komutları ve geri alma

Bu örnekteki metin düzenleyici, kullanıcı onunla her etkileşim kurduğunda yeni komut nesneleri oluşturur. Eylemlerini yürüttükten sonra geçmiş yığınına bir komut gönderilir.

Şimdi, geri alma işlemini gerçekleştirmek için uygulamanız, geçmişten yürütülen son komutu alır veya ters bir eylem gerçekleştirir ya da düzenleyicinin o komut tarafından kaydedilen geçmiş durumunu geri yükler.

**📁 commands**

⤵️ 📄 `commands/Command.java` : Soyut temel komut sınıfı

```java
package fatihes1.command.example.commands;

import fatihes1.command.example.editor.Editor;

public abstract class Command {
    public Editor editor;
    private String backup;

    Command(Editor editor) {
        this.editor = editor;
    }

    void backup() {
        backup = editor.textField.getText();
    }

    public void undo() {
        editor.textField.setText(backup);
    }

    public abstract boolean execute();
}
```

⤵️ 📄 `commands/CopyCommand.java`: Seçilen metni panoya kopyalama

```java
package fatihes1.command.example.commands;

import fatihes1.command.example.editor.Editor;

public class CopyCommand extends Command {

    public CopyCommand(Editor editor) {
        super(editor);
    }

    @Override
    public boolean execute() {
        editor.clipboard = editor.textField.getSelectedText();
        return false;
    }
}
```


⤵️ 📄 `commands/PasteCommand.java`: Panodan metin yapıştırma

```java
package fatihes1.command.example.commands;

import fatihes1.command.example.editor.Editor;

public class PasteCommand extends Command {

    public PasteCommand(Editor editor) {
        super(editor);
    }

    @Override
    public boolean execute() {
        if (editor.clipboard == null || editor.clipboard.isEmpty()) return false;

        backup();
        editor.textField.insert(editor.clipboard, editor.textField.getCaretPosition());
        return true;
    }
}
```

⤵️ 📄 `commands/CutCommand.java`: Metni panoya kesme

```java

package fatihes1.command.example.commands;

import fatihes1.command.example.editor.Editor;

public class CutCommand extends Command {

    public CutCommand(Editor editor) {
        super(editor);
    }

    @Override
    public boolean execute() {
        if (editor.textField.getSelectedText().isEmpty()) return false;

        backup();
        String source = editor.textField.getText();
        editor.clipboard = editor.textField.getSelectedText();
        editor.textField.setText(cutString(source));
        return true;
    }

    private String cutString(String source) {
        String start = source.substring(0, editor.textField.getSelectionStart());
        String end = source.substring(editor.textField.getSelectionEnd());
        return start + end;
    }
}
```

⤵️ 📄 `commands/CommandHistory.java`: Komut geçmişi

```java
package fatihes1.command.example.commands;

import java.util.Stack;

public class CommandHistory {
    private Stack<Command> history = new Stack<>();

    public void push(Command c) {
        history.push(c);
    }

    public Command pop() {
        return history.pop();
    }

    public boolean isEmpty() { return history.isEmpty(); }
}
```


**📁 editor**

⤵️ 📄 `editor/Editor.java`: Metin düzenleyicinin GUI'ı

```java
package fatihes1.command.example.editor;

import fatihes1.command.example.commands.*;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class Editor {
    public JTextArea textField;
    public String clipboard;
    private CommandHistory history = new CommandHistory();

    public void init() {
        JFrame frame = new JFrame("Text editor (type & use buttons, Luke!)");
        JPanel content = new JPanel();
        frame.setContentPane(content);
        frame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        content.setLayout(new BoxLayout(content, BoxLayout.Y_AXIS));
        textField = new JTextArea();
        textField.setLineWrap(true);
        content.add(textField);
        JPanel buttons = new JPanel(new FlowLayout(FlowLayout.CENTER));
        JButton ctrlC = new JButton("Ctrl+C");
        JButton ctrlX = new JButton("Ctrl+X");
        JButton ctrlV = new JButton("Ctrl+V");
        JButton ctrlZ = new JButton("Ctrl+Z");
        Editor editor = this;
        ctrlC.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                executeCommand(new CopyCommand(editor));
            }
        });
        ctrlX.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                executeCommand(new CutCommand(editor));
            }
        });
        ctrlV.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                executeCommand(new PasteCommand(editor));
            }
        });
        ctrlZ.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                undo();
            }
        });
        buttons.add(ctrlC);
        buttons.add(ctrlX);
        buttons.add(ctrlV);
        buttons.add(ctrlZ);
        content.add(buttons);
        frame.setSize(450, 200);
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }

    private void executeCommand(Command command) {
        if (command.execute()) {
            history.push(command);
        }
    }

    private void undo() {
        if (history.isEmpty()) return;

        Command command = history.pop();
        if (command != null) {
            command.undo();
        }
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.command.example;

import fatihes1.command.example.editor.Editor;

public class Demo {
    public static void main(String[] args) {
        Editor editor = new Editor();
        editor.init();
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.png`: Çalışma Çıktısı

![](https://refactoring.guru/images/patterns/examples/java/command/OutputDemo.png)
