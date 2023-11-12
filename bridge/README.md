#  Köprü (Bridge) Tasarım Deseni


##  💬 Amaç

Köprü, büyük bir sınıfı veya yakından ilişkili sınıflar kümesini, birbirinden bağımsız olarak geliştirilebilen iki ayrı hiyerarşiye (soyutlama ve uygulama - abstraction and implementation) ayırmanıza olanak tanıyan yapısal (structural) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/bridge/bridge-2x.png)


##  🙁 Problem


Abstraction? Implementation? Kavramsal olarak anlaması zor terimler olabilir. Neyse ki bu github repo'sundasınız. Basit bir örnek düşünelim.

Diyelim ki bir geometrik Şekil (`Shape`) sınıfınız var ve bu sınıfın iki alt sınıfı var: Daire (`Circle`) ve Kare (`Square`). Bu sınıf hiyerarşisine renkleri dahil etmek istiyorsunuz, bu nedenle Kırmızı (`Red`) ve Mavi (`Blue`) şekil alt sınıfları oluşturmayı planlıyorsunuz. Ancak zaten iki alt sınıfa sahip olduğunuzdan,  MaviDaire (`BlueCircle`) ve KırmızıKare (`RedSquare`) alt sınıfları gibi dört sınıf kombinasyonu oluşturmanız gerekecektir.

