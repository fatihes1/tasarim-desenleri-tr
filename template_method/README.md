#  Şablon Yöntemi (Template Method) Tasarım Deseni
 

##  💬 Amaç

Şablon Yöntemi, üst sınıftaki bir algoritmanın iskeletini tanımlayan ancak alt sınıfların, yapısını değiştirmeden algoritmanın belirli adımlarını geçersiz kılmasına olanak tanıyan davranışsal (behavioral) bir tasarım modelidir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/template-method/template-method-2x.png)

</div>


##  🙁 Problem

Kurumsal belgeleri analiz eden bir veri madenciliği uygulaması oluşturduğunuzu düşünün. Kullanıcılar uygulama belgelerini çeşitli formatlarda (PDF, DOC, CSV) yükler ve uygulama bu belgelerden anlamlı verileri tek tip bir formatta çıkarmaya çalışır.

Uygulamanın ilk sürümü yalnızca DOC dosyalarıyla çalışabildiğini düşünelim. Bir sonraki sürümde CSV dosyalarını destekleyebilir hale geldi. Bir ay sonra ona PDF dosyalarından veri çıkarmayı öğrettiniz.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/template-method/problem-2x.png)

*Veri madenciliği sınıfları çok sayıda yinelenen kod içerir.*

</div>

Bir noktada, her üç sınıfın da pek çok benzer koda sahip olduğunu fark ettiniz. Çeşitli veri formatlarıyla ilgilenmeye yönelik kod tüm sınıflarda tamamen farklı olsa da, veri işleme ve analize yönelik kod neredeyse aynıdır. Algoritma yapısını olduğu gibi bırakarak kod tekrarından kurtulmak harika olmaz mıydı?

Bu sınıfları kullanan istemci koduyla ilgili başka bir sorun daha olabilir. İşleme nesnesinin sınıfına bağlı olarak uygun bir eylem planı seçen birçok koşula sahiptir. Her üç işleme sınıfının da ortak bir arayüzü veya temel sınıfı olsaydı, müşteri kodundaki koşul şartlarını ortadan kaldırabilir ve bir işleme nesnesinde yöntemleri çağırırken çok biçimlilik (polymorphism) kullanabilirsiniz.

##  😊 Çözüm

Şablon Yöntemi modeli, bir algoritmayı bir dizi adıma ayırmanızı, bu adımları yöntemlere dönüştürmenizi ve sonrasında ise bu yöntemlere bir dizi çağrıyı tek bir şablon yönteminin içine yerleştirmenizi önerir. Adımlar soyut (abstract) olabilir veya bazı varsayılan uygulamalara (implementation) sahip olabilir. Algoritmayı kullanmak için istemcinin kendi alt sınıfını sağlaması (provide), tüm soyut adımları uygulaması ve gerekirse isteğe bağlı olanlardan bazılarını geçersiz kılması gerekir (ancak şablon yönteminin kendisini değil).

Bunun veri madenciliği uygulamamızda nasıl sonuçlanacağını görelim. Üç ayrıştırma algoritmasının tümü için bir temel sınıf oluşturabiliriz. Bu sınıf, çeşitli belge işleme adımlarına yapılan bir dizi çağrıdan oluşan bir şablon yöntemini tanımlar.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/template-method/solution-en-2x.png)

*Şablon yöntemi, algoritmayı adımlara bölerek alt sınıfların bu adımları geçersiz kılmasına izin verir, ancak asıl yöntemi geçersiz kılmaz.*

</div>

İlk başta, alt sınıfları bu yöntemler için kendi uygulamalarını sağlamaya zorlayarak tüm adımları soyut (`abstract`) olarak tanımlayabiliriz. Bizim durumumuzda, alt sınıflar zaten gerekli tüm uygulamalara sahiptir, bu nedenle yapmamız gereken tek şey, yöntemlerin imzalarını üst sınıfın yöntemleriyle eşleşecek şekilde ayarlamaktır.

