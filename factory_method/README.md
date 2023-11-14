# Fabrika Yöntemi (Factory Method)
> Diğer adıyla: Sanal Oluşturucu (Virtual Constructor)

## 💬 Amaç
"Fabrika Yöntemi" (Factory Method), nesne oluşturmak için bir üst sınıfta bir arayüz sağlayan bununla beraber alt sınıfların oluşturulacak nesne türünü değiştirmelerine izin veren bir yaratıcı tasarım desenidir. 

<div align="center">

![](https://refactoring.guru/images/patterns/content/factory-method/factory-method-en.png)

</div>

## 🙁 Problem
Düşünün ki bir lojistik yönetimi uygulaması oluşturuyorsunuz. Uygulamanızın ilk sürümü yalnızca kamyonlarla taşımacılığı işleyebiliyor, bu nedenle kodun büyük bir kısmı Kamyon sınıfının (Truck class) içindedir.

Bir süre sonra uygulamanız oldukça popüler hale gelir. Her gün deniz taşımacılığı şirketlerinden onlarca istek almaya başladınız ve deniz lojistiğini uygulamanıza dahil etmeye karar verdiniz.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrxams/factory-method/problem1-en.png)

*Kodun geri kalanı zaten mevcut sınıflara bağlıysa programa yeni bir sınıf eklemek sanıldığı kadar kolay olmayacaktır.*

</div>

Bu işiniz için oldukça iyi bir gelişme değil mi? Ancak kodun durumu nasıl? Şu anda kodun büyük bir kısmı Kamyon (`Truck`) sınıfına  bağlıdır. Gemileri (`Ships`) uygulamaya eklemek, tüm kod tabanında değişiklik yapmayı gerektirecektir. Dahası, ileride uygulamaya başka bir taşıma türü eklemeye karar verirseniz, muhtemelen tüm bu değişiklikleri yeniden yapmanız gerekecektir.

Sonuç olarak, taşınan nesnelerinin sınıfına bağlı olarak uygulamanın davranışını değiştiren koşullu ifadelerle doldukça, günbegün daha karmaşık bir kod elde edersiniz.

## 😊 Çözüm
"Factory Method" deseni, doğrudan nesne oluşturma çağrılarını (`new` operatörü ile) özel bir fabrika yöntemi çağrılarıyla değiştirmenizi önerir. Endişelenmeyin: nesneler hala `new` operatörü kullanılarak oluşturuyor olacaksınız, ancak bu işlem fabrika yöntemi içinden çağrılacaktır. Bir fabrika (Factory) yöntemi tarafından döndürülen nesnelere genellikle ürünler (products) denir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution1.png)

*Alt sınıflar, fabrika yöntemiyle döndürülen nesnelerin sınıfını değiştirebilir.*

</div>

İlk bakışta, bu değişiklik anlamsız gibi görünebilir; sadece yapılandırıcı (constructor) çağrısını programın bir bölümünden başka bir bölümüne taşıdık gibi. Ancak şu şekilde  düşünün: şimdi fabrika (Factory) yöntemini bir alt sınıfta geçersiz kılabilir (override) ve bu yöntem tarafından oluşturulan ürünlerin sınıfını değiştirebilirsiniz.

Ancak küçük bir kısıtlama vardır: alt sınıflar, bu ürünlerin ortak bir temel sınıfa veya arayüze (interface) sahip olduğu durumlarda yalnızca farklı türde ürünler döndürebilirler. Ayrıca, temel sınıftaki fabrika yöntemi bu arayüze sahip bir dönüş türüne sahip olmalıdır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution2-en.png)

*Tüm ürünler aynı arayüzü (interface) takip etmelidir.*

</div>

Örneğin, hem Kamyon (`Truck`) hem de Gemi (`Ship`) sınıfları, `deliver` adlı bir yöntemi tanımlayan `Transport` arayüzünü (interface) uygulamalıdır. Her sınıf bu yöntemi farklı şekillerde kullanır: kamyonlar paketleri/kargoları karayoluyla teslim eder, gemiler ise paketleri/kargoları deniz yoluyla teslim eder. `RoadLogistics` sınıfındaki fabrika (factory) yöntemi kamyon nesneleri döndürürken, `SeaLogistics` sınıfındaki fabrika (factory) yöntemi gemileri döndürür.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution3-en.png)

