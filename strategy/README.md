#  Strateji (Strategy) Tasarım Deseni
 

##  💬 Amaç

Strateji, bir algoritma ailesi tanımlamanıza, her birini ayrı bir sınıfa yerleştirmenize ve nesnelerini birbirinin yerine kullanılabilir hale getirmenize olanak tanıyan davranışsal (behavioral) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/strategy/strategy-2x.png)


##  🙁 Problem

Bir gün sıradan gezginler için bir navigasyon uygulaması oluşturmaya karar verdiğinizi düşünelim. Uygulama, kullanıcıların herhangi bir şehirde hızlı bir şekilde yönlendirilmelerine yardımcı olan güzel bir harita etrafında şekillenmektedir.

Uygulamanın en çok talep edilen özelliklerinden biri de, süpriz olmayacağı üzere otomatik rota planlamadır. Kullanıcı bir adres girebilmeli ve o hedefe giden en hızlı rotanın haritada görüntülendiğini görebilmelidir.

Uygulamanın ilk sürümü yalnızca yollar üzerindeki rotaları oluşturabiliyordu. Arabayla seyahat eden vatandaşlar sevinçten havalara uçtu. Ancak görünen o ki herkes tatilde araba kullanmayı sevmiyor. Bir sonraki güncellemeyle birlikte yürüyüş rotaları oluşturma seçeneği eklediniz. Hemen ardından insanların güzergahlarında toplu taşımayı kullanmalarına imkan veren bir seçenek daha eklediniz.

Ancak bu yalnızca başlangıçtır. Daha sonra bisikletçiler için rota oluşturmayı eklemeyi düşünebilirsiniz. Ve daha sonra, bir şehrin tüm turistik mekanlarını dolaşacak rotalar oluşturmak için başka bir seçenek daha...

![](https://refactoring.guru/images/patterns/diagrams/strategy/problem-2x.png)

*Navigasyon uygulaması kodu şişirildi.*

İş açısından bakıldığında uygulama başarılı olsa da teknik kısım başınızı çok ağrıtacaktır. Her yeni yönlendirme algoritması eklediğinizde, gezginin (navigator) main sınıfının boyutu iki katına çıkar. Bir noktada bu devasa kodun bakımı çok zor hale gelebilir.

Algoritmalardan birinde yapılacak herhangi bir değişiklik, ister basit bir hata düzeltmesi ister sokak puanında hafif bir ayarlama olsun, tüm sınıfı etkiler ve halihazırda çalışmakta olan kodda hata oluşturma olasılığını da arttırır.

Ayrıca ekip çalışması verimsiz hale gelecektir. Başarılı sürümün hemen ardından işe alınan ekip arkadaşlarınız, birleştirme çakışmalarını (merge conflicts) çözmek için çok fazla zaman harcadıklarından şikayetçi olmaya başlayabilirler. Yeni bir özelliğin uygulanması için, diğer kişilerin ürettiği kodlarla çelişen (conflict) aynı büyük sınıfın kodlarını değiştirmenizi gerektirir.


##  😊 Çözüm

Strateji modeli, belirli bir şeyi birçok farklı yolla yapan bir sınıfı almanızı ve bu algoritmaların tümünü stratejiler adı verilen ayrı sınıflara çıkarmanızı önerir.

Bağlam (context) olarak adlandırılan orijinal sınıfın, stratejilerden birine referansı depolamak için bir alana sahip olması gerekir. Bağlam, işi kendi başına yürütmek yerine bağlantılı bir strateji nesnesine yürütme işini devreder.

Bağlam, iş için uygun bir algoritmanın seçilmesinden sorumlu değildir. Bunun yerine istemci (client) istenen stratejiyi bağlama aktarır. Aslında bağlam yani context, stratejiler hakkında pek bir şey bilmemektedir. Aynı genel arayüz (interface) üzerinden tüm stratejilerle çalışır; bu durum, seçilen strateji içinde kapsüllenmiş algoritmayı tetiklemek için yalnızca tek bir yöntemi ortaya çıkarır.

Bu şekilde bağlam stratejilerden bağımsız hale gelir, böylece bağlamın kodunu veya diğer stratejileri değiştirmeden yeni algoritmalar ekleyebilir veya mevcut algoritmaları değiştirebilirsiniz.

![](https://refactoring.guru/images/patterns/diagrams/strategy/solution-2x.png)

*Rota planlama stratejileri.*

Navigasyon uygulamamızda her yönlendirme algoritması tek bir `buildRoute` yöntemiyle kendi sınıfına çıkarılabilir (extracted). Yöntem bir başlangıç ​​ve varış noktasını parametre olarak bekler. Rotanın kontrol noktalarının bir koleksiyonunu döndürür.

Aynı argümanlar verildiğinde, her yönlendirme sınıfı farklı bir rota oluşturabilse de, asıl görevi harita üzerinde bir dizi kontrol noktası oluşturmak olduğundan, main `Navigator` sınıfı hangi algoritmanın seçildiğiyle pek ilgilenmez. Sınıfın, aktif yönlendirme stratejisini değiştirmeye yönelik bir yöntemi vardır. Bu yöntem sayesinde, kullanıcı arayüzündeki düğmeler gibi UI elementlerini kullanarak, halihazırda seçili olan yönlendirme davranışını başka bir davranışla değiştirebilir.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/content/strategy/strategy-comic-1-en-2x.png)


Havaalanına gitmeniz gerektiğini düşünün. Otobüse binebilir, taksi çağırabilir veya bisikletinize binebilirsiniz. Bunlar sizin ulaşım stratejilerinizdir. Bütçe veya zaman kısıtlaması gibi faktörlere bağlı olarak stratejilerden birini seçebilirsiniz.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/strategy/structure-2x.png)