Şimdi kopya koddan kurtulmak için neler yapabileceğimize bakalım. Dosyaları açma/kapatma ve verileri ayıklama/ayrıştırma kodu çeşitli veri formatları için farklı gibi görünüyor, dolayısıyla bu yöntemlere dokunmanın bir anlamı yok. Bununla birlikte, ham verilerin analiz edilmesi ve raporların oluşturulması gibi diğer adımların uygulanması da çok benzer olduğundan, alt sınıfların bu kodu paylaşabileceği temel bir sınıfa çekilebilir.

Gördüğünüz gibi iki tür adımımız var:
- soyut adımlar her alt sınıf tarafından uygulanmalıdır yani implement edilmelidir.
- isteğe bağlı adımların zaten bazı varsayılan uygulamaları vardır, ancak gerekirse yine de geçersiz kılınabilir

Kanca (Hooks) adı verilen başka bir adım türü daha var. Hook, boş gövdeli isteğe bağlı bir adımdır. Bir hook geçersiz kılınmasa bile şablon yöntemi işe yarar. Genellikle hook'lar, algoritmaların önemli adımlarından önce ve sonra yerleştirilerek alt sınıflara bir algoritma için ek uzantı noktaları sağlanır.


## 🚙 Gerçek Dünya Örneği

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/template-method/live-example-2x.png)

*Tipik bir mimari plan, müşterinin ihtiyaçlarına daha iyi uyacak şekilde biraz değiştirilebilir.*

</div>

Toplu konut inşaatlarında şablon yöntemi yaklaşımı kullanılabilir. Standart bir ev inşa etmeye yönelik mimari plan, potansiyel sahibinin ortaya çıkan evin bazı ayrıntılarını ayarlamasına olanak tanıyan birkaç uzatma noktası içerebilir.

Temelin döşenmesi, çerçeveleme, duvarların inşa edilmesi, su ve elektrik için sıhhi tesisat ve kabloların döşenmesi gibi her inşaat adımı, ortaya çıkan evi diğerlerinden biraz farklı kılmak için biraz değiştirilebilir.

##  ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/template-method/structure-2x.png)

</div>

1. **Soyut Sınıf (Abstract Class)**, bir algoritmanın adımları olarak hareket eden yöntemlerin yanı sıra bu yöntemleri belirli bir sırayla çağıran gerçek şablon yöntemini de tanımlar. Adımlar soyut (`abstract`) olarak bildirilebilir veya bazı varsayılan uygulamalara sahip olabilir.

2. **Concrete Classes** tüm adımları geçersiz kılabilir (override) ancak şablon yönteminin kendisini geçersiz kılamaz.

##  💻 Sözde Kod (Pseudocode)

Bu örnekte Şablon Yöntemi modeli, basit bir strateji video oyununda yapay zekanın çeşitli dalları için bir **iskelet (skeleton)** sağlar.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/template-method/example-2x.png)

*Basit bir video oyununun yapay zeka sınıfları.*

</div>

Oyundaki tüm ırklar neredeyse aynı türde birimlere ve binalara sahiptir. Bu nedenle, bazı ayrıntıları geçersiz kılabilirken aynı yapay zeka yapısını çeşitli ırklar için yeniden kullanabilirsiniz. Bu yaklaşımla, orkların yapay zekasını geçersiz kılarak onu daha agresif hale getirebilir, insanları daha savunma odaklı hale getirebilir ve canavarların hiçbir şey inşa etmesini engelleyebilirsiniz. Oyuna yeni bir ırk eklemek, yeni bir AI alt sınıfı oluşturmayı ve temel AI sınıfında belirtilen varsayılan yöntemlerin geçersiz kılınmasını gerektirecektir.

