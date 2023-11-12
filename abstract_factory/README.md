#  Soyut Fabrika (Abstract Factory)

  

##  💬 Amaç

"Soyut Fabrika" (Abstract Factory), sınıflarını belirtmeden ilgili nesne ailelerini üretmenize olanak tanıyan yaratıcı (creational) bir tasarım modelidir.

  

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-en-2x.png)

  

##  🙁 Problem

Bir mobilya mağazası simülatörü oluşturduğunuzu hayal edin. Kodunuz aşağıdakileri temsil eden sınıflardan oluşur:

1. Ürünleriniz, Sandalye, Kanepe ve Kahve Masası için: `Chair` , `Sofa` ve `CoffeeTable` sınıfları.

2. Bu ürün ailesinin çeşitli varyantları olabilir. Örneğin `Chair` , `Sofa` , `CoffeeTable`  ürünlerinin her biri için şu çeşitleri mevcuttur: `Modern`, `Victorian`, `ArtDeco`.

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/problem-en-2x.png)

*Ürün çeşitleri ve varyantları.*

 Müşteriler, birbiriyle uyumlu olmayan mobilyalar aldıklarında oldukça sinirleniyorlar. Bundan dolayı aynı ailedeki diğer nesnelerle eşleşecek şekilde bireysel mobilya nesneleri oluşturmanın bir yoluna ihtiyacınız var.

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-comic-1-en-2x.png)

*Modern tarzda bir kanepe, Viktorya tarzı sandalyelerle eşleşmez.*

Ayrıca programa yeni ürünler veya ürün aileleri eklerken mevcut kodunuzu değiştirmek istemezsiniz. Buna karşın, mobilya satıcıları kataloglarını çok sık günceller ve bu durum her gerçekleştiğinde temel kodu değiştirmek zorunda kalmak işin içinden çıkılmaz bir hal alacaktır.


##  😊 Çözüm

Abstract Factory deseninin önerdiği ilk şey, ürün ailesinin her farklı ürünü için arayüzleri (interface) açıkça tanımlamaktır (örneğin, sandalye, kanepe veya sehpa gibi). Ardından ürünlerin tüm varyasyonlarını bu arayüzleri takip edecek şekilde yapabilirsiniz. Örneğin, tüm sandalye varyasyonları `Chair` arayüzünü uygulayabilir; tüm kahve masası varyasyonları `CoffeeTable` arayüzünü uygulayabilirsiniz.

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/solution1-2x.png)

*Aynı nesnenin tüm varyantları tek bir sınıf hiyerarşisine taşınmalıdır.*

![](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

Bir sonraki adım, Abstract Factory olarak adlandırılan, ürün ailesinin bir parçası olan tüm ürünler için yaratma yöntemlerinin bir listesini içeren bir arayüzü (interface) bildirmektir (örneğin, `createChair`, `createSofa` ve `createCoffeeTable` gibi). Bu yöntemler, önceki arayüzler tarafından temsil edilen soyut (abstract) ürün türlerini döndürmelidir: `Chair`, `Sofa`, `CoffeeTable` vb.

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/solution2-2x.png)

*Her concrete fabrikası belirli bir ürün çeşidine karşılık gelir.*

Peki, ürün varyasyonları nasıl işlenir? Ürün ailesinin her varyasyonu için `AbstractFactory` arayüzüne dayalı ayrı bir fabrika sınıfı oluştururuz. Bir fabrika, belirli bir türün ürünlerini döndüren bir sınıftır. Örneğin, `ModernFurnitureFactory` sınıfını kullanarak sadece `ModernChair`, `ModernSofa` ve `ModernCoffeeTable` nesneleri oluşturabilir.

İstemci (client) kodunun hem fabrikalarla hem de ürünlerle ilgili soyut arayüzler (abstract interface) aracılığıyla çalışması gerekir. Bu, gerçek istemci kodunu bozmadan, istemci koduna ilettiğiniz fabrikanın türünü ve istemci kodunun aldığı ürün çeşidini değiştirmenize olanak tanır.

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-comic-2-en-2x.png)

*İstemci, birlikte çalıştığı fabrikanın soyut olmayan (non abstract) sınıfını umursamamalıdır.*