*Tüm ürün sınıfları ortak bir arayüz (interface) uyguladığı sürece nesnelerini müşterilere, bozmadan iletebilirsiniz.* 

</div>

Fabrika yöntemini kullanan kod (genellikle istemci kodu olarak adlandırılır), çeşitli alt sınıflar tarafından döndürülen gerçek ürünler arasında bir fark görmemektedir. İstemci, tüm ürünleri soyut (abstract) `Transport` olarak ele alır. İstemci, tüm taşıma nesnelerinin `deliver` yöntemine sahip olması gerektiğini bilir, ancak bu yöntemin tam olarak nasıl çalıştığı istemci için önemli değildir.

## ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

</div>

1. **Product**, yaratıcının (creator) ve alt sınıflarının (sub classes) üretebileceği tüm nesneler için ortak olan **arayüzü** (interface) tanımlar.
2. **Concrete Products**, ürün arayüzünün (Product interface) farklı uygulamalarıdır yani implementasyonudur.
3. **Creator sınıfı**, yeni ürün nesnelerini döndüren fabrika yöntemini tanımlar. Bu yöntemin dönüş türünün ürün arayüzü (Product interface) ile eşleşmesi önemlidir.
Fabrika yöntemini, tüm alt sınıflarında kendine özgü tanımlanmasını zorlamak için bu yöntemi soyut (abstract) olarak tanımlayabilirsiniz. Alternatif olarak, temel fabrika yöntemi varsayılan bir ürün türünü döndürebilir.
Dikkat edilmesi gereken bir nokta, adına rağmen ürün oluşturma işleminin yaratıcının (`ConcreteCreatorA` ve `ConcreteCreatorA`) asıl sorumluluğu olmadığıdır. Genellikle yaratıcı sınıf, ürünlerle ilgili bazı temel iş mantığına zaten sahiptir. Fabrika yöntemi, bu mantığı somut ürün sınıflarından ayırmaya yardımcı olur. İşte bir benzetme: Büyük bir yazılım geliştirme şirketi, programcılar için bir eğitim departmanına sahip olabilir. Ancak şirketin bütün olarak ana işlevi hala kod yazmaktır, programcılar üretmek değildir.
4. Somut Yaratıcılar (Concrete Creators), temel fabrika yöntemini geçersiz kılarlar (override), böylece farklı türde ürün döndürürler.
Unutmayın ki fabrika yöntemi her zaman yeni örnekler oluşturmak zorunda değildir. Aynı zamanda bir önbellekten, bir nesne havuzundan veya başka bir kaynaktan mevcut nesneleri de döndürebilir.

## 💻 Sözde Kod (Pseudocode)
Bu örnek, Factory Method'un istemci kodunu oluşturucu UI sınıflarına bağlamadan çapraz platform kullanıcı arayüzü öğeleri oluşturmak için nasıl kullanılabileceğini göstermektedir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/factory-method/example.png)

*Platformlar arası iletişim kutusu örneği.*

</div>

Temel `Dialog` sınıfı, penceresini oluşturmak için farklı UI öğelerini kullanır. Çeşitli işletim sistemlerinde bu öğeler biraz farklı görünebilir, ancak yine de tutarlı bir şekilde davranmalıdır. Windows işetim sistemindeki bir düğme hala Linux'taki bir düğmedir. İşlev olarak aynı amaca hizmet ederler.

Fabrika yöntemi devreye girdiğinde, her işletim sistemi için `Dialog` sınıfının mantığını yeniden yazmanıza gerek kalmaz. Temel `Dialog` sınıfı içinde düğmeleri üreten bir fabrika yöntemi tanımlarsak, daha sonra fabrika yönteminden Windows işletim sistemine ait düğmeler döndüren bir alt sınıf oluşturabiliriz. Alt sınıf, temel sınıftan çoğu kodu miras alır, ancak fabrika yöntemi sayesinde ekranda Windows görünümlü düğmeleri oluşturabilir.