```java
// Soyut sınıf, genellikle soyut ilkel işlemlere çağrılar içeren bir algoritmanın şablon yöntemini tanımlar.
// Somut alt sınıflar bu işlemleri uygular, ancak şablon yöntemini kendisiyle bırakır.
class GameAI is
    // Şablon yöntem, bir algoritmanın şablonunu tanımlar.
    method turn() is
        collectResources()
        buildStructures()
        buildUnits()
        attack()

    // Adımların bazıları doğrudan temel sınıfta uygulanabilir.
    method collectResources() is
        foreach (s in this.builtStructures) do
            s.collect()

    // Ve bazıları soyut olarak tanımlanabilir.
    abstract method buildStructures()
    abstract method buildUnits()

    // Bir sınıfın birden fazla şablon yöntemi olabilir.
    method attack() is
        enemy = closestEnemy()
        if (enemy == null)
            sendScouts(map.center)
        else
            sendWarriors(enemy.position)

    abstract method sendScouts(position)
    abstract method sendWarriors(position)

// Somut sınıflar, temel sınıfın tüm soyut işlemlerini uygulamak zorundadır,
// ancak şablon yöntemini kendisi ile geçersiz kılmamalıdır.
class OrcsAI extends GameAI is
    method buildStructures() is
        if (there are some resources) then
            // Çiftlikler, ardından kışlalar, ardından kale inşa et.

    method buildUnits() is
        if (there are plenty of resources) then
            if (there are no scouts)
                // Peon inşa et, kesifçi grubuna ekle.
            else
                // Grunt inşa et, savaşçı grubuna ekle.

    // ...

    method sendScouts(position) is
        if (scouts.length > 0) then
            // Kesifçileri pozisyona gönder.

    method sendWarriors(position) is
        if (warriors.length > 5) then
            // Savaşçıları pozisyona gönder.

// Alt sınıflar ayrıca bazı işlemleri varsayılan bir uygulama ile geçersiz kılabilir.
class MonstersAI extends GameAI is
    method collectResources() is
        // Canavarlar kaynak toplamazlar.

    method buildStructures() is
        // Canavarlar yapı inşa etmezler.

    method buildUnits() is
        // Canavarlar birim inşa etmezler.

```


##  💡Uygulanabilirlik


**🐞 İstemcilerin bir algoritmanın tamamını veya yapısını değil, yalnızca belirli adımlarını genişletmelerine izin vermek istediğinizde Şablon Yöntemi modelini kullanın.**

⚡️ Şablon Yöntemi, monolitik bir algoritmayı, bir üst sınıfta tanımlanan yapıyı korurken alt sınıflar tarafından kolayca genişletilebilen bir dizi ayrı adıma dönüştürmenize olanak tanır.

----------------

**🐞 Bazı küçük farklılıklar dışında hemen hemen aynı algoritmaları içeren birkaç sınıfınız olduğunda bu modeli kullanın. Sonuç olarak algoritma değiştiğinde tüm sınıfları değiştirmeniz gerekebilir.**

⚡️ Böyle bir algoritmayı şablon yöntemine dönüştürdüğünüzde, benzer uygulamalara sahip adımları da bir üst sınıfa çekerek kod tekrarını ortadan kaldırabilirsiniz. Alt sınıflar arasında değişen kodlar alt sınıflarda kalabilir.

----------------

**🐞 Bir sınıfın iş mantığını, o mantık bağlamında o kadar önemli olmayabilecek algoritmaların uygulama ayrıntılarından ayırmak için modeli kullanın.**

⚡️ Strateji modeli, kodu, dahili verileri ve çeşitli algoritmaların bağımlılıklarını kodun geri kalanından ayırmanıza olanak tanır. Çeşitli istemciler, algoritmaları yürütmek ve bunları çalışma zamanında değiştirmek için basit bir arayüze sahip olur.


##  📝 Nasıl Uygulanır?

1. Adımlara ayırıp ayıramayacağınızı görmek için hedef algoritmayı analiz edin. Hangi adımların tüm alt sınıflar için ortak olduğunu ve hangilerinin ise benzersiz olacağını düşünün.

2. Soyut temel sınıfı (abstract base class) oluşturun. Şablon yöntemini ve algoritmanın adımlarını temsil eden bir dizi soyut yöntemi tanımlayın. İlgili adımları yürüterek şablon yöntemindeki algoritmanın yapısını ana hatlarıyla belirtin. Alt sınıfların geçersiz kılmasını (override) önlemek için şablon yöntemini sonlandırmayı (`final`) göz önüne alabilirsiniz.

3. Tüm adımların soyut olması sorun değildir. Ancak bazı adımların varsayılan uygulamaya (default implementation) sahip olması faydalı olabilir. Alt sınıfların bu yöntemleri uygulaması gerekmez.

4. Algoritmanın önemli adımları arasına hooks eklemeyi düşünün.