1. **Bağlam (Context)**, stratejilerden birine referansı tutar ve bu nesneyle yalnızca strateji arayüzü aracılığıyla iletişim kurar.

2. **Strateji (Strategy)** arayüzü tüm stratejiler için ortaktır. Bağlamın bir stratejiyi yürütmek için kullandığı yöntemi bildirir.

3. **Concrete Strategies**, bağlamın kullandığı algoritmanın farklı varyasyonlarını uygular yani implement eder.

4. **Bağlam (Context)**, algoritmayı çalıştırmaya her ihtiyaç duyduğunda bağlantılı strateji nesnesindeki yürütme yöntemini çağırır. Bağlam ne tür bir stratejiyle çalıştığını veya algoritmanın nasıl yürütüldüğünü bilmez.

5. **İstemci (Client)** belirli bir strateji nesnesi oluşturur ve onu bağlama aktarır. Bağlam, istemcilerin çalışma zamanında bağlamla ilişkili stratejiyi değiştirmesine olanak tanıyan bir ayarlayıcıyı ortaya çıkarır.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte bağlam, çeşitli aritmetik işlemleri yürütmek için birden fazla strateji kullanıyor.

```java
// Strateji arayüzü, bir algoritmanın tüm desteklenen sürümleri için ortak olan
// işlemleri bildirir. Bağlam, bu arayüzü kullanarak somut stratejiler tarafından
// tanımlanan algoritmayı çağırır.
interface Strategy is
    method execute(a, b)

// Somut stratejiler, temel strateji arayüzünü takip ederek algoritmayı uygular.
// Bu arayüz, onları bağlamda değiştirilebilir hale getirir.
class ConcreteStrategyAdd implements Strategy is
    method execute(a, b) is
        return a + b

class ConcreteStrategySubtract implements Strategy is
    method execute(a, b) is
        return a - b

class ConcreteStrategyMultiply implements Strategy is
    method execute(a, b) is
        return a * b

// Bağlam, istemcilere ilgi duyduğu arayüzü tanımlar.
class Context is
    // Bağlam, strateji nesnelerinden birine bir başvuruyu sürdürür. Bağlam, bir
    // stratejinin somut sınıfını bilmez. Strateji arayüzü üzerinden tüm
    // stratejilerle çalışmalıdır.
    private strategy: Strategy

    // Genellikle bağlam, stratejiyi kurucu üzerinden kabul eder ve aynı zamanda
    // stratejinin çalışma zamanında değiştirilebilmesi için bir ayarlayıcı sağlar.
    method setStrategy(Strategy strategy) is
        this.strategy = strategy

    // Bağlam, bazı işi strateji nesnesine bırakır ve algoritmanın kendi üzerinde
    // birden fazla sürümünü uygulamak yerine strateji nesnesine yönlendirir.
    method executeStrategy(int a, int b) is
        return strategy.execute(a, b)

// İstemci kodu somut bir strateji seçer ve onu bağlama geçirir. İstemci, doğru
// seçimi yapabilmek için stratejiler arasındaki farkları bilmelidir.
class ExampleApplication is
    method main() is
        Bağlam nesnesi oluşturun.

        İlk sayıyı okuyun.
        Son sayıyı okuyun.
        Kullanıcı girişinden istenen işlemi okuyun.

        if (action == addition) then
            context.setStrategy(new ConcreteStrategyAdd())

        if (action == subtraction) then
            context.setStrategy(new ConcreteStrategySubtract())

        if (action == multiplication) then
            context.setStrategy(new ConcreteStrategyMultiply())

        Sonucu hesapla ve yazdır.

```