Bu desenin çalışması için temel `Dialog` sınıfının soyut (abstract) düğmelerle çalışması gerekmektedir. Bu abstract düğmeler, tüm oluşturucu düğmelerin takip ettiği bir temel sınıf veya bir arayüzdür. Bu sayede `Dialog` içindeki kod, hangi tür düğmelerle çalışırsa çalışsın işlevsel kalır.

Tabii ki, bu yaklaşımı diğer UI öğelerine de uygulayabilirsiniz. Ancak `Dialog` sınıfına eklediğiniz her yeni fabrika yöntemiyle, **Abstract Factory** desenine daha da yaklaşırsınız. Abstract Factory terimi yabancı geldiyse, endişe etmeyin. Bir sonraki başlıkta bu desen ele alınacaktır.

```java
// Yaratıcı (Creator) sınıf: Bir ürün sınıfının bir nesnesini döndürmesi gereken fabrika yöntemini tanımlar.
    // Yaratıcının alt sınıfları genellikle bu yöntemin uygulamasını sağlar.
class Dialog is
// İçerik oluşturucu, fabrika yönteminin bazı varsayılan uygulamalarını da sağlayabilir.
    abstract method createButton():Button

    // Adına rağmen, yaratıcının temel sorumluluğu ürünleri oluşturmak değildir. 
    // Genellikle ürün yöntemi tarafından döndürülen ürün nesnelerine dayanan temel iş mantığı içerir.
    // Alt sınıflar, fabrika yöntemini geçersiz kılarak (override) ve farklı bir ürün türünü döndürerek
    // bu iş mantığını dolaylı olarak değiştirebilirler.
    method render() is
        // Bir ürün (product) nesnesi oluşturmak için fabrika yöntemini çağırın. 
        // Fabrika yöntemi yukarıda abstract method olarak çağrılmıştır.
        Button okButton = createButton()
        // Şimdi ürünü kullanın.
        okButton.onClick(closeDialog)
        okButton.render()

// Sonuçlanan ürünün türünü değiştirmek için yaratıcılar fabrika yöntemini geçersiz kılar (override).
class WindowsDialog extends Dialog is
    method createButton():Button is
        return new WindowsButton()

class WebDialog extends Dialog is
    method createButton():Button is
        return new HTMLButton()
// Ürün arayüzü (interface), tüm somut ürünlerin uygulaması gereken işlemleri beyan eder.
interface Button is
    method render()
    method onClick(f)

class WindowsButton implements Button is
    method render(a, b) is
        // Windows stilinde bir düğmeyi render et.
    method onClick(f) is
        // Bir native işletim sistemine tıklama olayı bağla.

class HTMLButton implements Button is
    method render(a, b) is
        // Bir düğün HTML temsilini döndür.
    method onClick(f) is
        // Bir web tarayıcısı tıklama olayını bağla.

class Application is
    field dialog: Dialog

    // Uygulama, mevcut yapılandırmaya veya ortam ayarlarına bağlı olarak bir yaratıcının türünü seçer.
    method initialize() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            dialog = new WindowsDialog()
        else if (config.OS == "Web") then
            dialog = new WebDialog()
        else
            throw new Exception("Hata! Bilinmeyen işletim sistemi.")

    // İstemci kod, somut bir yaratıcının örneğiyle çalışırken bile temel arayüzü (interface) üzerinden çalışır.
    // İstemci, yaratıcıyı temel arayüz üzerinden kullanmaya devam ettiği sürece, herhangi bir yaratıcı alt sınıfını (creator's subclass) geçirebilirsiniz.
    method main() is
        this.initialize()
        dialog.render()
```

## 💡Uygulanabilirlik

**🐞 Kodunuzun çalışması gereken nesnelerin türlerini ve bağımlılıklarını önceden bilmiyorsanız Fabrika Yöntemini kullanın.**

⚡️ Fabrika Yöntemi, ürün (product) oluşturma kodunu ürünü gerçekten kullanan koddan ayırır. Bu nedenle ürün oluşturma kodunu kodun geri kalanından bağımsız olarak genişletmek daha kolaydır.

