#  Ziyaretçi (Visitor) Tasarım Deseni
 

##  💬 Amaç

Ziyaretçi, algoritmaları üzerinde çalıştıkları nesnelerden ayırmanıza olanak tanıyan davranışsal (behavioral) bir tasarım modelidir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/visitor/visitor-2x.png)

<div align="center">


##  🙁 Problem

Ekibinizin devasa bir grafik (graph) halinde yapılandırılmış coğrafi bilgilerle çalışan bir uygulama geliştirdiğini hayal edin. Grafiğin her düğümü (node), şehir gibi karmaşık bir varlığın yanı sıra endüstriler, gezi alanları gibi daha ayrıntılı şeyleri de temsil edebilir. Temsil ettikleri nesneler arasında bir yol varsa, düğümler diğerleriyle bağlanır. Temelde her düğüm türü kendi sınıfı tarafından temsil edilirken, her belirli düğüm bir nesnedir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/visitor/problem1-2x.png)

*Grafiği XML'e aktarma.*

</div>

Bir noktada grafiği XML formatına aktarmanız gerekebilir. İlk başta iş oldukça basit görünür. Her düğüm sınıfına bir dışa aktarma yöntemi eklemeyi ve ardından grafiğin her bir düğümünün üzerinden geçmek için özyinelemeden yararlanarak dışa aktarma yöntemini yürütmeyi planlayabilirsiniz. Çözüm basit ve zarifti: Polimorfizm sayesinde, dışa aktarma yöntemini çağıran kodu somut düğüm sınıflarına bağlayamazsınız.

Maalesef sistem mimarı mevcut düğüm sınıflarını değiştirmenize izin vermedi. Kodun halihazırda canlıda (production) olduğunu ve değişikliklerinizdeki olası bir hata nedeniyle kodu bozma riskini almak istemediğini söyledi. Bu durumda ne yapacaksınız?

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/visitor/problem2-en-2x.png)

*XML dışa aktarma yönteminin (export) tüm düğüm sınıflarına eklenmesi gerekir. Ancak bu şekilde, değişiklikle birlikte herhangi bir hatanın da gözden kaçması durumunda tüm uygulamanın bozulması riskini taşır.*

</div>

Ayrıca sistem mimarı XML dışa aktarma kodunun düğüm sınıfları içinde olmasının mantıklı olup olmadığını sorgulayabilir. Bu sınıfların birincil işi coğrafi verilerle çalışmaktır. XML dışa aktarma davranışı burada yanlış bir konumlandırma olabilir.

Reddetmesinin başka bir nedeni daha vardır: Bu özellik uygulandıktan sonra, pazarlama departmanından birinin sizden farklı bir formatta dışa aktarma olanağı sağlamanızı veya başka tuhaf şeyler talep etmesi oldukça muhtemeldir. Bu sizi yine o değerli ve çabuk bozulabilecek sınıfları değiştirmeye zorlayacaktır.

##  😊 Çözüm

Ziyaretçi modeli, yeni davranışı mevcut sınıflara entegre etmeye çalışmak yerine, ziyaretçi (visitor) adı verilen ayrı bir sınıfa yerleştirmenizi önerir. Davranışı gerçekleştirmesi gereken orijinal nesne artık ziyaretçinin yöntemlerinden birine argüman olarak aktarılır ve yöntemin nesne içinde bulunan tüm gerekli verilere erişimi sağlanır.

Peki ya bu davranış farklı sınıflardaki nesneler üzerinde yürütülebiliyorsa? Örneğin, XML dışa aktarımındaki durumumuzda, gerçek uygulama muhtemelen çeşitli düğüm sınıflarında biraz farklı olacaktır. Böylece, ziyaretçi sınıfı bir tane değil, her biri farklı türde argümanlar alabilen bir dizi yöntem tanımlayabilir, örneğin:

```java
class ExportVisitor implements Visitor is
    method doForCity(City c) { ... }
    method doForIndustry(Industry f) { ... }
    method doForSightSeeing(SightSeeing ss) { ... }
    // ...
```

Peki özellikle grafiğin tamamıyla ilgilenirken bu yöntemleri tam olarak nasıl adlandırırız? Bu yöntemlerin farklı imzaları olduğundan polimorfizmi kullanamayız. Belirli bir nesneyi işleyebilecek uygun bir ziyaretçi yöntemi seçmek için onun sınıfını kontrol etmemiz gerekir. Bu bir oldukça kafa karıştırıcı duruyor değil mi ?