Diyelim ki istemci bir fabrikanın bir sandalye üretmesini istiyor. İstemcinin fabrikanın sınıfı hakkında bilgi sahibi olması gerekmez ve hangi türde bir sandalye aldığı önemli değildir. Modern bir model veya Viktorya tarzı bir sandalye olması fark etmeksizin, istemci tüm sandalyeleri aynı şekilde işlemelidir. Bu işlemi soyut (abstract) `Chair` arayüzünü (interface) kullanarak yapar. Bu yaklaşımla, istemcinin sandalye hakkında bildiği tek şey, sandalyenin `sitOn` yani oturma yöntemini herhangi bir şekilde uyguladığıdır. Ayrıca, döndürülen sandalye varyasyonu her ne olursa olsun, aynı fabrika nesnesi tarafından üretilen koltuk veya sehpa türüyle her zaman eşleşecektir.


Açıklığa kavuşturulması gereken bir şey daha kaldı: Eğer istemci yalnızca soyut arayüzlere maruz kalıyorsa, gerçek fabrika nesnelerini ne yaratır? Genellikle uygulama, başlatma aşamasında soyut olmayan bir fabrika nesnesi oluşturur. Bundan hemen önce uygulamanın, yapılandırmaya veya ortam ayarlarına bağlı olarak fabrika türünü seçmesi gerekir.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure-2x.png)
  
1. **Abstract Products**, bir ürün ailesini oluşturan farklı ilgili ürünler için arayüzleri bildirir.
2. **Concrete Products**, soyut ürünlerin farklı uygulamalarıdır ve varyantlar tarafından gruplandırılır. Her soyut ürün (sandalye/kanepe), verilen tüm varyasyonlarda (Viktorya/Modern) uygulanmalıdır.
3. **Abstract Factory** arayüzü, her bir soyut ürünün oluşturulması için bir dizi yöntemi bildirir.
4. **Concrete Factories**, soyut fabrikanın oluşturma yöntemlerini uygular. Her soyut olmayan fabrika, belirli bir ürün varyantına karşılık gelir ve yalnızca bu ürün varyasyonlarını oluşturur.
5. Her ne kadar soyut olmayan fabrika sınıfları somut ürünleri oluştursa da, oluşturma yöntemlerinin imzaları karşılık gelen soyut ürünleri döndürmelidir. Bu sayede bir fabrikadan aldığı ürünün belirli bir varyasyonuna bağlı hale gelmeyen bir fabrikayı kullanan istemci kodu elde edilir. İstemci, soyut arayüzler (abstract interface) aracılığıyla nesneleriyle iletişim kurduğu sürece herhangi bir somut olmayan fabrika/ürün varyantı ile çalışabilir.

  
##  💻 Sözde Kod (Pseudocode)

Bu örnek, Abstract Factory deseninin, istemci kodunu somut UI sınıflarına bağlamadan, oluşturulan tüm öğelerin seçilen işletim sistemi ile uyumlu olduğu ve  çoklu platformlar için UI öğeleri oluşturmak için nasıl kullanılabileceğini göstermektedir.


![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/example-2x.png)

*Platformlar arası kullanıcı arayüzü sınıfları örneği.*

Aynı UI öğelerinin çoklu platform uygulamasında benzer şekilde davranması beklenir, ancak farklı işletim sistemlerinde biraz farklı görünmelidir. Ayrıca, UI öğelerinin mevcut işletim sisteminin stilini yansıttığından emin olmak sizin görevinizdir. Örneğin, programınızın Windows'ta çalıştırıldığında macOS denetimlerini oluşturmasını istemezsiniz.

Abstract Factory arayüzü, istemci kodunun farklı türde UI öğelerini oluşturmak için kullanabileceği bir dizi yaratma yöntemini bildirir. Somut fabrikalar belirli işletim sistemlerine karşılık gelir ve o belirli işletim sistemi ile eşleşen UI öğelerini oluşturur.

Bu şöyle çalışır: Bir uygulama başlatıldığında, mevcut işletim sisteminin türünü kontrol eder. Uygulama bu bilgiyi kullanarak işletim sistemine uyan bir sınıfından bir fabrika nesnesi oluşturur. Geri kalan kod bu fabrikayı kullanarak UI öğelerini oluşturur. Bu, yanlış öğelerin oluşturulmasını önler.