##  💡Uygulanabilirlik


**🐞 Bir nesne içinde bir algoritmanın farklı varyantlarını kullanmak ve çalışma zamanı sırasında bir algoritmadan diğerine geçiş yapabilmek istediğinizde Strateji modelini kullanın.**

⚡️ Strateji modeli, nesneyi belirli alt görevleri farklı şekillerde gerçekleştirebilen farklı alt nesnelerle ilişkilendirerek çalışma zamanındaki nesnenin davranışını dolaylı olarak değiştirmenize olanak tanır.

----------------

**🐞 Yalnızca bazı davranışları yürütme biçimleri farklılık gösteren çok sayıda benzer sınıfınız olduğunda Stratejiyi kullanın.**

⚡️ Strateji modeli, değişen davranışı ayrı bir sınıf hiyerarşisine çıkarmanıza ve orijinal sınıfları tek bir sınıf halinde birleştirmenize, böylece yinelenen kodu azaltmanıza olanak tanır.

----------------

**🐞 Bir sınıfın iş mantığını, o mantık bağlamında o kadar önemli olmayabilecek algoritmaların uygulama ayrıntılarından ayırmak için modeli kullanın.**

⚡️ Strateji modeli, kodu, dahili verileri ve çeşitli algoritmaların bağımlılıklarını kodun geri kalanından ayırmanıza olanak tanır. Çeşitli istemciler, algoritmaları yürütmek ve bunları çalışma zamanında değiştirmek için basit bir arayüze sahip olur.

----------------

**🐞 Sınıfınızda aynı algoritmanın farklı varyantları arasında geçiş yapan çok büyük bir koşullu ifade olduğunda bu modeli kullanın.**

⚡️ Strateji modeli, tüm algoritmaları, tümü aynı arayüzü uygulayan ayrı sınıflara ayırarak bu tür bir koşulluluğu ortadan kaldırmanıza olanak tanır. Orijinal nesne, algoritmanın tüm değişkenlerini uygulamak yerine, yürütmeyi bu nesnelerden birine devreder.


##  📝 Nasıl Uygulanır?

1. Bağlam sınıfında sık sık değişiklik yapmaya yatkın bir algoritma tanımlayın. Ayrıca çalışma zamanında aynı algoritmanın bir varyantını seçip yürüten büyük bir koşullu ifade de olabilir.

2. Algoritmanın tüm değişkenleri için ortak olan strateji arayüzünü tanımlayın.

3. Tüm algoritmaları tek tek kendi sınıflarına çıkarın. Hepsi strateji arayüzünü uygulamalıdır yani implement etmelidir.

4. Bağlam sınıfında, bir strateji nesnesine referansı depolamak için bir alan ekleyin. Bu alanın değerlerini değiştirmek için bir `setter` tanımlayın. Bağlam, strateji nesnesiyle yalnızca strateji arayüzü aracılığıyla çalışmalıdır. Bağlam, stratejinin verilerine erişmesini sağlayan bir arayüz tanımlayabilir.

5. Bağlamın istemcileri onu, bağlamın birincil işini gerçekleştirmesini bekledikleri yöntemle eşleşen uygun bir stratejiyle ilişkilendirmelidir.

##  ⚖️ Artıları ve Eksileri

✅  Açık/Kapalı Prensibi (Open/Closed Principle): Bağlamı değiştirmek zorunda kalmadan yeni stratejiler sunabilirsiniz.

✅ Çalışma zamanında bir nesnenin içinde kullanılan algoritmaları değiştirebilirsiniz.

✅ Bir algoritmanın uygulama (implementation) ayrıntılarını onu kullanan koddan ayırabilirsiniz.

✅ Kalıtımı kompozisyonla değiştirebilirsiniz.