5. Algoritmanın her varyasyonu için yeni bir soyut olmayan alt sınıf oluşturun. Bu sınıf, tüm soyut (abstract) adımları uygulamalıdır, ancak isteğe bağlı olanlardan bazılarını da geçersiz (override) kılabilir.

##  ⚖️ Artıları ve Eksileri

✅  İstemcilerin büyük bir algoritmanın yalnızca belirli bölümlerini geçersiz kılmasına izin vererek, algoritmanın diğer bölümlerinde meydana gelen değişikliklerden daha az etkilenmelerini sağlayabilirsiniz.

✅ Yinelenen kodu bir üst sınıfa çekebilirsiniz.

❌ Bazı istemciler, bir algoritmanın sağlanan iskeletiyle sınırlı olabilir.

❌ Bir alt sınıf aracılığıyla varsayılan adım uygulamasını bastırarak Liskov Değiştirme (Liskov Substitution) İlkesini ihlal edebilirsiniz.

❌ Şablon yöntemlerinin sahip olduğu adımların sürdürülmesi daha zor olma eğilimindedir.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Fabrika Yöntemi (Factory Method)**, **Şablon Yönteminin (Template Method)** bir uzmanlığıdır. Aynı zamanda bir Fabrika Yöntemi, büyük bir Şablon Yönteminin bir adımı olarak da hizmet edebilir.

- **Şablon Yönteminin (Template Method)** kalıtıma (inheritance) dayalıdır: bir algoritmanın bölümlerini, bu bölümleri alt sınıflara genişleterek değiştirmenize olanak tanır. **Strateji (Strategy)** tasarım deseni ise kompozisyona dayalıdır: Nesnenin davranışının bazı kısımlarını, ona o davranışa karşılık gelen farklı stratejiler sağlayarak değiştirebilirsiniz. Şablon Yöntemi sınıf düzeyinde çalışır, dolayısıyla statiktir. Strateji nesne düzeyinde çalışır ve çalışma zamanında davranışları değiştirmenize olanak tanır.
  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Şablon Yöntemi modeli Java çerçevelerinde oldukça yaygındır. Geliştiriciler bunu genellikle çerçeve kullanıcılarına kalıtım kullanarak standart işlevselliği genişletmenin basit bir yolunu sağlamak için kullanırlar.

Java 8, Strateji modeline daha basit alternatifler olarak hizmet edebilecek lambda işlevlerinin desteğini getirdi.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.io.InputStream`](http://docs.oracle.com/javase/8/docs/api/java/io/InputStream.html),  [`java.io.OutputStream`](http://docs.oracle.com/javase/8/docs/api/java/io/OutputStream.html),  [`java.io.Reader`](http://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)  ve [`java.io.Writer`](http://docs.oracle.com/javase/8/docs/api/java/io/Writer.html) soyut olmayan tüm yöntemleri.
    
-   [`java.util.AbstractList`](http://docs.oracle.com/javase/8/docs/api/java/util/AbstractList.html),  [`java.util.AbstractSet`](http://docs.oracle.com/javase/8/docs/api/java/util/AbstractSet.html)  ve  [`java.util.AbstractMap`](http://docs.oracle.com/javase/8/docs/api/java/util/AbstractMap.html) soyut olmayan tüm yöntemleri.
    
- [`javax.servlet.http.HttpServlet`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html) sınıfı, tüm doXXX() yöntemleri varsayılan olarak HTTP 405 "Yönteme İzin Verilmiyor" hatasını gönderir. Ancak farklı bir yanıt göndermek için bu yöntemlerden herhangi birini geçersiz kılabilirsiniz.

**Tanımlama:** Şablon Yöntemi, temel sınıfta soyut veya boş bir dizi başka yöntemi çağıran bir yöntem görürseniz tanınabilir.

####  Bir algoritmanın standart adımlarını geçersiz kılma

Bu örnekte Şablon Yöntemi modeli, bir sosyal ağla çalışmanın algoritmasını tanımlar. Belirli bir sosyal ağla eşleşen alt sınıflar, bu adımları sosyal ağın sağladığı API'ye göre uygular.


**📁 networks**

⤵️ 📄 `networks/Network.java`: Temel sosyal ağ sınıfı

```java
package fatihes1.template_method.example.networks;

