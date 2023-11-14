#  Gözlemci (Observer) Tasarım Deseni

> Diğer Adıyla: Etkinlik Abonesi (Event-Subscriber), Dinleyici (Listener)
 

##  💬 Amaç

Gözlemci, birden çok nesneyi, gözlemledikleri nesnenin başına gelen olaylar hakkında bilgilendirmek için bir abonelik mekanizması tanımlamanıza olanak tanıyan davranışsal (behavioral) bir tasarım modelidir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/observer/observer-2x.png?id=d5a83e115528e9fd633f04ad2650f1db)

</div>

##  🙁 Problem

İki tür nesneniz olduğunu hayal edin: Müşteri ve Mağaza (`Customer` ve `Store`). Müşteri, çok yakında mağazada satışa sunulacak belirli bir ürün markasıyla (örneğin, iPhone'un yeni bir modeliyle) çok ilgileniyor.

Müşteri her gün mağazayı ziyaret edebilir ve ürünün stok durumunu kontrol edebilir. Ancak ürün hâlâ yoldayken bu gezilerin çoğu anlamsız olacaktır.

<div align="center">

![](https://refactoring.guru/images/patterns/content/observer/observer-comic-1-en-2x.png?id=8e89674eb83b01e82203987e600ba59e)

*Mağazayı ziyaret etme ve spam gönderme karşılaştırması*

</div>

Öte yandan, mağaza her yeni ürün piyasaya çıktığında tüm müşterilerine tonlarca e-posta gönderebilir. Bu e-postalar spam olarak kabul edilebilir. Bu, bazı müşterileri mağazaya bitmek bilmeyen yolculuklardan kurtaracaktır. Aynı zamanda yeni ürünlerle ilgilenmeyen diğer müşterilerinin de canını sıkacaktır.

Görünüşe göre aramızda bir anlaşmazlık var. Ya müşteri ürünün mevcut olup olmadığını kontrol etmek için zaman harcar ya da mağaza yanlış müşterilere bildirimde bulunarak kaynaklarını israf eder.

##  😊 Çözüm

İlgilenen/gözlenecek bir durumu olan nesneye genellikle konu (subject) adı verilir, ancak durumundaki değişiklikler hakkında diğer nesneleri de bilgilendireceği için ona yayıncı adını vereceğiz. Yayıncının durumundaki değişiklikleri izlemek isteyen diğer tüm nesnelere aboneler (subscribers) adı verilir.

Observer modeli, yayıncı sınıfına bir abonelik mekanizması eklemenizi ve böylece tek tek nesnelerin o yayıncıdan gelen bir olay akışına abone olabilmesini veya abonelikten çıkabilmesini önerir. Merak etmeyin! Durum göründüğü kadar karmaşık değil. Gerçekte, bu mekanizma;
 1.  abone nesnelerine yapılan referansların listesini depolamak için bir dizi alanından,
 2. abonelerin bu listeye eklenmesine ve listeden çıkarılmasına izin veren çeşitli genel yöntemlerden oluşur.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/observer/solution1-en-2x.png?id=a6bc643488b8fbc8bbb309539139c316)

*Abonelik mekanizması, tek tek nesnelerin etkinlik bildirimlerine abone olmasına olanak tanır.*

</div>

Artık yayıncının başına önemli bir olay geldiğinde abonelerinin üzerinden geçer ve nesnelerine özel bildirim yöntemini çağırır.

Gerçek uygulamalarda, aynı yayıncı sınıfının etkinliklerini izlemekle ilgilenen düzinelerce farklı abone sınıfı bulunabilir. Yayıncıyı tüm bu sınıflara bağlamak istemezsiniz. Ayrıca, eğer yayıncı sınıfınızın başkaları tarafından kullanılması gerekiyorsa, bunların bazılarını önceden bilmiyor olabilirsiniz.

Bu nedenle tüm abonelerin aynı arayüzü uygulaması ve yayıncının onlarla yalnızca bu arayüz üzerinden iletişim kurması çok önemli. Bu arayüz, yayıncının bildirimle birlikte bazı bağlamsal verileri iletmek için kullanabileceği bir dizi parametreyle birlikte bildirim yöntemini beyan etmelidir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/observer/solution2-en-2x.png?id=630cfb84753c258aa4e8500e189c0b65)