```java
foreach (Node node in graph)
    if (node instanceof City)
        exportVisitor.doForCity((City) node)
    if (node instanceof Industry)
        exportVisitor.doForIndustry((Industry) node)
    // ...
}
```

Neden yöntem aşırı yüklemeyi kullanmıyoruz (method overloading) diye sorabilirsiniz. Bu, farklı parametre kümelerini destekleseler bile tüm yöntemlere aynı adı verdiğiniz zamanda kullanılan bir yöntemdir. Ne yazık ki, programlama dilimizin onu desteklediğini varsaysak bile (Java ve C#'ın yaptığı gibi), bunun bize bir faydası olmayacaktır. Bir düğüm nesnesinin tam sınıfı önceden bilinmediğinden, aşırı yükleme (overloading) mekanizması yürütülecek doğru yöntemi belirleyemez. Temel `Node` sınıfının bir nesnesini alan yöntem varsayılan olarak kullanılır.

Şanslıyız ki Ziyaretçi modeli bu sorunu giderir. Nasıl mı? **Double Dispatch** adlı bir teknik kullanarak. Bu teknik, hantal koşullar olmadan bir nesne üzerinde doğru yöntemin yürütülmesine yardımcı olur. İstemcinin çağrılacak yöntemin uygun bir sürümünü seçmesine izin vermek yerine, bu seçimi ziyaretçiye argüman olarak ilettiğimiz nesnelere devretsek nasıl olur? Nesneler kendi sınıflarını bildikleri için ziyaretçiye uygun yöntemi daha az sıkıntıyla seçebilecekler. Bir ziyaretçiyi kabul eder ve ona hangi ziyaret yönteminin uygulanması gerektiğini söyler.

```java
// Client code
foreach (Node node in graph)
    node.accept(exportVisitor)

// City
class City is
    method accept(Visitor v) is
        v.doForCity(this)
    // ...

// Industry
class Industry is
    method accept(Visitor v) is
        v.doForIndustry(this)
    // ...
```

Ancak şöyle bir durum var. Sonuçta düğüm sınıflarını değiştirmek zorunda kaldık. Neyse ki, değişiklik çokta önemli değildir ve kodu bir kez daha değiştirmeden başka davranışlar eklememize olanak tanır.

Artık tüm ziyaretçiler için ortak bir arayüz çıkarırsak, mevcut tüm düğümler, uygulamaya tanıttığınız herhangi bir ziyaretçiyle çalışabilir. Kendinizi düğümlerle ilgili yeni bir davranış tanıtırken bulursanız tek yapmanız gereken yeni bir ziyaretçi sınıfı uygulamak yani implement etmektir.


## 🚙 Gerçek Dünya Örneği

<div align="center">

![](https://refactoring.guru/images/patterns/content/visitor/visitor-comic-1-2x.png)

*İyi bir sigorta acentesi her zaman çeşitli kuruluş türlerine farklı poliçeler sunmaya hazırdır.*

</div>

Yeni müşteriler kazanmaya hevesli deneyimli bir sigorta acentesini hayal edin. Mahalledeki her binayı gezebiliyor, karşılaştığı herkese sigorta satmaya çalışıyor. Binayı kullanan kuruluşun türüne bağlı olarak özel sigorta poliçeleri sunabilir:

- Eğer bu bir konut binasıysa sağlık sigortası satabilir.
- Eğer bankaysa hırsızlık sigortası satabilir.
- Kahvehane ise yangın ve su baskını sigortası satabilir.


##  ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/visitor/structure-en-2x.png)

</div>

1. **Ziyaretçi (Visitor)** arayüzü, bir nesne yapısının somut öğelerini argüman olarak alabilen bir dizi ziyaret yöntemini bildirir. Program aşırı yüklemeyi destekleyen bir dilde yazılmışsa bu yöntemler aynı adlara sahip olabilir ancak parametrelerinin türü farklı olmalıdır.

2. Her **Concrete Visitor** aynı davranışların farklı concrete eleman sınıflarına göre uyarlanmış çeşitli versiyonlarını uygular yani implement eder.

3. **Element** arayüzü ziyaretçileri “kabul etmek” için bir yöntem bildirir. Bu yöntemin ziyaretçi arayüzünün türüyle birlikte bildirilen bir parametresi olmalıdır.

4. Her **Concrete Element** kabul (accept) yöntemini uygulamalıdır. Bu metodun amacı, çağrıyı mevcut eleman sınıfına karşılık gelen uygun ziyaretçi metoduna yönlendirmektir. Bir temel öğe sınıfı bu yöntemi uygulasa bile, tüm alt sınıfların yine de kendi sınıflarında bu yöntemi geçersiz kılmaları ve ziyaretçi nesnesinde uygun yöntemi çağırmaları gerektiğini unutmayın.

5. **İstemci (Client)** genellikle bir koleksiyonu veya başka bir karmaşık nesneyi (örneğin, bir **Bileşik (Composite)** ağaç) temsil eder. Genellikle istemciler tüm somut öğe sınıflarının farkında değildir çünkü o koleksiyondaki nesnelerle soyut bir arayüz aracılığıyla çalışırlar.



##  💻 Sözde Kod (Pseudocode)

Bu örnekte Ziyaretçi modeli, geometrik şekillerin sınıf hiyerarşisine XML dışa aktarma desteği ekler.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/visitor/example-2x.png)

*Bir ziyaretçi nesnesi aracılığıyla çeşitli nesne türlerini XML biçimine aktarma.*

</div>

```java
// Element arayüzü, temel ziyaretçi arayüzünü bir bağımsız değişken olarak alan 'accept' metodunu bildirir.
interface Shape is
    method move(x, y)
    method draw()
    method accept(v: Visitor)

// Her somut eleman sınıfı, elemanın sınıfına karşılık gelen ziyaretçinin yöntemini çağıracak şekilde 'accept' metodunu uygulamalıdır.
class Dot implements Shape is
    // ...

    // Şu anki sınıf adına karşılık gelen 'visitDot' çağırdığımıza dikkat edin. Bu şekilde ziyaretçiye çalıştığı elemanın sınıfını bildiririz.
    method accept(v: Visitor) is
        v.visitDot(this)

class Circle implements Shape is
    // ...
    method accept(v: Visitor) is
        v.visitCircle(this)

class Rectangle implements Shape is
    // ...
    method accept(v: Visitor) is
        v.visitRectangle(this)

class CompoundShape implements Shape is
    // ...
    method accept(v: Visitor) is
        v.visitCompoundShape(this)

// Ziyaretçi arayüzü, eleman sınıflarına karşılık gelen ziyaret yöntemlerini bildirir. Bir ziyaret yönteminin imzası, ziyaretçinin işlediği elemanın sınıfını tanımasına olanak tanır.
interface Visitor is
    method visitDot(d: Dot)
    method visitCircle(c: Circle)
    method visitRectangle(r: Rectangle)
    method visitCompoundShape(cs: CompoundShape)

// Somut ziyaretçiler, tüm somut eleman sınıflarıyla çalışabilen aynı algoritmanın farklı sürümlerini uygular.
//
// Ziyaretçi desenini, bir Bileşik ağaç gibi karmaşık bir nesne yapısıyla kullanırken en büyük faydasını gözlemleyebilirsiniz. Bu durumda, ziyaretçinin yöntemlerini nesnenin farklı nesneleri üzerinde çalıştırırken algoritmanın ara durumu depolanmasına yardımcı olabilir.
class XMLExportVisitor implements Visitor is
    method visitDot(d: Dot) is
        // Noktanın ID'sini ve merkez koordinatlarını dışa aktar.

    method visitCircle(c: Circle) is
        // Dairenin ID'sini, merkez koordinatlarını ve yarıçapını dışa aktar.

    method visitRectangle(r: Rectangle) is
        // Dikdörtgenin ID'sini, sol-üst koordinatlarını, genişliğini ve yüksekliğini dışa aktar.

    method visitCompoundShape(cs: CompoundShape) is
        // Şeklin ID'sini ve çocuklarının ID listesini dışa aktar.

// İstemci kod, somut sınıflarını belirlemeden herhangi bir eleman kümesi üzerinde ziyaretçi işlemlerini çalıştırabilir. 'accept' işlemi, çağrıyı ziyaretçi nesnesindeki ilgili işleme yönlendirir.
class Application is
    field allShapes: array of Shapes

    method export() is
        exportVisitor = new XMLExportVisitor()

        foreach (shape in allShapes) do
            shape.accept(exportVisitor)

```


##  💡Uygulanabilirlik


**🐞 Karmaşık bir nesne yapısının (örneğin bir nesne ağacı) tüm öğeleri üzerinde bir işlem gerçekleştirmeniz gerektiğinde Ziyaretçiyi kullanın.**

⚡️ Ziyaretçi modeli, bir ziyaretçi nesnesinin aynı işlemin tüm hedef sınıflara karşılık gelen çeşitli varyantlarını uygulamasını sağlayarak, farklı sınıflara sahip bir dizi nesne üzerinde bir işlem yürütmenize olanak tanır.

----------------

**🐞 Yardımcı davranışların iş mantığını temizlemek için Ziyaretçiyi kullanın.**

⚡️ Desen, diğer tüm davranışları bir dizi ziyaretçi sınıfına çıkararak uygulamanızın birincil sınıflarını ana işlerine daha odaklı hale getirmenize olanak tanır.

----------------

**🐞 Bir davranışın sınıf hiyerarşisinin yalnızca bazı sınıflarında anlamlı olup diğerlerinde anlamlı olmadığı durumlarda bu modeli kullanın.**

⚡️ Bu davranışı ayrı bir ziyaretçi sınıfına çıkarabilir ve yalnızca ilgili sınıfların nesnelerini kabul eden ziyaret yöntemlerini uygulayabilir, gerisini boş bırakabilirsiniz.


##  📝 Nasıl Uygulanır?

1. Ziyaretçi arayüzünü, programda bulunan her somut öğe sınıfı için bir ziyaret (visiting) yöntemi seti ile tanımlayın.

2. Eleman arayüzünü tanımlayın. Mevcut bir öğe sınıfı hiyerarşisiyle çalışıyorsanız, soyut kabul (acceptance) yöntemini hiyerarşinin temel sınıfına ekleyin. Bu yöntem bir ziyaretçi nesnesini argüman olarak kabul etmelidir.

3. Kabul (acceptance) yöntemlerini tüm concrete element sınıflarında uygulayın. Bu yöntemler, çağrıyı, gelen ziyaretçi nesnesindeki mevcut öğenin sınıfıyla eşleşen bir ziyaret yöntemine yönlendirmelidir.

4. Öğe sınıfları yalnızca ziyaretçi arayüzü aracılığıyla ziyaretçilerle (visitor) çalışmalıdır. Ancak ziyaretçilerin, ziyaret yöntemlerinin parametre türleri olarak adlandırılan tüm concrete element sınıflarından haberdar olmaları gerekir.
Ziyaretçinin element sınıfının bazı özel üyelerine erişmesi gerekeceği bir durumla karşılaşabilirsiniz. Bu durumda, öğenin kapsüllenmesini ihlal ederek bu alanları veya yöntemleri herkese açık hale (public) getirebilir. Bununla beraber dilerseniz ziyaretçi sınıfını öğe sınıfına yerleştirebilirsiniz. İkincisi yalnızca iç içe geçmiş sınıfları destekleyen bir programlama diliyle çalışıyorsanız mümkündür.

5. Müşteri, ziyaretçi nesneleri oluşturmalı ve bunları kabul (acceptance) yöntemleriyle öğelere aktarmalıdır.

##  ⚖️ Artıları ve Eksileri

✅  Açık/Kapalı Prensibi (Open/Closed Principle): Bu sınıfları değiştirmeden, farklı sınıflardaki nesnelerle çalışabilecek yeni bir davranış tanıtabilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): Aynı davranışın birden çok versiyonunu aynı sınıfa taşıyabilirsiniz.