/**
 * Base class of social network.
 */
public abstract class Network {
    String userName;
    String password;

    Network() {}

    /**
     * Publish the data to whatever network.
     */
    public boolean post(String message) {
        // Authenticate before posting. Every network uses a different
        // authentication method.
        if (logIn(this.userName, this.password)) {
            // Send the post data.
            boolean result =  sendData(message.getBytes());
            logOut();
            return result;
        }
        return false;
    }

    abstract boolean logIn(String userName, String password);
    abstract boolean sendData(byte[] data);
    abstract void logOut();
}
```

⤵️ 📄 `networks/Facebook.java`

```java
package fatihes1.template_method.example.networks;

/**
 * Class of social network
 */
public class Facebook extends Network {
    public Facebook(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    public boolean logIn(String userName, String password) {
        System.out.println("\nChecking user's parameters");
        System.out.println("Name: " + this.userName);
        System.out.print("Password: ");
        for (int i = 0; i < this.password.length(); i++) {
            System.out.print("*");
        }
        simulateNetworkLatency();
        System.out.println("\n\nLogIn success on Facebook");
        return true;
    }

    public boolean sendData(byte[] data) {
        boolean messagePosted = true;
        if (messagePosted) {
            System.out.println("Message: '" + new String(data) + "' was posted on Facebook");
            return true;
        } else {
            return false;
        }
    }

    public void logOut() {
        System.out.println("User: '" + userName + "' was logged out from Facebook");
    }

    private void simulateNetworkLatency() {
        try {
            int i = 0;
            System.out.println();
            while (i < 10) {
                System.out.print(".");
                Thread.sleep(500);
                i++;
            }
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
    }
}
```

⤵️ 📄 `networks/Twitter.java`

```java
package fatihes1.template_method.example.networks;

/**
 * Class of social network
 */
public class Twitter extends Network {

    public Twitter(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    public boolean logIn(String userName, String password) {
        System.out.println("\nChecking user's parameters");
        System.out.println("Name: " + this.userName);
        System.out.print("Password: ");
        for (int i = 0; i < this.password.length(); i++) {
            System.out.print("*");
        }
        simulateNetworkLatency();
        System.out.println("\n\nLogIn success on Twitter");
        return true;
    }

    public boolean sendData(byte[] data) {
        boolean messagePosted = true;
        if (messagePosted) {
            System.out.println("Message: '" + new String(data) + "' was posted on Twitter");
            return true;
        } else {
            return false;
        }
    }

    public void logOut() {
        System.out.println("User: '" + userName + "' was logged out from Twitter");
    }

    private void simulateNetworkLatency() {
        try {
            int i = 0;
            System.out.println();
            while (i < 10) {
                System.out.print(".");
                Thread.sleep(500);
                i++;
            }
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
    }
}
```


⤵️ 📄 `Demo.java`: İstemci kodu

```java
package fatihes1.template_method.example;

import fatihes1.template_method.example.networks.Facebook;
import fatihes1.template_method.example.networks.Network;
import fatihes1.template_method.example.networks.Twitter;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    public static void main(String[] args) throws IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        Network network = null;
        System.out.print("Input user name: ");
        String userName = reader.readLine();
        System.out.print("Input password: ");
        String password = reader.readLine();

        // Enter the message.
        System.out.print("Input message: ");
        String message = reader.readLine();

        System.out.println("\nChoose social network for posting message.\n" +
                "1 - Facebook\n" +
                "2 - Twitter");
        int choice = Integer.parseInt(reader.readLine());

        // Create proper network object and send the message.
        if (choice == 1) {
            network = new Facebook(userName, password);
        } else if (choice == 2) {
            network = new Twitter(userName, password);
        }
        network.post(message);
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalışma Çıktısı

```
Input user name: Jhonatan
Input password: qswe
Input message: Hello, World!

Choose social network for posting message.
1 - Facebook
2 - Twitter
2

Checking user's parameters
Name: Jhonatan
Password: ****
..........

LogIn success on Twitter
Message: 'Hello, World!' was posted on Twitter
User: 'Jhonatan' was logged out from Twitter
```