![](https://refactoring.guru/images/patterns/diagrams/bridge/problem-en-2x.png)

*Sınıf kombinasyonlarının sayısı geometrik artış ile çoğalır.*

Yeni şekil türlerini ve renkleri hiyerarşiye eklemek, yeni sınıflar ekler ve hiyerarşiyi üzerine koyarak büyütür. Örneğin, bir üçgen şekli eklemek için her renk için birer alt sınıf tanımlamanız gerekecektir. Bunun gibi, bundan sonra yeni bir renk eklemek, her şekil türü için birer alt sınıf oluşturmayı gerektirecektir. Böylece gittikçe karmaşıklaşan bir yapı ortaya çıkar.

##  😊 Çözüm

Bu sorun, şekil sınıflarını iki bağımsız boyutta genişletmeye çalıştığımızdan kaynaklanır. Bu bağımsız boyutlar; biçim ve renktir. Bu, sınıf kalıtımı (class inheritance) ile sık karşılaşılan bir sorundur.

Bridge deseni, bu sorunu çözmeye çalışırken kalıtımdan nesne bileşimine (object composition) geçiş yaparak bu sorunu çözmeye çalışır. Bunun anlamı, boyutlardan birini ayrı bir sınıf hiyerarşisine ayıklamanızdır, böylece orijinal sınıflar, tüm durumunu ve davranışlarını tek bir sınıf içinde tutmak yerine yeni hiyerarşinin bir nesnesine başvurur.

![](https://refactoring.guru/images/patterns/diagrams/bridge/solution-en-2x.png)

*Bir sınıf hiyerarşisinin sorununu, onu birkaç ilgili hiyerarşiye dönüştürerek önleyebilirsiniz.*

Bu yaklaşımı takiben, renkle ilgili kodu iki alt sınıfa bölerek kendi sınıfına çıkarabiliriz: Kırmızı (`Red`) ve Mavi (`Blue`). Şekil (`Shape`) sınıfı daha sonra renk nesnelerinden birine işaret eden bir başvuru alanı alır. Artık şekil, renkle ilgili herhangi bir işi bağlantılı renk nesnesine devredebilir. Bu referans, Şekil (`Shape`) ve Renk (`Color`) sınıfları arasında bir köprü görevi görecektir. Bundan sonra, yeni renkler eklemek şekil hiyerarşisini değiştirmeyi gerektirmeyecek ve aynı şekilde yeni şekiller eklenmesi de renk hiyerarşisini değiştirmemize neden olmayacaktır.

#### Abstraction and Implementation

Tanımlanan "Soyutlama" ve Implementation "Uygulama" terimleri, Dörtlü Grup (GoF) kitabının tanımı içinde oldukça akademik ve karmaşık gelebilir. Ancak, bu terimlerin anlamını anlamak için daha ayrıntılı bir bakış açısı sunabiliriz. İşte bu terimlerin ardındaki anlam:

- Soyutlama (veya Arayüz): Bu, bir varlık için yüksek seviye kontrol katmanıdır ve kendi başına gerçek bir işi yapmaz. İşleri gerçekleştirmek için uygulama katmanına (aynı zamanda platform olarak da adlandırılır) görevleri devreder.
Programlama desenleri bağlamında, "soyutlama" ve "uygulama," programlama dilinizdeki arayüzler veya soyut sınıflarla aynı anlama gelmeyebilir.

- Uygulama (veya Platform): Bu, gerçek işi yapan alt seviye bir katmandır. Görevleri soyutlama katmanı tarafından devredilen görevleri gerçekleştirmekle sorumludur.

Örnek vermek gerekirse, gerçek bir uygulamada, soyutlama bir grafik kullanıcı arayüzü (GUI) olabilir ve uygulama, kullanıcı etkileşimlerine yanıt olarak GUI katmanının çağrılarını alan işletim sistemi altındaki kod (API) olabilir. Soyutlama katmanı kullanıcı için kullanıcı dostu bir arayüz sunmaya odaklanırken, uygulama katmanı teknik yönlerle ilgilenir.

Bridge deseni bağlamında, soyutlama ve uygulama, bir nesnenin farklı boyutlarını ayırmak için kullanılır. Örneğin, şekiller ve renkler örneğinde, soyutlama şekillerle ilgilenecek ve uygulama renklerle ilgilenecektir. Bu ayrım, daha fazla esneklik sağlar ve yeni şekil ve renk kombinasyonları eklerken alt sınıf patlaması oluşturmayı önler.

En kötü senaryoda, karmaşık ve karışık bir kod tabanına sahip olabilirsiniz, burada pek çok koşullu ifade farklı GUI'leri farklı API'lerle bağlar ve bakım kabusa dönüşebilir. Bu, kodun açıklığının eksikliğine, hata riskinin artmasına ve uygulamanın genişletilmesinin veya değiştirilmesinin zorluğuna neden olabilir.

Bu zorlukları ele almak için yazılım geliştiricileri, Köprü tasarım deseni gibi tasarım desenlerini kullanabilirler. Köprü deseni, soyutlamaları (GUI'leri) uygulamalardan (API'ler) ayırmanıza olanak tanır ve daha modüler ve esnek bir kod yapısı sağlar.

Köprü desenini kullanarak daha temiz ve sürdürülebilir bir kod tabanı oluşturabilirsiniz. Bu tasarım deseni, uygulamayı GUI ve API boyutlarında bağımsız olarak genişletebilmenizi sağlar ve fazla karmaşıklık veya koşullu ifadeleri devreye sokmadan farklı GUI ve API kombinasyonlarını yönetmeyi kolaylaştırır.

Genel olarak, Köprü deseni gibi tasarım desenleri, yazılım tasarımındaki yaygın zorluklara zarif çözümler sunar ve uygulamalarınızda bakım, esneklik ve ölçeklenebilirliği teşvik eder.

![](https://refactoring.guru/images/patterns/content/bridge/bridge-3-en-2x.png)

*Monolitik bir kod tabanında basit bir değişiklik yapmak bile oldukça zordur çünkü her şeyi çok iyi anlamanız ve her şeye hakim olmanız gerekir. Daha küçük, iyi tanımlanmış modüllerde değişiklik yapmak çok daha kolaydır.*

Belirli arayüz-platform kombinasyonlarıyla ilgili kodu ayrı sınıflara ayırarak bu kaosa düzen getirebilirsiniz. Ancak, yakında bu gibi sınıflayın sayısının çok olduğunu fark edeceksiniz. Sınıf hiyerarşisi katlanarak büyüyecektir çünkü yeni bir GUI eklemek veya farklı bir API'yi desteklemek giderek daha fazla sınıf oluşturmayı gerektirecektir.

Bu sorunu Köprü deseni ile çözmeye çalışalım. Sınıfları iki hiyerarşiye bölmemizi önerir:

- Soyutlama (Abstraction): uygulamanın GUI katmanı.
- Uygulama (Implementation): işletim sistemlerinin API'leri.

![](https://refactoring.guru/images/patterns/content/bridge/bridge-2-en-2x.png)

*Platformlar arası bir uygulamayı yapılandırmanın yollarından biri.*

Soyutlaştırma nesnesi, uygulamanın görünümünü kontrol eder ve gerçek çalışmayı bağlı uygulama nesnesine iletir. Farklı uygulamalar, ortak bir arayüzü takip ettikleri sürece değiştirilebilir, bu da aynı GUI'nin Windows ve Linux altında çalışmasını sağlar.

Sonuç olarak, API ile ilgili sınıflara dokunmadan GUI sınıflarını değiştirebilirsiniz. Dahası, başka bir işletim sistemini desteklemek için sadece uygulama hiyerarşisinde bir alt sınıf oluşturmak gereklidir.


##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/bridge/structure-en-2x.png)

1. Soyutlama (**Abstraction**), üst düzey kontrol mantığı sağlar. Gerçek düşük seviyeli işi yapmak için uygulama nesnesine (implementation object) güvenir.
2. Uygulama (**Implementation**), tüm uygulamalar için ortak olan arayüzü (interface) tanımlanır. Bir soyutlama, bir uygulama nesnesiyle yalnızca burada (bu tanımlanan interface )bildirilen yöntemler aracılığıyla iletişim kurabilir.
Soyutlama, uygulamayla aynı yöntemleri listeleyebilir, ancak genellikle soyutlama, uygulama tarafından tanımlanan çok çeşitli ilkel işlemlere dayanan bazı karmaşık metodlar tanımlar.
3. **Concrete Implementations** platforma özel kod içerir.
4. **Refined Abstractions**, kontrol mantığının çeşitlerini sağlar. Ebeveynleri gibi onlar da genel uygulama arayüzü aracılığıyla farklı uygulamalarla çalışırlar.
5. Genellikle İstemci (**Client**) yalnızca soyutlamayla çalışmakla ilgilenir. Ancak soyutlama nesnesini uygulama nesnelerinden birine bağlamak istemcinin görevidir.


##  💻 Sözde Kod (Pseudocode)

Bu örnek, Köprü modelinin, cihazları ve uzaktan kumandalarını yöneten bir uygulamanın yekpare kodunu bölmeye nasıl yardımcı olabileceğini göstermektedir. Cihaz (`Device`) sınıfları uygulama yani implementation görevi görürken, Uzaktan Kumandalar (`Remote`) soyutlama yani abstraction görevi görür.

![](https://refactoring.guru/images/patterns/diagrams/bridge/example-en-2x.png)

*Orijinal sınıf hiyerarşisi iki bölüme ayrılmıştır: cihazlar ve uzaktan kumandalar.*

Temel uzaktan kumanda sınıfı, bir cihaz nesnesi ile bağlantı kurduğu bir referans alanı tanımlar. Tüm uzaktan kumandalar, genel cihaz arabirimini (interface) kullanarak cihazlarla çalışır. Bu durum da aynı uzaktan kumandanın birden fazla cihaz türünü desteklemesine olanak tanır.

Uzaktan kumanda sınıflarını cihaz sınıflarından bağımsız olarak geliştirebilirsiniz. Tek gereken yeni bir uzaktan kumanda alt sınıfı (subclass) oluşturmaktır. Örneğin, temel bir uzaktan kumanda sadece iki düğmeye sahip olabilir. Bununla beraber, ek bir pil veya dokunmatik ekran gibi ekstra özellikler ekleyebilirsiniz.

İstemci kodu istenen türdeki uzaktan kumandayı, uzaktan kumandanın yapıcı işlevi (constructor) aracılığıyla belirli bir cihaz nesnesi ile bağlar.

```java
// "Somutlaştırma," yani "abstraction" iki sınıf hiyerarşisinin "kontrol" kısmı için arayüzü tanımlar.
// Bu, bir "uygulama" yani "implementation" hiyerarşisine ait bir nesneye bir referansı sürdürür
// ve tüm gerçek işi bu nesneye iletir.
class RemoteControl is
    protected field device: Device
    constructor RemoteControl(device: Device) is
        this.device = device
    method togglePower() is
        if (device.isEnabled()) then
            device.disable()
        else
            device.enable()
    method volumeDown() is
        device.setVolume(device.getVolume() - 10)
    method volumeUp() is
        device.setVolume(device.getVolume() + 10)
    method channelDown() is
        device.setChannel(device.getChannel() - 1)
    method channelUp() is
        device.setChannel(device.getChannel() + 1)

// "Soyutlaştırma" yani "abstraction" hiyerarşisinden sınıfları cihaz sınıflarından bağımsız olarak genişletebilirsiniz.
class AdvancedRemoteControl extends RemoteControl is
    method mute() is
        device.setVolume(0)

// "Uygulama" yani "implementation" arayüzü, tüm somut uygulama sınıflarında ortak olan yöntemleri bildirir.
// "Soyutlaştırma" arayüzü ile eşleşmesi gerekmez. Aslında, iki arayüz tamamen farklı olabilir.
// Genellikle "soyutlaştırma" arayüzü yalnızca ilkel işlemleri sağlarken,
// "soyutlaştırma" bu ilkel işlemlere dayalı daha yüksek seviye işlemleri tanımlar.
interface Device is
    method isEnabled()
    method enable()
    method disable()
    method getVolume()
    method setVolume(percent)
    method getChannel()
    method setChannel(channel)

// Tüm cihazlar aynı arayüzü izler.
class Tv implements Device is
    // ...

class Radio implements Device is
    // ...

// İstemci kodun bir yerinde
tv = new Tv()
remote = new RemoteControl(tv)
remote.togglePower()

radio = new Radio()
remote = new AdvancedRemoteControl(radio)

```


##  💡Uygulanabilirlik

**🐞 Bazı işlevlerin çeşitli varyantlarına sahip monolitik bir sınıfı bölmek ve düzenlemek istediğinizde Köprü modelini kullanın (örneğin, sınıf çeşitli veritabanı sunucularıyla çalışabiliyorsa)**

⚡️ Bir sınıf ne kadar büyürse, nasıl çalıştığını anlamak o kadar zorlaşır ve değişiklik yapmak o kadar uzun sürer. İşlevin bir varyasyonuna yapılan değişiklikler, genellikle tüm sınıfa değişiklik yapmayı gerektirir, bu da genellikle hatalara yol açar veya bazı oluşabilecek kritik senaryoları göz ardı etmeyi getirir.
Köprü deseni, monolitik sınıfı birkaç sınıf hiyerarşisine bölmeyi sağlar. Bundan sonra, her hiyerarşideki sınıfları diğer hiyerarşilerdeki sınıflardan bağımsız olarak değiştirebilirsiniz. Bu yaklaşım, kod bakımını basitleştirir ve mevcut kodu bozma riskini en aza indirir.
  
----------------

**🐞 Bir sınıfı birkaç bağımsız boyutta genişletmeniz gerektiğinde modeli kullanın.**

⚡️ Köprü, boyutların her biri için ayrı bir sınıf hiyerarşisi çıkarmanızı önerir. Özgün sınıf, her şeyi kendi başına yapmak yerine ilgili çalışmayı bu hiyerarşilere ait nesnelere devreder.

------------

**🐞 Uygulamaları çalışma zamanında değiştirebilmeniz gerekiyorsa Köprü desenini kullanın.**

⚡️ Opsiyonel olmasına rağmen, Köprü deseni size soyutlaştırma (abstraction) içindeki uygulama nesnesini değiştirme olanağı sağlar. Bunu yapmak bir alana yeni bir değer atamak kadar kolaydır.
Bu arada, bu son madde, bir çok insanın Köprü tasarım desenini Strateji tasarım deseniyle karıştırmasının ana nedenidir. Bir örüntünün, sınıflarınızı yapılandırmanın belirli bir yolundan daha fazlası olduğunu unutmayın. Ayrıca niyet ve ele alınan bir sorunu da iletebilir.


##  📝 Nasıl Uygulanır?

1. Sınıflarınızda bağımsız kavramları tanımlayın. Bu bağımsız kavramlar şunlar olabilir: abstraction/platform, domain/infrastructure, front-end/back-end, or interface/implementation.

2. İstemcinin ihtiyaç duyduğu işlemleri belirleyin ve bunları temel soyutlama sınıfında tanımlayın.

3. Tüm platformlarda mevcut olan işlemleri belirleyin. Soyutlamanın ihtiyaç duyduğu işlemleri genel uygulama (implementation) arayüzünde bildirin.

4. Alanınızdaki (domain) tüm platformlar için concrete implementation sınıfları oluşturun, ancak hepsinin uygulama (implementation) arayüzünü izlediğinden emin olun.

5. Soyutlama sınıfının içine, uygulama türü için bir referans alanı ekleyin. Soyutlama, o alandaki referansı olan uygulama yani implementation nesnesine çoğu işi devreder.

6. Yüksek seviyeli mantığın çeşitli varyantlarına sahipseniz, her varyant için temel soyutlama sınıfını genişleterek iyileştirilmiş soyutlamalar (refined abstractions) oluşturun.

7. İstemci kodu, soyutlama ile bir uygulama (implementation) nesnesini ilişkilendirmek için soyutlamanın yapıcı işlevine bir implementation nesnesi iletmelidir. Bundan sonra istemci, implementation'ı unutabilir ve yalnızca soyutlama nesnesi ile çalışabilir.
  

##  ⚖️ Artıları ve Eksileri

✅ Platformdan bağımsız sınıflar ve uygulamalar oluşturabilirsiniz.

✅ İstemci kodu üst düzey soyutlamalarla çalışır. Platform ayrıntılarına maruz kalmaz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle): Yeni soyutlamaları ve uygulamaları birbirinden bağımsız olarak tanıtabilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): Soyutlamada üst düzey mantığa, uygulamada ise platform ayrıntılarına odaklanabilirsiniz.

❌ Deseni halihazırda oldukça uyumlu bir sınıfa uygulayarak kodu daha karmaşık hale getirebilirsiniz.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Köprü** deseni genellikle önceden tasarlanır ve size uygulamanın parçalarını birbirinden bağımsız olarak geliştirme olanağı sağlar. Öte yandan, **Adaptör** deseni genellikle mevcut bir uygulama ile kullanılır ve aksi takdirde uyumsuz olan bazı sınıfların iyi bir şekilde birlikte çalışmasını sağlar.
- **Köprü**, **Durum**, **Strateji** (ve belirli bir ölçüde **Adaptör**) desenleri oldukça benzer yapılar içerir. Aslında, bu desenlerin hepsi işi diğer nesnelere devretme prensibine dayanır. Ancak hepsi farklı sorunları çözer. Bir desen, sadece kodunuzu belirli bir şekilde yapılandırma reçetesi değildir. Aynı zamanda desen, desenin çözdüğü sorunu diğer geliştiricilere iletebilir.
- **Köprü** deseni ile birlikte **Soyut Fabrika** desenini kullanabilirsiniz. Bu eşleme, Köprü tarafından tanımlanan bazı soyutlamaların yalnızca belirli uygulamalarla çalışabilmesi durumunda kullanışlıdır. Bu durumda, **Soyut Fabrika** deseni, bu ilişkileri kapsayabilir ve karmaşıklığı istemci kodundan gizleyebilir.
- **Köprü** deseni ile **Builder** desenini birleştirebilirsiniz: yönetici sınıf soyutlamayı oynar, farklı oluşturucular ise uygulamaları gerçekler."

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Köprü modeli özellikle platformlar arası uygulamalarla uğraşırken, birden çok türde veritabanı sunucusunu desteklerken veya belirli türdeki çeşitli API sağlayıcılarıyla (örneğin, bulut platformları, sosyal ağlar vb.) çalışırken kullanışlıdır.


**Tanımlama:** Bridge, bazı kontrol birimleri ile güvendiği birkaç farklı platform arasındaki net ayrımla tanınabilir.

 
####  Cihazlar ve uzaktan kumandalar arasında köprü oluşturma

Bu örnek, uzaktan kumanda sınıfları ile kontrol ettikleri cihazlar arasındaki ayrımı gösterir.

Uzaktan kumandalar soyutlama görevi görür ve cihazlar onların uygulamalarıdır. Ortak arayüzler sayesinde aynı uzaktan kumandalar farklı cihazlarla çalışabilir ve bunun tersi de geçerlidir.

Köprü deseni, karşıt hiyerarşinin koduna dokunmadan yeni sınıfların değiştirilmesine ve hatta oluşturulmasına izin verir.

**📁 devices**

⤵️ 📄 `devices/Device.java`: Tüm cihazların ortak arayüz

```java
package fatihes1.bridge.example.devices;

public interface Device {
    boolean isEnabled();

    void enable();

    void disable();

    int getVolume();

    void setVolume(int percent);

    int getChannel();

    void setChannel(int channel);

    void printStatus();
}
```

⤵️ 📄 `devices/Radio.java`: Radyo

```java
package fatihes1.bridge.example.devices;

public class Radio implements Device {
    private boolean on = false;
    private int volume = 30;
    private int channel = 1;

    @Override
    public boolean isEnabled() {
        return on;
    }

    @Override
    public void enable() {
        on = true;
    }

    @Override
    public void disable() {
        on = false;
    }

    @Override
    public int getVolume() {
        return volume;
    }

    @Override
    public void setVolume(int volume) {
        if (volume > 100) {
            this.volume = 100;
        } else if (volume < 0) {
            this.volume = 0;
        } else {
            this.volume = volume;
        }
    }

    @Override
    public int getChannel() {
        return channel;
    }

    @Override
    public void setChannel(int channel) {
        this.channel = channel;
    }

    @Override
    public void printStatus() {
        System.out.println("------------------------------------");
        System.out.println("| I'm radio.");
        System.out.println("| I'm " + (on ? "enabled" : "disabled"));
        System.out.println("| Current volume is " + volume + "%");
        System.out.println("| Current channel is " + channel);
        System.out.println("------------------------------------\n");
    }
}
```


⤵️ 📄 `devices/Tv.java`: TV

```java
package fatihes1.bridge.example.devices;

public class Tv implements Device {
    private boolean on = false;
    private int volume = 30;
    private int channel = 1;

    @Override
    public boolean isEnabled() {
        return on;
    }

    @Override
    public void enable() {
        on = true;
    }

    @Override
    public void disable() {
        on = false;
    }

    @Override
    public int getVolume() {
        return volume;
    }

    @Override
    public void setVolume(int volume) {
        if (volume > 100) {
            this.volume = 100;
        } else if (volume < 0) {
            this.volume = 0;
        } else {
            this.volume = volume;
        }
    }

    @Override
    public int getChannel() {
        return channel;
    }

    @Override
    public void setChannel(int channel) {
        this.channel = channel;
    }

    @Override
    public void printStatus() {
        System.out.println("------------------------------------");
        System.out.println("| I'm TV set.");
        System.out.println("| I'm " + (on ? "enabled" : "disabled"));
        System.out.println("| Current volume is " + volume + "%");
        System.out.println("| Current channel is " + channel);
        System.out.println("------------------------------------\n");
    }
}
```

**📁 remotes**

⤵️ 📄 `remotes/Remote.java`: Tüm uzaktan kumandalar için ortak arayüz

```java
package fatihes1.bridge.example.remotes;

public interface Remote {
    void power();

    void volumeDown();

    void volumeUp();

    void channelDown();

    void channelUp();
}
```

⤵️ 📄 `remotes/BasicRemote.java`: Basit uzaktan kumanda

```java
package fatihes1.bridge.example.remotes;

import fatihes1.bridge.example.devices.Device;

public class BasicRemote implements Remote {
    protected Device device;

    public BasicRemote() {}

    public BasicRemote(Device device) {
        this.device = device;
    }

    @Override
    public void power() {
        System.out.println("Remote: power toggle");
        if (device.isEnabled()) {
            device.disable();
        } else {
            device.enable();
        }
    }

    @Override
    public void volumeDown() {
        System.out.println("Remote: volume down");
        device.setVolume(device.getVolume() - 10);
    }

    @Override
    public void volumeUp() {
        System.out.println("Remote: volume up");
        device.setVolume(device.getVolume() + 10);
    }

    @Override
    public void channelDown() {
        System.out.println("Remote: channel down");
        device.setChannel(device.getChannel() - 1);
    }

    @Override
    public void channelUp() {
        System.out.println("Remote: channel up");
        device.setChannel(device.getChannel() + 1);
    }
}
```

⤵️ 📄 `remotes/AdvancedRemote.java`: Gelişmiş uzaktan kumanda

```java
package fatihes1.bridge.example.remotes;

import fatihes1.bridge.example.devices.Device;

public class AdvancedRemote extends BasicRemote {

    public AdvancedRemote(Device device) {
        super.device = device;
    }

    public void mute() {
        System.out.println("Remote: mute");
        device.setVolume(0);
    }
}
```

⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.bridge.example;

import fatihes1.bridge.example.devices.Device;
import fatihes1.bridge.example.devices.Radio;
import fatihes1.bridge.example.devices.Tv;
import fatihes1.bridge.example.remotes.AdvancedRemote;
import fatihes1.bridge.example.remotes.BasicRemote;

public class Demo {
    public static void main(String[] args) {
        testDevice(new Tv());
        testDevice(new Radio());
    }

    public static void testDevice(Device device) {
        System.out.println("Tests with basic remote.");
        BasicRemote basicRemote = new BasicRemote(device);
        basicRemote.power();
        device.printStatus();

        System.out.println("Tests with advanced remote.");
        AdvancedRemote advancedRemote = new AdvancedRemote(device);
        advancedRemote.power();
        advancedRemote.mute();
        device.printStatus();
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemoSingleThread.txt`: Çalıştırma Sonucu

```
Tests with basic remote.
Remote: power toggle
------------------------------------
| I'm TV set.
| I'm enabled
| Current volume is 30%
| Current channel is 1
------------------------------------

Tests with advanced remote.
Remote: power toggle
Remote: mute
------------------------------------
| I'm TV set.
| I'm disabled
| Current volume is 0%
| Current channel is 1
------------------------------------

Tests with basic remote.
Remote: power toggle
------------------------------------
| I'm radio.
| I'm enabled
| Current volume is 30%
| Current channel is 1
------------------------------------

Tests with advanced remote.
Remote: power toggle
Remote: mute
------------------------------------
| I'm radio.
| I'm disabled
| Current volume is 0%
| Current channel is 1
------------------------------------
```




