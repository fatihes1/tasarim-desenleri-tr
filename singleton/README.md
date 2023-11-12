#  Tekil (Singleton) Tasarım Deseni


##  💬 Amaç

Singleton, bir sınıfın yalnızca bir örneğe sahip olmasını sağlarken bu örneğe küresel bir erişim noktası sağlamanıza olanak tanıyan yaratıcı (creational) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/singleton/singleton-2x.png)


##  🙁 Problem

Singleton modeli, Tek Sorumluluk İlkesini (Single Responsibility Principle) ihlal ederek iki sorunu aynı anda çözer:

1. **Bir sınıfın yalnızca tek bir örneği olmasını sağlamak.** Bir sınıfın kaç örneği olduğunu kontrol etmek neden istenir ki? Bunun en yaygın nedeni, paylaşılan kaynağa erişimi kontrol etmektir; örneğin, bir veritabanı veya bir dosya bu kaynak olabilir.
Nasıl çalıştığına gelirsek; bir nesne oluşturduğunuzu hayal edin, ancak bir süre sonra yeni bir tane oluşturmak istediğinize karar verdiniz. Yeni bir nesne oluşturmak yerine zaten oluşturduğunuz nesneyi kullanabilirsiniz.
Bu davranışın, tasarım gereği bir yapıcı (constructor) çağrısının her zaman yeni bir nesneyi döndürmesi gerekir. Bu yüzden bu davranışın normal bir yapıcı (constructor) ile uygulanamayacağını unutmayın.


![](https://refactoring.guru/images/patterns/content/singleton/singleton-comic-1-en-2x.png)

*İstemci her zaman aynı nesneyle çalıştırdığının farkına bile varmayabilir.*

2. **Oluşturulan örneğe global erişim noktası sağlama.** Aşina olabileceğiniz gibi, bazı temel nesneleri saklamak için global değişkenler kullanılabilir. Bunlar çok kullanışlı olsa da, aynı zamanda çok güvensizdir çünkü herhangi bir kod, bu değişkenlerin içeriğini üzerine yazabilir ve uygulamayı çökertebilir.
Singleton deseni, bir nesneye programın herhangi bir yerinden erişmenizi sağlar. Ancak, bu örneğin diğer kodlar tarafından üzerine yazılmasını yani değiştirilmesini engeller.
Bu sorunu çözen kodun programınızın her tarafına saçılmasını istemezsiniz. Özellikle geri kalan kodunuzun zaten ona bağlı olduğu bir sınıf içinde olması çok daha iyidir.

Günümüzde Singleton modeli o kadar popüler hale geldi ki, listelenen sorunlardan sadece birini çözse bile insanlar bir şeye singleton diyebilir.

##  😊 Çözüm

Singleton tasarım deseninin tüm uygulamalarında şu iki adım ortaktır:

- Diğer nesnelerin Singleton sınıfıyla birlikte `new` operatörünü kullanmasını önlemek, için varsayılan kurucuyu (constructor) özel yani `private` olarak tanımlayın.
- Yapıcı görevi gören statik bir oluşturma (constructor) yöntemi oluşturun. Bu yöntemin özünde private kurucuyu bir nesne yaratması için çağırır ve onu statik bir alana kaydeder. Bu yönteme yapılan çağrıların tümü, önbelleğe alınmış nesneyi döndürür.

Kodunuzun Singleton sınıfına erişimi varsa Singleton'ın statik yöntemini çağırabilir. Yani bu yöntem çağrıldığında her zaman aynı nesne döndürülür.

## 🚙 Gerçek Dünya Örneği

Hükümet, Singleton deseninin mükemmel bir örneğidir. Bir ülkede sadece bir resmi hükümet olabilir. Hükümeti oluşturan bireylerin kişisel kimliklerinden bağımsız olarak, "X Hükümeti" başlığı, sorumlu kişiler grubunu tanımlayan global bir erişim noktasıdır.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/singleton/structure-en-2x.png)

1. Singleton sınıfı, kendi sınıfının aynı örneğini döndüren statik yöntem `getInstance` barındırır.
Singleton'un yapıcısı (constructor) istemci kodundan gizlenmelidir. Singleton nesnesini almanın tek yolu `getInstance` yöntemini çağırmak olmalıdır.

  
##  💻 Sözde Kod (Pseudocode)

 Bu örnekte veritabanı bağlantı sınıfı Singleton görevi görüyor. Bu sınıfın ortak bir kurucusu yoktur, dolayısıyla nesnesini almanın tek yolu `getInstance` yöntemini çağırmaktır. Bu yöntem, ilk oluşturulan nesneyi önbelleğe alır ve sonraki tüm çağrılarda onu döndürür.