✅ Bir ziyaretçi nesnesi, çeşitli nesnelerle çalışırken bazı yararlı bilgiler toplayabilir. Nesne ağacı gibi bazı karmaşık nesne yapılarını geçmek ve ziyaretçiyi bu yapının her nesnesine uygulamak istediğinizde bu kullanışlı olabilir.

❌ Öğe hiyerarşisine bir sınıf eklendiğinde veya öğe hiyerarşisinden kaldırıldığında tüm ziyaretçileri güncellemeniz gerekir.

❌ Ziyaretçiler, üzerinde çalışmaları gereken öğelerin özel alanlarına ve yöntemlerine gerekli erişime sahip olmayabilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Ziyaretçi (Visitor)** tasarım desenini **Komut (Command)** modelinin güçlü bir sürümü olarak değerlendirebilirsiniz. Nesneleri, farklı sınıflardaki çeşitli nesneler üzerinde işlemler gerçekleştirebilir.

- Bir **Bileşik (Composite)** ağacın tamamı üzerinde bir işlem yürütmek için **Ziyaretçi (Visitor)** desenini kullanabilirsiniz.

- Karmaşık bir veri yapısında gezinmek ve hepsi farklı sınıflara sahip olsalar bile öğeleri üzerinde bazı işlemler yürütmek için **Ziyaretçi (Visitor)** desenini **Yineleyici (Iterator)** deseni ile birlikte kullanabilirsiniz.
  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Ziyaretçi karmaşıklığı ve dar uygulanabilirliği nedeniyle çok yaygın bir kalıp değildir.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`javax.lang.model.element.AnnotationValue`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/AnnotationValue.html)  ve  [`AnnotationValueVisitor`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/AnnotationValueVisitor.html)