*Yayıncı, abonelerin nesnelerinde belirli bildirim yöntemini çağırarak aboneleri bilgilendirir.*

</div>

Uygulamanızda birden fazla farklı yayıncı türü varsa ve abonelerinizi hepsiyle uyumlu hale getirmek istiyorsanız daha da ileri giderek tüm yayıncıların aynı arayüzü izlemesini sağlayabilirsiniz. Bu arayüzün yalnızca birkaç abonelik yöntemini tanımlaması gerekir. Arayüz, abonelerin yayıncıların durumlarını sınıflarıyla bağlantı kurmadan gözlemlemelerine olanak tanıyacak.

## 🚙 Gerçek Dünya Örneği

<div align="center">

![](https://refactoring.guru/images/patterns/content/observer/observer-comic-2-en-2x.png?id=2147046fb16c427533db8ed85e8cce4c)

*Dergi ve gazete abonelikleri.*

</div>

Bir gazete veya dergiye abone olursanız, bir sonraki sayının mevcut olup olmadığını kontrol etmek için artık mağazaya gitmenize gerek yoktur. Bunun yerine, yayıncı yeni sayıları yayınlandıktan hemen sonra veya hatta önceden doğrudan posta kutunuza gönderir.

Yayıncı, abonelerin bir listesini tutar ve hangi dergilerle ilgilendiklerini bilir. Aboneler, yayıncının kendilerine yeni dergi sayıları göndermesini engellemek istediklerinde listeden istedikleri zaman ayrılabilirler.


##  ⚙️ Yapı

<div align="center">
	
![](https://refactoring.guru/images/patterns/diagrams/observer/structure-2x.png?id=228af9bded4d6ee6daf43a0e23cca9ff)

</div>

1. **Yayıncı (Publisher)** diğer nesnelerin ilgisini çeken olayları yayınlar. Bu olaylar, yayıncı durumunu değiştirdiğinde veya bazı davranışları yürüttüğünde meydana gelir. Yayıncılar, listeye yeni abonelerin katılmasına ve mevcut abonelerin listeden çıkmasına olanak tanıyan bir abonelik altyapısı içerir.

2. Yeni bir olay meydana geldiğinde, yayıncı abonelik listesine gider ve her abone nesnesindeki abone arayüzünde bildirilen bildirim yöntemini çağırır.

3. **Abone (Subscriber)** arayüzü bildirim arayüzünü bildirir. Çoğu durumda tek bir güncelleme (`update`) yönteminden oluşur. Yöntem, yayıncının güncellemeyle birlikte bazı etkinlik ayrıntılarını iletmesine olanak tanıyan birkaç parametreye sahip olabilir.

4. **Concrete Subscribers**, yayıncının yayınladığı bildirimlere yanıt olarak bazı eylemler gerçekleştirir. Yayıncının somut sınıflara bağlanmaması için bu sınıfların tümü aynı arayüzü uygulamalıdır.

5. Genellikle abonelerin güncellemeyi doğru bir şekilde gerçekleştirebilmeleri için bazı bağlamsal bilgilere ihtiyaçları vardır. Bu nedenle yayıncılar genellikle bazı bağlam verilerini bildirim yönteminin argümanları olarak iletirler. Yayıncı, abonenin gerekli verileri doğrudan almasına izin vererek kendisini bir argüman olarak aktarabilir.

6. **İstemci (Client)**, yayıncı ve abone nesnelerini ayrı ayrı oluşturur ve ardından aboneleri yayıncı güncellemeleri için kaydeder.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte **Observer modeli**, metin düzenleyici nesnesinin durumundaki değişiklikler hakkında diğer hizmet nesnelerine bildirimde bulunmasına olanak tanır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/observer/example-2x.png?id=e2838e1562325e485fc7c2828a8ca445)

*Nesneleri, diğer nesnelerin başına gelen olaylar hakkında bilgilendirmek.*

</div>

Abonelerin listesi dinamik olarak derlenir: Nesneler, uygulamanızın istenen davranışına bağlı olarak çalışma zamanında bildirimleri dinlemeye başlayabilir veya durdurabilir.

Bu uygulamada editör sınıfı abonelik listesini tek başına tutmaz. Bu işi, tam da buna ayrılmış özel yardımcı nesneye devreder. Bu nesneyi, merkezi bir olay gönderici olarak hizmet verecek şekilde yükseltebilir ve herhangi bir nesnenin yayıncı olarak hareket etmesine izin verebilirsiniz.

Programa yeni abonelerin eklenmesi, tüm abonelerle aynı arayüz üzerinden çalıştığı sürece mevcut yayıncı sınıflarında değişiklik yapılmasını gerektirmez.

```java
// Temel yayıncı sınıfı, abonelik yönetimi kodunu ve bildirim yöntemlerini içerir.
class EventManager is
    private field listeners: hash map of event types and listeners

    method subscribe(eventType, listener) is
        listeners.add(eventType, listener)

    method unsubscribe(eventType, listener) is
        listeners.remove(eventType, listener)

    method notify(eventType, data) is
        foreach (listener in listeners.of(eventType)) do
            listener.update(data)

// Somut yayıncı, bazı aboneler için ilginç olan gerçek iş mantığını içerir.
// Bu sınıfı temel yayıncıdan türetmeyi düşünebiliriz, ancak bu her zaman mümkün
// olmayabilir çünkü somut yayıncı zaten bir alt sınıf olabilir.
// Bu durumda, burada olduğu gibi bileşimle abonelik mantığını düzeltebilirsiniz.
class Editor is
    public field events: EventManager
    private field file: File

    constructor Editor() is
        events = new EventManager()

    // İş mantığının yöntemleri abonelere değişiklikler hakkında bilgi verebilir.
    method openFile(path) is
        this.file = new File(path)
        events.notify("open", file.name)

    method saveFile() is
        file.write()
        events.notify("save", file.name)

    // ...


// İşte abone arayüzü. Programlama diliniz işlevsel türleri destekliyorsa,
// abone hiyerarşisini tamamen bir dizi işlevle değiştirebilirsiniz.
interface EventListener is
    method update(filename)

// Somut aboneler, bağlı oldukları yayıncı tarafından verilen güncellemelere tepki verir.
class LoggingListener implements EventListener is
    private field log: File
    private field message: string

    constructor LoggingListener(log_filename, message) is
        this.log = new File(log_filename)
        this.message = message

    method update(filename) is
        log.write(replace('%s',filename,message))

class EmailAlertsListener implements EventListener is
    private field email: string
    private field message: string

    constructor EmailAlertsListener(email, message) is
        this.email = email
        this.message = message

    method update(filename) is
        system.email(email, replace('%s',filename,message))

// Bir uygulama, yayıncıları ve aboneleri çalışma zamanında yapılandırabilir.
class Application is
    method config() is
        editor = new Editor()

        logger = new LoggingListener(
            "/path/to/log.txt",
            "Someone has opened the file: %s")
        editor.events.subscribe("open", logger)

        emailAlerts = new EmailAlertsListener(
            "admin@example.com",
            "Someone has changed the file: %s")
        editor.events.subscribe("save", emailAlerts)

```


##  💡Uygulanabilirlik


**🐞 Bir nesnenin durumundaki değişikliklerin diğer nesnelerin değiştirilmesini gerektirebileceği ve gerçek nesne kümesinin önceden bilinmediği veya dinamik olarak değiştiği durumlarda Observer modelini kullanın.**

⚡️ Grafiksel kullanıcı arayüzünün sınıflarıyla çalışırken bu sorunu sıklıkla yaşayabilirsiniz. Örneğin, özel düğme sınıfları oluşturdunuz ve istemcilerin, kullanıcı bir düğmeye bastığında etkinleşmesi için düğmelerinize bazı özel kodlar bağlamasına izin vermek istiyorsunuz.

Gözlemci modeli, abone arayüzünü uygulayan herhangi bir nesnenin, yayıncı nesnelerindeki olay bildirimlerine abone olmasını sağlar. Abonelik mekanizmasını düğmelerinize ekleyerek istemcilerin özel abone sınıfları aracılığıyla özel kodlarını bağlamalarına olanak tanıyabilirsiniz.

----------------

**🐞 Uygulamanızdaki bazı nesnelerin diğerlerini gözlemlemesi gerektiğinde, ancak yalnızca sınırlı bir süre için veya belirli durumlarda bu modeli kullanın.**

⚡️ Abonelik listesi dinamik olduğundan aboneler ihtiyaç duydukları anda listeye katılabilir veya listeden ayrılabilirler.
  

##  📝 Nasıl Uygulanır?

1. İş mantığınızı gözden geçirin ve işlevselliği iki bölüme ayırmaya çalışın: diğer kodlardan bağımsız olan temel işlevsellik, yayıncı olarak hareket edecektir; geri kalanı bir dizi abone sınıfına dönüşecektir.

2. Abone arabirimini tanımlayın. En azından tek bir güncelleme yöntemini barındırmalıdır.

3. Yayıncı arabirimini tanımlayın ve bir abone nesnesini bir listeye eklemek veya onu çıkarmak için bir çift yöntemi tanımlayın. Unutmayın ki yayıncılar, abonelerle yalnızca abone arabirimi aracılığıyla çalışmalıdır.

4. Gerçek abonelik listesini ve abonelik yöntemlerinin uygulanmasını nereye yerleştireceğinize karar verin. Genellikle bu kod, tüm yayın türleri için aynı görünür, bu nedenle koymak için en uygun yer, doğrudan yayıncı arabiriminden türetilmiş soyut bir sınıftır. Somut yayıncılar bu sınıfı genişleterek abonelik davranışını devralırlar.
Ancak, deseni mevcut bir sınıf hiyerarşisine uyguluyorsanız, kompozisyona dayalı bir yaklaşımı düşünün: abonelik mantığını ayrı bir nesneye koyun ve tüm gerçek yayıncıların bunu kullanmasını sağlayın.

5. Somut yayıncı sınıfları oluşturun. Bir yayıncı içinde önemli bir şey olduğunda, bu durum tüm abonelerine bildirilmelidir.

6. Somut abone sınıflarında güncelleme bildirim yöntemlerini uygulayın. Çoğu abone, etkinlikle ilgili bağlam verilerine ihtiyaç duyacaktır. Bu veriler, bildirim yönteminin bir argümanı olarak iletilir.
Ancak başka bir seçenek de vardır. Bildirim aldıktan sonra, abone, verileri doğrudan bildirimden alabilir. Bu durumda, yayıncı kendisini güncelleme yöntemi aracılığıyla iletmelidir. Daha az esnek seçenek, bir yayıncıyı kalıcı olarak bir aboneye bağlamaktır, bunu yaparken yapıcı aracılığıyla.

7. İstemci, tüm gerekli aboneleri oluşturmalı ve bunları uygun yayıncılara kaydetmelidir.

##  ⚖️ Artıları ve Eksileri

✅  Açık/Kapalı Prensibi (Open/Closed Principle): Yayıncının kodunu değiştirmek zorunda kalmadan yeni abone sınıfları tanıtabilirsiniz (ve yayıncı arayüzü varsa tam tersi).

✅ Çalışma zamanında nesneler arasında ilişkiler kurabilirsiniz.


❌ Abonelere rastgele sırayla bilgi verilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Sorumluluk Zinciri (Chain of Responsibility)**, **Komuta (Command)**, **Arabulucu (Mediator)** ve **Gözlemci (Observer)**, isteklerin göndericileri ve alıcıları arasında bağlantı kurmanın çeşitli yollarını ele alır:
	- Sorumluluk Zinciri, bir talebi potansiyel alıcılardan oluşan dinamik bir zincir boyunca, içlerinden biri bunu yerine getirene kadar sırayla iletir.
	- Komut, gönderenler ve alıcılar arasında tek yönlü bağlantılar kurar.
	-  Arabulucu, gönderenler ve alıcılar arasındaki doğrudan bağlantıları ortadan kaldırarak onları bir aracı nesne aracılığıyla dolaylı olarak iletişim kurmaya zorlar.
	- Observer, alıcıların dinamik olarak alma isteklerine abone olmalarını ve abonelikten çıkmalarını sağlar.

- Arabulucu ve Gözlemci arasındaki fark çoğu zaman anlaşılması zordur. Çoğu durumda bu modellerden herhangi birini uygulayabilirsiniz; ancak bazen ikisini aynı anda uygulayabilirsiniz. Bunu nasıl yapabileceğimize bakalım.
Mediator'ın temel amacı, bir dizi sistem bileşeni arasındaki karşılıklı bağımlılıkları ortadan kaldırmaktır. Bunun yerine, bu bileşenler tek bir aracı nesneye bağımlı hale gelir. Observer'ın amacı, bazı nesnelerin diğerlerinin astı gibi davrandığı nesneler arasında dinamik tek yönlü bağlantılar kurmaktır.
Arabulucu modelinin Observer'a dayanan popüler bir uygulaması var. Arabulucu nesnesi yayıncı rolünü oynar ve bileşenler, arabulucunun etkinliklerine abone olan ve bu etkinliklerden aboneliği iptal eden aboneler olarak hareket eder. Mediator bu şekilde uygulandığında Observer'a çok benzeyebilir.
Kafanız karıştığında Mediator modelini başka yollarla da uygulayabileceğinizi unutmayın. Örneğin, tüm bileşenleri kalıcı olarak aynı aracı nesneye bağlayabilirsiniz. Bu uygulama Observer'a benzemeyecek ancak yine de Mediator modelinin bir örneği olacaktır.
Şimdi tüm bileşenlerin yayıncı haline geldiği ve birbirleri arasında dinamik bağlantılara izin verdiği bir program hayal edin. Merkezi bir aracı nesne olmayacak, yalnızca dağıtılmış bir gözlemci kümesi olacak.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Observer modeli Java kodunda, özellikle de GUI bileşenlerinde oldukça yaygındır. Diğer nesnelerde meydana gelen olaylara, sınıflarıyla eşleşmeden tepki vermenin bir yolunu sağlar.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.util.Observer`](http://docs.oracle.com/javase/8/docs/api/java/util/Observer.html)/[`java.util.Observable`](http://docs.oracle.com/javase/8/docs/api/java/util/Observable.html)  (gerçek dünyada nadiren kullanılır)
-   Tüm uygulamaları  [`java.util.EventListener`](http://docs.oracle.com/javase/8/docs/api/java/util/EventListener.html)  (Swing bileşenlerinin hemen hemen her yerinde)
-   [`javax.servlet.http.HttpSessionBindingListener`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpSessionBindingListener.html)
-   [`javax.servlet.http.HttpSessionAttributeListener`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpSessionAttributeListener.html)
-   [`javax.faces.event.PhaseListener`](http://docs.oracle.com/javaee/7/api/javax/faces/event/PhaseListener.html)

**Tanımlama:** Gelen nesneleri bir listede saklayan bir abonelik yöntemi görürseniz bu model tanınabilir. Bu listedeki nesneler üzerinde yinelenen ve onların "güncelleme" yöntemini çağıran bir tür bildirim yöntemi görürseniz, tanımlamayı onaylayabilirsiniz.

 
####  Etkinlik aboneliği

Bu örnekte Observer modeli, bir metin düzenleyicinin nesneleri arasında dolaylı işbirliği kurar. `Editor` nesnesi her değiştiğinde abonelerine bilgi verir. `EmailNotificationListener` ve `LogOpenListener`, birincil davranışlarını yürüterek bu bildirimlere tepki verir.

Abone sınıfları, editör sınıfına bağlanmaz ve gerektiğinde diğer uygulamalarda yeniden kullanılabilir. `Editor` sınıfı yalnızca soyut abone arayüzüne bağlıdır. Bu, editörün kodunu değiştirmeden yeni abone türlerinin eklenmesine olanak tanır.


**📁 publisher**

⤵️ 📄 `publisher/EventManager.java`: Basit Publisher Sınıfı

```java
package fatihes1.observer.example.publisher;

import fatihes1.observer.example.listeners.EventListener;

import java.io.File;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class EventManager {
    Map<String, List<EventListener>> listeners = new HashMap<>();

    public EventManager(String... operations) {
        for (String operation : operations) {
            this.listeners.put(operation, new ArrayList<>());
        }
    }

    public void subscribe(String eventType, EventListener listener) {
        List<EventListener> users = listeners.get(eventType);
        users.add(listener);
    }

    public void unsubscribe(String eventType, EventListener listener) {
        List<EventListener> users = listeners.get(eventType);
        users.remove(listener);
    }

    public void notify(String eventType, File file) {
        List<EventListener> users = listeners.get(eventType);
        for (EventListener listener : users) {
            listener.update(eventType, file);
        }
    }
}
```

**📁 editor**

⤵️ 📄 `editor/Editor.java`

```java
package fatihes1.observer.example.editor;

import fatihes1.observer.example.publisher.EventManager;

import java.io.File;

public class Editor {
    public EventManager events;
    private File file;

    public Editor() {
        this.events = new EventManager("open", "save");
    }

    public void openFile(String filePath) {
        this.file = new File(filePath);
        events.notify("open", file);
    }

    public void saveFile() throws Exception {
        if (this.file != null) {
            events.notify("save", file);
        } else {
            throw new Exception("Please open a file first.");
        }
    }
}
```

**📁 listeners**

⤵️ 📄 `listeners/EventListener.java`: Ortak Observer arayüzü

```java
package fatihes1.observer.example.listeners;

import java.io.File;

public interface EventListener {
    void update(String eventType, File file);
}
```

⤵️ 📄 `listeners/EmailNotificationListener.java`: Bildirim alındığında e-posta gönderir

```java
package fatihes1.observer.example.listeners;

import java.io.File;

public class EmailNotificationListener implements EventListener {
    private String email;

    public EmailNotificationListener(String email) {
        this.email = email;
    }

    @Override
    public void update(String eventType, File file) {
        System.out.println("Email to " + email + ": Someone has performed " + eventType + " operation with the following file: " + file.getName());
    }
}
```


⤵️ 📄 `listeners/LogOpenListener.java`: Bildirim alındığında log kaydı oluşturur

```java
package fatihes1.observer.example.listeners;

import java.io.File;

public class LogOpenListener implements EventListener {
    private File log;

    public LogOpenListener(String fileName) {
        this.log = new File(fileName);
    }

    @Override
    public void update(String eventType, File file) {
        System.out.println("Save to log " + log + ": Someone has performed " + eventType + " operation with the following file: " + file.getName());
    }
}
```

⤵️ 📄 `Demo.java`: Initialization kodu

```java
package fatihes1.observer.example;

import fatihes1.observer.example.editor.Editor;
import fatihes1.observer.example.listeners.EmailNotificationListener;
import fatihes1.observer.example.listeners.LogOpenListener;

public class Demo {
    public static void main(String[] args) {
        Editor editor = new Editor();
        editor.events.subscribe("open", new LogOpenListener("/path/to/log/file.txt"));
        editor.events.subscribe("save", new EmailNotificationListener("admin@example.com"));

        try {
            editor.openFile("test.txt");
            editor.saveFile();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalışma Çıktısı

```
Save to log \path\to\log\file.txt: Someone has performed open operation with the following file: test.txt
Email to admin@example.com: Someone has performed save operation with the following file: test.txt
```