Bu yaklaşımla, istemci kodu, bu nesneleri soyut arayüzleri üzerinden çalıştığı sürece fabrikaların ve UI öğelerinin soyut olmayan sınıflarına bağlı değildir. Bu aynı zamanda istemci kodun, gelecekte ekleyebileceğiniz diğer fabrikaları veya UI öğelerini desteklemesine olanak tanır.

Sonuç olarak, uygulamanıza yeni bir UI öğesi varyasyonu eklediğinizde her seferinde istemci kodunu değiştirmeniz gerekmez. Sadece bu öğeleri üreten yeni bir fabrika sınıfı (factory class) oluşturmanız ve uygulamanın başlatma kodunu uygun olduğunda bu sınıfı seçecek birazcık hafifçe değiştirmeniz yeterlidir.

```java
//Soyut fabrika arayüzü, farklı soyut ürünleri döndüren bir dizi yöntemi bildirir. Bu ürünler bir aileyi oluşturur ve yüksek seviye bir tema veya kavram tarafından ilişkilendirilirler. Bir ailenin ürünleri genellikle kendi aralarında işbirliği yapabilirler. Bir ürün ailesinin birkaç varyasyonu olabilir, ancak bir varyantın ürünleri diğer bir varyantın ürünleriyle uyumsuzdur. 
interface GUIFactory is method createButton():Button method createCheckbox():Checkbox

// Soyut olmayan fabrikalar, tek bir varyanta ait ürün ailesini üretir. Fabrika, sonuç ürünlerinin uyumlu olduğunu garanti eder. Soyut olmayan fabrika yöntemlerinin imzaları soyut bir ürünü döndürürken, yöntem içinde soyut olmayan bir ürün oluşturulur. 

class WinFactory implements GUIFactory is method createButton():Button is return new WinButton() method createCheckbox():Checkbox is return new WinCheckbox()

// Her soyut olmayan fabrikanın karşılık gelen bir ürün varyantı vardır. 
class MacFactory implements GUIFactory is method createButton():Button is return new MacButton() method createCheckbox():Checkbox is return new MacCheckbox()

// Bir ürün ailesinin her farklı ürünü için bir temel arayüze sahip olmalıdır. Ürünün tüm varyasyonları bu arayüzü uygulamalıdır. 
interface Button is method paint()

// Soyut olmayan ürünler karşılık gelen soyut olmayan fabrika sınıfları tarafından oluşturulur. 
class WinButton implements Button is method paint() is 
// Windows tarzında bir düğmeyi çiz.

class MacButton implements Button is method paint() is 
// macOS tarzında bir düğmeyi çiz.

// Aşağıda başka bir ürünün temel arayüzü verilmiştir. Tüm ürünler birbirleriyle etkileşimde bulunabilir, ancak doğru etkileşim yalnızca aynı soyut olmayan varyantın ürünleri arasında mümkündür. 
interface Checkbox is method paint()

class WinCheckbox implements Checkbox is method paint() is 
// Windows tarzında bir onay kutusunu çiz.

class MacCheckbox implements Checkbox is method paint() is 
// macOS tarzında bir onay kutusunu çiz.

// İstemci kodu, fabrikalar ve ürünlerle yalnızca soyut türler aracılığıyla çalışır: GUIFactory, Button ve Checkbox. Bu, istemci koduna herhangi bir fabrika veya ürün alt sınıfını geçirmenize ve kodu bozmadan çalışmasını sağlar. 
class Application is private field factory: GUIFactory private field button: Button constructor Application(factory: GUIFactory) is this.factory = factory method createUI() is this.button = factory.createButton() method paint() is button.paint()

// Uygulama, mevcut yapılandırmaya veya ortam ayarlarına bağlı olarak fabrika türünü seçer ve genellikle çalışma zamanında (genellikle başlatma aşamasında) oluşturur.

class ApplicationConfigurator is method main() is config = readApplicationConfigFile()
```

  

##  💡Uygulanabilirlik

  