❌ Yalnızca birkaç algoritmanız varsa ve bunlar nadiren değişiyorsa, modelle birlikte gelen yeni sınıflar ve arayüzlerle programı aşırı karmaşık hale getirmenin gerçek bir nedeni yoktur.

❌ İstemcilerin uygun olanı seçebilmeleri için stratejiler arasındaki farkların farkında olmaları gerekir.

❌ Pek çok modern programlama dili, bir algoritmanın farklı sürümlerini bir dizi anonim işlev içinde uygulamanıza olanak tanıyan işlevsel tür desteğine sahiptir. Daha sonra bu işlevleri, strateji nesnelerini kullandığınız gibi, ancak kodunuzu ekstra sınıflar ve arayüzlerle şişirmeden kullanabilirsiniz.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Köprü (Bridge)**, **Durum (State)**, **Strateji (Strategy)** (ve bir dereceye kadar **Adapter**) çok benzer yapılara sahiptir. Aslında tüm bu desenler, işi başka nesnelere devreden kompozisyona dayanıyor. Ancak hepsi farklı sorunları çözüyor. Desen yalnızca kodunuzu belirli bir şekilde yapılandırmak için kullanılan bir tarif değildir. Ayrıca diğer geliştiricilere modeldeki sorunu iletebilir.

- **Komut (Command)** ve **Strateji (Strategy)** benzer görünebilir çünkü her ikisini de bir nesneyi bazı eylemlerle parametreleştirmek için kullanabilirsiniz. Ancak temelde hedefleri çok farklıdır:
	- Herhangi bir işlemi bir nesneye dönüştürmek için Komut'u kullanabilirsiniz. İşlemin parametreleri o nesnenin alanları haline gelir. Dönüşüm, işlemin yürütülmesini ertelemenize, sıraya koymanıza, komut geçmişini saklamanıza, uzak hizmetlere komut göndermenize vb. olanak tanır.
	- Öte yandan, Strateji genellikle aynı şeyi yapmanın farklı yollarını açıklar ve bu algoritmaları tek bir bağlam sınıfı içinde değiştirmenize olanak tanır.

- **Dekoratör (Decorator)** bir nesnenin dış görünüşünü değiştirmenizi sağlarken **Strateji (Strategy)** iç organlarını değiştirmenizi sağlar. 😅

- **Şablon Yöntemi (Template Method)** kalıtıma dayalıdır: bir algoritmanın bölümlerini, bu bölümleri alt sınıflara genişleterek değiştirmenize olanak tanır. **Strateji (Strategy)** kompozisyona dayalıdır: Nesnenin davranışının bazı kısımlarını, ona o davranışa karşılık gelen farklı stratejiler sağlayarak değiştirebilirsiniz. Şablon Yöntemi sınıf düzeyinde çalışır, dolayısıyla statiktir. Strateji nesne düzeyinde çalışır ve çalışma zamanında davranışları değiştirmenize olanak tanır.

- **Durum (State)** deseni, **Strateji (Strategy)** deseninin bir uzantısı olarak düşünülebilir. Her iki model de kompozisyona dayalıdır: Bazı işleri yardımcı nesnelere devrederek bağlamın davranışını değiştirirler. Strateji bu nesneleri tamamen bağımsız ve birbirlerinden habersiz hale getirir. Ancak Devlet, somut durumlar arasındaki bağımlılıkları kısıtlamaz ve bağlamın durumunu istediği gibi değiştirmelerine izin verir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Strateji modeli Java kodunda çok yaygındır. Kullanıcılara bir sınıfın davranışını genişletmeden değiştirmenin bir yolunu sağlamak için sıklıkla çeşitli çerçevelerde kullanılır.

Java 8, Strateji modeline daha basit alternatifler olarak hizmet edebilecek lambda işlevlerinin desteğini getirdi.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.util.Comparator#compare()`](http://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#compare-T-T-)  çağrıldığı yer:  `Collections#sort()`.
    
-   [`javax.servlet.http.HttpServlet`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html):  `service()`  metod, artı tüm  `doXXX()`  methodları `HttpServletRequest`  ve   `HttpServletResponse`  nesneleri argüman olarak kullanır
    