-   [`javax.lang.model.element.Element`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/Element.html)  ve  [`ElementVisitor`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/ElementVisitor.html)
-   [`javax.lang.model.type.TypeMirror`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/type/TypeMirror.html)  ve  [`TypeVisitor`](http://docs.oracle.com/javase/8/docs/api/javax/lang/model/type/TypeVisitor.html)
-   [`java.nio.file.FileVisitor`](http://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitor.html)  ve  [`SimpleFileVisitor`](http://docs.oracle.com/javase/8/docs/api/java/nio/file/SimpleFileVisitor.html)
-   [`javax.faces.component.visit.VisitContext`](http://docs.oracle.com/javaee/7/api/javax/faces/component/visit/VisitContext.html)  ve  [`VisitCallback`](http://docs.oracle.com/javaee/7/api/javax/faces/component/visit/VisitCallback.html)

####  Şekilleri XML'e aktarma

Bu örnekte, bir dizi geometrik şekli XML'e aktarmak istiyoruz. İşin püf noktası, şekillerin kodunu doğrudan değiştirmek veya en azından bunu minimumda tutmak istemiyoruz.

Sonuçta Visitor modeli, o sınıfların mevcut kodunu değiştirmeden, şekiller hiyerarşisine herhangi bir davranışı eklememize olanak tanıyan bir altyapı kurar.


**📁 shapes**

⤵️ 📄 `shapes/Shape.java`: Ortak şekil arayüzü

```java
package fatihes1.visitor.example.shapes;

import fatihes1.visitor.example.visitor.Visitor;

public interface Shape {
    void move(int x, int y);
    void draw();
    String accept(Visitor visitor);
}
```

⤵️ 📄 `shapes/Dot.java`

```java
package fatihes1.visitor.example.shapes;

import fatihes1.visitor.example.visitor.Visitor;

public class Dot implements Shape {
    private int id;
    private int x;
    private int y;

    public Dot() {
    }

    public Dot(int id, int x, int y) {
        this.id = id;
        this.x = x;
        this.y = y;
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitDot(this);
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public int getId() {
        return id;
    }
}
```

⤵️ 📄 `shapes/Circle.java`

```java
package fatihes1.visitor.example.shapes;

import fatihes1.visitor.example.visitor.Visitor;

public class Circle extends Dot {
    private int radius;

    public Circle(int id, int x, int y, int radius) {
        super(id, x, y);
        this.radius = radius;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitCircle(this);
    }

    public int getRadius() {
        return radius;
    }
}
```

⤵️ 📄 `shapes/Rectangle.java`

```java
package fatihes1.visitor.example.shapes;

import fatihes1.visitor.example.visitor.Visitor;

public class Rectangle implements Shape {
    private int id;
    private int x;
    private int y;
    private int width;
    private int height;

    public Rectangle(int id, int x, int y, int width, int height) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitRectangle(this);
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    public int getId() {
        return id;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public int getWidth() {
        return width;
    }

    public int getHeight() {
        return height;
    }
}
```

⤵️ 📄 `shapes/CompoundShape.java`

```java
package fatihes1.visitor.example.shapes;

import fatihes1.visitor.example.visitor.Visitor;

import java.util.ArrayList;
import java.util.List;

public class CompoundShape implements Shape {
    public int id;
    public List<Shape> children = new ArrayList<>();

    public CompoundShape(int id) {
        this.id = id;
    }

    @Override
    public void move(int x, int y) {
        // move shape
    }

    @Override
    public void draw() {
        // draw shape
    }

    public int getId() {
        return id;
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitCompoundGraphic(this);
    }

    public void add(Shape shape) {
        children.add(shape);
    }
}
```

**📁 visitor**

⤵️ 📄 `visitor/Visitor.java`: Ortak ziyaretçi arayüzü

```java
package fatihes1.visitor.example.visitor;

import fatihes1.visitor.example.shapes.Circle;
import fatihes1.visitor.example.shapes.CompoundShape;
import fatihes1.visitor.example.shapes.Dot;
import fatihes1.visitor.example.shapes.Rectangle;

public interface Visitor {
    String visitDot(Dot dot);

    String visitCircle(Circle circle);

    String visitRectangle(Rectangle rectangle);

    String visitCompoundGraphic(CompoundShape cg);
}
```

⤵️ 📄 `visitor/XMLExportVisitor.java`: Tüm şekilleri XML'e aktarır

```java
package fatihes1.visitor.example.visitor;

import fatihes1.visitor.example.shapes.*;

public class XMLExportVisitor implements Visitor {

    public String export(Shape... args) {
        StringBuilder sb = new StringBuilder();
        sb.append("<?xml version=\"1.0\" encoding=\"utf-8\"?>" + "\n");
        for (Shape shape : args) {
            sb.append(shape.accept(this)).append("\n");
        }
        return sb.toString();
    }

    public String visitDot(Dot d) {
        return "<dot>" + "\n" +
                "    <id>" + d.getId() + "</id>" + "\n" +
                "    <x>" + d.getX() + "</x>" + "\n" +
                "    <y>" + d.getY() + "</y>" + "\n" +
                "</dot>";
    }

    public String visitCircle(Circle c) {
        return "<circle>" + "\n" +
                "    <id>" + c.getId() + "</id>" + "\n" +
                "    <x>" + c.getX() + "</x>" + "\n" +
                "    <y>" + c.getY() + "</y>" + "\n" +
                "    <radius>" + c.getRadius() + "</radius>" + "\n" +
                "</circle>";
    }

    public String visitRectangle(Rectangle r) {
        return "<rectangle>" + "\n" +
                "    <id>" + r.getId() + "</id>" + "\n" +
                "    <x>" + r.getX() + "</x>" + "\n" +
                "    <y>" + r.getY() + "</y>" + "\n" +
                "    <width>" + r.getWidth() + "</width>" + "\n" +
                "    <height>" + r.getHeight() + "</height>" + "\n" +
                "</rectangle>";
    }

    public String visitCompoundGraphic(CompoundShape cg) {
        return "<compound_graphic>" + "\n" +
                "   <id>" + cg.getId() + "</id>" + "\n" +
                _visitCompoundGraphic(cg) +
                "</compound_graphic>";
    }

    private String _visitCompoundGraphic(CompoundShape cg) {
        StringBuilder sb = new StringBuilder();
        for (Shape shape : cg.children) {
            String obj = shape.accept(this);
            // Proper indentation for sub-objects.
            obj = "    " + obj.replace("\n", "\n    ") + "\n";
            sb.append(obj);
        }
        return sb.toString();
    }

}
```

⤵️ 📄 `Demo.java`: İstemci kodu

```java
package fatihes1.visitor.example;

import fatihes1.visitor.example.shapes.*;
import fatihes1.visitor.example.visitor.XMLExportVisitor;

public class Demo {
    public static void main(String[] args) {
        Dot dot = new Dot(1, 10, 55);
        Circle circle = new Circle(2, 23, 15, 10);
        Rectangle rectangle = new Rectangle(3, 10, 17, 20, 30);

        CompoundShape compoundShape = new CompoundShape(4);
        compoundShape.add(dot);
        compoundShape.add(circle);
        compoundShape.add(rectangle);

        CompoundShape c = new CompoundShape(5);
        c.add(dot);
        compoundShape.add(c);

        export(circle, compoundShape);
    }

    private static void export(Shape... shapes) {
        XMLExportVisitor exportVisitor = new XMLExportVisitor();
        System.out.println(exportVisitor.export(shapes));
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.txt`: Çalışma Çıktısı

```xml
<?xml version="1.0" encoding="utf-8"?>
<circle>
    <id>2</id>
    <x>23</x>
    <y>15</y>
    <radius>10</radius>
</circle>

<?xml version="1.0" encoding="utf-8"?>
<compound_graphic>
   <id>4</id>
    <dot>
        <id>1</id>
        <x>10</x>
        <y>55</y>
    </dot>
    <circle>
        <id>2</id>
        <x>23</x>
        <y>15</y>
        <radius>10</radius>
    </circle>
    <rectangle>
        <id>3</id>
        <x>10</x>
        <y>17</y>
        <width>20</width>
        <height>30</height>
    </rectangle>
    <compound_graphic>
       <id>5</id>
        <dot>
            <id>1</id>
            <x>10</x>
            <y>55</y>
        </dot>
    </compound_graphic>
</compound_graphic>
```
