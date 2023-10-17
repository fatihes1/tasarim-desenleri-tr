#  Dekoratör (Decorator) Tasarım Deseni

> Diğer adıyla: Paketleyici/Sarmalayıcı (Wrapper)


##  💬 Amaç

Dekoratör, nesneleri, davranışları içeren özel sarmalayıcı nesnelerin içine yerleştirerek nesnelere yeni davranışlar eklemenizi sağlayan yapısal (structural) bir tasarım desenidir.

![](https://refactoring.guru/images/patterns/content/decorator/decorator-2x.png)


##  🙁 Problem

Diğer programların kullanıcılarını önemli olaylar hakkında bilgilendirmesine olanak tanıyan bir bildirim kitaplığı/kütüphanesi üzerinde çalıştığınızı hayal edin.

Kütüphanenin ilk sürümü, yalnızca birkaç alanı, bir yapıcıyı ve tek bir gönderme yöntemini içeren `Notifier` sınıfına dayanıyordu. Bu yöntem, bir istemciden bir ileti argümanını kabul edebilir ve iletiyi bir bildirimciye (notifier) yapıcı aracılığıyla iletilen e-posta listesine gönderebilirdi. İstemci olarak hareket eden üçüncü taraf bir uygulama, bildirimci nesnesini bir kez oluşturup yapılandırmalı ve ardından her önemli bir şey olduğunda bunu kullanmalıydı.


![](https://refactoring.guru/images/patterns/diagrams/decorator/problem1-en-2x.png)

*Bir program, önemli etkinliklerle ilgili bildirimleri önceden tanımlanmış bir e-posta grubuna göndermek için notifier sınıfını kullanabilir.*

Bir noktada, kütüphanenin kullanıcılarının yalnızca e-posta bildirimlerinden daha fazlasını beklediğini fark edersiniz. Birçoğu kritik konularda SMS almayı tercih eder. Diğerleri Facebook'ta bildirilmeyi ister ve elbette kurumsal kullanıcılar Slack bildirimleri almayı çok severler.

![](https://refactoring.guru/images/patterns/diagrams/decorator/problem2-2x.png)

*Her bildirim türü, bir bildirimcinin (notifier) alt sınıfı olarak uygulanır.*

Ne kadar zor olabilir ki? `Notifier` yani sınıfını genişlettiniz ve ek bildirim yöntemlerini yeni alt sınıflara koydunuz. Artık istemcinin istediği bildirim sınıfını örneklemesi ve tüm sonraki bildirimler için kullanması gerekiyordu.

Ancak, ardından biri size makul bir şekilde şunu sordu: 'Neden birden fazla bildirim türünü aynı anda kullanamıyorsunuz? Eviniz yanıyorsa, muhtemelen her kanal aracılığıyla bilgilendirilmek istersiniz.' Konu öznelinde bakınca oldukça haklı bir soru değil mi?

Bu sorunu ele almak için birkaç bildirim yöntemini aynı sınıf içinde birleştiren özel alt sınıflar oluşturarak çözmeye çalıştınız. Ancak, bu yaklaşımın hem kütüphane kodunu hem de istemci kodunu aşırı şişireceği hızlıca kendini belli eder.

![](https://refactoring.guru/images/patterns/diagrams/decorator/problem3-2x.png)

*Alt sınıfların kombinasyonu çoğaldıkça karmaşıklaşan yapı.*

Bildirim sınıflarını yapılandırmanın başka bir yolunu bulmalısınız ki sayıları yanlışlıkla Guinness rekorlarını kırmasın. Değil mi? 😅

##  😊 Çözüm

Bir nesnenin davranışını değiştirmeniz gerektiğinde akla gelen ilk şey bir sınıfı genişletmektir. Ancak, kalıtımın dikkate almanız gereken ciddi bazı sakıncaları vardır.

- **Kalıtım statiktir.** Mevcut bir nesnenin davranışını çalışma zamanında değiştiremezsiniz. Yalnızca nesnenin tamamını, farklı bir alt sınıftan yaratılan başka bir nesne ile değiştirebilirsiniz. 
- **Alt sınıflar** yalnızca bir ana sınıfa sahip olabilir. Çoğu dillerde kalıtım, bir sınıfın aynı anda birden çok sınıfın davranışlarını devralmasına izin vermez. 

Bu sakıncaları aşmanın yollarından biri, Kalıtım (Inheritance) deseni yerine Birleştirme (Aggregation) deseni veya Kompozisyon (Composition) deseni kullanmaktır. Her iki alternatif de neredeyse aynı şekilde çalışır: bir nesne başka bir nesneye bir referansa sahiptir ve ona bazı işleri devreder, oysa kalıtım ile nesne kendisi o işi yapabilir ve davranışı üst sınıfından devralabilir.

Bu yeni yaklaşımla bağlı yardımcı (helper) nesneyi kolayca başka bir nesne ile değiştirebilir ve çalışma zamanında konteynerın davranışını değiştirebilirsiniz. Bir nesne, çeşitli sınıfların davranışını kullanabilir, birden fazla nesneye referanslara sahip olabilir ve onlara her türlü işi devredebilir. Birleştirme/Kompozisyon deseni, Dekoratör deseni dahil birçok tasarım deseninin arkasındaki temel ilkedir. Bu konuyla ilgili olarak, desen tartışmasına geri dönelim.


![](https://refactoring.guru/images/patterns/diagrams/decorator/solution1-en-2x.png)

*Inheritance vs. Aggregation*

Sarıcı (Wrapper), Dekoratör deseninin ana fikrini açıkça ifade eden alternatif bir takma adıdır. Bir sarıcı, hedef bir nesne ile ilişkilendirilebilen bir nesnedir. Sarıcı, hedefle aynı yöntem setini içerir ve aldığı tüm istekleri hedefe iletilmek üzere ona devreder. Ancak sarıcı, isteği hedefe iletmeden önce veya sonra işlemler yaparak sonucu değiştirebilir.

Basit bir sarıcı ne zaman gerçek bir dekoratör olur? Bahsettiğim gibi, sarıcı, sarılı nesne ile aynı arayüzü (interface) uygular. Bu nedenle istemcinin bakış açısından bu nesneler aynıdır. Sarıcı desenin referans alanının bu arayüzü takip eden herhangi bir nesneyi kabul etmesini sağlamalısınız. Bu, bir nesneyi birden çok sarıcı ile kaplayarak, tüm sarıcıların birleşik davranışını ona eklemenizi sağlar.

Bildirimler örneğimizde, basit e-posta bildirimi davranışını temel Bildirimci (`Notifier`) sınıfının içinde bırakalım, ancak diğer tüm bildirim yöntemlerini dekoratör haline getirelim.

![](https://refactoring.guru/images/patterns/diagrams/decorator/solution2-2x.png)

*Çeşitli bildirim yöntemleri dekoratör haline gelir.*

İstemci kodunun, temel bir bildirim nesnesini, müşterinin tercihleriyle eşleşen bir dizi dekoratöre sarması gerekir. Ortaya çıkan nesneler bir yığın (stack) olarak yapılandırılacaktır.

![](https://refactoring.guru/images/patterns/diagrams/decorator/solution3-en-2x.png)

*Uygulamalar, karmaşık bildirim dekoratör yığınlarını yapılandırabilir.*

Yığındaki son dekoratör, istemcinin aslında çalıştığı nesne olacaktır. Tüm dekoratörler, temel bildirimci ile aynı arayüzü uyguladığı için, istemci kodunun geri kalanı, saf (pure) bildirimci nesnesi ile mi yoksa dekore edilmiş nesne ile mi çalıştığına aldırmaz.

Bu yaklaşımı mesaj biçimlendirme veya alıcı listesi oluşturma gibi diğer davranışlara da uygulayabiliriz. İstemci, diğerleri ile aynı arayüzü takip ettiği sürece özel dekoratörlerle nesneyi dekore edebilir.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/content/decorator/decorator-comic-1-2x.png)

*Birden fazla parça kıyafet giyerek birleşik bir etki elde edersiniz.*

Giysi giymek, dekoratörleri kullanmanın bir örneğidir. Soğuk olduğunuzda kendinizi bir kazakla sararsınız. Kazakla hala soğuksanız, üzerine bir ceket giyebilirsiniz. Yağmur yağıyorsa, bir yağmurluk giyebilirsiniz. Tüm bu giysiler temel davranışınızı extend alır , ancak sizin bir parçanız değildir. Sıcak bir ortama girdiğinizde ve artık ihtiyaç duymadığınızda giysinin herhangi bir parçasını kolayca çıkarabilirsiniz.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/decorator/structure-2x.png)

1. **Component**, hem sarmalayıcılar hem de sarılmış nesneler için ortak arayüzü bildirir.
2. **Concrete Component**, sarılmış nesnelerin bir sınıfıdır. Dekoratörler tarafından değiştirilebilecek temel davranışı tanımlar.
3. **Base Decorator** sınıfı sarılmış bir nesneye referans vermek için bir alana sahiptir. Alanın türü, hem bileşenleri hem de dekoratörleri içerebilmesi için bileşen arayüzü (component interface) olarak bildirilmelidir. Temel dekoratör tüm işlemleri sarılmış nesneye devreder.
4. **Concrete Decorators**, bileşenlere dinamik olarak eklenebilecek ekstra davranışları tanımlar. Concrete decorators, temel dekoratörün yöntemlerini geçersiz kılar ve davranışlarını ana yöntemi çağırmadan önce veya sonra yürütür.
5. **Client**, bileşen arayüzü aracılığıyla tüm nesnelerle çalıştığı sürece bileşenleri birden çok dekoratör katmanına sarabilir.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte Dekoratör modeli, hassas verileri, bu verileri gerçekten kullanan koddan bağımsız olarak sıkıştırmanıza ve şifrelemenize olanak tanır.

![](https://refactoring.guru/images/patterns/diagrams/decorator/example-2x.png)

*Şifreleme ve sıkıştırma dekoratörleri örneği.*



Uygulama, veri kaynağı nesnesini iki dekoratör çifti ile sarar. Her iki sargı, verinin diske yazılma ve diskten okunma şeklini değiştirir:

- Veri diske yazılmadan hemen önce, dekoratörler veriyi şifreler ve sıkıştırır. Orijinal sınıf, değişiklik hakkında bilgi sahibi olmadan şifrelenmiş ve korunan veriyi dosyaya yazar.

- Veri diske yazıldıktan hemen sonra, aynı dekoratörlerden geçer, bu da veriyi açıklar ve çözer.

Dekoratörler ve veri kaynağı sınıfı aynı arayüzü uygular, bu da onları istemci kodunda tümüyle değiştirilebilir kılar.

```java
// Bileşen arayüzü, süsleyiciler tarafından değiştirilebilecek işlemleri tanımlar.
interface DataSource is
    method writeData(data)
    method readData():data

// Somut bileşenler işlemler için varsayılan uygulamaları sağlar.
// Bir programda bu sınıfların çeşitli varyasyonları olabilir.
class FileDataSource implements DataSource is
    constructor FileDataSource(filename) { ... }

    method writeData(data) is
        // Veriyi dosyaya yaz.

    method readData():data is
        // Veriyi dosyadan oku.

// Temel süsleyici sınıfı, diğer bileşenlerle aynı arayüzü izler.
// Bu sınıfın başlıca amacı, tüm somut süsleyiciler için sarma arayüzünü tanımlamaktır.
// Sarma kodunun varsayılan uygulaması, sarmalanan bir bileşeni saklamak ve
// bunu başlatma araçları gibi bir alan içermesi olabilir.
class DataSourceDecorator implements DataSource is
    protected field wrappee: DataSource

    constructor DataSourceDecorator(source: DataSource) is
        wrappee = source

    // Temel süsleyici, tüm işi sadece sarmalanan bileşene yönlendirir.
    // Somut süsleyicilerde ek davranışlar ekleyebilir.
    method writeData(data) is
        wrappee.writeData(data)

    // Somut süsleyiciler, bir nesnenin sarmalanan nesneyi doğrudan çağırmak yerine
    // işlemin üst sınıf uygulamasını çağırabilir. Bu yaklaşım, süsleyici sınıflarının
    // genişletilmesini kolaylaştırır.
    method readData():data is
        return wrappee.readData()

// Somut süsleyiciler, sarmalanan nesne üzerinde işlemler yapmalıdır,
// ancak sonuca kendi katkılarını ekleyebilirler. Süsleyiciler ek davranışı,
// sarmalanan nesneye çağrıdan önce veya sonra yürütebilirler.
class EncryptionDecorator extends DataSourceDecorator is
    method writeData(data) is
        // 1. Gönderilen veriyi şifrele.
        // 2. Şifrelenmiş veriyi sarmalananın writeData yöntemine ilet.

    method readData():data is
        // 1. Veriyi sarmalananın readData yönteminden al.
        // 2. Eğer veri şifreliyse onu çözmeye çalış.
        // 3. Sonucu döndür.

// Nesneleri birkaç kat süsleyici ile sarabilirsiniz.
class CompressionDecorator extends DataSourceDecorator is
    method writeData(data) is
        // 1. Gönderilen veriyi sıkıştır.
        // 2. Sıkıştırılmış veriyi sarmalananın writeData yöntemine ilet.

    method readData():data is
        // 1. Veriyi sarmalananın readData yönteminden al.
        // 2. Eğer veri sıkıştırılmışsa onu açmaya çalış.
        // 3. Sonucu döndür.

// Seçenek 1. Süsleyici montajının basit bir örneği.
class Application is
    method dumbUsageExample() is
        source = new FileDataSource("somefile.dat")
        source.writeData(salaryRecords)
        // Hedef dosya düz verilerle yazıldı.

        source = new CompressionDecorator(source)
        source.writeData(salaryRecords)
        // Hedef dosya sıkıştırılmış verilerle yazıldı.

        source = new EncryptionDecorator(source)
        // source değişkeni şimdi şunu içerir:
        // Şifreleme > Sıkıştırma > FileDataSource
        source.writeData(salaryRecords)
        // Dosya sıkıştırılmış ve şifrelenmiş verilerle yazıldı.

// Seçenek 2. Harici bir veri kaynağı kullanan istemci kodu.
// SalaryManager nesneleri veri depolama ayrıntılarını bilmemeli veya umursamamalıdır.
// Önceden yapılandırılmış bir veri kaynağı ile çalışırlar.
class SalaryManager is
    field source: DataSource

    constructor SalaryManager(source: DataSource) { ... }

    method load() is
        return source.readData()

    method save() is
        source.writeData(salaryRecords)
    // ...Diğer faydalı yöntemler...

// Uygulama, yapılandırmaya veya ortama bağlı olarak çalışma zamanında
// farklı süsleyici yığınlarını birleştirebilir.
class ApplicationConfigurator is
    method configurationExample() is
        source = new FileDataSource("salary.dat")
        if (enabledEncryption)
            source = new EncryptionDecorator(source)
        if (enabledCompression)
            source = new CompressionDecorator(source)

        logger = new SalaryManager(source)
        salary = logger.load()
    // ...

```


##  💡Uygulanabilirlik

**🐞 Çalışma zamanında nesnelere, bu nesneleri kullanan kodu bozmadan ekstra davranışlar atayabilmeniz gerektiğinde Dekoratör modelini kullanın.**

⚡️ Dekoratör, iş mantığınızı katmanlar halinde yapılandırmanıza, her katman için bir dekoratör oluşturmanıza ve çalışma zamanında bu mantığın çeşitli kombinasyonlarıyla nesneler oluşturmanıza olanak tanır. İstemci kodu, hepsi ortak bir arayüzü takip ettiğinden, tüm bu nesnelere aynı şekilde davranabilir.
  
----------------

**🐞 Kalıtım kullanarak bir nesnenin davranışını genişletmek zor olduğunda veya mümkün olmadığında bu modeli kullanın.**

⚡️ Birçok programlama dilinde, bir sınıfın daha fazla genişletilmesini önlemek için kullanılabilecek `final` anahtar sözcüğü bulunur. Son sınıf için, mevcut davranışı yeniden kullanmanın tek yolu, Decorator modelini kullanarak sınıfı kendi sarmalayıcınızla sarmak olacaktır.


##  📝 Nasıl Uygulanır?

1. İş alanınızın (domain), üzerinde birden çok isteğe bağlı katman bulunan birincil bileşen (component) olarak gösterilebildiğinden emin olun.

2. Hem birincil bileşen hem de isteğe bağlı katmanlar için hangi yöntemlerin ortak olduğunu öğrenin. Bir bileşen arabirimi (interface) oluşturun ve bu yöntemleri orada bildirin.

3. Concrete component sınıfı oluşturun ve içindeki temel davranışları tanımlayın.

4. Bir temel dekoratör sınıfı oluşturun. Sarılmış bir nesneye referansı saklamak için bir alana sahip olmalıdır. Alan, dekoratörlerin yanı sıra concrete componentlere de bağlantıya izin vermek için bileşen arayüz tipiyle bildirilmelidir. Temel dekoratörü tüm işi sarılmış nesneye devretmelidir.

5. Tüm sınıfların bileşen arayüzünü (component interface) uyguladığından emin olun.

6. Temel dekoratörden uzatarak concrete dekoratörler oluşturun. Concrete bir dekoratör, davranışını ana yönteme yapılan çağrıdan önce veya sonra yürütmelidir (bu, her zaman sarılmış nesneye temsilci gönderir).

7. İstemci kodu, dekoratörlerin oluşturulmasından ve bunların müşterinin ihtiyaç duyduğu şekilde oluşturulmasından sorumlu olmalıdır.


  

##  ⚖️ Artıları ve Eksileri

✅ Yeni bir alt sınıf oluşturmadan bir nesnenin davranışını genişletebilirsiniz.

✅ Çalışma zamanında bir nesneye sorumluluklar ekleyebilir veya kaldırabilirsiniz.

✅ Bir nesneyi birden çok dekoratöre sararak çeşitli davranışları birleştirebilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle). Pek çok olası davranış çeşidini uygulayan yekpare bir sınıfı birkaç küçük sınıfa bölebilirsiniz.

❌ Belirli bir ambalajı ambalaj yığınından çıkarmak zordur.

❌ Bir dekoratörü, davranışı dekoratörler yığınındaki sıraya bağlı olmayacak şekilde uygulamak zordur.

❌ Katmanların ilk yapılandırma kodu oldukça çirkin görünebilir.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Adapter deseni, mevcut bir nesnenin arayüzünü değiştirirken, Decorator deseni nesneyi arayüzünü değiştirmeden geliştirir. Ayrıca, Decorator deseni, Adapter deseni kullanıldığında mümkün olmayan özyinelemeli bir bileşimi destekler.

- Adapter deseni, sarmalanan nesneye farklı bir arayüz sağlar, Proxy deseni ona aynı arayüzü sağlar ve Decorator deseni ona geliştirilmiş bir arayüz sağlar.

- Sorumluluk Zinciri deseni ve Decorator deseni çok benzer sınıf yapılarına sahiptir. Her iki desen de işlemi bir dizi nesne üzerinden geçirmek için özyinelemeli kompozisyona dayanır. Ancak, birkaç önemli fark vardır.

- Sorumluluk Zinciri deseni elemanları, bağımsız olarak herhangi bir işlemi gerçekleştirebilirler. Ayrıca, isteği herhangi bir noktada iletmeyi de durdurabilirler. Öte yandan, çeşitli Decorator'lar nesnenin davranışını temel arayüzle uyumlu tutarken genişletebilirler. Ayrıca, dekoratörlerin isteği kesintiye uğratma yetkisi yoktur.

- Kompozit deseni ve Decorator deseni benzer yapı diyagramlarına sahiptir, çünkü her ikisi de açık uçlu bir nesne sayısını düzenlemek için özyinelemeli bir kompozisyona dayanır.

- Bir Decorator, bir Kompozit'e benzer, ancak yalnızca bir çocuk bileşeni vardır. Başka önemli bir fark daha vardır: Decorator deseni, sarmalanan nesneye ek sorumluluklar eklerken, Kompozit deseni yalnızca çocuklarının sonuçlarını "toplar".

- Ancak, desenler aynı zamanda işbirliği yapabilir: Bir Kompozit ağacındaki belirli bir nesnenin davranışını genişletmek için Decorator kullanabilirsiniz.

- Kompozit deseni ve Decorator desenini yoğun bir şekilde kullanan tasarımlar, genellikle Prototip deseni kullanmanın faydalarını görebilir. Desenin uygulanması, karmaşık yapıları sıfırdan yeniden oluşturmak yerine kopyalamanıza olanak tanır.

- Decorator deseni, bir nesnenin kabuğunu değiştirmenize olanak tanırken, Strateji deseni size içini değiştirme olanağı sunar.

- Decorator deseni ve Proxy deseni benzer yapıları paylaşırlar, ancak çok farklı amaçlara sahiptirler. Her iki desen de bir nesnenin bazı işleri başka bir nesneye devretmesi gerektiği kompozisyon prensibine dayanmaktadır. Fark, bir Proxy'nin genellikle kendi hizmet nesnesinin yaşam döngüsünü kendi başına yönetmesidir, oysa Decorator'ların kompozisyonu her zaman istemci tarafından kontrol edilir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Dekoratör, Java kodunda, özellikle akışlarla (streams)  ilgili kodda oldukça standarttır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   All subclasses of  [`java.io.InputStream`](http://docs.oracle.com/javase/8/docs/api/java/io/InputStream.html),  [`OutputStream`](http://docs.oracle.com/javase/8/docs/api/java/io/OutputStream.html),  [`Reader`](http://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)  and  [`Writer`](http://docs.oracle.com/javase/8/docs/api/java/io/Writer.html)  have constructors that accept objects of their own type.
    
-   [`java.util.Collections`](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html), methods  [`checkedXXX()`](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedCollection-java.util.Collection-java.lang.Class-),  [`synchronizedXXX()`](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedCollection-java.util.Collection-)  and  [`unmodifiableXXX()`](http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableCollection-java.util.Collection-).
    
-   [`javax.servlet.http.HttpServletRequestWrapper`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequestWrapper.html)  and  [`HttpServletResponseWrapper`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletResponseWrapper.html)


**Tanımlama:** Dekoratör, geçerli bir sınıfla aynı sınıfa veya arayüze ait nesneleri kabul eden oluşturma yöntemleri veya yapıcılar tarafından tanınabilir.

 
####  Kodlama ve sıkıştırma dekoratörleri

Bu örnek, bir nesnenin kodunu değiştirmeden davranışını nasıl ayarlayabileceğinizi gösterir.

Başlangıçta, iş mantığı sınıfı verileri yalnızca düz metin olarak okuyabilir ve yazabilirdi. Ardından, sarılmış bir nesnede standart işlemleri yürüttükten sonra yeni davranış ekleyen birkaç küçük sarmalayıcı sınıfı oluşturduk.

İlk sarmalayıcı verileri şifreler ve şifresini çözer, ikincisi ise verileri sıkıştırır ve çıkarır.

Bu sarmalayıcıları bir dekoratörü diğeriyle sararak bile birleştirebilirsiniz.

**📁 decorators**

⤵️ 📄 `decorators/DataSource.java`: Okuma ve yazma işlemlerini tanımlayan ortak bir veri arayüzü

```java
package refactoring_guru.decorator.example.decorators;

public interface DataSource {
    void writeData(String data);

    String readData();
}
```

⤵️ 📄 `decorators/FileDataSource.java`: Basit veri okuyucu-yazarı

```java
package refactoring_guru.decorator.example.decorators;

import java.io.*;

public class FileDataSource implements DataSource {
    private String name;

    public FileDataSource(String name) {
        this.name = name;
    }

    @Override
    public void writeData(String data) {
        File file = new File(name);
        try (OutputStream fos = new FileOutputStream(file)) {
            fos.write(data.getBytes(), 0, data.length());
        } catch (IOException ex) {
            System.out.println(ex.getMessage());
        }
    }

    @Override
    public String readData() {
        char[] buffer = null;
        File file = new File(name);
        try (FileReader reader = new FileReader(file)) {
            buffer = new char[(int) file.length()];
            reader.read(buffer);
        } catch (IOException ex) {
            System.out.println(ex.getMessage());
        }
        return new String(buffer);
    }
}
```


⤵️ 📄 `decorators/DataSourceDecorator.java`: Soyut temel dekoratör

```java
package refactoring_guru.decorator.example.decorators;

public class DataSourceDecorator implements DataSource {
    private DataSource wrappee;

    DataSourceDecorator(DataSource source) {
        this.wrappee = source;
    }

    @Override
    public void writeData(String data) {
        wrappee.writeData(data);
    }

    @Override
    public String readData() {
        return wrappee.readData();
    }
}
```

⤵️ 📄 `decorators/EncryptionDecorator.java`: Şifreleme dekoratörü

```java
package refactoring_guru.decorator.example.decorators;

import java.util.Base64;

public class EncryptionDecorator extends DataSourceDecorator {

    public EncryptionDecorator(DataSource source) {
        super(source);
    }

    @Override
    public void writeData(String data) {
        super.writeData(encode(data));
    }

    @Override
    public String readData() {
        return decode(super.readData());
    }

    private String encode(String data) {
        byte[] result = data.getBytes();
        for (int i = 0; i < result.length; i++) {
            result[i] += (byte) 1;
        }
        return Base64.getEncoder().encodeToString(result);
    }

    private String decode(String data) {
        byte[] result = Base64.getDecoder().decode(data);
        for (int i = 0; i < result.length; i++) {
            result[i] -= (byte) 1;
        }
        return new String(result);
    }
}
```

⤵️ 📄 `decorators/CompressionDecorator.java`: Sıkıştırma dekoratörü

```java
package refactoring_guru.decorator.example.decorators;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Base64;
import java.util.zip.Deflater;
import java.util.zip.DeflaterOutputStream;
import java.util.zip.InflaterInputStream;

public class CompressionDecorator extends DataSourceDecorator {
    private int compLevel = 6;

    public CompressionDecorator(DataSource source) {
        super(source);
    }

    public int getCompressionLevel() {
        return compLevel;
    }

    public void setCompressionLevel(int value) {
        compLevel = value;
    }

    @Override
    public void writeData(String data) {
        super.writeData(compress(data));
    }

    @Override
    public String readData() {
        return decompress(super.readData());
    }

    private String compress(String stringData) {
        byte[] data = stringData.getBytes();
        try {
            ByteArrayOutputStream bout = new ByteArrayOutputStream(512);
            DeflaterOutputStream dos = new DeflaterOutputStream(bout, new Deflater(compLevel));
            dos.write(data);
            dos.close();
            bout.close();
            return Base64.getEncoder().encodeToString(bout.toByteArray());
        } catch (IOException ex) {
            return null;
        }
    }

    private String decompress(String stringData) {
        byte[] data = Base64.getDecoder().decode(stringData);
        try {
            InputStream in = new ByteArrayInputStream(data);
            InflaterInputStream iin = new InflaterInputStream(in);
            ByteArrayOutputStream bout = new ByteArrayOutputStream(512);
            int b;
            while ((b = iin.read()) != -1) {
                bout.write(b);
            }
            in.close();
            iin.close();
            bout.close();
            return new String(bout.toByteArray());
        } catch (IOException ex) {
            return null;
        }
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package refactoring_guru.decorator.example;

import refactoring_guru.decorator.example.decorators.*;

public class Demo {
    public static void main(String[] args) {
        String salaryRecords = "Name,Salary\nJohn Smith,100000\nSteven Jobs,912000";
        DataSourceDecorator encoded = new CompressionDecorator(
                                         new EncryptionDecorator(
                                             new FileDataSource("out/OutputDemo.txt")));
        encoded.writeData(salaryRecords);
        DataSource plain = new FileDataSource("out/OutputDemo.txt");

        System.out.println("- Input ----------------");
        System.out.println(salaryRecords);
        System.out.println("- Encoded --------------");
        System.out.println(plain.readData());
        System.out.println("- Decoded --------------");
        System.out.println(encoded.readData());
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu

```
- Input ----------------
Name,Salary
John Smith,100000
Steven Jobs,912000
- Encoded --------------
Zkt7e1Q5eU8yUm1Qe0ZsdHJ2VXp6dDBKVnhrUHtUe0sxRUYxQkJIdjVLTVZ0dVI5Q2IwOXFISmVUMU5rcENCQmdxRlByaD4+
- Decoded --------------
Name,Salary
John Smith,100000
Steven Jobs,912000
```