```java
// Database sınıfı, istemcilerin program boyunca aynı veritabanı bağlantısı örneğine erişmelerini sağlayan `getInstance` yöntemini tanımlar. 

class Database is 
// Singleton örneğini saklamak için alanın static olarak // tanımlanması gerekir. 
	private static field instance: Database
	
	// Singleton'ın yapıcı metodu her zaman `new`
	// operatörü ile doğrudan yapılandırma çağrılarını engellemek
	// için özel olmalıdır.
	private constructor Database() is
	    // Gerçek veritabanı sunucusuna bağlantı dahil
	    // bazı başlatma kodları.
	    // ...

	// Singleton örneğine erişimi denetleyen statik yöntem.
	public static method getInstance() is
	    if (Database.instance == null) then
	        acquireThreadLock() and then
	            // Bu sırada beklerken başka bir
	            // thread'in örneği zaten başlatıp
	            // başlatmadığını kontrol edin.
	            if (Database.instance == null) then
	                Database.instance = new Database()
	    return Database.instance

	// Son olarak, herhangi bir singleton, örneği üzerinde
	// çalıştırılabilen iş mantığını tanımlamalıdır.
	public method query(sql) is
	    // Örneğin, bir uygulamanın tüm veritabanı sorguları
	    // bu yöntem üzerinden geçer. Bu nedenle, buraya
	    // sınırlama veya önbellekleme mantığı yerleştirebilirsiniz.
	    // ...


class Application is 
	method main() is 
		Database foo = Database.getInstance() 			
		foo.query("SELECT ...") 
		// ... 
		Database bar = Database.getInstance()
		bar.query("SELECT ...") 
		// `bar` değişkeni, `foo` değişkeni ile aynı nesneyi içerecektir.
```


##  💡Uygulanabilirlik

**🐞 Programınızdaki bir sınıfın tüm istemciler için yalnızca tek bir örneğe sahip olması gerektiğinde Singleton modelini kullanın; örneğin, programın farklı bölümleri tarafından paylaşılan tek bir veritabanı nesnesi.**

⚡️ Singleton modeli, private oluşturma yöntemi dışında, bir sınıfın nesnelerini oluşturmanın diğer tüm yollarını devre dışı bırakır. Bu yöntem ya yeni bir nesne oluşturur ya da önceden oluşturulmuşsa mevcut bir nesneyi döndürür.
  
----------------

**🐞 Global değişkenler üzerinde daha sıkı kontrole ihtiyaç duyduğunuzda Singleton modelini kullanın.**

⚡️ Global değişkenlerin aksine Singleton modeli, bir sınıfın yalnızca bir örneğinin olduğunu garanti eder. Singleton sınıfı dışında hiçbir şey önbelleğe alınan örneğin yerini alamaz.
Bu sınırlamayı istediğiniz zaman ayarlayabileceğinizi ve istediğiniz sayıda Singleton örneğinin oluşturulmasına izin verebileceğinizi unutmayın. Değiştirilmesi gereken tek kod parçası `getInstance` yönteminin gövdesidir.



##  📝 Nasıl Uygulanır?

1. Singleton örneğini depolamak için sınıfa özel bir statik değişken tanımlayın.

2. Singleton örneğini almak için `public static` bir oluşturma yöntemi oluşturun.

3. Statik yöntemin içine "tembel başlatma" yani “lazy initialization” uygulayın. İlk çağrısında yeni bir nesne oluşturmalı ve onu statik alana koymalıdır. Yöntem, sonraki tüm çağrılarda her zaman bu önceden oluşturulmuş örneği döndürmelidir.

4. Sınıfın yapıcısını `private` yapın. Sınıfın statik yöntemi yine de yapıcıyı çağırabilecektir ancak diğer nesneleri çağıramayacaktır.

5. İstemci kodunun üzerinden geçin ve singleton yapıcısına yapılan tüm doğrudan çağrıları, statik oluşturma yöntemine yapılan çağrılarla değiştirin.
  

##  ⚖️ Artıları ve Eksileri

✅ Bir sınıfın yalnızca tek bir örneğinin olduğundan emin olabilirsiniz.

✅ Bu örneğe küresel yani global bir erişim noktası kazanırsınız.

✅ Karmaşık nesneleri daha rahat üretebilirsiniz.