Örneğin, uygulamaya yeni bir ürün türü eklemek isterseniz, sadece yeni bir yaratıcı alt sınıfı (creator subclass) oluşturmanız ve kodun içindeki fabrika yöntemini geçersiz kılmanız yeterlidir.

----------------
**🐞 Kütüphanenizin (library) veya çerçevenizin (framework) kullanıcılarına dahili bileşenlerini genişletmenin bir yolunu sağlamak istediğinizde Fabrika Yöntemini kullanın.**

⚡️ Kalıtım (Inheritance) muhtemelen bir kütüphane veya çerçevenin (framework) varsayılan davranışını genişletmenin en kolay yolu olabilir. Ancak çerçeve (framework), standart bir bileşen yerine alt sınıfınızın kullanılması gerektiğini nasıl anlar?

Çözüm, çerçeve genelinde bileşenleri oluşturan kodu tek bir fabrika yöntemine indirgemek ve bileşeni genişletmek için olduğu gibi bu yöntemi geçersiz kılmalarına izin vermek olabilir.

Bunu nasıl çalıştırabileceğimizi görelim. Bir açık kaynaklı bir UI çerçevesi (framework) kullanarak bir uygulama yazdığınızı hayal edin. Uygulamanızın yuvarlak düğmelere sahip olması gerekiyor, ancak çerçeve yalnızca kare düğmeler sunuyor. Standart `Button` sınıfını `RoundButton` alt sınıfı ile genişletirsiniz (extend edersiniz). Ancak şimdi ana `UIFramework` sınıfına, varsayılan kare düğmeler sınıfı yerine yeni düğme alt sınıfını kullanmasını söylemeniz gerekiyor.

Bunu başarmak için, temel bir çerçeve sınıfından `UIWithRoundButtons` adında bir alt sınıf (subclass) oluşturur ve `createButton` yöntemini geçersiz kılarsınız (override). Bu yöntem, temel sınıfta `Button` nesnelerini döndürürken, alt sınıfınızın `RoundButton` nesnelerini döndürmesini sağlarsınız. Şimdi `UIFramework` yerine `UIWithRoundButtons` sınıfını kullanmanız yeterlidir! İşte bu kadar kolay!

----------------
**🐞 Mevcut nesneleri her seferinde yeniden oluşturmak yerine yeniden kullanarak sistem kaynaklarından tasarruf etmek istediğinizde Fabrika Yöntemini kullanın.**

⚡️ Büyük ve yoğun kaynakları nesnelerle, özellikle veritabanı bağlantıları, dosya sistemleri ve ağ kaynakları gibi işlemler yaparken, bu ihtiyacı sıklıkla deneyimlersiniz.

Mevcut bir nesneyi yeniden kullanmak için yapılması gerekenleri düşünelim:

1. Öncelikle, oluşturulan tüm nesnelerin izlenmesi için bir depo oluşturmanız gerekir. 
2. Birisi bir nesne talep ettiğinde, programın bu depo içinde boş bir nesneyi araması gerekir. 
3. ... ve ardından bu nesneyi istemci koduna geri döndürmelidir. 
4. Eğer boşta herhangi bir yoksa, program yeni bir nesne oluşturmalı ve depoya eklemelidir. 

Bu oldukça fazla kod yazmak anlamına gelir! Programı yinelenen kodlarla kirletmemeniz için bunların hepsinin tek bir yere konulması gerekir.

Bu kodun yerleştirilebileceği en açık ve uygun yer, yeniden kullanmaya çalıştığımız nesnelerin sınıfının yapıcısı (constructor) olabilir. Ancak bir yapılandırıcı, tanım gereği her zaman yeni nesneleri döndürmelidir. Mevcut örnekleri döndüremez!

Bu nedenle, yeni nesneleri oluşturabilen ve mevcut nesneleri yeniden kullanabilen bir düzenli bir yönteme ihtiyacınız vardır. Bu durum bir fabrika yöntemine oldukça benzerdir.