**🐞 Kodunuzun ilgili ürünlerin çeşitli aileleriyle çalışması gerektiğinde ve bu ürünlerin soyut olmayan sınıflarına bağlı olmasını istemezseniz Soyut Fabrikayı kullanın. Bu gibi ön koşullar önceden bilinmeyebilir veya yalnızca gelecekteki genişletilebilirliğe izin vermek isteyebilirsiniz.**

⚡️ Abstract Factory, ürün ailesinin her sınıfından nesneler oluşturmak için bir arayüz sağlar. Kodunuz bu arayüz aracılığıyla nesneler oluşturduğu sürece, uygulamanız tarafından zaten oluşturulan ürünlerle eşleşmeyen yanlış bir ürün varyasyonu oluşturma konusunda endişe etmenize gerek yoktur.
  
----------------

**🐞 Birincil sorumluluğunu tam olarak uygulayamayan bir dizi Fabrika Yöntemine sahip bir sınıfınız olduğunda Soyut Fabrikayı uygulamayı düşünün.**

⚡️ İyi tasarlanmış bir programda her sınıf yalnızca bir şeyden sorumludur. Bir sınıf birden fazla ürün türüyle ilgileniyorsa, fabrika yöntemlerini tek başına bir fabrika sınıfına veya tam gelişmiş bir Soyut Fabrika uygulamasına çıkarmak faydalı olabilir.

  

##  📝 Nasıl Uygulanır?

1. Farklı ürün türlerinin ve bu ürünlerin çeşitlerinin yer aldığı bir matrisin haritasını çıkarın.

2. Tüm ürün türleri için soyut ürün arayüzlerini tanımlayın. Daha sonra tüm soyut olmayan ürün sınıflarının bu arayüzleri uygulamasını/kullanmasını (implement etmesini) sağlayın.

3. Tüm soyut ürünler için bir dizi oluşturma yöntemiyle soyut fabrika arayüzünü tanımlayın.

4. Her ürün çeşidi için bir tane olmak üzere bir dizi soyut olmayan fabrikası sınıfı oluşturun.

5. Uygulamada bir yerde fabrika başlatma kodu oluşturun. Uygulama yapılandırmasına veya mevcut ortama bağlı olarak soyut olmayan fabrika sınıflarından birini oluşturmalıdır. Bu fabrika nesnesini ürünleri oluşturan tüm sınıflara iletin.

6. Kodu tarayın ve ürün yapıcılarına yapılan tüm doğrudan çağrıları bulun. Bunları fabrika nesnesindeki uygun oluşturma yöntemine yapılan çağrılarla değiştirin.
  

##  ⚖️ Artıları ve Eksileri

✅ Bir fabrika sınıfından alacağınız ürünlerin birbiriyle uyumlu olduğundan emin olabilirsiniz.

✅ Soyut olmayan ürünler ile istemci kodu arasında sıkı bağlantıdan kurtulursunuz.

✅ *Tek Sorumluluk İlkesi (Single Responsibility Principle)*. Ürün oluşturma kodunu programda tek bir yere taşıyabilir, böylece kodun kolayca desteklenmesini sağlayabilirsiniz.

✅ *Açık/Kapalı Prensibi (Open/Closed Principle)*. Varolan istemci kodunu bozmadan yeni ürün varyasyonları ekleyebilirsiniz.
  

❌ Deseni uygulamak için birçok yeni alt sınıf tanıtmanız gerektiğinden dolayı kod daha karmaşık hale gelebilir. En iyi durum senaryosu, deseni mevcut yaratıcı sınıf hiyerarşisine dahil ettiğiniz zamandır.

  

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Birçok tasarım, Fabrika Yöntemi (Factory method) (daha az karmaşık ve alt sınıflar yoluyla daha fazla özelleştirilebilir) kullanılarak başlar ve Soyut Fabrika (Abstract Factory), Prototip (Prototype) veya Oluşturucuya (Builder) (daha esnek, ancak daha karmaşık) gibi tasarım desenlerine doğru gelişir.

- Builder, karmaşık nesneleri adım adım oluşturmaya odaklanır. Abstract Factory, ilişkili nesnelerin ailelerini oluşturmayı özelleştirir. Abstract Factory, ürünü hemen döndürürken, Builder size ürünü almadan önce bazı ek inşaat adımlarını çalıştırma imkanı sunar.

