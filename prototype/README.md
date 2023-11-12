#  Prototip (Prototype) Tasarım Deseni


##  💬 Amaç

Prototip, kodunuzu sınıflarına bağımlı hale getirmeden mevcut nesneleri kopyalamanıza olanak tanıyan, yaratıcı (creational) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/prototype/prototype-2x.png)

  

##  🙁 Problem

  
Bir nesnenin tam bir kopyasını oluşturmak istiyorsanız, öncelikle aynı sınıftan yeni bir nesne oluşturmalısınız. Daha sonra orijinal nesnenin tüm alanlarını dolaşmalı ve değerlerini yeni nesneye kopyalamalısınız.

Gayet kolay değil mi! Ancak burada bir hile var. Bu şekilde kopyalanamayan nesneler de vardır çünkü nesnenin bazı alanları gizli (private) olabilir ve nesnenin kendisi dışında görünmez olabilir.

![](https://refactoring.guru/images/patterns/content/prototype/prototype-comic-1-en-2x.png)

*Bir nesnenin "dışarıdan" kopyalanması her zaman mümkün değildir.*

 Doğrudan yaklaşımla ilgili başka bir sorun daha var. Bir kopya oluşturmak için nesnenin sınıfını bilmelisiniz, bu da kodunuzu o sınıfa bağımlı hale getirir. Eğer bu ek bağımlılık sizi korkutmuyorsa, başka bir sorun daha var. Bazen (örneğin bir yöntemdeki bir parametre bazı arabirimleri (interface) izleyen nesneleri kabul ettiğinde,) yalnızca nesnenin extend edildiği arabirimi (interface) bilirsiniz, ancak sınıfını bilmezsiniz.

##  😊 Çözüm

Prototip deseni, klonlama sürecini klonlanan gerçek nesnelere devreder. Desen, klonlamayı destekleyen tüm nesneler için ortak bir arayüzü (interface) tanımlar. Bu arayüz, bir nesneyi klonlamak için kodunuzu o nesnenin sınıfına bağlamadan kullanmanızı sağlar. Genellikle böyle bir arayüz yalnızca tek bir klonlama yöntemi (`clone`) içerir.

Klonlama yönteminin uygulanması, tüm sınıflarda çok benzerdir. Yöntem, mevcut sınıfın bir nesnesini oluşturur ve eski nesnenin tüm alan değerlerini yeni nesneye aktarır. Hatta çoğu programlama dili, nesnelerin aynı sınıfa ait diğer nesnelerin private alanlarına erişmesine izin verdiği için private alanları kopyalayabilirsiniz.

Klonlamayı destekleyen bir nesneye prototip denir. Nesnelerinizin onlarca alanı ve yüzlerce olası yapılandırması varsa, bunları alt sınıflandırmak yerine klonlamak, alternatif bir çözüm olabilir.


![](https://refactoring.guru/images/patterns/content/prototype/prototype-comic-2-en-2x.png)

*İnşaatçı (Builder) modeli, karmaşık nesneleri adım adım oluşturmanıza olanak tanır. Builder, ürün oluşturulurken diğer nesnelerin ürüne erişmesine izin vermez.*

Bu desen, nesne oluşturmayı bir dizi adıma (duvarları inşa etme (`buildWalls`), kapıyı inşa etme (`buildDoor`) vb.) ayırır ve organize eder. Bir nesne oluşturmak için bu adımlardan bazılarını bir oluşturucu (builder) nesne üzerinde çalıştırırsınız. Önemli olan şudur ki tüm adımları çağırmak zorunda değilsiniz. Bir nesnenin belirli bir yapılandırmasını üretmek için sadece gereken adımları çağırabilirsiniz.

Nesnenin çeşitli temsillerini oluşturmanız gerektiğinde, inşaat adımlarının bazıları farklı bir uygulama gerektirebilir. Örneğin, bir kulübenin duvarları ahşaptan oluşabilir, ancak kale duvarları taştan inşa edilmelidir.

Bu durumda, aynı inşaat adımlarını uygulayan ancak farklı bir şekilde uygulayan bir dizi farklı oluşturucu (builder) sınıfı oluşturabilirsiniz. Ardından bu oluşturuculara inşaat sürecinde (yani inşaat adımlarına çağrıların sıralı bir kümesi) farklı türde nesneler üretmek için kullanabilirsiniz.

![](https://refactoring.guru/images/patterns/content/builder/builder-comic-1-en-2x.png)

*Önceden oluşturulmuş prototipler alt sınıflandırmaya (subclassing) alternatif olabilir.*

Örneğin, her şeyi ahşap ve camdan inşa eden bir oluşturucu (builder) hayal edin, her şeyi taş ve demirden inşa eden ikincisi ve altın ve elmas kullanan üçüncü bir oluşturucu (builder). Aynı adımları çağırsanız da, ilk oluşturucudan normal bir ev, ikincisinden küçük bir kale ve üçüncüsünden ise bir saray oluşturduğunu görürsünüz. Ancak, bu yalnızca inşaat adımlarını çağıran istemci kodunun ortak bir arayüz (interface) kullanarak yapımcılarla etkileşimde bulunabilmesi durumunda bu şekilde olur.

Nasıl çalıştığını merak ediyorsanız, şu şekilde: çeşitli şekillerde yapılandırılmış bir nesne kümesi oluşturursunuz. Yapılandırdığınız gibi bir nesneye ihtiyacınız olduğunda, sıfırdan yeni bir nesne oluşturmak yerine bir prototip klonlarsınız.

## 🚙 Gerçek Dünya Örneği

Gerçek hayatta prototipler, bir ürünün seri üretimine başlamadan önce çeşitli testler yapmak için kullanılır. Ancak bu durumda prototipler herhangi bir gerçek üretimde rol almaz, pasif bir rol oynarlar.

![](https://refactoring.guru/images/patterns/content/prototype/prototype-comic-3-en-2x.png)

*Bir hücrenin bölünmesi.*

Endüstri prototipleri kendilerini gerçekten kopyalamadığı için, deseni çok daha yakın bir benzetme, mitotik hücre bölünme sürecidir (biyoloji dersinden aşina olabilirsiniz). Mitotik bölünmeden sonra aynı hücrelerin bir çifti oluşur. Orijinal hücre bir prototip gibi hareket eder ve kopyayı oluştururken aktif bir rol oynar.


##  ⚙️ Yapı

#### Temel Uygulama

![](https://refactoring.guru/images/patterns/diagrams/prototype/structure-2x.png)
  
1. **Prototip** arayüzü klonlama yöntemlerini bildirir. Çoğu durumda, tek bir klon (`clone`) yöntemi vardır.
2. **Concrete Prototype** sınıfı klonlama yöntemini uygular. Orijinal nesnenin verilerini klona kopyalamanın yanı sıra, bu yöntem bağlantılı nesneleri klonlamak, özyinelemeli bağımlılıkları çözmek vb. için de kullanılır. Ayrıca, ilgili klonlama işleminin bazı aşırı durumlarını da ele alabilir.
3. **İstemci (Client)**, prototip arayüzünü takip eden herhangi bir nesnenin bir kopyasını üretebilir.

#### Prototip Kayıt Uygulaması

![](https://refactoring.guru/images/patterns/diagrams/prototype/structure-prototype-cache-2x.png)

1. **Prototip Registry**, sık kullanılan prototiplere erişmenin kolay bir yolunu sağlar. Kopyalanmaya hazır bir dizi önceden oluşturulmuş nesneyi saklar. En basit prototip kayıt defteri, bir ad → prototip (`name -> prototype`) karma haritasıdır. Ancak, basit bir addan daha iyi arama ölçütlerine ihtiyacınız varsa, kayıt defterinin çok daha sağlam bir sürümünü oluşturabilirsiniz.

  
##  💻 Sözde Kod (Pseudocode)

Bu örnekte Prototip modeli, kodu sınıflarına bağlamadan geometrik nesnelerin tam kopyalarını oluşturmanıza olanak tanır.

![](https://refactoring.guru/images/patterns/diagrams/prototype/example-2x.png)

*Bir sınıf hiyerarşisine ait olan bir dizi nesneyi klonlama.*

Tüm şekil (`Shape`) sınıfları, bir klonlama yöntemi sağlayan aynı arayüzü izler. Bir alt sınıf, kendi alan değerlerini sonuç nesnesine kopyalamadan önce ebeveynin klonlama yöntemini çağırabilir.

```java
// Temel prototip. 
abstract class Shape is 
	field X: int 
	field Y: int 
	field color: string
	
	// Normal bir yapılandırıcı.
	constructor Shape() is
    // ...

	// Prototip yapılandırıcısı. Yeni bir nesne, mevcut nesnenin 		değerleriyle başlatılır.
	constructor Shape(source: Shape) is
	    this()
	    this.X = source.X
	    this.Y = source.Y
	    this.color = source.color

	// Klon işlemi, Shape alt sınıflarından birini döndürür.
	abstract method clone():Shape

// Somut prototip. Klonlama yöntemi mevcut sınıfın yapıcısını çağırarak yeni bir nesne oluşturur 
// ve mevcut nesneyi yapıcının bağımsız değişkeni olarak iletir. 
// Tüm gerçek kopyalamayı yapıcı içinde gerçekleştirmek, sonucun tutarlı kalmasına yardımcı olur: yapıcı, yeni nesne tamamen oluşturulana kadar bir sonuç döndürmeyecektir; 
// bu nedenle, hiçbir nesne, kısmen oluşturulmuş bir klonun başvurusuna sahip olamaz. 
class Rectangle extends Shape is 
	field width: int 
	field height: int

	constructor Rectangle(source: Rectangle) is
	    // Üst sınıf yapıcı çağrısı, üst sınıfta tanımlanan özel alanları kopyalamak için gereklidir.
	    super(source)
	    this.width = source.width
	    this.height = source.height

	method clone():Shape is
	    return new Rectangle(this)

class Circle extends Shape is 
	field radius: int
	
	constructor Circle(source: Circle) is
	    super(source)
	    this.radius = source.radius

	method clone():Shape is
	    return new Circle(this)

class Application is 
	field shapes: array of Shape

	constructor Application() is
	    Circle circle = new Circle()
	    circle.X = 10
	    circle.Y = 10
	    circle.radius = 20
	    shapes.add(circle)

	    Circle anotherCircle = circle.clone()
	    shapes.add(anotherCircle)
	    // 'anotherCircle' değişkeni, 'circle' nesnesinin tam bir kopyasını içerir.

	    Rectangle rectangle = new Rectangle()
	    rectangle.width = 10
	    rectangle.height = 20
	    shapes.add(rectangle)

	method businessLogic() is
	    // Prototip, türü hakkında hiçbir şey bilmeden bir nesnenin bir kopyasını üretmenizi sağlar.
	    Array shapesCopy = new Array of Shapes.

	    // Örneğin, shapes dizisindeki tam olarak hangi öğeleri bilmiyoruz. Tek bildiğimiz, hepsinin şekiller olduğu. Ancak çok biçimlilik sayesinde, bir şekil üzerinde 'clone' yöntemini çağırdığımızda program gerçek sınıfını kontrol eder
	    // ve o sınıfta tanımlanan uygun klon yöntemini çalıştırır. Bu nedenle basit Shape nesneleri yerine uygun kopyalar elde ederiz.
	    foreach (s in shapes) do
        shapesCopy.add(s.clone())

	    // 'shapesCopy' dizisi, 'shape' dizisinin çocuklarının tam kopyalarını içerir.

```


##  💡Uygulanabilirlik

**🐞 "Kodunuz kopyalamanız gereken nesne sınıflarına bağlı olmaması gerektiğinde Prototip desenini kullanın.**

⚡️ Bu durum, kodunuzun 3. taraf kod tarafından bir arayüz aracılığıyla size iletilen nesnelerle çalıştığında sıkça karşılaşır. Bu nesnelerin somut sınıfları bilinmez ve isteseniz bile bunlara bağlı olamazsınız.
Prototip deseni, istemci koduna klonlamayı destekleyen tüm nesnelerle çalışmak için genel bir arayüz sunar. Bu arayüz, istemci kodunun klonladığı nesnelerin somut sınıflarından bağımsız olmasını sağlar.
  
----------------

**🐞 Yalnızca ilgili nesneleri başlatma (initialize) biçimleri farklılık gösteren alt sınıfların sayısını azaltmak istediğinizde bu modeli kullanın.**

⚡️ Taslağınızı kullanmadan önce yapılandırılması gereken karmaşık bir sınıfınız olduğunu varsayalım. Bu sınıfı yapılandırmak için kullanılan birkaç yaygın yol vardır ve bu kod uygulamanızın her yerine dağılmış durumda. Tekrarlamanın azaltılması için birkaç alt sınıf oluşturursunuz ve her ortak yapılandırma kodunu onların yapıcılarına koyarsınız. Tekrarlama sorununu çözdünüz, ancak şimdi sahte alt sınıflarınız var.
Prototip deseni, çeşitli şekillerde yapılandırılmış önceden oluşturulmuş nesneler kümesini kullanmanıza olanak tanır. Bir yapılandırmayla eşleşen bir alt sınıfı örneklemek yerine, istemci sadece uygun bir prototip arayabilir ve onu çoğaltabilir.




##  📝 Nasıl Uygulanır?

1. Prototip arayüzünü oluşturun ve içinde klonlama (`clone`) yöntemini bildirin. Eğer bir sınıf hiyerarşiniz varsa, zaten var olan bir sınıf hiyerarşisinin tüm sınıflarına yöntemi ekleyin, 

2. Bir prototip sınıfı, bu sınıfın bir nesnesini bir argüman olarak kabul eden alternatif bir yapıcıyı tanımlamalıdır. Yapıcı, bu nesneden yeni oluşturulan örneğe bu sınıfta tanımlanan tüm alanların değerlerini kopyalamalıdır. Bir alt sınıfı değiştiriyorsanız, üst sınıfın kendi özel alanlarını klonlamasına izin vermek için üst sınıfın yapıcıyı çağırmanız gerekir.
Programlama diliniz yöntem aşırı yüklemeyi desteklemiyorsa, ayrı bir "prototip" yapıcı oluşturamazsınız demektir. Bu nedenle nesnenin verilerini yeni oluşturulan klon içine kopyalama (`clone`) işlemi, klon yöntemi içinde gerçekleştirilmelidir. Yine de bu kodun düzenli bir yapıcı içinde olması daha güvenlidir çünkü sonuç nesnesi, `new` operatörünü çağırdıktan hemen sonra tamamen yapılandırılmış olarak döner.

3. Klonlama yöntemi genellikle sadece bir satırdan oluşur: yapıcının prototip sürümünü kullanarak `new` ile yeni bir operatörünü çalıştırmak. Unutmayın ki her sınıf, klonlama yöntemini açıkça geçersiz kılmalı ve kendi sınıf adını ve `new` operatörünü kullanmalıdır. Aksi takdirde, klonlama yöntemi bir üst sınıfın bir nesnesini üretebilir.

4. İsteğe bağlı olarak, sık kullanılan prototiplerin bir kataloğunu saklamak için merkezi bir prototip kaydı oluşturun.
Kaydı yeni bir fabrika sınıfı olarak uygulayabilir veya temel prototip sınıfının içine ekleyebilirsiniz ve prototip almak için bir statik yöntem ekleyebilirsiniz. Bu yöntem, istemci kodunun yönteme ilettiği arama kriterlerine dayalı olarak bir prototip aramalıdır. Kriterler ya basit bir dize etiketi veya karmaşık bir dizi arama parametresi olabilir. Uygun prototip bulunduktan sonra, kayıt onu çoğaltmalı ve kopyayı istemciye döndürmelidir.
Son olarak, alt sınıfların yapıcılarına doğrudan çağrıları, prototip kaydının fabrika yöntemiyle çağrılara değiştirin.
  

##  ⚖️ Artıları ve Eksileri

✅ Nesneleri sınıflarına bağlı kalmadan klonlayabilirsiniz.

✅ Önceden oluşturulmuş prototipleri klonlamak adına tekrarlanan başlatma kodlarından kurtulabilirsiniz.

✅ Karmaşık nesneleri daha rahat üretebilirsiniz.

✅ Karmaşık nesnelere yönelik yapılandırma ön ayarlarıyla uğraşırken kalıtıma alternatif elde edersiniz.
  
❌ Döngüsel referansları olan karmaşık nesnelerin klonlanması çok zor olabilir.
  

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Abstract Factory, Factory Method, Prototype ve Builder gibi farklı tasarım desenleri, projelerinizin ihtiyaçlarına ve karmaşıklığına göre kullanılabilecek alternatifler sunar.

- Abstract Factory sınıfları genellikle bir dizi Factory Method üzerine kurulur, ancak bu sınıflardaki yöntemleri oluşturmak için Prototype'i de kullanabilirsiniz.

- Prototype, Command nesnelerini geçmişe kaydetmeniz gerektiğinde yardımcı olabilir.

- Kompozit ve Decorator desenlerini yoğun bir şekilde kullanan tasarımlarda, Prototype kullanmaktan fayda sağlayabilir. Bu deseni uygulamak, karmaşık yapıları sıfırdan yeniden oluşturmak yerine klonlamanıza olanak tanır.

- Prototype, miras alınmaya dayalı olmadığı için miras almanın getirdiği bazı sorunları içermez. Öte yandan, Prototype, klonlanan nesnenin karmaşık bir başlatılmasını gerektirir. Factory Method ise miras alınmaya dayalıdır, ancak başlatma adımını gerektirmez.

- Bazen Prototype, Memento'ya daha basit bir alternatif olabilir. Bu, geçmişte saklamak istediğiniz nesnenin durumu oldukça basitse ve harici kaynaklara bağlantıları yoksa veya bağlantıları yeniden kurmak kolaysa çalışır.

- Abstract Factory, Builder ve Prototype gibi desenlerin hepsi Singleton olarak uygulanabilir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Prototip modeli, Java'da, `Cloneable` bir arayüzle kutudan çıktığı gibi mevcuttur.

Klonlanabilir hale gelmek için herhangi bir sınıf bu arayüzü uygulayabilir.

- [`java.lang.Object#clone()`](http://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#clone--) (class should implement the [`java.lang.Cloneable`](http://docs.oracle.com/javase/8/docs/api/java/lang/Cloneable.html) interface)



**Tanımlama:** Prototip, klonlama (`clone`) veya kopyalama (`copy`) yöntemleri vb. ile kolayca tanınabilir.

 
####  Grafik Şekilleri Kopyalama

Standart `Cloneable` arayüz olmadan Prototipin nasıl uygulanabileceğine bir göz atalım.

**📁 shapes**

⤵️ 📄 `shapes/Shape.java`: Ortak shape arabirimi/arayüzü 

```java
package fatihes1.prototype.example.shapes;

import java.util.Objects;

public abstract class Shape {
    public int x;
    public int y;
    public String color;

    public Shape() {
    }

    public Shape(Shape target) {
        if (target != null) {
            this.x = target.x;
            this.y = target.y;
            this.color = target.color;
        }
    }

    public abstract Shape clone();

    @Override
    public boolean equals(Object object2) {
        if (!(object2 instanceof Shape)) return false;
        Shape shape2 = (Shape) object2;
        return shape2.x == x && shape2.y == y && Objects.equals(shape2.color, color);
    }
}
```

⤵️ 📄 `shapes/Rectangle.java`: Diğer bir şekil

```java
package fatihes1.prototype.example.shapes;

public class Rectangle extends Shape {
    public int width;
    public int height;

    public Rectangle() {
    }

    public Rectangle(Rectangle target) {
        super(target);
        if (target != null) {
            this.width = target.width;
            this.height = target.height;
        }
    }

    @Override
    public Shape clone() {
        return new Rectangle(this);
    }

    @Override
    public boolean equals(Object object2) {
        if (!(object2 instanceof Rectangle) || !super.equals(object2)) return false;
        Rectangle shape2 = (Rectangle) object2;
        return shape2.width == width && shape2.height == height;
    }
}
```

⤵️ 📄 `Demo.java` Klonlama örneği

```java
package fatihes1.prototype.example;

import fatihes1.prototype.example.shapes.Circle;
import fatihes1.prototype.example.shapes.Rectangle;
import fatihes1.prototype.example.shapes.Shape;

import java.util.ArrayList;
import java.util.List;

public class Demo {
    public static void main(String[] args) {
        List<Shape> shapes = new ArrayList<>();
        List<Shape> shapesCopy = new ArrayList<>();

        Circle circle = new Circle();
        circle.x = 10;
        circle.y = 20;
        circle.radius = 15;
        circle.color = "red";
        shapes.add(circle);

        Circle anotherCircle = (Circle) circle.clone();
        shapes.add(anotherCircle);

        Rectangle rectangle = new Rectangle();
        rectangle.width = 10;
        rectangle.height = 20;
        rectangle.color = "blue";
        shapes.add(rectangle);

        cloneAndCompare(shapes, shapesCopy);
    }

    private static void cloneAndCompare(List<Shape> shapes, List<Shape> shapesCopy) {
        for (Shape shape : shapes) {
            shapesCopy.add(shape.clone());
        }

        for (int i = 0; i < shapes.size(); i++) {
            if (shapes.get(i) != shapesCopy.get(i)) {
                System.out.println(i + ": Shapes are different objects (yay!)");
                if (shapes.get(i).equals(shapesCopy.get(i))) {
                    System.out.println(i + ": And they are identical (yay!)");
                } else {
                    System.out.println(i + ": But they are not identical (booo!)");
                }
            } else {
                System.out.println(i + ": Shape objects are the same (booo!)");
            }
        }
    }
}
```


**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu

```
0: Shapes are different objects (yay!)
0: And they are identical (yay!)
1: Shapes are different objects (yay!)
1: And they are identical (yay!)
2: Shapes are different objects (yay!)
2: And they are identical (yay!)
```

#### Prototip kaydı

Merkezi bir prototip kaydı (veya fabrika) uygulayabilirsiniz. Bu, önceden tanımlanmış prototip nesnelerin bir kümesini içeren bir fabrikayı içerebilir. Bu şekilde, fabrikadan nesneleri adını veya diğer parametreleri geçirerek alabilirsiniz. Fabrika, uygun bir prototip arar, onu klonlar ve size bir kopya döndürür.

**📁 cache**

⤵️ 📄 `cache/BundledShapeCache.java`: Prototip fabrikası

```java
package fatihes1.prototype.caching.cache;

import fatihes1.prototype.example.shapes.Circle;
import fatihes1.prototype.example.shapes.Rectangle;
import fatihes1.prototype.example.shapes.Shape;

import java.util.HashMap;
import java.util.Map;

public class BundledShapeCache {
    private Map<String, Shape> cache = new HashMap<>();

    public BundledShapeCache() {
        Circle circle = new Circle();
        circle.x = 5;
        circle.y = 7;
        circle.radius = 45;
        circle.color = "Green";

        Rectangle rectangle = new Rectangle();
        rectangle.x = 6;
        rectangle.y = 9;
        rectangle.width = 8;
        rectangle.height = 10;
        rectangle.color = "Blue";

        cache.put("Big green circle", circle);
        cache.put("Medium blue rectangle", rectangle);
    }

    public Shape put(String key, Shape shape) {
        cache.put(key, shape);
        return shape;
    }

    public Shape get(String key) {
        return cache.get(key).clone();
    }
}
```

⤵️ 📄 `Demo.java`: Klonlama örneği

```java
package fatihes1.prototype.caching;

import fatihes1.prototype.caching.cache.BundledShapeCache;
import fatihes1.prototype.example.shapes.Shape;

public class Demo {
    public static void main(String[] args) {
        BundledShapeCache cache = new BundledShapeCache();

        Shape shape1 = cache.get("Big green circle");
        Shape shape2 = cache.get("Medium blue rectangle");
        Shape shape3 = cache.get("Medium blue rectangle");

        if (shape1 != shape2 && !shape1.equals(shape2)) {
            System.out.println("Big green circle != Medium blue rectangle (yay!)");
        } else {
            System.out.println("Big green circle == Medium blue rectangle (booo!)");
        }

        if (shape2 != shape3) {
            System.out.println("Medium blue rectangles are two different objects (yay!)");
            if (shape2.equals(shape3)) {
                System.out.println("And they are identical (yay!)");
            } else {
                System.out.println("But they are not identical (booo!)");
            }
        } else {
            System.out.println("Rectangle objects are the same (booo!)");
        }
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalıştırma Sonucu

```
Big green circle != Medium blue rectangle (yay!)
Medium blue rectangles are two different objects (yay!)
And they are identical (yay!)
```