-   [`javax.servlet.Filter#doFilter()`](http://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html#doFilter-javax.servlet.ServletRequest-javax.servlet.ServletResponse-javax.servlet.FilterChain-)

**Tanımlama:** Strateji modeli, iç içe geçmiş bir nesnenin asıl işi yapmasına izin veren bir yöntemle ve aynı zamanda o nesnenin farklı bir nesneyle değiştirilmesine izin veren bir ayarlayıcıyla tanınabilir.

####  Bir e-ticaret uygulamasında ödeme yöntemi

Bu örnekte, bir e-ticaret uygulamasında çeşitli ödeme yöntemlerini uygulamak için Strateji modeli kullanılmıştır. Müşteri, satın almak üzere bir ürün seçtikten sonra bir ödeme yöntemi seçer: Paypal veya kredi kartı.

Somut stratejiler yalnızca fiili ödemeyi gerçekleştirmekle kalmaz, aynı zamanda ödeme ayrıntılarının kaydedilmesi için uygun alanlar sağlayarak ödeme formunun davranışını da değiştirir.


**📁 strategies**

⤵️ 📄 `strategies/PayStrategy.java`: Ödeme yöntemlerinin ortak arayüzü

```java
package fatihes1.strategy.example.strategies;

/**
 * Common interface for all strategies.
 */
public interface PayStrategy {
    boolean pay(int paymentAmount);
    void collectPaymentDetails();
}
```

⤵️ 📄 `strategies/PayByPayPal.java`: PayPal ile ödeme

```java
package fatihes1.strategy.example.strategies;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;

/**
 * Concrete strategy. Implements PayPal payment method.
 */
public class PayByPayPal implements PayStrategy {
    private static final Map<String, String> DATA_BASE = new HashMap<>();
    private final BufferedReader READER = new BufferedReader(new InputStreamReader(System.in));
    private String email;
    private String password;
    private boolean signedIn;

    static {
        DATA_BASE.put("amanda1985", "amanda@ya.com");
        DATA_BASE.put("qwerty", "john@amazon.eu");
    }

    /**
     * Collect customer's data.
     */
    @Override
    public void collectPaymentDetails() {
        try {
            while (!signedIn) {
                System.out.print("Enter the user's email: ");
                email = READER.readLine();
                System.out.print("Enter the password: ");
                password = READER.readLine();
                if (verify()) {
                    System.out.println("Data verification has been successful.");
                } else {
                    System.out.println("Wrong email or password!");
                }
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    private boolean verify() {
        setSignedIn(email.equals(DATA_BASE.get(password)));
        return signedIn;
    }

    /**
     * Save customer data for future shopping attempts.
     */
    @Override
    public boolean pay(int paymentAmount) {
        if (signedIn) {
            System.out.println("Paying " + paymentAmount + " using PayPal.");
            return true;
        } else {
            return false;
        }
    }

    private void setSignedIn(boolean signedIn) {
        this.signedIn = signedIn;
    }
}
```

⤵️ 📄 `strategies/PayByCreditCard.java`: Kredi kartıyla ödeme

```java
package fatihes1.strategy.example.strategies;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

/**
 * Concrete strategy. Implements credit card payment method.
 */
public class PayByCreditCard implements PayStrategy {
    private final BufferedReader READER = new BufferedReader(new InputStreamReader(System.in));
    private CreditCard card;

    /**
     * Collect credit card data.
     */
    @Override
    public void collectPaymentDetails() {
        try {
            System.out.print("Enter the card number: ");
            String number = READER.readLine();
            System.out.print("Enter the card expiration date 'mm/yy': ");
            String date = READER.readLine();
            System.out.print("Enter the CVV code: ");
            String cvv = READER.readLine();
            card = new CreditCard(number, date, cvv);

            // Validate credit card number...

        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    /**
     * After card validation we can charge customer's credit card.
     */
    @Override
    public boolean pay(int paymentAmount) {
        if (cardIsPresent()) {
            System.out.println("Paying " + paymentAmount + " using Credit Card.");
            card.setAmount(card.getAmount() - paymentAmount);
            return true;
        } else {
            return false;
        }
    }

    private boolean cardIsPresent() {
        return card != null;
    }
}
```

⤵️ 📄 `strategies/CreditCard.java`: Kredi kartı sınıfı

```java
package fatihes1.strategy.example.strategies;

/**
 * Dummy credit card class.
 */
public class CreditCard {
    private int amount;
    private String number;
    private String date;
    private String cvv;

    CreditCard(String number, String date, String cvv) {
        this.amount = 100_000;
        this.number = number;
        this.date = date;
        this.cvv = cvv;
    }

    public void setAmount(int amount) {
        this.amount = amount;
    }

    public int getAmount() {
        return amount;
    }
}
```

⤵️ 📄 `order/Order.java`: Sipariş sınıfı

```java
ackage fatihes1.strategy.example.order;

import fatihes1.strategy.example.strategies.PayStrategy;

/**
 * Order class. Doesn't know the concrete payment method (strategy) user has
 * picked. It uses common strategy interface to delegate collecting payment data
 * to strategy object. It can be used to save order to database.
 */
public class Order {
    private int totalCost = 0;
    private boolean isClosed = false;

    public void processOrder(PayStrategy strategy) {
        strategy.collectPaymentDetails();
        // Here we could collect and store payment data from the strategy.
    }

    public void setTotalCost(int cost) {
        this.totalCost += cost;
    }

    public int getTotalCost() {
        return totalCost;
    }

    public boolean isClosed() {
        return isClosed;
    }

    public void setClosed() {
        isClosed = true;
    }
}
```



⤵️ 📄 `Demo.java`: Initialization kodu

```java
package fatihes1.strategy.example;

import fatihes1.strategy.example.order.Order;
import fatihes1.strategy.example.strategies.PayByCreditCard;
import fatihes1.strategy.example.strategies.PayByPayPal;
import fatihes1.strategy.example.strategies.PayStrategy;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;

/**
 * World first console e-commerce application.
 */
public class Demo {
    private static Map<Integer, Integer> priceOnProducts = new HashMap<>();
    private static BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
    private static Order order = new Order();
    private static PayStrategy strategy;

    static {
        priceOnProducts.put(1, 2200);
        priceOnProducts.put(2, 1850);
        priceOnProducts.put(3, 1100);
        priceOnProducts.put(4, 890);
    }

    public static void main(String[] args) throws IOException {
        while (!order.isClosed()) {
            int cost;

            String continueChoice;
            do {
                System.out.print("Please, select a product:" + "\n" +
                        "1 - Mother board" + "\n" +
                        "2 - CPU" + "\n" +
                        "3 - HDD" + "\n" +
                        "4 - Memory" + "\n");
                int choice = Integer.parseInt(reader.readLine());
                cost = priceOnProducts.get(choice);
                System.out.print("Count: ");
                int count = Integer.parseInt(reader.readLine());
                order.setTotalCost(cost * count);
                System.out.print("Do you wish to continue selecting products? Y/N: ");
                continueChoice = reader.readLine();
            } while (continueChoice.equalsIgnoreCase("Y"));

            if (strategy == null) {
                System.out.println("Please, select a payment method:" + "\n" +
                        "1 - PalPay" + "\n" +
                        "2 - Credit Card");
                String paymentMethod = reader.readLine();

                // Client creates different strategies based on input from user,
                // application configuration, etc.
                if (paymentMethod.equals("1")) {
                    strategy = new PayByPayPal();
                } else {
                    strategy = new PayByCreditCard();
                }
            }

            // Order object delegates gathering payment data to strategy object,
            // since only strategies know what data they need to process a
            // payment.
            order.processOrder(strategy);

            System.out.print("Pay " + order.getTotalCost() + " units or Continue shopping? P/C: ");
            String proceed = reader.readLine();
            if (proceed.equalsIgnoreCase("P")) {
                // Finally, strategy handles the payment.
                if (strategy.pay(order.getTotalCost())) {
                    System.out.println("Payment has been successful.");
                } else {
                    System.out.println("FAIL! Please, check your data.");
                }
                order.setClosed();
            }
        }
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalışma Çıktısı

```
Please, select a product:
1 - Mother board
2 - CPU
3 - HDD
4 - Memory
1
Count: 2
Do you wish to continue selecting products? Y/N: y
Please, select a product:
1 - Mother board
2 - CPU
3 - HDD
4 - Memory
2
Count: 1
Do you wish to continue selecting products? Y/N: n
Please, select a payment method:
1 - PalPay
2 - Credit Card
1
Enter the user's email: user@example.com
Enter the password: qwerty
Wrong email or password!
Enter user email: amanda@ya.com
Enter password: amanda1985
Data verification has been successful.
Pay 6250 units or Continue shopping?  P/C: p
Paying 6250 using PayPal.
Payment has been successful.
```
