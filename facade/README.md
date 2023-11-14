#  Cephe (Facade) Tasarım Deseni

##  💬 Amaç

Cephe, bir kitaplığa, bir çerçeveye veya diğer herhangi bir karmaşık sınıf kümesine basitleştirilmiş bir arayüz sağlayan yapısal bir tasarım desenidir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/facade/facade-2x.png)


</div>


##  🙁 Problem

Düşünün ki kodunuzu, karmaşık bir kütüphane veya çerçeveye ait bir dizi nesneyle çalışacak şekilde düzenlemeniz gerekiyor. Normalde, bu nesnelerin tümünü başlatmanız, bağımlılıkları izlemeniz, yöntemleri doğru sırayla yürütmeniz ve benzer işlemleri yapmanız gerekir.

Sonuç olarak, sınıflarınızın iş mantığı, 3. taraf sınıfların uygulama ayrıntılarına sıkı sıkıya bağlanır, bu da kodun anlaşılmasını ve bakımını zorlaştırır.


##  😊 Çözüm

Bir arayüz, çok sayıda hareketli parçaya sahip karmaşık bir alt sistemi basit bir şekilde sunan bir sınıftır. Bir arayüz, alt sistemi doğrudan kullanmak yerine sınırlı işlevsellik sunabilir. Ancak, yalnızca istemcilerin gerçekten ilgilendiği özellikleri içerir.

Bir arayüz, uygulamanızı çok sayıda özelliğe sahip karmaşık bir kütüphaneyle entegre etmeniz gerektiğinde işe yarar, ancak yalnızca bu kütüphanenin küçük bir bölümüne ihtiyacınız olduğunda istediğiniz verimi alamazsınız.

Örneğin, kısa komik kedi videolarını sosyal medyada paylaşan bir uygulama, teorik olarak profesyonel bir video dönüştürme kütüphanesini kullanabilir. Ancak gerçekte ihtiyacı olan şey, tek bir yönteme sahip bir sınıftır. Örneğin; `encode(filename, format)`. Böyle bir sınıfı oluşturduktan ve video dönüştürme kütüphanesiyle bağlantı kurduktan sonra, ilk arayüzünüze sahip olursunuz.

## 🚙 Gerçek Dünya Örneği

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/facade/live-example-en-2x.png)

*Telefonla sipariş verme..*

</div>

Telefon siparişi vermek için bir mağazayı aradığınızı düşünün. Bir operatör, mağazanın tüm hizmetlerine ve departmanlarına karşı sizin cephenizdir (facade). Operatör size sipariş sistemine, ödeme ağ geçitlerine ve çeşitli teslimat hizmetlerine basit bir sesli arayüz sağlar.

##  ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/facade/structure-2x.png)

</div>

1. **Facade** yani cephe, alt sistemin işlevselliğinin belirli bir bölümüne kolay erişim sağlar. Müşterinin talebini nereye yönlendireceğini ve tüm hareketli parçaların nasıl çalıştırılacağını bilir.
2. Tek bir cepheyi, onu başka bir karmaşık yapı haline getirebilecek ilgisiz özelliklerle kirletmeyi önlemek için bir **Additional Facade** sınıfı oluşturulabilir. Ek cepheler hem müşteriler hem de diğer cepheler tarafından kullanılabilir.
3. **Complex Subsystem** düzinelerce çeşitli nesneden oluşur. Hepsinin anlamlı bir şey yapmasını sağlamak için, nesneleri doğru sırayla başlatmak ve onlara uygun biçimde veri sağlamak gibi alt sistemin uygulama ayrıntılarına derinlemesine dalmanız gerekir.
Alt sistem sınıfları cephenin varlığından haberdar değildir. Sistem içinde çalışırlar ve doğrudan birbirleriyle çalışırlar.
4. **İstemci**, alt sistem nesnelerini doğrudan çağırmak yerine cepheyi kullanır.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte Cephe (Facade) modeli, karmaşık bir video dönüştürme çerçevesiyle etkileşimi basitleştirir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/facade/example-2x.png)

*Tek bir cephe sınıfında birden fazla bağımlılığın izole edilmesine bir örnek.*

</div>