✅ Singleton nesnesi yalnızca ilk kez istendiğinde initialize olur yani oluşturulur.
  
❌ Döngüsel referansları olan karmaşık nesnelerin klonlanması çok zor olabilir.

❌ Tek Sorumluluk İlkesini (Single Responsibility Principle) ihlal ediyor. Desen aynı anda iki sorunu çözüyor.

❌ Singleton modeli, kötü tasarımı maskeleyebilir. Örneğin; programın bileşenlerinin birbirleri hakkında çok fazla şey bilmesi durumu gibi.

❌ Desen, çok iş parçacıklı bir ortamda özel bir işlem gerektirir, bundan dolayı birden çok iş parçacığı birkaç kez tekil bir nesne oluşturmaz.

❌ Singleton'un istemci kodunun birim testini yapmak zor olabilir çünkü birçok test çerçevesi sahte nesneler üretirken kalıtımı kullanır. Singleton sınıfının yapıcısı `private` olduğundan ve çoğu dilde statik yöntemlerin geçersiz kılınması imkansız olduğundan, singleton için test yaparken yaratıcı bir yolunu düşünmeniz gerekecektir. Ya testleri yazmayın, ya da Singleton modelini kullanmayın.
  

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Facade sınıfı genellikle çoğu durumda tek bir facade nesnesine dönüştürülebilir çünkü çoğu durumda tek bir facade nesnesi yeterlidir.

- Flyweight, nesnelerin tüm paylaşılan durumlarını sadece bir flyweight nesnesine indirgemeyi başarabilirseniz Singleton'a benzeyebilir. Ancak bu desenler arasında iki temel fark vardır:
	- Tek bir Singleton örneği olmalıdır, oysa bir Flyweight sınıfı farklı içsel durumlara sahip çoklu örneklere sahip olabilir.
	- Singleton nesnesi değiştirilebilirken, Flyweight nesneleri değiştirilemezler.

- Abstract Factory, Builder ve Prototype gibi desenlerin hepsi Singleton olarak uygulanabilir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Pek çok geliştirici Singleton modelini bir antimodel olarak görüyor. Bu yüzden Java kodunda kullanımı azalıyor.

Buna rağmen Java çekirdek kütüphanelerinde oldukça fazla Singleton örneği var:

-   [`java.lang.Runtime#getRuntime()`](http://docs.oracle.com/javase/8/docs/api/java/lang/Runtime.html#getRuntime--)
-   [`java.awt.Desktop#getDesktop()`](http://docs.oracle.com/javase/8/docs/api/java/awt/Desktop.html#getDesktop--)
-   [`java.lang.System#getSecurityManager()`](http://docs.oracle.com/javase/8/docs/api/java/lang/System.html#getSecurityManager--)


**Tanımlama:** Singleton, aynı önbelleğe alınmış nesneyi döndüren statik bir oluşturma yöntemiyle tanınabilir.

 
####  Naïve Singleton (single-threaded) Örneği

Özensiz bir Singleton uygulamak oldukça kolaydır. Yapıcıyı (constructor) gizlemeniz ve statik bir oluşturma yöntemi uygulamanız yeterlidir.

⤵️ 📄 `Singleton.java`: Singleton

```java
package fatihes1.singleton.example.non_thread_safe;

public final class Singleton {
    private static Singleton instance;
    public String value;

    private Singleton(String value) {
        // The following code emulates slow initialization.
        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
        this.value = value;
    }

    public static Singleton getInstance(String value) {
        if (instance == null) {
            instance = new Singleton(value);
        }
        return instance;
    }
}
```

⤵️ 📄 `DemoSingleThread.java`: İstemci Kodu

```java
package fatihes1.singleton.example.non_thread_safe;

public class DemoSingleThread {
    public static void main(String[] args) {
        System.out.println("If you see the same value, then singleton was reused (yay!)" + "\n" +
                "If you see different values, then 2 singletons were created (booo!!)" + "\n\n" +
                "RESULT:" + "\n");
        Singleton singleton = Singleton.getInstance("FOO");
        Singleton anotherSingleton = Singleton.getInstance("BAR");
        System.out.println(singleton.value);
        System.out.println(anotherSingleton.value);
    }
}
```


**🖨️ Çıktılar**

⤵️ 📄 `OutputDemoSingleThread.txt`: Çalıştırma Sonucu

```
If you see the same value, then singleton was reused (yay!)
If you see different values, then 2 singletons were created (booo!!)

RESULT:

FOO
FOO
```




