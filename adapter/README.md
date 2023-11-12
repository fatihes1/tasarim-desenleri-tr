#  Adaptör/Bağdaştırıcı (Adapter) Tasarım Deseni


##  💬 Amaç

Bağdaştırıcı, uyumsuz arayüzlere sahip nesnelerin ortak çalışmasına olanak tanıyan yapısal (structural) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/adapter/adapter-en-2x.png)


##  🙁 Problem

Hayal edin ki bir hisse senedi piyasası izleme uygulaması oluşturuyorsunuz. Uygulama, kullanıcı için güzel grafikler ve diyagramlar görüntülemek üzere hisse senedi verilerini XML formatında birden çok kaynaktan indirir.

Bir noktada, uygulamayı akıllı bir üçüncü taraf analiz kütüphanesi ile entegre ederek iyileştirmeye karar verebilirsiniz. Ancak bir sorun var: analiz kütüphanesi yalnızca JSON formatındaki verilerle çalışıyor.

![](https://refactoring.guru/images/patterns/diagrams/adapter/problem-en-2x.png)

*Verilerin uygulamanızla uyumlu olmayan bir biçimde olmasını beklediğinden, analiz kütüphanesini olduğu gibi kullanamazsınız.*

Kütüphaneyi XML ile çalışacak şekilde değiştirebilirsiniz. Ancak bu, kütüphaneye dayalı olan mevcut kodları bozabilir. Dahası, kütüphanenin kaynak koduna erişiminiz olmayabilir, bu da bu yaklaşımı imkansız hale getirecektir.


##  😊 Çözüm

Bir adaptör yani bağdaştırıcı oluşturabilirsiniz. Bu, bir nesnenin arayüzünü (interface) başka bir nesnenin anlayabileceği bir biçime dönüştüren özel bir nesnedir.

Bir adaptör, dönüşümün arka planda gerçekleştiği karmaşıklığı gizlemek için bir nesneyi sarmalar. Sarılan nesne adaptörün bile farkında değildir. Örneğin, metre ve kilometre birimleriyle çalışan bir nesneyi, verileri fit ve mil gibi İngiliz birimlerine dönüştüren bir adaptörle sarmak mümkündür.

Adaptörler, verileri farklı biçimlere dönüştürmekle kalmaz, aynı zamanda farklı arayüzlere sahip nesnelerin işbirliği yapmasına da yardımcı olabilir. Nasıl çalıştığına gelecek olursak:

1. Adaptör, mevcut nesnelerden biriyle uyumlu bir arayüz tanımlar. 
2. Bu arayüzü kullanarak mevcut nesne, adaptörün yöntemlerini güvenli bir şekilde çağırabilir. 
3. Bir çağrı aldığında, adaptör bu isteği ikinci nesneye iletiyor, ancak ikinci nesnenin beklediği biçimde ve sırayla iletiyor. 

![](https://refactoring.guru/images/patterns/diagrams/adapter/solution-en-2x.png)


Stock market uygulamamıza geri dönelim. Uyumsuz format sorununu çözmek için, uygulamanızın doğrudan çalıştığı her sınıf için XML'den JSON'a dönüşüm adaptörleri oluşturabilirsiniz. Ardından kodunuzu yalnızca bu adaptörler aracılığıyla kütüphane ile iletişim kuracak şekilde ayarlayabilirsiniz. Bir adaptör bir çağrı aldığında, gelen XML verilerini JSON yapısına çevirir ve çağrıyı sarmalanan bir analiz nesnesinin uygun yöntemlerine ileterek işlemi gerçekleştirir.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/content/adapter/adapter-comic-1-en-2x.png)

*Yurt dışı gezisinden önce ve sonra bir bavul.*

Amerika'dan Avrupa'ya ilk seyahat ettiğinizde, dizüstü bilgisayarınızı şarj etmeye çalışırken sürpriz bir durumla karşılaşabilirsiniz. Farklı ülkelerde farklı güç prizi ve priz standartları olduğu için Amerikan prizi Alman prizine uymaz. Sorunu Amerikan tarzı bir prizi ve Avrupa tarzı bir fişi olan bir güç prizi adaptörü kullanarak çözebilirsiniz.


##  ⚙️ Yapı

#### Nesne Adaptörü

Bu uygulama, nesne kompozisyonu ilkesini kullanır: bağdaştırıcı bir nesnenin arayüzünü uygular ve diğerini sarar. Tüm popüler programlama dillerinde uygulanabilir.


![](https://refactoring.guru/images/patterns/diagrams/adapter/structure-object-adapter-2x.png)

1. **Client**, programın mevcut iş mantığını içeren bir sınıftır.
2. **İstemci Arayüzü (Client Interface)**, istemci koduyla işbirliği yapabilmek için diğer sınıfların izlemesi gereken bir protokolü açıklar.
3. **Servis (Service)** bazı yararlı sınıflardır (genellikle 3. taraf veya eski). İstemci, uyumsuz bir arayüze sahip olduğundan bu sınıfı doğrudan kullanamaz.
4. **Bağdaştırıcı yani adaptör**, hem istemciyle hem de hizmetle çalışabilen bir sınıftır: hizmet nesnesini sararken istemci arayüzünü uygular. Bağdaştırıcı, istemci arayüzü aracılığıyla istemciden gelen çağrıları alır ve bunları, anlayabileceği bir formatta sarılmış hizmet nesnesine yapılan çağrılara dönüştürür.
5. İstemci kodu, istemci arabirimi aracılığıyla bağdaştırıcıyla birlikte çalıştığı sürece somut bağdaştırıcı sınıfına bağlanmaz. Bu sayede mevcut istemci kodunu bozmadan programa yeni adaptör türleri tanıtabilirsiniz. Bu, hizmet sınıfının arayüzü değiştirildiğinde veya değiştirildiğinde yararlı olabilir: istemci kodunu değiştirmeden yeni bir bağdaştırıcı sınıfı oluşturabilirsiniz.

#### Sınıf Adaptörü

![](https://refactoring.guru/images/patterns/diagrams/adapter/structure-class-adapter-2x.png)

1. Sınıf Bağdaştırıcısının (Class Adapter) davranışları hem istemciden hem de hizmetten devraldığı için herhangi bir nesneyi sarması gerekmez. Uyarlama, geçersiz kılınan yöntemler dahilinde gerçekleşir. Ortaya çıkan bağdaştırıcı, mevcut bir istemci sınıfının yerine kullanılabilir.

##  💻 Sözde Kod (Pseudocode)

Adaptör modelinin bu örneği, kare mandallar (square pegs) ve yuvarlak delikler (round holes) arasındaki klasik çatışmaya dayanmaktadır.

![](https://refactoring.guru/images/patterns/diagrams/adapter/example-2x.png)

*Kare mandalların yuvarlak deliklere uyarlanması.*

Adaptör, yarıçapı karenin çapının yarısına (başka bir deyişle, kare çiviyi barındırabilecek en küçük dairenin yarıçapına) sahip yuvarlak bir çivi gibi davranır.

```java
// İki uyumlu arabirimlere sahip iki sınıfınız olduğunu varsayalım: RoundHole ve RoundPeg. 
class RoundHole is 
	constructor RoundHole(radius) { ... }

	method getRadius() is
	    // Deliğin yarıçapını döndürür.

	method fits(peg: RoundPeg) is
	    return this.getRadius() >= peg.getRadius()

class RoundPeg is 
	constructor RoundPeg(radius) { ... }
	
	method getRadius() is  
		// İğnenin yarıçapını döndürür.

// Ancak uyumsuz bir sınıfımız var: SquarePeg. 
class SquarePeg is 
	constructor SquarePeg(width) { ... }

	method getWidth() is  
		// Kare iğnenin genişliğini döndürür.

// Bir adaptör sınıfı, kare iğneleri yuvarlak deliklere sığdırmanıza olanak tanır. // Adaptör nesnelerinin yuvarlak iğneler gibi davranmasını sağlamak için RoundPeg sınıfını genişletir. 
class SquarePegAdapter extends RoundPeg is 
	// Gerçekte, adaptör, SquarePeg sınıfının bir örneğini içerir. 
	private field peg: SquarePeg

	constructor SquarePegAdapter(peg: SquarePeg) is
    this.peg = peg

	method getRadius() is
	    // Adaptör, kendisinin aslında sardığı kare iğneye sığabilecek bir yuvarlak iğne gibi davranır.
	    return peg.getWidth() * Math.sqrt(2) / 2

// İstemci kodunun bir yerinde. 
hole = new RoundHole(5) rpeg = new RoundPeg(5) hole.fits(rpeg) // true

small_sqpeg = new SquarePeg(5) large_sqpeg = new SquarePeg(10) hole.fits(small_sqpeg) 
// bu derlenmeyecektir (uyumsuz tipler)

small_sqpeg_adapter = new SquarePegAdapter(small_sqpeg) large_sqpeg_adapter = new SquarePegAdapter(large_sqpeg) hole.fits(small_sqpeg_adapter) // true 
hole.fits(large_sqpeg_adapter) // false

```


##  💡Uygulanabilirlik

**🐞 Arayüzü kodunuzun geri kalanıyla uyumlu değilken ve mevcut bir sınıfı kullanmak istediğinizde Adapter sınıfını kullanın. **

⚡️ Bağdaştırıcı modeli, kodunuz ile eski bir sınıf, 3. taraf bir sınıf veya garip bir arayüze sahip başka bir sınıf arasında çevirmen görevi gören bir orta katman sınıfı oluşturmanıza olanak tanır.
  
----------------

**🐞 Üst sınıfa eklenemeyen bazı ortak işlevlerden yoksun mevcut birkaç alt sınıfı yeniden kullanmak istediğinizde bu modeli kullanın.**

⚡️ Şu yaklaşımları kullanabilirsiniz: Her alt sınıfı genişletebilir ve eksik işlevselliği yeni alt sınıflara ekleyebilirsiniz. Ancak, bu yeni sınıfların tümünde kodu çoğaltmanız gerekeceğinden, bu kötü bir koku yaratır.
Daha zarif bir çözüm, eksik işlevselliği bir adaptör sınıfına koymak olacaktır. Ardından, eksik özelliklere sahip nesneleri adaptörün içine alarak gereken özelliklere dinamik olarak sahip olursunuz. Bu işlem için hedef sınıfların ortak bir arabirime sahip olması ve adaptörün alanının bu arabirimi takip etmesi gerekmektedir. Bu yaklaşım, Dekoratör desenine oldukça benzer görünmektedir.


##  📝 Nasıl Uygulanır?

1. En az iki uyumsuz arabirimlere sahip sınıfınız olduğundan emin olun:
	- Değiştiremeyeceğiniz (genellikle 3. taraf, eski veya çok sayıda mevcut bağımlılığa sahip) kullanışlı bir hizmet sınıfı.
	- Hizmet sınıfından yararlanabilecek bir veya birden fazla istemci sınıfı.

2. İstemci arabirimini tanımlayın ve istemcilerin hizmetle iletişim kurma şeklini açıklayın.

3. Adaptör sınıfını oluşturun ve onu istemci arabirimini takip edecek şekilde yapın. Şimdiye kadar tüm yöntemleri boş bırakın.

4. Adaptör sınıfına, hizmet nesnesine bir referansı depolamak için bir alan ekleyin. Genellikle bu alanı yapılandırıcı aracılığıyla başlatmak yaygın bir uygulamadır, ancak bazen bu alanı adaptörün yöntemlerini çağırırken geçirmek daha uygun olabilir.

5. Sırayla adaptör sınıfında istemci arabirimindeki tüm yöntemleri uygulayın. Adaptör, gerçek işin büyük bir kısmını hizmet nesnesine iletmesi gerektiği için yalnızca arabirim veya veri biçimi dönüşümünü işlemelidir.

6. İstemciler, adaptörü istemci arabirimi aracılığıyla kullanmalıdır. Bu, adaptörleri değiştirmenize veya genişletmenize olanak tanırken istemci kodunu etkilemeden yapmanızı sağlar.
  

##  ⚖️ Artıları ve Eksileri

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle). Arayüzü veya veri dönüştürme kodunu programın birincil iş mantığından ayırabilirsiniz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle). İstemci arayüzü üzerinden bağdaştırıcılarla çalıştığı sürece, mevcut istemci kodunu bozmadan programa yeni tür bağdaştırıcılar tanıtabilirsiniz.
  
❌ Bir dizi yeni arayüz ve sınıf tanıtmanız gerektiğinden kodun genel karmaşıklığı artar. Bazen hizmet sınıfını kodunuzun geri kalanıyla eşleşecek şekilde değiştirmek daha kolaydır.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Bridge genellikle önceden tasarlanır ve uygulamanın farklı bileşenlerini bağımsız olarak geliştirmenize olanak tanır. Öte yandan, Adapter genellikle mevcut bir uygulama ile kullanılır ve aksi takdirde uyumsuz olan sınıfları uyumlu bir şekilde bir araya getirmenizi sağlar.
- Adapter, mevcut bir nesnenin arabirimini değiştirirken, Decorator, arabirimini değiştirmeden bir nesneyi geliştirir. Ayrıca, Decorator, Adapter kullanıldığında mümkün olmayan özyinelemeli bileşimi destekler.
- Adapter, sarılan nesneye farklı bir arabirim sağlar, Proxy aynı arabirimle sağlar ve Decorator, geliştirilmiş bir arabirimle sağlar.
- Facade, mevcut nesneler için yeni bir arabirim tanımlar, Adapter ise mevcut arabirimi kullanılabilir hale getirmeye çalışır. Adapter genellikle yalnızca bir nesneyi sarmalar, Facade ise nesne alt sistemiyle çalışır.
- Bridge, State, Strategy (ve bir dereceye kadar Adapter), çok benzer yapıları paylaşır. Gerçekten de, bu desenlerin hepsi, işi başka nesnelere devreden kompozisyona dayanır. Bununla birlikte, hepsi farklı sorunları çözer. Bir desen, kodunuzu belirli bir şekilde yapılandırma reçetesi olmanın ötesinde, desenin hangi sorunu çözdüğünü diğer geliştiricilere iletebilir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Bağdaştırıcı modeli Java kodunda oldukça yaygındır. Bazı eski kodlara dayalı sistemlerde sıklıkla kullanılır. Bu gibi durumlarda Bağdaştırıcılar eski kodun modern sınıflarla çalışmasını sağlar.

Java çekirdek kitaplıklarında bazı standart Bağdaştırıcılar vardır:

-   [`java.util.Arrays#asList()`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#asList-T...-)
-   [`java.util.Collections#list()`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#list-java.util.Enumeration-)
-   [`java.util.Collections#enumeration()`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#enumeration-java.util.Collection-)
-   [`java.io.InputStreamReader(InputStream)`](https://docs.oracle.com/javase/8/docs/api/java/io/InputStreamReader.html#InputStreamReader-java.io.InputStream-)  (bir `Reader`  objesi döndürür )
-   [`java.io.OutputStreamWriter(OutputStream)`](https://docs.oracle.com/javase/8/docs/api/java/io/OutputStreamWriter.html#OutputStreamWriter-java.io.OutputStream-)  (bir `Writer` objesi çevirir)
-   [`javax.xml.bind.annotation.adapters.XmlAdapter#marshal()`](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/adapters/XmlAdapter.html#marshal-BoundType-)  ve  `#unmarshal()`

**Tanımlama:** Bağdaştırıcı, farklı bir soyut/arayüz türünün örneğini alan bir yapıcı tarafından tanınabilir. Bağdaştırıcı, yöntemlerinden herhangi birine bir çağrı aldığında, parametreleri uygun biçime çevirir ve ardından çağrıyı sarılmış nesnenin bir veya daha fazla yöntemine yönlendirir.

 
####  Kare Mandalları Yuvarlak Deliklere Takma Örneği 

Bu basit örnek, bir Bağdaştırıcının uyumsuz nesnelerin birlikte çalışmasını nasıl sağlayabileceğini gösterir.

**📁 round**

⤵️ 📄 `round/RoundHole.java`: Yuvarlak delikler

```java
package fatihes1.adapter.example.round;

/**
 * RoundHoles are compatible with RoundPegs.
 */
public class RoundHole {
    private double radius;

    public RoundHole(double radius) {
        this.radius = radius;
    }

    public double getRadius() {
        return radius;
    }

    public boolean fits(RoundPeg peg) {
        boolean result;
        result = (this.getRadius() >= peg.getRadius());
        return result;
    }
}
```

⤵️ 📄 `round/RoundPeg.java`: Yuvarlak mandallar

```java
package fatihes1.adapter.example.round;

/**
 * RoundPegs are compatible with RoundHoles.
 */
public class RoundPeg {
    private double radius;

    public RoundPeg() {}

    public RoundPeg(double radius) {
        this.radius = radius;
    }

    public double getRadius() {
        return radius;
    }
}
```

**📁 square**

⤵️ 📄 `square/SquarePeg.java`: Kare mandallar

```java
package fatihes1.adapter.example.square;

/**
 * SquarePegs are not compatible with RoundHoles (they were implemented by
 * previous development team). But we have to integrate them into our program.
 */
public class SquarePeg {
    private double width;

    public SquarePeg(double width) {
        this.width = width;
    }

    public double getWidth() {
        return width;
    }

    public double getSquare() {
        double result;
        result = Math.pow(this.width, 2);
        return result;
    }
}
```

**📁 adapters**

⤵️ 📄 `adapters/SquarePegAdapter.java`: Kare dübellerin yuvarlak deliklere adaptörü

```java
package fatihes1.adapter.example.adapters;

import fatihes1.adapter.example.round.RoundPeg;
import fatihes1.adapter.example.square.SquarePeg;

/**
 * Adapter allows fitting square pegs into round holes.
 */
public class SquarePegAdapter extends RoundPeg {
    private SquarePeg peg;

    public SquarePegAdapter(SquarePeg peg) {
        this.peg = peg;
    }

    @Override
    public double getRadius() {
        double result;
        // Calculate a minimum circle radius, which can fit this peg.
        result = (Math.sqrt(Math.pow((peg.getWidth() / 2), 2) * 2));
        return result;
    }
}
```

⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.adapter.example;

import fatihes1.adapter.example.adapters.SquarePegAdapter;
import fatihes1.adapter.example.round.RoundHole;
import fatihes1.adapter.example.round.RoundPeg;
import fatihes1.adapter.example.square.SquarePeg;

/**
 * Somewhere in client code...
 */
public class Demo {
    public static void main(String[] args) {
        // Round fits round, no surprise.
        RoundHole hole = new RoundHole(5);
        RoundPeg rpeg = new RoundPeg(5);
        if (hole.fits(rpeg)) {
            System.out.println("Round peg r5 fits round hole r5.");
        }

        SquarePeg smallSqPeg = new SquarePeg(2);
        SquarePeg largeSqPeg = new SquarePeg(20);
        // hole.fits(smallSqPeg); // Won't compile.

        // Adapter solves the problem.
        SquarePegAdapter smallSqPegAdapter = new SquarePegAdapter(smallSqPeg);
        SquarePegAdapter largeSqPegAdapter = new SquarePegAdapter(largeSqPeg);
        if (hole.fits(smallSqPegAdapter)) {
            System.out.println("Square peg w2 fits round hole r5.");
        }
        if (!hole.fits(largeSqPegAdapter)) {
            System.out.println("Square peg w20 does not fit into round hole r5.");
        }
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemoSingleThread.txt`: Çalıştırma Sonucu

```
Round peg r5 fits round hole r5.
Square peg w2 fits round hole r5.
Square peg w20 does not fit into round hole r5.
```