Kodunuzu doğrudan düzinelerce çerçeve sınıfı ile çalıştırmak yerine, bu işlevselliği kapsayan ve kodun geri kalanından gizleyen bir arayüz sınıfı oluşturursunuz. Bu yapı aynı zamanda çerçevenin gelecekteki sürümlerine yükseltme veya başka bir çerçeve ile değiştirme çabasını minimize etmenize de yardımcı olur. Uygulamanızda değiştirmeniz gereken tek şey, arayüzün yöntemlerinin uygulanması olurdu.

```java
// Bunlar karmaşık bir 3. taraf video dönüştürme çerçevesinin bazı sınıflarıdır.
// Bu kodu kontrol etmediğimiz için basitleştiremiyoruz.

class VideoFile
// ...

class OggCompressionCodec
// ...

class MPEG4CompressionCodec
// ...

class CodecFactory
// ...

class BitrateReader
// ...

class AudioMixer
// ...


// Çerçevenin karmaşıklığını basit bir arayüzün ardında gizlemek için bir cephe sınıfı oluşturuyoruz.
// Bu, işlevsellik ve basitlik arasında bir denge anlamına gelir.
class VideoConverter is
    method convert(filename, format):File is
        file = new VideoFile(filename)
        sourceCodec = (new CodecFactory).extract(file)
        if (format == "mp4")
            destinationCodec = new MPEG4CompressionCodec()
        else
            destinationCodec = new OggCompressionCodec()
        buffer = BitrateReader.read(filename, sourceCodec)
        result = BitrateReader.convert(buffer, destinationCodec)
        result = (new AudioMixer()).fix(result)
        return new File(result)

// Uygulama sınıfları karmaşık çerçeve tarafından sağlanan milyonlarca sınıfa bağlı değildir.
// Ayrıca, çerçeveyi değiştirmeye karar verirseniz, yalnızca cephe sınıfını yeniden yazmanız yeterlidir.
class Application is
    method main() is
        convertor = new VideoConverter()
        mp4 = convertor.convert("komik-kediler-video.ogg", "mp4")
        mp4.save()

```


##  💡Uygulanabilirlik

**🐞 Facade desenini, karmaşık bir alt sistemde sınırlı ancak basit bir arayüze ihtiyacınız olduğunda kullanın.**

⚡️ Genellikle alt sistemler zaman içinde daha karmaşık hale gelir. Tasarım desenlerinin uygulanması genellikle daha fazla sınıf oluşturmaya yol açar. Bir alt sistem, çeşitli bağlamlarda daha esnek ve yeniden kullanılabilir hale gelebilir, ancak müşteriden talep ettiği yapılandırma ve tekrarlayan kod miktarı her zaman daha da büyür. Facade, alt sistemin en çok kullanılan özelliklerine kısa bir yol sağlayarak bu sorunu çözmeye çalışır ki bu özellikler, çoğu müşteri gereksinimine uygun olanlardır.
  
----------------

**🐞 Alt sistemleri katmanlara bölmek istediğinizde Facade desenini kullanın.**

⚡️ Her seviyenin giriş noktalarını tanımlamak için arayüzler oluşturun. Birden çok alt sistem arasındaki bağlantıları sadece arayüzler aracılığıyla iletişim kurmalarını gerektirerek bağımlılığı azaltabilirsiniz.
Örneğin, video dönüşüm çerçevemize geri dönelim. Bu, iki katmana ayrılabilir: video ve sesle ilgili olanlar. Her katman için bir arayüz oluşturabilir ve sonra her katmanın sınıflarını bu arayüzler aracılığıyla iletişim kuracak şekilde düzenleyebilirsiniz. Bu yaklaşım, Mediator desenine çok benzer görünüyor.


##  📝 Nasıl Uygulanır?

1. Mevcut bir alt sistemin zaten sağladığı arayüzden daha basit bir arayüz sunmanın mümkün olup olmadığını kontrol edin. Eğer bu arayüz, istemci kodunu alt sistemin birçok sınıfından bağımsız hale getiriyorsa doğru yoldasınız demektir.