## 📝 Nasıl Uygulanır?
1. Tüm ürünlerin (products) aynı arayüzü (interface) takip etmesini sağlayın. Bu arayüz, her üründe mantıklı olan yöntemleri bildirmelidir.
2. Yaratıcı sınıfın (Creator class) içine boş bir fabrika yöntemi (factory method) ekleyin. Yöntemin dönüş türü, ortak ürün arayüzü (interface) ile eşleşmelidir.
3. Yaratıcı sınıfın kodunda ürün oluşturuculara ilişkin tüm referansları bulabilirsiniz. Ürün oluşturma kodunu fabrika yöntemine çıkarırken bunları teker teker fabrika yöntemine yapılan çağrılarla değiştirin.
Döndürülen (return edilen) ürünün türünü kontrol etmek için fabrika yöntemine geçici bir parametre eklemeniz gerekebilir.
Bu noktada, fabrika yöntemi kodu oldukça karmaşık görünebilir. Hangi ürün sınıfının oluşturulacağını seçen büyük bir `switch` ifadesine sahip olabilir. Ama merak etmeyin, yakında düzelteceğiz.
4. Şimdi fabrika yönteminde listelenen her ürün türü için bir dizi yaratıcı alt sınıf oluşturun. Alt sınıflardaki fabrika yöntemini geçersiz kılın (override edin) ve temel yöntemden uygun yapı kodu parçalarını çıkarın.
5. Çok fazla ürün türü varsa ve hepsi için alt sınıflar oluşturmak mantıklı değilse, temel sınıftan kontrol parametresini alt sınıflarda yeniden kullanabilirsiniz.
Örneğin, şu sınıf hiyerarşisine sahip olduğunuzu hayal edin: Temel bir `Mail` sınıfı, bu sınıfın birkaç alt sınıfı: `AirMail` ve `GroundMail`. 
`Transport` sınıfları ise `Plane`, `Truck` ve `Train` olsun. `AirMail` sınıfı sadece `Plane` nesnelerini kullanırken, `GroundMail` hem `Truck` hem de `Train` nesneleriyle çalışabilir. Dilerseniz iki durumu ele alacak yeni bir alt sınıf (örneğin `TrainMail`) oluşturabilirsiniz, ancak başka bir seçenek daha vardır: İstemci kodu, `GroundMail` sınıfının fabrika yöntemine istediği ürünü almak için bir argüman iletebilir.
6. Tüm dışarı çıkarımlardan (export edilmelerden) sonra temel fabrika yöntemi boşalmışsa onu soyut (abstract) hale getirebilirsiniz. Eğer geriye bir şey kaldıysa, bunu yöntemin varsayılan davranışı haline getirebilirsiniz.

## ⚖️ Artıları ve Eksileri

✅ Yaratıcı (creator) ile oluşturulan ürünler arasında sıkı bir bağlantı oluşmasını önlersiniz.

✅ *Tek Sorumluluk İlkesi (Single Responsibility Principle)*. Ürün oluşturma kodunu programda tek bir yere taşıyabilir, böylece kodun kolayca desteklenmesini sağlayabilirsiniz.

✅ *Açık/Kapalı Prensibi (Open/Closed Principle)*. Mevcut client kodunu bozmadan programa yeni ürün türleri tanıtabilirsiniz/ekleyebilirsiniz.


❌ Deseni uygulamak için birçok yeni alt sınıf tanıtmanız gerektiğinden dolayı kod daha karmaşık hale gelebilir. En iyi durum senaryosu, deseni mevcut yaratıcı sınıf hiyerarşisine dahil ettiğiniz zamandır.

