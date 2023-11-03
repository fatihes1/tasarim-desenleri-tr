#  Vekil (Proxy) Tasarım Deseni


##  💬 Amaç

Proxy, başka bir nesne için bir yedek veya yer tutucu sağlamanıza olanak tanıyan yapısal (structural) bir tasarım desenidir. Proxy, orijinal nesneye erişimi kontrol ederek, isteğin orijinal nesneye ulaşmasından önce veya sonra bir şeyler yapmanıza olanak tanır.

![](https://refactoring.guru/images/patterns/content/proxy/proxy-2x.png)


##  🙁 Problem

Bir nesneye erişimi neden kontrol etmek isteriz ki? İşte bir örnek: Çok büyük miktarda sistem kaynağı tüketen devasa bir nesneniz olduğunu düşünün. Zaman zaman ihtiyacınız olur ama her zaman ihtiyacınız olmayacaktır.

![](https://refactoring.guru/images/patterns/diagrams/proxy/problem-en-2x.png)

*Veritabanı sorguları gerçekten yavaş olabilir.*

Tembel başlatmayı (lazy initialization) deneyebilirsiniz: bu nesneyi yalnızca gerçekten ihtiyaç duyulduğunda oluşturur. Nesnenin tüm istemcilerinin bazı ertelenmiş başlatma kodlarını yürütmesi gerekir. Ne yazık ki, bu durum, muhtemelen çok fazla kod kopyasına neden olacaktır.

İdeal şartlarda bu kodu doğrudan nesnemizin sınıfına koymak isteriz, ancak bu her zaman mümkün değildir. Örneğin sınıf, kapalı bir 3. taraf kitaplığının parçası olabilir.

##  😊 Çözüm

Proxy modeli, orijinal hizmet nesnesiyle aynı arabirime (interface) sahip yeni bir proxy sınıfı oluşturmanızı önerir. Daha sonra uygulamanızı, proxy nesnesini orijinal nesnenin tüm istemcilerine aktaracak şekilde güncellersiniz. Bir istemciden bir istek aldıktan sonra proxy, gerçek bir hizmet nesnesi oluşturur ve tüm işi ona devreder.

![](https://refactoring.guru/images/patterns/diagrams/proxy/solution-en-2x.png)

*Proxy kendisini bir veritabanı nesnesi gibi gösterir. İstemcinin veya gerçek veritabanı nesnesinin haberi bile olmadan, yavaş başlatma (lazy initialization) ve sonuçları önbelleğe alma işlemlerinin üstesinden gelebilir.*

Ama bu kullanımın faydası nedir? Sınıfın birincil mantığından önce veya sonra bir şey yürütmeniz gerekiyorsa, proxy bunu o sınıfı değiştirmeden yapmanızı sağlar. Proxy orijinal sınıfla aynı arayüzü kullandığından, gerçek bir hizmet nesnesi bekleyen herhangi bir istemciye iletilebilir.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/diagrams/proxy/live-example-2x.png)

*Kredi kartları, tıpkı nakit para gibi ödemeler için kullanılabilir.*

Kredi kartı, bir para destesinin vekili (proxy) olan banka hesabının vekilidir. Her ikisi de aynı arayüzü uygular: ödeme yapmak için kullanılabilirler. Tüketici bu durumda çok mutludur çünkü etrafta bir sürü nakit taşımaya gerek kalmaz. Bir mağaza sahibi de, bir işlemden elde edilen gelirin, depozitoyu kaybetme veya bankaya giderken soyulma riski olmadan, mağazanın banka hesabına elektronik olarak eklenmesinden dolayı mutludur.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/proxy/structure-2x.png)

1. **Hizmet Arayüzü (Service Interface)**, Hizmetin arayüzünü belirtir. Proxy'nin kendisini bir hizmet nesnesi olarak gizleyebilmesi için bu arayüzü takip etmesi gerekir.
2. **Hizmet (Service)**, bazı yararlı iş mantığı sağlayan bir sınıftır.
3. **Proxy** sınıfı, bir hizmet nesnesine işaret eden bir referans alanına sahiptir. Proxy, işlemeyi tamamladıktan sonra (ör. yavaş başlatma, günlüğe kaydetme, erişim kontrolü, önbelleğe alma vb.), isteği hizmet nesnesine iletir.
Genellikle proxy'ler hizmet nesnelerinin tüm yaşam döngüsünü yönetir.
4. **İstemci (Client)** aynı arayüz üzerinden hem hizmetlerle hem de proxy'lerle çalışmalıdır. Bu şekilde, bir hizmet nesnesi bekleyen herhangi bir koda bir proxy aktarabilirsiniz.



##  💻 Sözde Kod (Pseudocode)

Bu örnek, Proxy modelinin, üçüncü taraf bir YouTube entegrasyon kitaplığında yavaş başlatma ve önbelleğe alma işlemlerine nasıl yardımcı olabileceğini göstermektedir.

![](https://refactoring.guru/images/patterns/diagrams/proxy/example-2x.png)

*Bir servisin sonuçlarının proxy ile önbelleğe alınması.*


Kütüphane bize video indirme sınıfını sağlar. Ancak şuanki hali ile çok verimsiz. İstemci uygulaması aynı videoyu birden çok kez isterse, kitaplık ilk indirilen dosyayı önbelleğe alıp yeniden kullanmak yerine videoyu tekrar tekrar indirir.

Proxy sınıfı, orijinal indiriciyle aynı arayüzü uygular ve tüm işi ona devreder. Ancak indirilen dosyaları takip eder ve uygulama aynı videoyu birden çok kez istediğinde önbelleğe alınan sonucu döndürür.

```java
// Uzak bir servisin arayüzü.
interface ThirdPartyYouTubeLib is
    method listVideos()
    method getVideoInfo(id)
    method downloadVideo(id)

// Bir servis bağlantısının somut uygulaması. Bu sınıfın yöntemleri
// YouTube'dan bilgi isteyebilir. İstek hızı, kullanıcının internet bağlantısına ve
// YouTube'un hızına bağlıdır. Uygulama, aynı bilgiyi isteyen çok sayıda
// istek aynı anda gönderildiğinde yavaşlar.
class ThirdPartyYouTubeClass implements ThirdPartyYouTubeLib is
    method listVideos() is
        // YouTube'a bir API isteği gönder.

    method getVideoInfo(id) is
        // Bir video hakkında meta veriler al.

    method downloadVideo(id) is
        // Bir video dosyasını YouTube'dan indir.

// Bant genişliği tasarrufu yapmak için istek sonuçlarını önbelleğe alabiliriz ve
// bir süre boyunca saklayabiliriz. Ancak bu tür kodu doğrudan servis sınıfına
// yerleştirmek mümkün olmayabilir. Örneğin, bu üçüncü taraf bir kitaplığın parçası olarak
// sağlanmış veya 'final' olarak tanımlanmış olabilir. Bu nedenle, önbellekleme kodunu
// aynı arayüzü uygulayan yeni bir proxy sınıfına yerleştiriyoruz. Bu proxy sınıfı,
// gerçek isteklerin gönderilmesi gerektiğinde sadece servis nesnesine yönlendirir.
class CachedYouTubeClass implements ThirdPartyYouTubeLib is
    private field service: ThirdPartyYouTubeLib
    private field listCache, videoCache
    field needReset

    constructor CachedYouTubeClass(service: ThirdPartyYouTubeLib) is
        this.service = service

    method listVideos() is
        if (listCache == null || needReset)
            listCache = service.listVideos()
        return listCache

    method getVideoInfo(id) is
        if (videoCache == null || needReset)
            videoCache = service.getVideoInfo(id)
        return videoCache

    method downloadVideo(id) is
        if (!downloadExists(id) || needReset)
            service.downloadVideo(id)

// GUI sınıfı, bir servis nesnesi üzerinden doğrudan çalışırken aynı kalır.
// Bu, bir arabirim aracılığıyla hizmet nesnesiyle çalıştığı sürece geçerlidir.
// Gerçek bir servis nesnesi yerine bir proxy nesnesi geçirebiliriz çünkü her ikisi
// de aynı arabirimi uygular.
class YouTubeManager is
    protected field service: ThirdPartyYouTubeLib

    constructor YouTubeManager(service: ThirdPartyYouTubeLib) is
        this.service = service

    method renderVideoPage(id) is
        info = service.getVideoInfo(id)
        // Video sayfasını oluştur.

    method renderListPanel() is
        list = service.listVideos()
        // Video küçük resimlerinin listesini oluştur.

    method reactOnUserInput() is
        renderVideoPage()
        renderListPanel()

// Uygulama, proxy'leri uçurumda yapılandırabilir.
class Application is
    method init() is
        aYouTubeService = new ThirdPartyYouTubeClass()
        aYouTubeProxy = new CachedYouTubeClass(aYouTubeService)
        manager = new YouTubeManager(aYouTubeProxy)
        manager.reactOnUserInput()

```


##  💡Uygulanabilirlik

Proxy modelini kullanmanın düzinelerce yolu vardır. En popüler kullanımlara bakalım.

**🐞 Tembel başlatma (sanal proxy): Yani, yalnızca zaman zaman ihtiyacınız olmasına rağmen her zaman çalışır durumda kalarak sistem kaynaklarını boşa harcayan ağır bir hizmet nesnesine sahip olduğunuz zamandır.**

⚡️ Uygulama başlatıldığında nesneyi oluşturmak yerine, nesnenin başlatılmasını gerçekten ihtiyaç duyulan bir zamana kadar erteleyebilirsiniz.

----------------

**🐞 Erişim kontrolü (koruma proxy'si): Bu, yalnızca belirli istemcilerin hizmet nesnesini kullanabilmesini istediğiniz zamandır; örneğin, nesneleriniz bir işletim sisteminin önemli parçaları olduğunda ve istemciler başlatılan çeşitli uygulamalar olduğunda (kötü niyetli olanlar dahil) kullanabilirsiniz.**

⚡️ Proxy, yalnızca istemcinin kimlik bilgilerinin bazı ölçütlerle eşleşmesi durumunda isteği hizmet nesnesine iletebilir.

----------------

**🐞 Uzak bir hizmetin yerel olarak yürütülmesi (uzak proxy): Bu, hizmet nesnesinin uzak bir sunucuda bulunduğu zamandır.**

⚡️ Bu durumda proxy, istemcinin isteğini ağ üzerinden ileterek ağla çalışmanın tüm kötü ayrıntılarını ele alır.

----------------

**🐞 İsteklerin log olarak kaydedilmesi (logging proxy): Bu, hizmet nesnesine yapılan isteklerin geçmişini tutmak istediğiniz zamandır.**

⚡️ Proxy her isteği hizmete iletmeden önce günlüğe kaydedebilir.

----------------

**🐞 İstek sonuçlarının önbelleğe alınması (proxy'nin önbelleğe alınması): Bu, özellikle sonuçların oldukça büyük olması durumunda, istemci isteklerinin sonuçlarını önbelleğe almanız ve bu önbelleğin yaşam döngüsünü yönetmeniz gerektiği zamandır.**

⚡️ Proxy, her zaman aynı sonuçları veren yinelenen istekler için önbelleğe alma uygulayabilir. Proxy, isteklerin parametrelerini önbellek anahtarları olarak kullanabilir.

----------------

**🐞 Akıllı referans. Bu, onu kullanan hiçbir müşteri kalmadığında ağır bir nesneyi göz ardı edebilmeniz gerektiği zamandır.**

⚡️ Proxy, hizmet nesnesine veya sonuçlarına referans alan istemcileri takip edebilir. Proxy zaman zaman istemcilerin üzerinden geçerek hâlâ etkin olup olmadıklarını kontrol edebilir. İstemci listesi boşalırsa proxy, hizmet nesnesini kapatabilir ve temeldeki sistem kaynaklarını serbest bırakabilir.
Proxy ayrıca istemcinin hizmet nesnesini değiştirip değiştirmediğini de izleyebilir. Daha sonra değiştirilmeyen nesneler diğer istemciler tarafından yeniden kullanılabilir.
  

##  📝 Nasıl Uygulanır?

1. Eğer önceden var olan bir hizmet arabirimi (interface) yoksa, bir proxy ve hizmet nesnelerini birbirinin yerine kullanılabilir hale getirmek için bir tane oluşturun. Hizmet sınıfından arabirimi ayıklamak her zaman mümkün olmayabilir, çünkü bu durumda tüm hizmetin istemcilerini bu arabirimi kullanmaya zorlamanız gerekebilir. İkinci seçenek, proxy'i hizmet sınıfının bir alt sınıfı yapmak ve bu şekilde hizmetin arabirimini miras (inherit) almasıdır.

2. Proxy sınıfını oluşturun. Bu sınıf, hizmete referans saklamak için bir alan içermelidir. Genellikle proxy'ler, hizmetlerinin tüm yaşam döngüsünü oluşturur ve yönetirler. Nadiren, bir servis, istemci tarafından proxy'e bir kurucuyla iletilir.

3. Proxy yöntemlerini amaçlarına göre uygulamayı düşünün. Çoğu durumda, bazı işlerin ardından proxy, işi hizmet nesnesine devretmelidir.

4. İstemcinin bir proxy veya gerçek bir hizmet alıp almayacağını belirleyen bir oluşturma yöntemi eklemeyi unutmayın. Bu, proxy sınıfında basit bir statik yöntem veya tam teşekküllü bir fabrika (factory) yöntemi olabilir.

5. Hizmet nesnesi için tembel başlatma (lazy initialization) uygulamayı düşünün.


##  ⚖️ Artıları ve Eksileri

✅ Servis nesnesini, müşterilerin haberi olmadan kontrol edebilirsiniz.

✅ İstemcinin umursamadığı durumlarda hizmet nesnesinin yaşam döngüsünü yönetebilirsiniz.

✅ Proxy, servis nesnesi hazır olmasa veya mevcut olmasa bile çalışır.

✅ Açık/Kapalı Prensibi (Open/Closed Principle). Hizmeti veya istemcileri değiştirmeden yeni proxy'ler tanıtabilirsiniz.

❌ Çok sayıda yeni sınıf tanımlamanız gerektiğinden kod daha karmaşık hale gelebilir.

❌ Servisten gelecek yanıt gecikebilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Adapter** deseni ile mevcut bir nesneye farklı bir arayüz üzerinden erişirsiniz. **Proxy** deseni ile arayüz aynı kalır. **Decorator** deseni ile nesneye gelişmiş bir arayüz aracılığıyla erişirsiniz.

- **Facade** deseni, hem karmaşık bir varlığı ara belleğe alması hem de onu kendi başına başlatması açısından **Proxy** desenine benzer. **Facade**'den farklı olarak **Proxy**, servis nesnesiyle aynı arayüze sahiptir ve bu da onları birbirinin yerine kullanılabilir hale getirir.

- **Decorator** ve **Proxy** benzer yapılara sahiptir ancak amaçları çok farklıdır. Her iki model de, bir nesnenin işin bir kısmını diğerine devretmesi gereken kompozisyon ilkesi üzerine inşa edilmiştir. Aradaki fark, bir **Proxy**'nin genellikle hizmet nesnesinin yaşam döngüsünü kendi başına yönetmesi, Dekoratörlerin bileşiminin ise her zaman istemci tarafından kontrol edilmesidir.


  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Proxy modeli çoğu Java uygulamasında sık sık kullanılmasa da bazı özel durumlarda hala çok kullanışlıdır. İstemci kodunu değiştirmeden mevcut bir sınıfın bir nesnesine bazı ek davranışlar eklemek istediğinizde yeri doldurulamaz.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.lang.reflect.Proxy`](http://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html)
-   [`java.rmi.*`](http://docs.oracle.com/javase/8/docs/api/java/rmi/package-summary.html)
-   [`javax.ejb.EJB`](http://docs.oracle.com/javaee/7/api/javax/ejb/EJB.html)
-   [`javax.inject.Inject`](http://docs.oracle.com/javaee/7/api/javax/inject/Inject.html)  
-   [`javax.persistence.PersistenceContext`](http://docs.oracle.com/javaee/7/api/javax/persistence/PersistenceContext.html)


**Tanımlama:** Proxy'ler gerçek işin tamamını başka bir nesneye devreder. Her proxy yöntemi, proxy bir hizmetin alt sınıfı olmadığı sürece, sonuçta bir hizmet nesnesine başvurmalıdır.

 
####  Proxy'yi önbelleğe alma

Bu örnekte Proxy modeli, yavaş başlatma ve verimsiz bir 3. taraf YouTube entegrasyon kitaplığında önbelleğe alma işleminin uygulanmasına yardımcı olur.

Kodu değiştiremeyeceğiniz bir sınıfa bazı ek davranışlar eklemeniz gerektiğinde proxy çok değerlidir.

**📁 some_cool_media_library**

⤵️ 📄 `some_cool_media_library/ThirdPartyYouTubeLib.java` : Remote servis arayüzü

```java
package fatihes1.proxy.example.some_cool_media_library;

import java.util.HashMap;

public interface ThirdPartyYouTubeLib {
    HashMap<String, Video> popularVideos();

    Video getVideo(String videoId);
}
```

⤵️ 📄 `some_cool_media_library/ThirdPartyYouTubeClass.java`: Remote servis uygulaması 

```java
package fatihes1.proxy.example.some_cool_media_library;

import java.util.HashMap;

public class ThirdPartyYouTubeClass implements ThirdPartyYouTubeLib {

    @Override
    public HashMap<String, Video> popularVideos() {
        connectToServer("http://www.youtube.com");
        return getRandomVideos();
    }

    @Override
    public Video getVideo(String videoId) {
        connectToServer("http://www.youtube.com/" + videoId);
        return getSomeVideo(videoId);
    }

    // -----------------------------------------------------------------------
    // Fake methods to simulate network activity. They as slow as a real life.

    private int random(int min, int max) {
        return min + (int) (Math.random() * ((max - min) + 1));
    }

    private void experienceNetworkLatency() {
        int randomLatency = random(5, 10);
        for (int i = 0; i < randomLatency; i++) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException ex) {
                ex.printStackTrace();
            }
        }
    }

    private void connectToServer(String server) {
        System.out.print("Connecting to " + server + "... ");
        experienceNetworkLatency();
        System.out.print("Connected!" + "\n");
    }

    private HashMap<String, Video> getRandomVideos() {
        System.out.print("Downloading populars... ");

        experienceNetworkLatency();
        HashMap<String, Video> hmap = new HashMap<String, Video>();
        hmap.put("catzzzzzzzzz", new Video("sadgahasgdas", "Catzzzz.avi"));
        hmap.put("mkafksangasj", new Video("mkafksangasj", "Dog play with ball.mp4"));
        hmap.put("dancesvideoo", new Video("asdfas3ffasd", "Dancing video.mpq"));
        hmap.put("dlsdk5jfslaf", new Video("dlsdk5jfslaf", "Barcelona vs RealM.mov"));
        hmap.put("3sdfgsd1j333", new Video("3sdfgsd1j333", "Programing lesson#1.avi"));

        System.out.print("Done!" + "\n");
        return hmap;
    }

    private Video getSomeVideo(String videoId) {
        System.out.print("Downloading video... ");

        experienceNetworkLatency();
        Video video = new Video(videoId, "Some video title");

        System.out.print("Done!" + "\n");
        return video;
    }

}
```


⤵️ 📄 `some_cool_media_library/Video.java`: Video dosyası

```java
package fatihes1.proxy.example.some_cool_media_library;

public class Video {
    public String id;
    public String title;
    public String data;

    Video(String id, String title) {
        this.id = id;
        this.title = title;
        this.data = "Random video.";
    }
}
```

**📁 proxy**

⤵️ 📄 ` proxy/YouTubeCacheProxy.java`: Caching proxy

```java
package fatihes1.proxy.example.proxy;

import fatihes1.proxy.example.some_cool_media_library.ThirdPartyYouTubeClass;
import fatihes1.proxy.example.some_cool_media_library.ThirdPartyYouTubeLib;
import fatihes1.proxy.example.some_cool_media_library.Video;

import java.util.HashMap;

public class YouTubeCacheProxy implements ThirdPartyYouTubeLib {
    private ThirdPartyYouTubeLib youtubeService;
    private HashMap<String, Video> cachePopular = new HashMap<String, Video>();
    private HashMap<String, Video> cacheAll = new HashMap<String, Video>();

    public YouTubeCacheProxy() {
        this.youtubeService = new ThirdPartyYouTubeClass();
    }

    @Override
    public HashMap<String, Video> popularVideos() {
        if (cachePopular.isEmpty()) {
            cachePopular = youtubeService.popularVideos();
        } else {
            System.out.println("Retrieved list from cache.");
        }
        return cachePopular;
    }

    @Override
    public Video getVideo(String videoId) {
        Video video = cacheAll.get(videoId);
        if (video == null) {
            video = youtubeService.getVideo(videoId);
            cacheAll.put(videoId, video);
        } else {
            System.out.println("Retrieved video '" + videoId + "' from cache.");
        }
        return video;
    }

    public void reset() {
        cachePopular.clear();
        cacheAll.clear();
    }
}
```

**📁 downloader**

⤵️ 📄 `downloader/YouTubeDownloader.java`: Medya indirme uygulaması

```java
package fatihes1.proxy.example.downloader;

import fatihes1.proxy.example.some_cool_media_library.ThirdPartyYouTubeLib;
import fatihes1.proxy.example.some_cool_media_library.Video;

import java.util.HashMap;

public class YouTubeDownloader {
    private ThirdPartyYouTubeLib api;

    public YouTubeDownloader(ThirdPartyYouTubeLib api) {
        this.api = api;
    }

    public void renderVideoPage(String videoId) {
        Video video = api.getVideo(videoId);
        System.out.println("\n-------------------------------");
        System.out.println("Video page (imagine fancy HTML)");
        System.out.println("ID: " + video.id);
        System.out.println("Title: " + video.title);
        System.out.println("Video: " + video.data);
        System.out.println("-------------------------------\n");
    }

    public void renderPopularVideos() {
        HashMap<String, Video> list = api.popularVideos();
        System.out.println("\n-------------------------------");
        System.out.println("Most popular videos on YouTube (imagine fancy HTML)");
        for (Video video : list.values()) {
            System.out.println("ID: " + video.id + " / Title: " + video.title);
        }
        System.out.println("-------------------------------\n");
    }
}

```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.proxy.example;

import fatihes1.proxy.example.downloader.YouTubeDownloader;
import fatihes1.proxy.example.proxy.YouTubeCacheProxy;
import fatihes1.proxy.example.some_cool_media_library.ThirdPartyYouTubeClass;

public class Demo {

    public static void main(String[] args) {
        YouTubeDownloader naiveDownloader = new YouTubeDownloader(new ThirdPartyYouTubeClass());
        YouTubeDownloader smartDownloader = new YouTubeDownloader(new YouTubeCacheProxy());

        long naive = test(naiveDownloader);
        long smart = test(smartDownloader);
        System.out.print("Time saved by caching proxy: " + (naive - smart) + "ms");

    }

    private static long test(YouTubeDownloader downloader) {
        long startTime = System.currentTimeMillis();

        // User behavior in our app:
        downloader.renderPopularVideos();
        downloader.renderVideoPage("catzzzzzzzzz");
        downloader.renderPopularVideos();
        downloader.renderVideoPage("dancesvideoo");
        // Users might visit the same page quite often.
        downloader.renderVideoPage("catzzzzzzzzz");
        downloader.renderVideoPage("someothervid");

        long estimatedTime = System.currentTimeMillis() - startTime;
        System.out.print("Time elapsed: " + estimatedTime + "ms\n");
        return estimatedTime;
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: RAM kullanım istatistikleri

```
Connecting to http://www.youtube.com... Connected!
Downloading populars... Done!

-------------------------------
Most popular videos on YouTube (imagine fancy HTML)
ID: sadgahasgdas / Title: Catzzzz.avi
ID: asdfas3ffasd / Title: Dancing video.mpq
ID: 3sdfgsd1j333 / Title: Programing lesson#1.avi
ID: mkafksangasj / Title: Dog play with ball.mp4
ID: dlsdk5jfslaf / Title: Barcelona vs RealM.mov
-------------------------------

Connecting to http://www.youtube.com/catzzzzzzzzz... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: catzzzzzzzzz
Title: Some video title
Video: Random video.
-------------------------------

Connecting to http://www.youtube.com... Connected!
Downloading populars... Done!

-------------------------------
Most popular videos on YouTube (imagine fancy HTML)
ID: sadgahasgdas / Title: Catzzzz.avi
ID: asdfas3ffasd / Title: Dancing video.mpq
ID: 3sdfgsd1j333 / Title: Programing lesson#1.avi
ID: mkafksangasj / Title: Dog play with ball.mp4
ID: dlsdk5jfslaf / Title: Barcelona vs RealM.mov
-------------------------------

Connecting to http://www.youtube.com/dancesvideoo... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: dancesvideoo
Title: Some video title
Video: Random video.
-------------------------------

Connecting to http://www.youtube.com/catzzzzzzzzz... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: catzzzzzzzzz
Title: Some video title
Video: Random video.
-------------------------------

Connecting to http://www.youtube.com/someothervid... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: someothervid
Title: Some video title
Video: Random video.
-------------------------------

Time elapsed: 9354ms
Connecting to http://www.youtube.com... Connected!
Downloading populars... Done!

-------------------------------
Most popular videos on YouTube (imagine fancy HTML)
ID: sadgahasgdas / Title: Catzzzz.avi
ID: asdfas3ffasd / Title: Dancing video.mpq
ID: 3sdfgsd1j333 / Title: Programing lesson#1.avi
ID: mkafksangasj / Title: Dog play with ball.mp4
ID: dlsdk5jfslaf / Title: Barcelona vs RealM.mov
-------------------------------

Connecting to http://www.youtube.com/catzzzzzzzzz... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: catzzzzzzzzz
Title: Some video title
Video: Random video.
-------------------------------

Retrieved list from cache.

-------------------------------
Most popular videos on YouTube (imagine fancy HTML)
ID: sadgahasgdas / Title: Catzzzz.avi
ID: asdfas3ffasd / Title: Dancing video.mpq
ID: 3sdfgsd1j333 / Title: Programing lesson#1.avi
ID: mkafksangasj / Title: Dog play with ball.mp4
ID: dlsdk5jfslaf / Title: Barcelona vs RealM.mov
-------------------------------

Connecting to http://www.youtube.com/dancesvideoo... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: dancesvideoo
Title: Some video title
Video: Random video.
-------------------------------

Retrieved video 'catzzzzzzzzz' from cache.

-------------------------------
Video page (imagine fancy HTML)
ID: catzzzzzzzzz
Title: Some video title
Video: Random video.
-------------------------------

Connecting to http://www.youtube.com/someothervid... Connected!
Downloading video... Done!

-------------------------------
Video page (imagine fancy HTML)
ID: someothervid
Title: Some video title
Video: Random video.
-------------------------------

Time elapsed: 5875ms
Time saved by caching proxy: 3479ms
```