2. Bu arayüzü yeni bir facade sınıfında bildirin ve uygulayın. Facade, istemci kodundan gelen çağrıları alt sistem objelerine yönlendirmelidir. Facade, alt sistemin başlatılması ve daha sonraki yaşam döngüsünün yönetilmesinden sorumlu olmalıdır, ancak istemci kodu bunu zaten yapıyorsa bu işlevi yönetmelidir.

3. Desenin tam faydasını elde etmek için, tüm istemci kodunun alt sistemle sadece arayüz aracılığıyla iletişim kurmasını sağlayın. Artık istemci kodu, alt sistem kodundaki herhangi bir değişiklikten korunur. Örneğin, bir alt sistem yeni bir sürüme yükseltildiğinde, sadece facade içindeki kodu değiştirmeniz gerekecektir.

4. Eğer facade çok büyük hale gelirse, davranışının bir kısmını yeni, daha rafine bir facade sınıfına çıkarmayı düşünün.


##  ⚖️ Artıları ve Eksileri

✅ Kodunuzu bir alt sistemin karmaşıklığından izole edebilirsiniz.

❌ Bir cephe, bir uygulamanın tüm sınıflarıyla birleştirilmiş bir tanrı nesnesi ([god object](https://en.wikipedia.org/wiki/God_object)) haline gelebilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Facade** deseni, mevcut nesneler için yeni bir arayüz tanımlar. **Adapter** deseni ise mevcut arayüzü kullanılabilir hale getirmeye çalışır. **Adapter** deseni genellikle sadece bir nesneyi sarmalar, oysa **Facade** deseni bir nesne alt sistemiyle çalışır.

- **Abstract Factory** deseni, alt sistem nesnelerinin nasıl oluşturulduğunu istemci kodundan gizlemek istediğinizde **Facade** deseni için bir alternatif olarak hizmet edebilir.

- **Flyweight** deseni, çok sayıda küçük nesne oluşturmanın nasıl yapıldığını gösterirken, **Facade** deseni, bir alt sistemleri temsil eden tek bir nesne oluşturmanın nasıl yapıldığını gösterir.

- **Facade** ve **Mediator** deseni benzer görevlere sahiptir: sıkı bir şekilde bağlı çok sayıda sınıf arasındaki işbirliğini düzenlemeye çalışırlar.
	- **Facade** deseni, nesneler arasındaki iletişimi düzenlemeye çalışırken, **Mediator** deseni sistemin bileşenleri arasındaki iletişimi merkezileştirir.
	- Bileşenler yalnızca aracı nesneyi bilirler ve doğrudan iletişim kurmazlar.

- Bir **Facade** deseni sınıfı, genellikle bir **Singleton** desenine dönüştürülebilir, çünkü çoğu durumda tek bir facade nesnesi yeterlidir.

- **Facade** deseni, bir karmaşık varlığı tamponlayan ve kendi başına başlatan **Proxy** desenine benzer. **Facade** deseninin aksine, **Proxy** deseni, hizmet nesnesiyle aynı arayüze sahiptir, bu da onları değiştirilebilir kılar.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Cephe modeli, Java ile yazılan uygulamalarda yaygın olarak kullanılır. Özellikle karmaşık kitaplıklar ve API'lerle çalışırken kullanışlıdır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`javax.faces.context.FacesContext`](http://docs.oracle.com/javaee/7/api/javax/faces/context/FacesContext.html)  uses  [`LifeCycle`](http://docs.oracle.com/javaee/7/api/javax/faces/lifecycle/Lifecycle.html),  [`ViewHandler`](http://docs.oracle.com/javaee/7/api/javax/faces/application/ViewHandler.html),  [`NavigationHandler`](http://docs.oracle.com/javaee/7/api/javax/faces/application/NavigationHandler.html)  classes under the hood, but most clients aren’t aware of that.
    
-   [`javax.faces.context.ExternalContext`](http://docs.oracle.com/javaee/7/api/javax/faces/context/ExternalContext.html)  uses  [`ServletContext`](http://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html),  [`HttpSession`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpSession.html),  [`HttpServletRequest`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html),  [`HttpServletResponse`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletResponse.html)  and others inside.


**Tanımlama:** Facade, basit bir arayüze sahip olan ancak işin çoğunu diğer sınıflara devreden bir sınıfta tanınabilir. Genellikle cepheler, kullandıkları nesnelerin tüm yaşam döngüsünü yönetir.

 
####  Karmaşık bir video dönüştürme kitaplığı için basit arayüz

Bu örnekte Facade, karmaşık bir video dönüştürme çerçevesiyle iletişimi basitleştirir.

Facade, çerçevenin doğru sınıflarını yapılandırmanın ve sonucu doğru formatta almanın tüm karmaşıklığını ele alan tek bir yönteme sahip tek bir sınıf sağlar. 

**📁 some_complex_media_library**: Karmaşık video dönüştürme kitaplığı

⤵️ 📄 `some_complex_media_library/VideoFile.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public class VideoFile {
    private String name;
    private String codecType;

    public VideoFile(String name) {
        this.name = name;
        this.codecType = name.substring(name.indexOf(".") + 1);
    }

    public String getCodecType() {
        return codecType;
    }

    public String getName() {
        return name;
    }
}
```

⤵️ 📄 `some_complex_media_library/Codec.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public interface Codec {
}
```


⤵️ 📄 `some_complex_media_library/MPEG4CompressionCodec.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public class MPEG4CompressionCodec implements Codec {
    public String type = "mp4";

}
```

⤵️ 📄 `some_complex_media_library/OggCompressionCodec.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public class OggCompressionCodec implements Codec {
    public String type = "ogg";
}
```

⤵️ 📄 `some_complex_media_library/CodecFactory.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public class CodecFactory {
    public static Codec extract(VideoFile file) {
        String type = file.getCodecType();
        if (type.equals("mp4")) {
            System.out.println("CodecFactory: extracting mpeg audio...");
            return new MPEG4CompressionCodec();
        }
        else {
            System.out.println("CodecFactory: extracting ogg audio...");
            return new OggCompressionCodec();
        }
    }
}
```

⤵️ 📄 `some_complex_media_library/BitrateReader.java`

```java
package fatihes1.facade.example.some_complex_media_library;

public class BitrateReader {
    public static VideoFile read(VideoFile file, Codec codec) {
        System.out.println("BitrateReader: reading file...");
        return file;
    }

    public static VideoFile convert(VideoFile buffer, Codec codec) {
        System.out.println("BitrateReader: writing file...");
        return buffer;
    }
}
```

⤵️ 📄 `some_complex_media_library/AudioMixer.java`

```java
package fatihes1.facade.example.some_complex_media_library;

import java.io.File;

public class AudioMixer {
    public File fix(VideoFile result){
        System.out.println("AudioMixer: fixing audio...");
        return new File("tmp");
    }
}
```

**📁 facade**

⤵️ 📄 `facade/VideoConversionFacade.java`: Cephe, video dönüştürme için basit bir arayüz sağlar

```java
package fatihes1.facade.example.facade;

import fatihes1.facade.example.some_complex_media_library.*;

import java.io.File;

public class VideoConversionFacade {
    public File convertVideo(String fileName, String format) {
        System.out.println("VideoConversionFacade: conversion started.");
        VideoFile file = new VideoFile(fileName);
        Codec sourceCodec = CodecFactory.extract(file);
        Codec destinationCodec;
        if (format.equals("mp4")) {
            destinationCodec = new MPEG4CompressionCodec();
        } else {
            destinationCodec = new OggCompressionCodec();
        }
        VideoFile buffer = BitrateReader.read(file, sourceCodec);
        VideoFile intermediateResult = BitrateReader.convert(buffer, destinationCodec);
        File result = (new AudioMixer()).fix(intermediateResult);
        System.out.println("VideoConversionFacade: conversion completed.");
        return result;
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.facade.example;

import fatihes1.facade.example.facade.VideoConversionFacade;

import java.io.File;

public class Demo {
    public static void main(String[] args) {
        VideoConversionFacade converter = new VideoConversionFacade();
        File mp4Video = converter.convertVideo("youtubevideo.ogg", "mp4");
        // ...
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu

```
VideoConversionFacade: conversion started.
CodecFactory: extracting ogg audio...
BitrateReader: reading file...
BitrateReader: writing file...
AudioMixer: fixing audio...
VideoConversionFacade: conversion completed.
```