- Soyut Fabrika (Abstract Factory) deseni sınıfları genellikle bir dizi Fabrika Yöntemi (Factory method) desenine dayanır, ancak bu sınıflardaki yöntemleri oluşturmak için Prototip desenini de kullanabilirsiniz.

- Abstract Factory sınıfları genellikle bir dizi Factory Method'a dayanır, ancak bu sınıflardaki yöntemleri oluşturmak için Prototip deseni (Prototype) de kullanabilirsiniz.

- Abstract Factory, yalnızca alt sistem nesnelerinin nasıl oluşturulduğunu istemci kodundan gizlemek istediğinizde Facade desenine alternatif olarak hizmet edebilir.

- Abstract Factory'yi Bridge ile birlikte kullanabilirsiniz. Bu eşleştirme, Bridge tarafından tanımlanan bazı soyutlamaların yalnızca belirli uygulamalarla çalışabileceği durumlarda kullanışlıdır. Bu durumda, Abstract Factory bu ilişkileri kapsayabilir ve karmaşıklığı istemci kodundan gizleyebilir.

- Abstract Factory, Builder ve Prototype desenleri Singleton deseni olarak uygulanabilir.
  
##  👾 Kod Örnekleri

  

###  Java

**Kullanım örneği:** Soyut Fabrika modeli Java kodunda oldukça yaygındır. Birçok çerçeve ve kitaplık, standart bileşenlerini genişletmenin ve özelleştirmenin bir yolunu sağlamak için bunu kullanır.

  

Desen çekirdek Java kitaplıklarında mevcuttur:

-   [`javax.xml.parsers.DocumentBuilderFactory#newInstance()`](http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newInstance--)
    