## 🔀 Diğer Tasarım Desenleri İlişkileri
- Birçok tasarım, Fabrika Yöntemi (Factory method) (daha az karmaşık ve alt sınıflar yoluyla daha fazla özelleştirilebilir) kullanılarak başlar ve Soyut Fabrika (Abstract Factory), Prototip (Prototype) veya Oluşturucuya (Builder) (daha esnek, ancak daha karmaşık) gibi tasarım desenlerine doğru gelişir.
- Soyut Fabrika (Abstract Factory) deseni sınıfları genellikle bir dizi Fabrika Yöntemi (Factory method) desenine dayanır, ancak bu sınıflardaki yöntemleri oluşturmak için Prototip desenini de kullanabilirsiniz.
- Koleksiyon alt sınıflarının koleksiyonlarla uyumlu farklı türde yineleyiciler döndürmesine izin vermek için Iterator deseni ile birlikte Factory Method'u kullanabilirsiniz.
- Prototip deseni kalıtıma dayalı olmadığından dezavantajları yoktur. Öte yandan Prototip deseni, klonlanan nesnenin karmaşık bir şekilde başlatılmasını gerektirir. Fabrika Yöntemi deseni kalıtıma dayalıdır ancak bir başlatma adımı gerektirmez.
- Fabrika Yöntemi deseni, Şablon Yönteminin (Template Method) özelleştirilmiş bir halidir. Aynı zamanda bir Fabrika Yöntemi, büyük bir Şablon Yönteminin bir parçası olarak da hizmet edebilir.

## 👾 Kod Örnekleri

### Java
**Kullanım örneği:** Fabrika Yöntemi modeli Java kodunda yaygın olarak kullanılır. Kodunuz için yüksek düzeyde esneklik sağlamanız gerektiğinde çok kullanışlıdır.

