#  Bileşik/Kompozit (Composite) Tasarım Deseni

> Diğer adıyla: Nesne Ağacı (Object Tree)


##  💬 Amaç

Kompozit, nesneleri ağaç yapıları halinde oluşturmanıza ve daha sonra bu yapılarla sanki ayrı nesnelermiş gibi çalışmanıza olanak tanıyan yapısal (structural) bir tasarım desenidir.

![](https://refactoring.guru/images/patterns/content/composite/composite-2x.png)


##  🙁 Problem

Kompozit desenini kullanmak, uygulamanızın temel modelini bir ağaç olarak temsil edebildiğinde anlam ifade eder.

Örneğin, iki tür nesneniz olduğunu düşünelim: Ürünler (`Products`) ve Kutular (`Boxes`). Bir Kutu (`Box`), birkaç ürünü (`Products`) ve aynı zamanda daha küçük kutuların (`Boxes`) birkaçını içerebilir. Bu küçük kutular (`Boxes`) aynı zamanda bazı ürünleri (`Products`) veya daha küçük kutuları (`Boxes`) tutabilir ve bu böyle devam eder.

Diyelim ki bu sınıfları kullanan bir sipariş sistemi oluşturmaya karar verdiniz. Siparişler, paketlenmemiş basit ürünleri içerebileceği gibi aynı zamanda ürünlerle ya da diğer kutularla doldurulmuş kutuları içerebilir. Böyle bir siparişin toplam fiyatını nasıl belirlersiniz?

![](https://refactoring.guru/images/patterns/diagrams/composite/problem-en-2x.png)

*Bir sipariş, kutularda paketlenmiş, daha büyük kutuları içeren kutular vb. çeşitli ürünlerden oluşabilir. Bütün yapı baş aşağı bir ağaca benziyor.*

Doğrudan yaklaşımı deneyebilirsiniz: tüm kutuları açın, tüm ürünleri gözden geçirin ve sonra toplamı hesaplayın. Bu gerçek dünyada yapılabilir; ancak bir programda, bir döngüyü çalıştırmak kadar basit değildir. Hangi ürünlerin ve kutuların sınıfını geçtiğinizi, kutuların iç içe geçmişlik seviyesini ve diğer can sıkıcı ayrıntıları önceden bilmelisiniz. Tüm bunlar, doğrudan yaklaşımı uğraştırıcı, hatta imkansız kılar.

##  😊 Çözüm

Kompozit deseni, ürünler ve kutular ile ortak bir arayüz aracılığıyla çalışmanızı önerir ve bu arayüz, toplam fiyatı hesaplamak için bir yöntem tanımlar.

Bu yöntem nasıl çalışır? Bir ürün için, sadece ürünün fiyatını döndürmelidir. Bir kutu için ise kutunun içerdiği her öğeyi gözden geçirir, fiyatını sorar ve ardından bu kutu için bir toplam fiyat döndürür. Bu öğelerden biri daha küçük bir kutu ise, o kutunun da içeriği gözden geçirilir ve bu şekilde iç bileşenlerin fiyatları hesaplanana kadar devam eder. Bir kutu, nihai fiyata bazı ek maliyetler ekleyebilir, örneğin ambalaj maliyeti gibi.


![](https://refactoring.guru/images/patterns/content/composite/composite-comic-1-en-2x.png)

*Bileşik desen, bir davranışı bir nesne ağacının tüm bileşenleri üzerinde yinelemeli olarak çalıştırmanıza olanak tanır.*

Bu yaklaşımın en büyük faydası, ağacı oluşturan nesnelerin oluşturulduğu sınıflar hakkında endişelenmenize gerek olmamasıdır. Bir nesnenin basit bir ürün mü yoksa karmaşık bir kutu mu olduğunu bilmeye ihtiyacınız yoktur. Ortak arayüz üzerinden hepsini aynı şekilde işleyebilirsiniz. Bir yöntem çağırdığınızda, nesneler kendileri isteği aşağıdaki ağaca iletirler.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/diagrams/composite/live-example-2x.png)

*Askeri yapıya bir örnek.*

Çoğu ülkenin orduları hiyerarşik olarak yapılandırılmıştır. Bir ordu, birkaç tümeni içerir; bir tümen, bir dizi tugaydır ve bir tugay gruplara ayrılabilir, bu gruplar ise takımlara ayrılabilir. Sonunda, bir takım gerçek askerlerden oluşan küçük bir gruptur. Emirler hiyerarşinin en üstünden verilir ve her asker, ne yapılması gerektiğini öğrenene kadar her seviyeden aşağıya iletilir.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/composite/structure-en-2x.png)

1. Bileşen (**Composite**) arayüzü, ağacın hem basit hem de karmaşık elemanları için ortak olan işlemleri barındırır.
2. Yaprak (**Leaf**), bir ağacın alt öğeleri olmayan temel bir öğesidir.
Yaprak bileşenler genellikle asıl işin çoğunu yapar çünkü işi devredecek kimseleri yoktur.
3. **Container** (diğer adıyla kompozit), alt öğeleri olan bir yapıdır: yapraklar veya diğer konteynerleri. Bir konteyner, ondan üretilmiş  çocuklarının sınıflarını bilmiyor. Tüm alt öğelerle yalnızca bileşen arayüzü aracılığıyla çalışır.
Bir istek alındığında, konteyner işi alt öğelerine devreder, ara sonuçları işler ve ardından nihai sonucu müşteriye geri gönderir.
4. İstemci (**Client**), bileşen arayüzü aracılığıyla tüm öğelerle çalışır. Sonuç olarak istemci, ağacın hem basit hem de karmaşık öğeleriyle aynı şekilde çalışabilir.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte, bileşik/kompozit desen, bir grafik düzenleyicide geometrik şekilleri oluşturmanıza olanak tanır.

![](https://refactoring.guru/images/patterns/diagrams/composite/example-2x.png)

*Geometrik şekiller düzenleyici örneği.*

`CompoundGraphic` sınıfı, başka bileşik şekiller de dahil olmak üzere bir dizi alt şekli içerebilen bir konteynerdir. Bileşik bir şekil, basit bir şekil ile aynı yöntemlere sahiptir. Ancak, kendi başına bir şey yapmak yerine bileşik bir şekil isteği tüm çocuklarına tekrarlayarak iletir ve sonucu toplar.

İstemci kodu, tüm şekillerle, tüm şekil sınıflarına ortak olan tek bir arayüz (interface) aracılığıyla çalışır. Bu nedenle, istemci, basit bir şekille mi yoksa bileşik bir şekille mi çalıştığını bilmez. İstemci, bu nesneyi oluşturan sınıflara bağlı olmadan çok karmaşık nesne yapıları ile çalışabilir.

```java
// Bileşen arayüzü, bileşenin basit ve karmaşık nesneleri için
// ortak işlemleri tanımlar.
interface Graphic is
    method move(x, y)
    method draw()

// Yaprak sınıf, bileşenlerin son nesnelerini temsil eder. Bir
// yaprak nesne alt nesnelere sahip olamaz. Genellikle, gerçek işi
// yapan yaprak nesnelerdir, bileşik nesneler yalnızca
// alt bileşenlerine devreder.
class Dot implements Graphic is
    field x, y

    constructor Dot(x, y) { ... }

    method move(x, y) is
        this.x += x, this.y += y

    method draw() is
        // X ve Y konumunda bir nokta çizin.

// Tüm bileşen sınıfları diğer bileşenleri genişletebilir.
class Circle extends Dot is
    field radius

    constructor Circle(x, y, radius) { ... }

    method draw() is
        // X ve Y konumunda yarıçap R ile bir daire çizin.

// Bileşik sınıf, alt nesnelere sahip olabilen karmaşık bileşenleri
// temsil eder. Bileşik nesneler genellikle gerçek işi alt
// nesnelerine devreder ve sonra sonucu "toplar".
class CompoundGraphic implements Graphic is
    field children: array of Graphic

    // Bir bileşik nesne diğer bileşenleri (basit veya karmaşık)
    // çocuk listesine ekleyebilir veya çıkarabilir.
    method add(child: Graphic) is
        // Bir çocuğu çocuk dizisine ekleyin.

    method remove(child: Graphic) is
        // Bir çocuğu çocuk dizisinden çıkarın.

    method move(x, y) is
        foreach (child in children) do
            child.move(x, y)

    // Bir bileşik, asıl mantığını belirli bir şekilde yürütür. Tüm
    // çocuklarına rekürsif (yineleyen) olarak geçer ve sonuçlarını toplar. Çünkü
    // bileşik nesnelerin çocukları bu çağrıları kendi çocuklarına
    // geçirir ve böylece tüm nesne ağacı sonuç olarak geçilir.
    method draw() is
        // 1. Her çocuk bileşen için:
        //     - Bileşeni çizin.
        //     - Sınırlayıcı dikdörtgeni güncelleyin.
        // 2. Sınırlayıcı koordinatlarını kullanarak kesikli bir dikdörtgen çizin.

// İstemci kod, tüm bileşenlerle ortak olan temel arayüzleri
// aracılığıyla çalışır. Bu şekilde istemci kod, basit yaprak
// bileşenleri ve karmaşık bileşenleri destekleyebilir.
class ImageEditor is
    field all: CompoundGraphic

    method load() is
        all = new CompoundGraphic()
        all.add(new Dot(1, 2))
        all.add(new Circle(5, 3, 10))
        // ...

    // Seçilen bileşenleri karmaşık bir bileşik bileşene birleştir.
    method groupSelected(components: array of Graphic) is
        group = new CompoundGraphic()
        foreach (component in components) do
            group.add(component)
            all.remove(component)
        all.add(group)
        // Tüm bileşenler çizilecektir.
        all.draw()

```


##  💡Uygulanabilirlik

**🐞 Ağaç benzeri bir nesne yapısı uygulamanız gerektiğinde Bileşik deseni kullanın.**

⚡️ Bileşik desen size ortak bir arayüzü paylaşan iki temel öge türü sağlar: basit yapraklar ve karmaşık kaplar. Bir konteyner hem yapraklardan hem de diğer konteynerlerden oluşabilir. Bu, ağaca benzeyen iç içe geçmiş özyinelemeli bir nesne yapısı oluşturmanıza olanak tanır.
  
----------------

**🐞 İstemci kodunun hem basit hem de karmaşık öğeleri aynı şekilde ele almasını istediğinizde bu modeli kullanın.**

⚡️ Bileşik desen tarafından tanımlanan tüm öğeler ortak bir arayüzü paylaşır. Bu arayüzü kullanarak müşterinin birlikte çalıştığı nesnelerin somut sınıfı hakkında endişelenmesine gerek kalmaz.


##  📝 Nasıl Uygulanır?

1. Uygulamanızın temel modelini bir ağaç yapısı olarak temsil edebileceğinizden emin olun. Onu basit elemanlar ve konteynerlara bölmeye çalışın. Unutmayın ki konteynerlar, hem basit elemanları hem de diğer konteynerları içerebilmelidir.

2. Bileşen arayüzünü, basit ve karmaşık bileşenler için anlamlı olan yöntemlerin bir listesiyle tanımlayın.

3. Basit elemanları temsil etmek için bir yaprak sınıfı oluşturun. Bir programın birden çok farklı yaprak sınıfı olabilir.

4. Karmaşık elemanları temsil etmek için bir konteyner sınıfı oluşturun. Bu sınıfta, alt elemanlara referansları depolamak için bir dizi alanı sağlayın. Dizi, hem yaprakları hem de konteynerları depolayabilmelidir, bu nedenle bileşen arayüz türü ile bildirildiğinden emin olun.
Bileşen arayüzünün yöntemlerini uygularken, bir konteynerin çoğu işi alt elemanlara devretmesi gerektiğini unutmayın.

5. Son olarak, konteynerdaki alt elemanların eklenmesi ve kaldırılması için yöntemleri tanımlayın.
Bu işlemlerin bileşen arayüzünde bildirilebileceğini unutmayın. Bu, Arayüz Ayırma Prensibini (Interface Segregation Principle) ihlal eder çünkü bu yöntemler yaprak sınıfta boş olacaktır. Ancak istemci, ağacı oluştururken tüm elemanları eşit şekilde işleyebilecektir.
  

##  ⚖️ Artıları ve Eksileri

✅ Karmaşık ağaç yapılarıyla daha rahat çalışabilirsiniz: polimorfizmi ve özyinelemeyi kendi avantajınıza kullanın.

✅ Açık/Kapalı Prensibi (Open/Closed Principle). Artık nesne ağacıyla çalışan mevcut kodu bozmadan uygulamaya yeni öğe türleri ekleyebilirsiniz.

❌ İşlevselliği çok fazla farklılık gösteren sınıflar için ortak bir arayüz sağlamak zor olabilir. Bazı senaryolarda bileşen arayüzünü aşırı genelleştirmeniz gerekir, bu da anlaşılmasını zorlaştırır.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Karmaşık Bileşik/Kompozit ağaçlar oluştururken Yapıcı (Builder) tasarım desenini kullanabilirsiniz, çünkü yapısını çalışma adımlarını tekrarlayan şekilde programlayabilirsiniz.

- Sorumluluk Zinciri (Chain of Responsibility) tasarım deseni, genellikle Kompozit deseni ile birlikte kullanılır. Bu durumda, bir yaprak bileşen bir isteği aldığında, bu isteği tüm üst bileşenlerin zinciri boyunca nesne ağacının köküne kadar iletebilir.

- Kompozit ağaçlarını gezmeniz için Yineleyicileri (Iterators) deseni kullanabilirsiniz.

- Bir tüm Kompozit ağaç üzerinde bir işlem gerçekleştirmek için Ziyaretçi (Visitor) tasarım deseninini kullanabilirsiniz.



  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Bileşik desen Java kodunda oldukça yaygındır. Genellikle kullanıcı arayüzü bileşenlerinin hiyerarşilerini veya grafiklerle çalışan kodu temsil etmek için kullanılır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.awt.Container#add(Component)`](http://docs.oracle.com/javase/8/docs/api/java/awt/Container.html#add-java.awt.Component-)  (Swing bileşenlerinin hemen hemen her yerinde)
    
-   [`javax.faces.component.UIComponent#getChildren()`](http://docs.oracle.com/javaee/7/api/javax/faces/component/UIComponent.html#getChildren--)  (Hemen hemen tüm JSF UI bileşenlerinde)


**Tanımlama:** Bir nesne ağacınız varsa ve bir ağacın her nesnesi aynı sınıf hiyerarşisinin parçasıysa, bu büyük olasılıkla bir bileşiktir/kompozittir. Bu sınıfların yöntemleri, işi ağacın alt nesnelerine devrederse ve bunu hiyerarşinin temel sınıfı/arabirimi aracılığıyla yapıyorsa, bu kesinlikle bir bileşiktir.

 
####  Basit ve bileşik grafik şekiller

Bu örnek, daha basit şekillerden oluşan karmaşık grafik şekillerin nasıl oluşturulacağını ve her ikisine de aynı şekilde nasıl davranılacağını gösterir.

**📁 shapes**

⤵️ 📄 `shapes/Shape.java`: Tüm şekillerin ortak arayüzü

```java
package fatihes1.composite.example.shapes;

import java.awt.*;

public interface Shape {
    int getX();
    int getY();
    int getWidth();
    int getHeight();
    void move(int x, int y);
    boolean isInsideBounds(int x, int y);
    void select();
    void unSelect();
    boolean isSelected();
    void paint(Graphics graphics);
}
```

⤵️ 📄 `shapes/BaseShape.java`: Temel işlevlere sahip soyut şekil sınıfı

```java
package fatihes1.composite.example.shapes;

import java.awt.*;

abstract class BaseShape implements Shape {
    public int x;
    public int y;
    public Color color;
    private boolean selected = false;

    BaseShape(int x, int y, Color color) {
        this.x = x;
        this.y = y;
        this.color = color;
    }

    @Override
    public int getX() {
        return x;
    }

    @Override
    public int getY() {
        return y;
    }

    @Override
    public int getWidth() {
        return 0;
    }

    @Override
    public int getHeight() {
        return 0;
    }

    @Override
    public void move(int x, int y) {
        this.x += x;
        this.y += y;
    }

    @Override
    public boolean isInsideBounds(int x, int y) {
        return x > getX() && x < (getX() + getWidth()) &&
                y > getY() && y < (getY() + getHeight());
    }

    @Override
    public void select() {
        selected = true;
    }

    @Override
    public void unSelect() {
        selected = false;
    }

    @Override
    public boolean isSelected() {
        return selected;
    }

    void enableSelectionStyle(Graphics graphics) {
        graphics.setColor(Color.LIGHT_GRAY);

        Graphics2D g2 = (Graphics2D) graphics;
        float[] dash1 = {2.0f};
        g2.setStroke(new BasicStroke(1.0f,
                BasicStroke.CAP_BUTT,
                BasicStroke.JOIN_MITER,
                2.0f, dash1, 0.0f));
    }

    void disableSelectionStyle(Graphics graphics) {
        graphics.setColor(color);
        Graphics2D g2 = (Graphics2D) graphics;
        g2.setStroke(new BasicStroke());
    }


    @Override
    public void paint(Graphics graphics) {
        if (isSelected()) {
            enableSelectionStyle(graphics);
        }
        else {
            disableSelectionStyle(graphics);
        }

        // ...
    }
}
```


⤵️ 📄 `shapes/Dot.java`: Bir nokta (.)

```java
package fatihes1.composite.example.shapes;

import java.awt.*;

public class Dot extends BaseShape {
    private final int DOT_SIZE = 3;

    public Dot(int x, int y, Color color) {
        super(x, y, color);
    }

    @Override
    public int getWidth() {
        return DOT_SIZE;
    }

    @Override
    public int getHeight() {
        return DOT_SIZE;
    }

    @Override
    public void paint(Graphics graphics) {
        super.paint(graphics);
        graphics.fillRect(x - 1, y - 1, getWidth(), getHeight());
    }
}
```

⤵️ 📄 `shapes/Circle.java`: Bir daire

```java
package fatihes1.composite.example.shapes;

import java.awt.*;

public class Circle extends BaseShape {
    public int radius;

    public Circle(int x, int y, int radius, Color color) {
        super(x, y, color);
        this.radius = radius;
    }

    @Override
    public int getWidth() {
        return radius * 2;
    }

    @Override
    public int getHeight() {
        return radius * 2;
    }

    public void paint(Graphics graphics) {
        super.paint(graphics);
        graphics.drawOval(x, y, getWidth() - 1, getHeight() - 1);
    }
}
```

⤵️ 📄 `shapes/Rectangle.java`: Bir dikdörtgen

```java
package fatihes1.composite.example.shapes;

import java.awt.*;

public class Rectangle extends BaseShape {
    public int width;
    public int height;

    public Rectangle(int x, int y, int width, int height, Color color) {
        super(x, y, color);
        this.width = width;
        this.height = height;
    }

    @Override
    public int getWidth() {
        return width;
    }

    @Override
    public int getHeight() {
        return height;
    }

    @Override
    public void paint(Graphics graphics) {
        super.paint(graphics);
        graphics.drawRect(x, y, getWidth() - 1, getHeight() - 1);
    }
}
```

⤵️ 📄 `shapes/CompoundShape.java`: Diğer şekil nesnelerinden oluşan bileşik/kompozit şekil

```java
package fatihes1.composite.example.shapes;

import java.awt.*;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CompoundShape extends BaseShape {
    protected List<Shape> children = new ArrayList<>();

    public CompoundShape(Shape... components) {
        super(0, 0, Color.BLACK);
        add(components);
    }

    public void add(Shape component) {
        children.add(component);
    }

    public void add(Shape... components) {
        children.addAll(Arrays.asList(components));
    }

    public void remove(Shape child) {
        children.remove(child);
    }

    public void remove(Shape... components) {
        children.removeAll(Arrays.asList(components));
    }

    public void clear() {
        children.clear();
    }

    @Override
    public int getX() {
        if (children.size() == 0) {
            return 0;
        }
        int x = children.get(0).getX();
        for (Shape child : children) {
            if (child.getX() < x) {
                x = child.getX();
            }
        }
        return x;
    }

    @Override
    public int getY() {
        if (children.size() == 0) {
            return 0;
        }
        int y = children.get(0).getY();
        for (Shape child : children) {
            if (child.getY() < y) {
                y = child.getY();
            }
        }
        return y;
    }

    @Override
    public int getWidth() {
        int maxWidth = 0;
        int x = getX();
        for (Shape child : children) {
            int childsRelativeX = child.getX() - x;
            int childWidth = childsRelativeX + child.getWidth();
            if (childWidth > maxWidth) {
                maxWidth = childWidth;
            }
        }
        return maxWidth;
    }

    @Override
    public int getHeight() {
        int maxHeight = 0;
        int y = getY();
        for (Shape child : children) {
            int childsRelativeY = child.getY() - y;
            int childHeight = childsRelativeY + child.getHeight();
            if (childHeight > maxHeight) {
                maxHeight = childHeight;
            }
        }
        return maxHeight;
    }

    @Override
    public void move(int x, int y) {
        for (Shape child : children) {
            child.move(x, y);
        }
    }

    @Override
    public boolean isInsideBounds(int x, int y) {
        for (Shape child : children) {
            if (child.isInsideBounds(x, y)) {
                return true;
            }
        }
        return false;
    }

    @Override
    public void unSelect() {
        super.unSelect();
        for (Shape child : children) {
            child.unSelect();
        }
    }

    public boolean selectChildAt(int x, int y) {
        for (Shape child : children) {
            if (child.isInsideBounds(x, y)) {
                child.select();
                return true;
            }
        }
        return false;
    }

    @Override
    public void paint(Graphics graphics) {
        if (isSelected()) {
            enableSelectionStyle(graphics);
            graphics.drawRect(getX() - 1, getY() - 1, getWidth() + 1, getHeight() + 1);
            disableSelectionStyle(graphics);
        }

        for (Shape child : children) {
            child.paint(graphics);
        }
    }
}

```

**📁 editor**

⤵️ 📄 `editor/ImageEditor.java`: Şekil düzenleyici

```java
package fatihes1.composite.example.editor;

import fatihes1.composite.example.shapes.CompoundShape;
import fatihes1.composite.example.shapes.Shape;

import javax.swing.*;
import javax.swing.border.Border;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;

public class ImageEditor {
    private EditorCanvas canvas;
    private CompoundShape allShapes = new CompoundShape();

    public ImageEditor() {
        canvas = new EditorCanvas();
    }

    public void loadShapes(Shape... shapes) {
        allShapes.clear();
        allShapes.add(shapes);
        canvas.refresh();
    }

    private class EditorCanvas extends Canvas {
        JFrame frame;

        private static final int PADDING = 10;

        EditorCanvas() {
            createFrame();
            refresh();
            addMouseListener(new MouseAdapter() {
                @Override
                public void mousePressed(MouseEvent e) {
                    allShapes.unSelect();
                    allShapes.selectChildAt(e.getX(), e.getY());
                    e.getComponent().repaint();
                }
            });
        }

        void createFrame() {
            frame = new JFrame();
            frame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
            frame.setLocationRelativeTo(null);

            JPanel contentPanel = new JPanel();
            Border padding = BorderFactory.createEmptyBorder(PADDING, PADDING, PADDING, PADDING);
            contentPanel.setBorder(padding);
            frame.setContentPane(contentPanel);

            frame.add(this);
            frame.setVisible(true);
            frame.getContentPane().setBackground(Color.LIGHT_GRAY);
        }

        public int getWidth() {
            return allShapes.getX() + allShapes.getWidth() + PADDING;
        }

        public int getHeight() {
            return allShapes.getY() + allShapes.getHeight() + PADDING;
        }

        void refresh() {
            this.setSize(getWidth(), getHeight());
            frame.pack();
        }

        public void paint(Graphics graphics) {
            allShapes.paint(graphics);
        }
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.composite.example;

import fatihes1.composite.example.editor.ImageEditor;
import fatihes1.composite.example.shapes.Circle;
import fatihes1.composite.example.shapes.CompoundShape;
import fatihes1.composite.example.shapes.Dot;
import fatihes1.composite.example.shapes.Rectangle;

import java.awt.*;

public class Demo {
    public static void main(String[] args) {
        ImageEditor editor = new ImageEditor();

        editor.loadShapes(
                new Circle(10, 10, 10, Color.BLUE),

                new CompoundShape(
                    new Circle(110, 110, 50, Color.RED),
                    new Dot(160, 160, Color.RED)
                ),

                new CompoundShape(
                        new Rectangle(250, 250, 100, 100, Color.GREEN),
                        new Dot(240, 240, Color.GREEN),
                        new Dot(240, 360, Color.GREEN),
                        new Dot(360, 360, Color.GREEN),
                        new Dot(360, 240, Color.GREEN)
                )
        );
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.png`: Çalıştırma Sonucu

![](https://refactoring.guru/images/patterns/examples/java/composite/OutputDemo.png)