-   [`javax.xml.transform.TransformerFactory#newInstance()`](http://docs.oracle.com/javase/8/docs/api/javax/xml/transform/TransformerFactory.html#newInstance--)
    
-   [`javax.xml.xpath.XPathFactory#newInstance()`](http://docs.oracle.com/javase/8/docs/api/javax/xml/xpath/XPathFactory.html#newInstance--)


**Tanımlama:** Desenin, bir fabrika nesnesini döndüren yöntemlerle tanınması kolaydır. Daha sonra fabrika belirli alt bileşenleri oluşturmak için kullanılır.

 
####  Platformlar Arası GUI Bileşenleri Aileleri ve Bunların Üretimi

Bu örnekte, düğmeler ve onay kutuları ürün olarak işlev görecek. İki varyantları bulunmaktadır: macOS ve Windows.

Abstract factory, düğme ve onay kutuları oluşturmak için bir arayüz tanımlar. İki soyut olmayan fabrika sınıfı vardır ve her biri tek bir varyantta her iki ürünü de döndürür.

İstemci kodu, fabrikalar ve ürünlerle soyut arayüzleri kullanarak çalışır. Aynı istemci kodu, fabrika nesnesinin türüne bağlı olarak birçok ürün varyantı ile çalışır hale getirir.

**📁 buttons:** İlk ürün hiyerarşisi

⤵️ 📄 `buttons/Button.java`: 

```java
package fatihes1.abstract_factory.example.buttons;

/**
 * Abstract Factory assumes that you have several families of products,
 * structured into separate class hierarchies (Button/Checkbox). All products of
 * the same family have the common interface.
 *
 * This is the common interface for buttons family.
 */
public interface Button {
    void paint();
}
```

⤵️ 📄 `buttons/MacOSButton.java`

```java
package fatihes1.abstract_factory.example.buttons;

/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is a MacOS variant of a button.
 */
public class MacOSButton implements Button {

    @Override
    public void paint() {
        System.out.println("You have created MacOSButton.");
    }
}
```

⤵️ 📄 `buttons/WindowsButton.java`

```java
package fatihes1.abstract_factory.example.buttons;

/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is another variant of a button.
 */
public class WindowsButton implements Button {

    @Override
    public void paint() {
        System.out.println("You have created WindowsButton.");
    }
}
```

**📁 checkboxes:** İkinci ürün hiyerarşisi

⤵️ 📄 `checkboxes/Checkbox.java`

```java
package fatihes1.abstract_factory.example.checkboxes;

/**
 * Checkboxes is the second product family. It has the same variants as buttons.
 */
public interface Checkbox {
    void paint();
}
```

⤵️ 📄 `checkboxes/MacOSCheckbox.java` 

```java
package fatihes1.abstract_factory.example.checkboxes;

/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is a variant of a checkbox.
 */
public class MacOSCheckbox implements Checkbox {

    @Override
    public void paint() {
        System.out.println("You have created MacOSCheckbox.");
    }
}
```

⤵️ 📄 `checkboxes/WindowsCheckbox.java` 

```java
package fatihes1.abstract_factory.example.checkboxes;

/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is another variant of a checkbox.
 */
public class WindowsCheckbox implements Checkbox {

    @Override
    public void paint() {
        System.out.println("You have created WindowsCheckbox.");
    }
}
```

**📁 factories**

⤵️ 📄 `factories/GUIFactory.java` : Abstract factory

```java
package fatihes1.abstract_factory.example.factories;

import fatihes1.abstract_factory.example.buttons.Button;
import fatihes1.abstract_factory.example.checkboxes.Checkbox;

/**
 * Abstract factory knows about all (abstract) product types.
 */
public interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

⤵️ 📄 `factories/MacOSFactory.java` : Concrete factory (macOS)

```java
package fatihes1.abstract_factory.example.factories;

import fatihes1.abstract_factory.example.buttons.Button;
import fatihes1.abstract_factory.example.buttons.MacOSButton;
import fatihes1.abstract_factory.example.checkboxes.Checkbox;
import fatihes1.abstract_factory.example.checkboxes.MacOSCheckbox;

/**
 * Each concrete factory extends basic factory and responsible for creating
 * products of a single variety.
 */
public class MacOSFactory implements GUIFactory {

    @Override
    public Button createButton() {
        return new MacOSButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacOSCheckbox();
    }
}
```

⤵️ 📄 `factories/WindowsFactory.java` : : Concrete factory (Windows)

```java
package fatihes1.abstract_factory.example.factories;

import fatihes1.abstract_factory.example.buttons.Button;
import fatihes1.abstract_factory.example.buttons.WindowsButton;
import fatihes1.abstract_factory.example.checkboxes.Checkbox;
import fatihes1.abstract_factory.example.checkboxes.WindowsCheckbox;

/**
 * Each concrete factory extends basic factory and responsible for creating
 * products of a single variety.
 */
public class WindowsFactory implements GUIFactory {

    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}
```

**📁 app**

⤵️ 📄 `app/Application.java` : Client code

```java
package fatihes1.abstract_factory.example.app;

import fatihes1.abstract_factory.example.buttons.Button;
import fatihes1.abstract_factory.example.checkboxes.Checkbox;
import fatihes1.abstract_factory.example.factories.GUIFactory;

/**
 * Factory users don't care which concrete factory they use since they work with
 * factories and products through abstract interfaces.
 */
public class Application {
    private Button button;
    private Checkbox checkbox;

    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void paint() {
        button.paint();
        checkbox.paint();
    }
}
```

⤵️ 📄 `Demo.java` : App configuration

```java
package refactoring_guru.abstract_factory.example;

import refactoring_guru.abstract_factory.example.app.Application;
import refactoring_guru.abstract_factory.example.factories.GUIFactory;
import refactoring_guru.abstract_factory.example.factories.MacOSFactory;
import refactoring_guru.abstract_factory.example.factories.WindowsFactory;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {

    /**
     * Application picks the factory type and creates it in run time (usually at
     * initialization stage), depending on the configuration or environment
     * variables.
     */
    private static Application configureApplication() {
        Application app;
        GUIFactory factory;
        String osName = System.getProperty("os.name").toLowerCase();
        if (osName.contains("mac")) {
            factory = new MacOSFactory();
        } else {
            factory = new WindowsFactory();
        }
        app = new Application(factory);
        return app;
    }

    public static void main(String[] args) {
        Application app = configureApplication();
        app.paint();
    }
}
```

**🖨️ Çıktılar**


⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu (HtmlDialog)

```
You create WindowsButton.
You created WindowsCheckbox.
```

