#  Yineleyici (Iterator) Tasarım Deseni


##  💬 Amaç

Yineleyici, bir koleksiyonun öğeleri arasında, onun temel temsilini (liste, yığın, ağaç vb.) açığa çıkarmadan geçiş yapmanızı sağlayan davranışsal bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/iterator/iterator-en-2x.png)


##  🙁 Problem

Koleksiyonlar (Collections) programlamada en çok kullanılan veri türlerinden biridir. Bununla birlikte, bir koleksiyon yalnızca bir grup nesne için bir konteynerdir.

![](https://refactoring.guru/images/patterns/diagrams/iterator/problem1-2x.png)

*Çeşitli koleksiyon türleri.*

Çoğu koleksiyon, öğelerini basit listelerde saklar. Ancak bunlardan bazıları yığınlara, ağaçlara, grafiklere ve diğer karmaşık veri yapılarına dayanmaktadır.

Ancak bir koleksiyon nasıl yapılandırılmış olursa olsun, diğer kodların bu öğeleri kullanabilmesi için kendi öğelerine erişmenin bir yolunu sağlaması gerekir. Koleksiyonun her bir öğesinin üzerinden, aynı öğelere tekrar tekrar erişmeden geçmenin bir yolu olmalıdır.

Listeye dayalı bir koleksiyonunuz varsa bu kolay bir iş gibi görünebilir. Tüm öğelerin üzerinden geçmeniz yeterli. Peki ağaç gibi karmaşık bir veri yapısının öğelerini sırayla nasıl geçersiniz? Örneğin, bir gün bir ağacın derinliğini ön planda tutarak geçiş yapmakta sorun yaşamazsınız. Ancak ertesi gün genişlik öncelikli geçişe ihtiyaç duyabilirsiniz. Gelecek hafta ağaç öğelerine rastgele erişim gibi başka bir şeye ihtiyacınız olabilir.


![](https://refactoring.guru/images/patterns/diagrams/iterator/problem2-2x.png)

*Aynı koleksiyonda birkaç farklı şekilde geçiş yapılabilir.*

Çok geçmeden bu yaklaşımın son derece kusurlu olduğunu fark edeceksiniz. İlk olarak, çok sayıda alt sınıfınız oluşacaktır. Eğer temel `Button` sınıfını her değiştirdiğinizde bu alt sınıflardaki kodu kırma riskini almıyorsanız bu sorun değil. Basitçe söylemek gerekirse, GUI kodunuz iş mantığının geçici koduna garip bir şekilde bağımlı hale geldi.

![](https://refactoring.guru/images/patterns/diagrams/command/problem3-en-2x.png)

*Birkaç sınıf temelde aynı işlevi uygular.*

Koleksiyona giderek daha fazla geçiş algoritması eklemek, birincil sorumluluğu olan verimli veri depolamayı giderek zorlaştırır. Ek olarak, bazı algoritmalar belirli bir uygulamaya göre uyarlanabilir, dolayısıyla bunları genel bir koleksiyon sınıfına dahil etmek yanlış bir seçim olabilir.

Öte yandan, çeşitli koleksiyonlarla çalışması gereken istemci yani client kodu, öğelerini nasıl sakladığını bile umursamayabilir. Ancak koleksiyonların tümü, öğelerine erişmenin farklı yollarını sunduğundan, kodunuzu belirli koleksiyon sınıflarıyla eşleştirmekten başka seçeneğiniz yoktur.

##  😊 Çözüm

Yineleyici (iterator) modelinin ana fikri, bir koleksiyonun geçiş davranışını yineleyici adı verilen ayrı bir nesneye çıkarmaktır.

![](https://refactoring.guru/images/patterns/diagrams/iterator/solution1-2x.png)

*Yineleyiciler çeşitli geçiş algoritmaları uygular. Birkaç yineleyici nesne aynı anda aynı koleksiyonda dolaşabilir.*

Algoritmanın kendisinin uygulanmasına ek olarak, bir yineleyici nesne, geçerli konum ve sonuna kadar kaç öğenin kaldığı gibi tüm geçiş ayrıntılarını kapsar. Bu nedenle, birkaç yineleyici aynı koleksiyonda birbirinden bağımsız olarak aynı anda geçebilir.

Genellikle yineleyiciler, koleksiyonun öğelerini getirmek için bir birincil yöntem sağlar. İstemci hiçbir şey döndürmeyene kadar bu yöntemi çalıştırmaya devam edebilir; bu, yineleyicinin tüm öğeleri geçtiği anlamına gelir.

Tüm yineleyiciler aynı arayüzü uygulamalıdır yani implement etmelidir. Bu, uygun bir yineleyici olduğu sürece istemci kodunu herhangi bir koleksiyon türüyle veya herhangi bir geçiş algoritmasıyla uyumlu hale getirir. Bir koleksiyonda geçiş yapmak için özel bir yola ihtiyacınız varsa, koleksiyonu veya istemciyi değiştirmek zorunda kalmadan yeni bir yineleyici sınıf oluşturmanız yeterlidir.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/content/iterator/iterator-comic-1-en-2x.png)

*Roma'da dolaşmanın çeşitli yolları vardır.*

Birkaç günlüğüne Roma'yı ziyaret etmeyi ve onun tüm önemli turistik yerlerini ve ilgi çekici yerlerini ziyaret etmeyi planlıyorsunuz. Ancak oraya vardığınızda, Colosseum'u bile bulamadan daireler çizerek çok fazla zaman harcayabilirsiniz. Bununla beraber kaybolmanız işten bile değildir.

Öte yandan, akıllı telefonunuz için sanal bir rehber uygulaması satın alabilir ve bunu navigasyon için kullanabilirsiniz. Akıllı ve ucuzdur. Bununla beraber ilginç yerlerde istediğiniz kadar kalabilirsiniz.

Üçüncü bir alternatif ise gezi bütçenizin bir kısmını harcayıp şehri avucunun içi gibi bilen yerel bir rehber kiralamanızdır. Rehber, turu beğenilerinize göre düzenleyebilir, size her atraksiyonu gösterebilir ve birçok heyecan verici hikaye anlatabilir. Bu daha da eğlenceli olacak; ama ne yazık ki aynı zamanda daha pahalı.

Tüm bu seçenekler (kafanızda doğan rastgele yönler, akıllı telefon gezgini veya insan rehberi), Roma'da bulunan geniş manzara ve turistik yerler koleksiyonu üzerinde yineleyici görevi görür.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/iterator/structure-2x.png)

1. **Yineleyici (Iterator)** arayüzü, bir koleksiyonda geçiş yapmak için gereken işlemleri tanımlar: sonraki öğeyi getirme, geçerli konumu alma, yinelemeyi yeniden başlatma gibi.

2. **Concrete Iterators**, bir koleksiyonda gezinmek için özel algoritmalar kullanır. Yineleyici nesnesi geçiş ilerlemesini kendi başına izlemelidir. Bu, birkaç yineleyicinin aynı koleksiyonda birbirinden bağımsız olarak geçmesine olanak tanır.

3. **Koleksiyon (Collection)** arayüzü, yineleyicilerin koleksiyonla uyumlu olmasını sağlamak için bir veya daha fazla yöntem bildirir. Somut koleksiyonların çeşitli türde yineleyicileri döndürebilmesi için yöntemlerin dönüş türünün yineleyici arayüzü olarak bildirilmesi gerektiğini unutmayın.

4. **Concrete Collection**, müşteri her talep ettiğinde belirli bir somut yineleyici sınıfının yeni örneklerini döndürür. Koleksiyonun kodunun geri kalanının nerede olduğunu merak ediyor olabilirsiniz. Endişelenmeyin, aynı sınıfta olmalıdır. Sadece bu ayrıntılar asıl model için çok önemli değil, bu yüzden onları göz ardı edebiliriz.

5. **İstemci (Client)**, arayüzleri aracılığıyla hem koleksiyonlarla hem de yineleyicilerle çalışır. Bu şekilde istemci somut sınıflara bağlanmaz ve aynı istemci koduyla çeşitli koleksiyonları ve yineleyicileri kullanmanıza olanak tanır.
Genellikle istemciler yineleyicileri kendi başlarına oluşturmazlar; bunun yerine bunları koleksiyonlardan alırlar. Ancak bazı durumlarda müşteri doğrudan bir tane oluşturabilir; örneğin istemci kendi özel yineleyicisini tanımladığında.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte **Iterator** modeli, Facebook'un sosyal grafiğine erişimi kapsayan özel bir koleksiyon türünde gezinmek için kullanılıyor. Koleksiyon, profilleri çeşitli şekillerde geçebilen çeşitli yineleyiciler sağlar.


![](https://refactoring.guru/images/patterns/diagrams/iterator/example-2x.png)

*Sosyal profiller üzerinde yineleme örneği.*


**Arkadaşlar (friends)** yineleyicisi, belirli bir profildeki arkadaşların üzerinden geçmek için kullanılabilir. **Meslektaşlar (colleagues)** yineleyicisi de aynısını yapar, ancak hedef kişiyle aynı şirkette çalışmayan arkadaşları hariç tutar. Her iki yineleyici de istemcilerin, kimlik doğrulama ve REST isteklerini gönderme gibi uygulama ayrıntılarına girmeden profilleri almasına olanak tanıyan ortak bir arayüz uygular.

İstemci kodu somut sınıflara bağlı değildir çünkü koleksiyonlarla ve yineleyicilerle yalnızca arayüzler aracılığıyla çalışır. Uygulamanızı yeni bir sosyal ağa bağlamaya karar verirseniz mevcut kodu değiştirmeden yeni toplama ve yineleme sınıfları sağlamanız yeterlidir.


```java
// Koleksiyon arabirimine, yinelemelerin oluşturulması için bir fabrika yöntemi
// bildirmelidir. Programınızda farklı türde yineleme bulunuyorsa, birkaç
// yöntem bildirebilirsiniz.
interface SocialNetwork is
    method createFriendsIterator(profileId):ProfileIterator
    method createCoworkersIterator(profileId):ProfileIterator

// Her somut koleksiyon, döndürdüğü somut yineleyici sınıflarına bağlıdır. Ancak
// istemci değil, çünkü bu yöntemlerin imzası yineleyici arabirimlerini döndürür.
class Facebook implements SocialNetwork is
    // ... Koleksiyonun çoğu kodu buraya gitmelidir...

    // Yineleyici oluşturma kodu.
    method createFriendsIterator(profileId) is
        return new FacebookIterator(this, profileId, "friends")
    method createCoworkersIterator(profileId) is
        return new FacebookIterator(this, profileId, "coworkers")

// Tüm yineleyiciler için ortak arabirim.
interface ProfileIterator is
    method getNext():Profile
    method hasMore():bool

// Somut yineleyici sınıfı.
class FacebookIterator implements ProfileIterator is
    // Yineleyici, üzerinden geçtiği koleksiyona referansa ihtiyaç duyar.
    private field facebook: Facebook
    private field profileId, type: string

    // Bir yineleyici nesnesi, diğer yineleyicilerden bağımsız olarak koleksiyonu
    // gezmelidir. Bu nedenle yineleme durumunu saklamalıdır.
    private field currentPosition
    private field cache: dizi Profile

    constructor FacebookIterator(facebook, profileId, type) is
        this.facebook = facebook
        this.profileId = profileId
        this.type = type

    private method lazyInit() is
        if (cache == null)
            cache = facebook.socialGraphRequest(profileId, type)

    // Her somut yineleyici sınıfı, ortak yineleme arabirimine kendi uygulamasını
    // sağlar.
    method getNext() is
        if (hasMore())
            result = cache[currentPosition]
            currentPosition++
            return result

    method hasMore() is
        lazyInit()
        return currentPosition < cache.length

// İşte başka bir kullanışlı hile: bir koleksiyona erişimini vermek yerine bir istemci
// sınıfına bir yineleyici geçirebilirsiniz. Bu şekilde koleksiyonu istemciye açık
// etmezsiniz.
//
// Ve başka bir fayda daha var: istemci kodu somut yineleyici sınıflarına bağlı olmadığından,
// koleksiyonla nasıl çalıştığınızı geçirilen bir yineleyiciyi değiştirerek
// çalışma zamanında değiştirebilirsiniz.
class SocialSpammer is
    method send(iterator: ProfileIterator, message: string) is
        while (iterator.hasMore())
            profile = iterator.getNext()
            System.sendEmail(profile.getEmail(), message)

// Uygulama sınıfı koleksiyonları ve yineleyicileri yapılandırır ve ardından
// bunları istemci koduna geçirir.
class Application is
    field network: SocialNetwork
    field spammer: SocialSpammer

    method config() is
        if working with Facebook
            this.network = new Facebook()
        if working with LinkedIn
            this.network = new LinkedIn()
        this.spammer = new SocialSpammer()

    method sendSpamToFriends(profile) is
        iterator = network.createFriendsIterator(profile.getId())
        spammer.send(iterator, "Çok önemli mesaj")

    method sendSpamToCoworkers(profile) is
        iterator = network.createCoworkersIterator(profile.getId())
        spammer.send(iterator, "Çok önemli mesaj")

```


##  💡Uygulanabilirlik


**🐞 Koleksiyonunuzda karmaşık bir veri yapısı varsa ancak karmaşıklığını istemcilerden gizlemek istiyorsanız (kolaylık veya güvenlik nedeniyle) Yineleyici modelini kullanın.**

⚡️ Yineleyici, karmaşık bir veri yapısıyla çalışmanın ayrıntılarını kapsayarak istemciye koleksiyon öğelerine erişmesi için birkaç basit yöntem sağlar. Bu yaklaşım müşteri için çok uygun olsa da, koleksiyonu doğrudan koleksiyonla çalışırken müşterinin gerçekleştirebileceği dikkatsiz veya kötü niyetli eylemlerden de korur.

----------------

**🐞 Uygulamanızda geçiş kodunun yinelenmesini azaltmak için modeli kullanın.**

⚡️ Önemsiz olmayan yineleme algoritmalarının kodu çok hantal olma eğilimindedir. Bir uygulamanın iş mantığına yerleştirildiğinde, orijinal kodun sorumluluğunu bulanıklaştırabilir ve onu daha az bakım yapılabilir hale getirebilir. Geçiş kodunu belirlenmiş yineleyicilere taşımak, uygulamanın kodunu daha yalın ve temiz hale getirmenize yardımcı olabilir.

----------------

**🐞 Kodunuzun farklı veri yapılarını geçebilmesini istediğinizde veya bu yapıların türleri önceden bilinmediğinde Yineleyiciyi modelini kullanın.**

⚡️ Desen, hem koleksiyonlar hem de yineleyiciler için birkaç genel arayüz sağlar. Kodunuzun artık bu arayüzleri kullandığı göz önüne alındığında, bu arayüzleri uygulayan çeşitli koleksiyon ve yineleyici türlerini ona iletirseniz yine de çalışacaktır.
  

##  📝 Nasıl Uygulanır?

1. İteratör arayüzünü tanımlayın. En azından bir koleksiyondan sonraki öğeyi getiren bir yönteme sahip olmalıdır. Ancak kolaylık sağlamak için sonraki öğeyi almanın yanı sıra önceki öğeyi almak, mevcut konumu takip etmek ve iterasyonun sonunu kontrol etmek gibi başka yöntemler ekleyebilirsiniz.

2. Koleksiyon arayüzünü tanımlayın ve bir iteratör almak için bir yöntemi tanımlayın. Dönüş türü iteratör arayüzü ile aynı olmalıdır. Birden fazla farklı iteratör grubunuz olacaksa benzer yöntemler açıklayabilirsiniz.

3. İteratör nesnelerini, iteratörlerle gezilebilir olmasını istediğiniz koleksiyonlar için uygulayın. Bir iteratör nesnesi genellikle yineleyicinin yapıcısı aracılığıyla bir koleksiyon örneği ile ilişkilendirilir. Bu bağlantı genellikle iteratörün yapıcısı aracılığıyla kurulur.

4. Koleksiyon sınıflarınızda koleksiyon arayüzünü uygulayın yani implement edin. Temel fikir, istemciye belirli bir koleksiyon sınıfı için özelleştirilmiş iteratörler oluşturma için bir kısayol sağlamaktır. Koleksiyon nesnesi, aralarındaki bağı oluşturmak için iteratörün yapıcısına kendisini iletmelidir.

5. İstemci kodunu gözden geçirin ve tüm koleksiyon gezinme kodlarını iteratör kullanımıyla değiştirin. İstemci, koleksiyon öğeleri üzerinde yinelemek için her seferinde yeni bir iteratör nesnesi alır.

##  ⚖️ Artıları ve Eksileri

✅ Her yineleyici nesnenin kendi yineleme durumunu içermesi nedeniyle aynı koleksiyon üzerinde paralel olarak yineleme yapabilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): Hacimli geçiş algoritmalarını ayrı sınıflara ayırarak istemci kodunu ve koleksiyonları temizleyebilirsiniz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle): Yeni koleksiyon ve yineleyici türlerini uygulayabilir ve bunları hiçbir şeyi bozmadan mevcut koda aktarabilirsiniz.

✅ Aynı nedenle bir yinelemeyi geciktirebilir ve gerektiğinde devam ettirebilirsiniz.

❌ Uygulamanız yalnızca basit koleksiyonlarla çalışıyorsa, deseni uygulamak aşırıya kaçabilir.

❌ Yineleyici kullanmak, bazı özel koleksiyonların öğelerini doğrudan incelemekten daha az verimli olabilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Bileşik (Composite)** ağaçların arasında geçiş yapmak için **Yineleyicileri (Iterators)** kullanabilirsiniz.

- Koleksiyon alt sınıflarının koleksiyonlarla uyumlu farklı türde yineleyiciler döndürmesine izin vermek için **Iterator** ile birlikte **Factory Method**'u kullanabilirsiniz.

- Mevcut yineleme durumunu yakalamak ve gerekirse geri almak için **Memento**'yu **Iterator** ile birlikte kullanabilirsiniz.

- Karmaşık bir veri yapısında gezinmek ve hepsi farklı sınıflara sahip olsalar bile öğeleri üzerinde bazı işlemler yürütmek için **Visitor**'ı **Iterator** ile birlikte kullanabilirsiniz.
  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Desen Java kodunda çok yaygındır. Birçok çerçeve ve kütüphane, koleksiyonlarında gezinmek için standart bir yol sağlamak için bunu kullanır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   Tüm implementationları  [`java.util.Iterator`](http://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)  (ayrıca  [`java.util.Scanner`](http://docs.oracle.com/javase/8/docs/api/java/util/Scanner.html)).
    
-   Tüm implementationları [`java.util.Enumeration`](http://docs.oracle.com/javase/8/docs/api/java/util/Enumeration.html).

**Tanımlama:** Yineleyicinin gezinme yöntemleriyle (sonraki (`next`), önceki (`previous`) ve diğerleri gibi) tanınması kolaydır. Yineleyicileri kullanan istemci kodunun, geçilen koleksiyona doğrudan erişimi olmayabilir.

 
####  Sosyal medya profilleri üzerinde yineleme

Bu örnekte Iterator modeli, herhangi bir iletişim ayrıntısını istemci koduna göstermeden uzak bir sosyal ağ koleksiyonunun sosyal profilleri üzerinden geçmek için kullanılır.

**📁 iterators**

⤵️ 📄 `iterators/ProfileIterator.java` : Profil arayüzünü tanımlar

```java
package fatihes1.iterator.example.iterators;

import fatihes1.iterator.example.profile.Profile;

public interface ProfileIterator {
    boolean hasNext();

    Profile getNext();

    void reset();
}
```

⤵️ 📄 `iterators/FacebookIterator.java`: Facebook profilleri üzerinde yineleme yapar

```java
package fatihes1.iterator.example.iterators;

import fatihes1.iterator.example.profile.Profile;
import fatihes1.iterator.example.social_networks.Facebook;

import java.util.ArrayList;
import java.util.List;

public class FacebookIterator implements ProfileIterator {
    private Facebook facebook;
    private String type;
    private String email;
    private int currentPosition = 0;
    private List<String> emails = new ArrayList<>();
    private List<Profile> profiles = new ArrayList<>();

    public FacebookIterator(Facebook facebook, String type, String email) {
        this.facebook = facebook;
        this.type = type;
        this.email = email;
    }

    private void lazyLoad() {
        if (emails.size() == 0) {
            List<String> profiles = facebook.requestProfileFriendsFromFacebook(this.email, this.type);
            for (String profile : profiles) {
                this.emails.add(profile);
                this.profiles.add(null);
            }
        }
    }

    @Override
    public boolean hasNext() {
        lazyLoad();
        return currentPosition < emails.size();
    }

    @Override
    public Profile getNext() {
        if (!hasNext()) {
            return null;
        }

        String friendEmail = emails.get(currentPosition);
        Profile friendProfile = profiles.get(currentPosition);
        if (friendProfile == null) {
            friendProfile = facebook.requestProfileFromFacebook(friendEmail);
            profiles.set(currentPosition, friendProfile);
        }
        currentPosition++;
        return friendProfile;
    }

    @Override
    public void reset() {
        currentPosition = 0;
    }
}
```


⤵️ 📄 `iterators/LinkedInIterator.java`: LinkedIn profilleri üzerinde yineleme yapar

```java
package fatihes1.iterator.example.iterators;

import fatihes1.iterator.example.profile.Profile;
import fatihes1.iterator.example.social_networks.LinkedIn;

import java.util.ArrayList;
import java.util.List;

public class LinkedInIterator implements ProfileIterator {
    private LinkedIn linkedIn;
    private String type;
    private String email;
    private int currentPosition = 0;
    private List<String> emails = new ArrayList<>();
    private List<Profile> contacts = new ArrayList<>();

    public LinkedInIterator(LinkedIn linkedIn, String type, String email) {
        this.linkedIn = linkedIn;
        this.type = type;
        this.email = email;
    }

    private void lazyLoad() {
        if (emails.size() == 0) {
            List<String> profiles = linkedIn.requestRelatedContactsFromLinkedInAPI(this.email, this.type);
            for (String profile : profiles) {
                this.emails.add(profile);
                this.contacts.add(null);
            }
        }
    }

    @Override
    public boolean hasNext() {
        lazyLoad();
        return currentPosition < emails.size();
    }

    @Override
    public Profile getNext() {
        if (!hasNext()) {
            return null;
        }

        String friendEmail = emails.get(currentPosition);
        Profile friendContact = contacts.get(currentPosition);
        if (friendContact == null) {
            friendContact = linkedIn.requestContactInfoFromLinkedInAPI(friendEmail);
            contacts.set(currentPosition, friendContact);
        }
        currentPosition++;
        return friendContact;
    }

    @Override
    public void reset() {
        currentPosition = 0;
    }
}
```

**📁 social_networks**

⤵️ 📄 `social_networks/SocialNetwork.java`: Ortak sosyal ağ arayüzünü tanımlar

```java
package fatihes1.iterator.example.social_networks;

import fatihes1.iterator.example.iterators.ProfileIterator;

public interface SocialNetwork {
    ProfileIterator createFriendsIterator(String profileEmail);

    ProfileIterator createCoworkersIterator(String profileEmail);
}
```

⤵️ 📄 `social_networks/Facebook.java`: Facebook

```java
package fatihes1.iterator.example.social_networks;

import fatihes1.iterator.example.iterators.FacebookIterator;
import fatihes1.iterator.example.iterators.ProfileIterator;
import fatihes1.iterator.example.profile.Profile;

import java.util.ArrayList;
import java.util.List;

public class Facebook implements SocialNetwork {
    private List<Profile> profiles;

    public Facebook(List<Profile> cache) {
        if (cache != null) {
            this.profiles = cache;
        } else {
            this.profiles = new ArrayList<>();
        }
    }

    public Profile requestProfileFromFacebook(String profileEmail) {
        // Here would be a POST request to one of the Facebook API endpoints.
        // Instead, we emulates long network connection, which you would expect
        // in the real life...
        simulateNetworkLatency();
        System.out.println("Facebook: Loading profile '" + profileEmail + "' over the network...");

        // ...and return test data.
        return findProfile(profileEmail);
    }

    public List<String> requestProfileFriendsFromFacebook(String profileEmail, String contactType) {
        // Here would be a POST request to one of the Facebook API endpoints.
        // Instead, we emulates long network connection, which you would expect
        // in the real life...
        simulateNetworkLatency();
        System.out.println("Facebook: Loading '" + contactType + "' list of '" + profileEmail + "' over the network...");

        // ...and return test data.
        Profile profile = findProfile(profileEmail);
        if (profile != null) {
            return profile.getContacts(contactType);
        }
        return null;
    }

    private Profile findProfile(String profileEmail) {
        for (Profile profile : profiles) {
            if (profile.getEmail().equals(profileEmail)) {
                return profile;
            }
        }
        return null;
    }

    private void simulateNetworkLatency() {
        try {
            Thread.sleep(2500);
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
    }

    @Override
    public ProfileIterator createFriendsIterator(String profileEmail) {
        return new FacebookIterator(this, "friends", profileEmail);
    }

    @Override
    public ProfileIterator createCoworkersIterator(String profileEmail) {
        return new FacebookIterator(this, "coworkers", profileEmail);
    }

}
```

⤵️ 📄 `social_networks/LinkedIn.java`: LinkedIn

```java
package fatihes1.iterator.example.social_networks;

import fatihes1.iterator.example.iterators.LinkedInIterator;
import fatihes1.iterator.example.iterators.ProfileIterator;
import fatihes1.iterator.example.profile.Profile;

import java.util.ArrayList;
import java.util.List;

public class LinkedIn implements SocialNetwork {
    private List<Profile> contacts;

    public LinkedIn(List<Profile> cache) {
        if (cache != null) {
            this.contacts = cache;
        } else {
            this.contacts = new ArrayList<>();
        }
    }

    public Profile requestContactInfoFromLinkedInAPI(String profileEmail) {
        // Here would be a POST request to one of the LinkedIn API endpoints.
        // Instead, we emulates long network connection, which you would expect
        // in the real life...
        simulateNetworkLatency();
        System.out.println("LinkedIn: Loading profile '" + profileEmail + "' over the network...");

        // ...and return test data.
        return findContact(profileEmail);
    }

    public List<String> requestRelatedContactsFromLinkedInAPI(String profileEmail, String contactType) {
        // Here would be a POST request to one of the LinkedIn API endpoints.
        // Instead, we emulates long network connection, which you would expect
        // in the real life.
        simulateNetworkLatency();
        System.out.println("LinkedIn: Loading '" + contactType + "' list of '" + profileEmail + "' over the network...");

        // ...and return test data.
        Profile profile = findContact(profileEmail);
        if (profile != null) {
            return profile.getContacts(contactType);
        }
        return null;
    }

    private Profile findContact(String profileEmail) {
        for (Profile profile : contacts) {
            if (profile.getEmail().equals(profileEmail)) {
                return profile;
            }
        }
        return null;
    }

    private void simulateNetworkLatency() {
        try {
            Thread.sleep(2500);
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
    }

    @Override
    public ProfileIterator createFriendsIterator(String profileEmail) {
        return new LinkedInIterator(this, "friends", profileEmail);
    }

    @Override
    public ProfileIterator createCoworkersIterator(String profileEmail) {
        return new LinkedInIterator(this, "coworkers", profileEmail);
    }
}
```

**📁 profile**

⤵️ 📄 `profile/Profile.java`: Sosyal Profiller

```java
package fatihes1.iterator.example.profile;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Profile {
    private String name;
    private String email;
    private Map<String, List<String>> contacts = new HashMap<>();

    public Profile(String email, String name, String... contacts) {
        this.email = email;
        this.name = name;

        // Parse contact list from a set of "friend:email@gmail.com" pairs.
        for (String contact : contacts) {
            String[] parts = contact.split(":");
            String contactType = "friend", contactEmail;
            if (parts.length == 1) {
                contactEmail = parts[0];
            }
            else {
                contactType = parts[0];
                contactEmail = parts[1];
            }
            if (!this.contacts.containsKey(contactType)) {
                this.contacts.put(contactType, new ArrayList<>());
            }
            this.contacts.get(contactType).add(contactEmail);
        }
    }

    public String getEmail() {
        return email;
    }

    public String getName() {
        return name;
    }

    public List<String> getContacts(String contactType) {
        if (!this.contacts.containsKey(contactType)) {
            this.contacts.put(contactType, new ArrayList<>());
        }
        return contacts.get(contactType);
    }
}
```

**📁 spammer**

⤵️ 📄 `spammer/SocialSpammer.java`: Mesaj gönderme uygulaması

```java
package fatihes1.iterator.example.spammer;

import fatihes1.iterator.example.iterators.ProfileIterator;
import fatihes1.iterator.example.profile.Profile;
import fatihes1.iterator.example.social_networks.SocialNetwork;

public class SocialSpammer {
    public SocialNetwork network;
    public ProfileIterator iterator;

    public SocialSpammer(SocialNetwork network) {
        this.network = network;
    }

    public void sendSpamToFriends(String profileEmail, String message) {
        System.out.println("\nIterating over friends...\n");
        iterator = network.createFriendsIterator(profileEmail);
        while (iterator.hasNext()) {
            Profile profile = iterator.getNext();
            sendMessage(profile.getEmail(), message);
        }
    }

    public void sendSpamToCoworkers(String profileEmail, String message) {
        System.out.println("\nIterating over coworkers...\n");
        iterator = network.createCoworkersIterator(profileEmail);
        while (iterator.hasNext()) {
            Profile profile = iterator.getNext();
            sendMessage(profile.getEmail(), message);
        }
    }

    public void sendMessage(String email, String message) {
        System.out.println("Sent message to: '" + email + "'. Message body: '" + message + "'");
    }
}
```



⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.iterator.example;

import fatihes1.iterator.example.profile.Profile;
import fatihes1.iterator.example.social_networks.Facebook;
import fatihes1.iterator.example.social_networks.LinkedIn;
import fatihes1.iterator.example.social_networks.SocialNetwork;
import fatihes1.iterator.example.spammer.SocialSpammer;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    public static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        System.out.println("Please specify social network to target spam tool (default:Facebook):");
        System.out.println("1. Facebook");
        System.out.println("2. LinkedIn");
        String choice = scanner.nextLine();

        SocialNetwork network;
        if (choice.equals("2")) {
            network = new LinkedIn(createTestProfiles());
        }
        else {
            network = new Facebook(createTestProfiles());
        }

        SocialSpammer spammer = new SocialSpammer(network);
        spammer.sendSpamToFriends("anna.smith@bing.com",
                "Hey! This is Anna's friend Josh. Can you do me a favor and like this post [link]?");
        spammer.sendSpamToCoworkers("anna.smith@bing.com",
                "Hey! This is Anna's boss Jason. Anna told me you would be interested in [link].");
    }

    public static List<Profile> createTestProfiles() {
        List<Profile> data = new ArrayList<Profile>();
        data.add(new Profile("anna.smith@bing.com", "Anna Smith", "friends:mad_max@ya.com", "friends:catwoman@yahoo.com", "coworkers:sam@amazon.com"));
        data.add(new Profile("mad_max@ya.com", "Maximilian", "friends:anna.smith@bing.com", "coworkers:sam@amazon.com"));
        data.add(new Profile("bill@microsoft.eu", "Billie", "coworkers:avanger@ukr.net"));
        data.add(new Profile("avanger@ukr.net", "John Day", "coworkers:bill@microsoft.eu"));
        data.add(new Profile("sam@amazon.com", "Sam Kitting", "coworkers:anna.smith@bing.com", "coworkers:mad_max@ya.com", "friends:catwoman@yahoo.com"));
        data.add(new Profile("catwoman@yahoo.com", "Liza", "friends:anna.smith@bing.com", "friends:sam@amazon.com"));
        return data;
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalışma Çıktısı

```
Please specify social network to target spam tool (default:Facebook):
1. Facebook
2. LinkedIn
> 1

Iterating over friends...

Facebook: Loading 'friends' list of 'anna.smith@bing.com' over the network...
Facebook: Loading profile 'mad_max@ya.com' over the network...
Sent message to: 'mad_max@ya.com'. Message body: 'Hey! This is Anna's friend Josh. Can you do me a favor and like this post [link]?'
Facebook: Loading profile 'catwoman@yahoo.com' over the network...
Sent message to: 'catwoman@yahoo.com'. Message body: 'Hey! This is Anna's friend Josh. Can you do me a favor and like this post [link]?'

Iterating over coworkers...

Facebook: Loading 'coworkers' list of 'anna.smith@bing.com' over the network...
Facebook: Loading profile 'sam@amazon.com' over the network...
Sent message to: 'sam@amazon.com'. Message body: 'Hey! This is Anna's boss Jason. Anna told me you would be interested in [link].'
```