Desen çekirdek Java kitaplıklarında mevcuttur:
-   [`java.util.Calendar#getInstance()`](http://docs.oracle.com/javase/8/docs/api/java/util/Calendar.html#getInstance--)
-   [`java.util.ResourceBundle#getBundle()`](http://docs.oracle.com/javase/8/docs/api/java/util/ResourceBundle.html#getBundle-java.lang.String-)
-   [`java.text.NumberFormat#getInstance()`](http://docs.oracle.com/javase/8/docs/api/java/text/NumberFormat.html#getInstance--)
-   [`java.nio.charset.Charset#forName()`](http://docs.oracle.com/javase/8/docs/api/java/nio/charset/Charset.html#forName-java.lang.String-)
-   [`java.net.URLStreamHandlerFactory#createURLStreamHandler(String)`](http://docs.oracle.com/javase/8/docs/api/java/net/URLStreamHandlerFactory.html)  (Bir protokole bağlı olarak farklı tekil nesneleri döndürür)
-   [`java.util.EnumSet#of()`](https://docs.oracle.com/javase/8/docs/api/java/util/EnumSet.html#of(E))
-   [`javax.xml.bind.JAXBContext#createMarshaller()`](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/JAXBContext.html#createMarshaller--)  ve diğer benzer yöntemler.

**Tanımlama:** Fabrika yöntemleri, somut sınıflardan nesneler oluşturan, oluşturucu yöntemleriyle tanınabilir. Nesne oluşturma sırasında somut sınıflar kullanılırken, fabrika yöntemlerinin dönüş türü genellikle soyut (abstract) sınıf veya arayüz (interface) olarak bildirilir.

#### Platformlar Arası GUI Öğelerinin Üretimi Örneği

Bu örnekte, Düğmeler (`Button`) bir ürün rolü oynar ve diyaloglar yaratıcı görevi görür.

Farklı diyalog türleri, kendi türlerindeki öğeleri zorunlu tutar. Bu nedenle her diyalog türü için bir alt sınıf oluşturuyoruz ve bunların fabrika yöntemlerini geçersiz kılıyoruz (override).

Artık her diyalog türü uygun düğme (`Button`) sınıflarını başlatacaktır. Oluşturulan alt sınıflar, ortak arayüzlerini (interfaces) kullanan ürünlerle çalışır, bu nedenle kodu tüm değişikliklerden sonra işlevsel kalır.

**📁 buttons**

⤵️ 📄 `buttons/Button.java`: Ortak ürün (product) arayüzü (interface):
```java
package fatihes1.factory_method.example.buttons;

/**
 * Common interface for all buttons.
 */
public interface Button {
    void render();
    void onClick();
}
```

⤵️ 📄 `buttons/HtmlButton.java`: Fabrika yöntemi

```java
package fatihes1.factory_method.example.buttons;

/**
 * HTML button implementation.
 */
public class HtmlButton implements Button {*-* 📄 `buttons/HtmlButton.java`: Fabrika yönteminden oluşturulan sınıf

    public void render() {
        System.out.println("<button>Test Button</button>");
        onClick();
    }

    public void onClick() {
        System.out.println("Click! Button says - 'Hello World!'");
    }
}
```

⤵️ 📄 `buttons/WindowsButton.java`: Fabrika yönteminden oluşturulan diğer bir sınıf

```java
package fatihes1.factory_method.example.buttons;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

/**
 * Windows button implementation.
 */
public class WindowsButton implements Button {
    JPanel panel = new JPanel();
    JFrame frame = new JFrame();
    JButton button;

    public void render() {
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JLabel label = new JLabel("Hello World!");
        label.setOpaque(true);
        label.setBackground(new Color(235, 233, 126));
        label.setFont(new Font("Dialog", Font.BOLD, 44));
        label.setHorizontalAlignment(SwingConstants.CENTER);
        panel.setLayout(new FlowLayout(FlowLayout.CENTER));
        frame.getContentPane().add(panel);
        panel.add(label);
        onClick();
        panel.add(button);

        frame.setSize(320, 200);
        frame.setVisible(true);
        onClick();
    }

    public void onClick() {
        button = new JButton("Exit");
        button.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                frame.setVisible(false);
                System.exit(0);
            }
        });
    }
}
```
**📁 factory**

⤵️ 📄 `factory/Dialog.java`: Temel oluşturucu

```java
package fatihes1.factory_method.example.factory;

import fatihes1.factory_method.example.buttons.Button;

/**
 * Base factory class. Note that "factory" is merely a role for the class. It
 * should have some core business logic which needs different products to be
 * created.
 */
public abstract class Dialog {

    public void renderWindow() {
        // ... other code ...

        Button okButton = createButton();
        okButton.render();
    }

    /**
     * Subclasses will override this method in order to create specific button
     * objects.
     */
    public abstract Button createButton();
}
```
⤵️ 📄 `factory/HtmlDialog.java:` Temel oluşturucudan elde edilen alt oluşturucu
```java
package fatihes1.factory_method.example.factory;

import fatihes1.factory_method.example.buttons.Button;
import fatihes1.factory_method.example.buttons.HtmlButton;

/**
 * HTML Dialog will produce HTML buttons.
 */
public class HtmlDialog extends Dialog {

    @Override
    public Button createButton() {
        return new HtmlButton();
    }
}
```

⤵️ 📄 `factory/WindowsDialog.java:` Temel oluşturucudan elde edilen diğer bir alt oluşturucu
```java
package fatihes1.factory_method.example.factory;

import fatihes1.factory_method.example.buttons.Button;
import fatihes1.factory_method.example.buttons.WindowsButton;

/**
 * Windows Dialog will produce Windows buttons.
 */
public class WindowsDialog extends Dialog {

    @Override
    public Button createButton() {
        return new WindowsButton();
    }
}
```

⤵️ 📄 `Demo.java:`  İstemci (Client) kodu
```java
package fatihes1.factory_method.example;

import fatihes1.factory_method.example.factory.Dialog;
import fatihes1.factory_method.example.factory.HtmlDialog;
import fatihes1.factory_method.example.factory.WindowsDialog;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    private static Dialog dialog;

    public static void main(String[] args) {
        configure();
        runBusinessLogic();
    }

    /**
     * The concrete factory is usually chosen depending on configuration or
     * environment options.
     */
    static void configure() {
        if (System.getProperty("os.name").equals("Windows 10")) {
            dialog = new WindowsDialog();
        } else {
            dialog = new HtmlDialog();
        }
    }

    /**
     * All of the client code should work with factories and products through
     * abstract interfaces. This way it does not care which factory it works
     * with and what kind of product it returns.
     */
    static void runBusinessLogic() {
        dialog.renderWindow();
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu (HtmlDialog)
```
<button>Test Button</button>
Click! Button says - 'Hello World!'
```
⤵️ 📄 `OutputDemo.png`: Çalıştırma Sonucu (WindowsDialog)

![](https://refactoring.guru/images/patterns/examples/java/factory-method/OutputDemo.png)












