#  Flyweight Tasarım Deseni

> Diğer Adıyla: Önbellek (Cache)

##  💬 Amaç

Flyweight, her bir nesnedeki tüm verileri saklamak yerine, durumun ortak bölümlerini birden çok nesne arasında paylaşır. Böylelikle kullanılabilir RAM miktarına daha fazla nesne sığdırmanıza olanak tanıyan yapısal (structural) bir tasarım modelidir.

![](https://refactoring.guru/images/patterns/content/flyweight/flyweight-2x.png)


##  🙁 Problem


Uzun çalışma saatlerinin ardından eğlenmek için basit bir video oyunu oluşturmaya karar verdiniz: oyuncular harita üzerinde dolaşacak ve birbirlerine ateş edecekti. Gerçekçi bir parçacık sistemi uygulamaya karar verdiniz ve onu oyunun ayırt edici bir özelliği haline getirmeye karar verdiniz. Büyük miktarda mermi, füze ve patlamalardan çıkan şarapnelden oluşan parçacıklar harita üzerinde uçacak ve oyuncuya heyecan verici bir deneyim sunacaktı.

Tamamlandığında son işlemi yaptınız, oyunu oluşturdunuz ve arkadaşınıza test sürüşü için gönderdiniz. Oyun kendi bilgisayarınızda sorunsuz çalışıyordu, ancak arkadaşınız uzun süre oynayamadı. Onun bilgisayarında, oyun birkaç dakika oynandıktan sonra çökmeye devam etti. Hata ayıklama logları incelemeye saatler harcadıktan sonra, oyunun RAM miktarının yetersizliği nedeniyle çöktüğünü keşfettiniz. Arkadaşının bilgisayarının, kendi bilgisayarınızdan çok daha güçsüz olduğu ve bu nedenle sorunun bu denli kısa sürede kendini gösterdiğini gördünüz.

Asıl sorun parçacık sisteminizle ilgiliydi. Her bir parçacık, bir mermi, bir füze veya bir şarapnel parçası gibi, çok sayıda veri içeren ayrı bir nesne tarafından temsil ediliyordu. Bir oyuncunun ekranındaki vahşet bir noktaya ulaştığında, yeni oluşturulan parçacıklar artık kullanılabilir RAM'in içine sığmadı, bu nedenle program çöktü.

![](https://refactoring.guru/images/patterns/diagrams/flyweight/problem-en-2x.png)


##  😊 Çözüm

Parçacık (`Particle`) sınıfının daha yakından incelendiğinde, renk ve hareketli grafik alanlarının diğer alanlardan çok daha fazla bellek tükettiğini fark edebilirsiniz. Daha da kötüsü, bu iki alanın tüm parçacıklarda neredeyse aynı verileri depolamasıdır. Örneğin, tüm mermiler aynı renge ve hareketli grafiğe sahip olabilir.

![](![Flyweight pattern solution](https://refactoring.guru/images/patterns/diagrams/flyweight/solution1-en.png)

Bir parçacığın durumunun diğer kısımları, özellikle koordinatlar, hareket vektörü ve hız, her bir parçacık için benzersizdir. Sonuçta, bu alanların değerleri zaman içinde değişir. Bu veri, parçacığın var olduğu her zaman değişen bağlamı temsil ederken, renk ve sprite her bir parçacık için sabit kalır.

Bir nesnenin bu sabit verilerine genellikle içsel durum (**intrinsic state**) denir. Bu state'ler, nesnenin içinde bulunur; diğer nesneler sadece okuyabilir, değiştiremez. Nesnenin geri kalan durumu, genellikle diğer nesneler tarafından "dışarıdan" değiştirilen, dışsal durum (**extrinsic state**) olarak adlandırılır.

Flyweight deseni, dışsal durumu nesnenin içine saklamayı bırakmanızı önerir. Bunun yerine, bu durumu (state) kullanan belirli yöntemlere iletmelisiniz. Sadece içsel durum nesnenin içinde kalır, böylece farklı bağlamlarda yeniden kullanabilirsiniz. Sonuç olarak, bu nesnelerden daha azına ihtiyacınız olur, çünkü sadece içsel durumda farklılık gösterirler ve bu, dışsal durumdan çok daha az varyasyona sahiptir.

![](https://refactoring.guru/images/patterns/diagrams/flyweight/solution3-en-2x.png)

Oyunumuza geri dönelim. Parçacık sınıfımızdan dışsal durumu çıkardığımızı varsayarsak, oyundaki tüm parçacıkları temsil etmek için yalnızca üç farklı nesne yeterli olacaktır: bir mermi, bir füze ve bir şarapnel parçası. Muhtemelen şimdiye kadar tahmin ettiğiniz gibi, yalnızca içsel durumu depolayan bir nesneye flyweight denir. (Flyweight terimi, bokstan gelir ve 51 kg'dan (111 lbs) daha hafif olan bir dövüşçüyü ifade eder.)

### Dışsal durumun (Extrinsic state) depolaması

Dışsal durum nereye taşınır? Bazı sınıflar hala dışsal durumları saklamalı, değil mi? Çoğu durumda, deseni uygulamadan önce nesneleri toplayan kapsayıcı nesnesine taşınır.

Bizim durumumuzda, tüm parçacıkları, parçacıklar (`particles`) alanında depolayan ana Oyun (`Game`) nesnesi budur. Dışsal durumu bu sınıfa taşımak için, her bir parçacığın koordinatlarını, vektörlerini ve hızını depolamak için birkaç dizi alanı oluşturmanız gerekir. Ama hepsi bu kadar değil. Bir parçacığı temsil eden belirli bir flyweight'a referansları depolamak için başka bir diziye ihtiyacınız vardır. Aynı dizini kullanarak bir parçacığın tüm verilerine erişebilmeniz için bu dizilerin senkronize olması gerekir.

![](https://refactoring.guru/images/patterns/diagrams/flyweight/solution2-en-2x.png)

Daha zarif bir çözüm, dışsal durumu depolayacak ve flyweight nesnesini referans birlikte tutacak ayrı bir bağlam sınıfı oluşturmaktır. Bu yaklaşım, sadece konteyner sınıfında tek bir dizi gerektirir.

Bir dakika! Bu bağlamsal nesnelerin çoğuna başlangıçta sahip olduğumuz kadar ihtiyacımız olmayacak mı? Teknik olarak evet. Ancak işin aslı, bu nesneler öncekinden çok daha küçüktür. En fazla bellek tüketen alanlar yalnızca birkaç flyweight nesnesine taşınmıştır. Şimdi bin küçük bağlam nesnesi, verilerinin bin kopyasını saklamak yerine tek bir ağır flyweight nesnesini yeniden kullanabilir.

### Flyweight and immutability (Değişmezlik)

Aynı flyweight nesnesi farklı bağlamlarda kullanılabildiğinden, durumunun değiştirilemediğinden emin olmanız gerekir. Bir flyweight, kurucu parametreleri aracılığıyla durumunu yalnızca bir kez başlatmalıdır. Herhangi bir ayarlayıcıyı veya ortak alanı diğer nesnelere göstermemelidir.

### Flyweight factory 

Çeşitli flyweight'lara daha kolay erişim için, mevcut flyweight'lardan oluşan bir havuzu yöneten bir fabrika yöntemi oluşturabilirsiniz. Yöntem, bir istemciden istenen flyweight'ın içsel durumunu çeker, bu durumla eşleşen mevcut bir flyweight nesnesini arar ve bulunursa döndürür. Değilse, yeni bir flyweight nesnesi oluşturur ve havuza ekler.

Bu yöntemin yerleştirilebileceği birkaç seçenek vardır. En belirgin yer bir flyweight konteyneridir. Alternatif olarak, yeni bir fabrika sınıfı oluşturabilirsiniz. Ya da fabrika yöntemini statik hale getirebilir ve gerçek bir flyweight sınıfının içine koyabilirsiniz.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/flyweight/structure-2x.png)

1. **Flyweight** modeli sadece bir optimizasyondur. Optimizasyonu uygulamadan önce, programınızın aynı anda bellekte çok sayıda benzer nesneye sahip olmakla ilgili RAM tüketim sorununa sahip olduğundan emin olun. Bu sorunun başka anlamlı bir şekilde çözülemediğinden emin olun.
2. **Flyweight** sınıfı, özgün nesnenin durumunun birden çok nesne arasında paylaşılabilen bölümünü içerir. Aynı flyweight nesnesi birçok farklı bağlamda kullanılabilir. Bir flyweight nesnesinin içinde depolanan duruma içsel (**intrinsic**) denir. Flyweight nesnesinin yöntemlerine parametre olarak verilen duruma ise dışsal (**extrinsic**) denir.
3. **Context** sınıfı, tüm orijinal nesnelerde benzersiz olan dışsal durumu içerir. Bir bağlam, flyweight nesnelerinden biriyle eşleştirildiğinde, orijinal nesnenin tam durumunu temsil eder.
4. Genellikle, orijinal nesnenin davranışı flyweight sınıfında kalır. Bu durumda, bir flyweight yöntemini çağıran kişi, dışsal durumun uygun bitlerini de yöntemin parametrelerine geçirmelidir. Öte yandan, davranış, bağlantılı flyweight nesnesini yalnızca bir veri nesnesi olarak kullanacak olan bağlam sınıfına taşınabilir.
5. **Client**, ağırlıkların dış durumunu hesaplar veya depolar. İstemcinin bakış açısına göre, bir flyweight, bazı bağlamsal verileri yöntemlerinin parametrelerine geçirerek çalışma zamanında yapılandırılabilen bir şablon nesnesidir.
6. **Flyweight Factory**, mevcut ağırlıklardan oluşan bir havuzu yönetir. Fabrika ile müşteriler doğrudan ağırlık oluşturmazlar. Bunun yerine, fabrikayı, istenen flyweight nesnesinin içsel durumunun parçalarını geçirerek çağırırlar. Fabrika önceden oluşturulmuş ağırlıklara bakar ve arama ölçütleriyle eşleşen mevcut bir tane döndürür veya hiçbir şey bulunamazsa yeni bir tane oluşturur.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte Flyweight modeli, milyonlarca ağaç nesnesinin bir tuval üzerinde işlenmesi sırasında bellek kullanımının azaltılmasına yardımcı olur.

![](https://refactoring.guru/images/patterns/diagrams/flyweight/example-2x.png)

Desen, yinelenen içsel durumu bir ana `Tree` sınıfından çıkarır ve onu `TreeType` uçucu ağırlığı sınıfına taşır.

Artık aynı verileri birden fazla nesnede depolamak yerine, yalnızca birkaç flyweight nesnede tutuluyor ve bağlam görevi gören uygun `Tree` nesnelerine bağlanıyor. İstemci kodu, doğru nesneyi aramanın ve gerektiğinde yeniden kullanmanın karmaşıklığını özetleyen, flyweight fabrikasını kullanarak yeni ağaç nesneleri oluşturur.

```java
// Flyweight sınıfı, bir ağacın bir kısmını içerir. Bu alanlar, her belirli ağaç için
// benzersiz olan değerleri depolar. Örneğin, ağaç koordinatlarını burada bulamazsınız.
// Ancak çok sayıda ağaç arasında paylaşılan dokular ve renkler buradadır.
// Bu veri genellikle BÜYÜK olduğundan, her ağaç nesnesinde saklamak yerine
// tekrar eden verileri ayrı bir nesneye çıkarabiliriz ki, birçok ağaç nesnesi
// buna başvurabilir.
class TreeType is
    field name
    field color
    field texture
    constructor TreeType(name, color, texture) { ... }
    method draw(canvas, x, y) is
        // 1. Belirli bir tür, renk ve doku tipinde bir bitki oluştur.
        // 2. Bitkiyi X ve Y koordinatları üzerine çiz.

// Flyweight fabrikası, mevcut flyweight'ı yeniden kullanıp kullanmama konusunda karar verir
// veya yeni bir nesne oluşturur.
class TreeFactory is
    static field treeTypes: koleksiyon ağaç türleri
    static method getTreeType(name, color, texture) is
        type = treeTypes.find(name, color, texture)
        if (type == null)
            type = new TreeType(name, color, texture)
            treeTypes.add(type)
        return type

// Bağlam nesnesi, ağacın dışsal kısmını içerir. Bir uygulama, bunlardan milyarlarca oluşturabilir
// çünkü oldukça küçüktür: yalnızca iki tamsayı koordinat ve bir referans alanı.
class Tree is
    field x,y
    field type: TreeType
    constructor Tree(x, y, type) { ... }
    method draw(canvas) is
        type.draw(canvas, this.x, this.y)

// Ağaç ve Orman sınıfları flyweight'ın istemcileridir.
// Eğer Ağaç sınıfını daha fazla geliştirmeyi düşünmüyorsanız, bunları birleştirebilirsiniz.
class Forest is
    field trees: collection of Trees

    method plantTree(x, y, name, color, texture) is
        type = TreeFactory.getTreeType(name, color, texture)
        tree = new Tree(x, y, type)
        trees.add(tree)

    method draw(canvas) is
        foreach (tree in trees) do
            tree.draw(canvas)

```


##  💡Uygulanabilirlik

**🐞 Flyweight modelini yalnızca programınızın mevcut RAM'a zar zor sığan çok sayıda nesneyi desteklemesi gerektiğinde kullanın.**

⚡️ Deseni uygulamanın faydası büyük ölçüde nasıl ve nerede kullanıldığına bağlıdır. En çok şu durumlarda faydalıdır:
- bir uygulamanın çok sayıda benzer nesne oluşturması gerekiyor
- bu, hedef cihazdaki tüm kullanılabilir RAM'i tüketir
- nesneler, birden fazla nesne arasında çıkarılabilen ve paylaşılabilen yinelenen durumlar içerir.
  


##  📝 Nasıl Uygulanır?

1. Flyweight haline gelecek bir sınıfın alanlarını iki bölüme ayırın:
	- içsel durum (state): birçok nesne arasında tekrarlanan veri içeren alanlar
	- dışsal durum (state): her nesneye özgü bağlamsal verileri içeren alanlar

2. İçsel durumu temsil eden alanları sınıfta bırakın, ancak bunların değişmez olduğundan emin olun. İlk değerlerini yalnızca kurucu içinde almalıdırlar.

3. Dışsal durumu kullanan yöntemleri inceleyin. Her yöntemde kullanılan alan için yeni bir parametre tanıtın ve alan yerine onu kullanın.

4. İsteğe bağlı olarak, flyweight havuzunu yönetmek için bir fabrika sınıfı oluşturun. Yeni bir tane oluşturmadan önce var olan bir flyweight'ı kontrol etmelidir. Fabrika yerine geldiğinde müşteriler yalnızca flyweight'ları üzerinden istemelidir. İstenen flyweight'ı tanımlamak için, içsel durumunu fabrikaya ileterek yapmalıdır.

5. İstemcinin, flyweight nesnelerinin yöntemlerini çağırabilmek için dışsal durumun (bağlam) değerlerini depolaması veya hesaplaması gerekir. Kolaylık sağlamak için, dışsal durum flyweight referans alanı ile birlikte ayrı bir bağlam sınıfına taşınabilir.


##  ⚖️ Artıları ve Eksileri

✅ Programınızda tonlarca benzer nesne bulunduğunu varsayarsak, çok fazla RAM tasarrufu sağlayabilirsiniz.

❌ Birisi bir flyweight yöntemini her çağırdığında bağlam verilerinin bir kısmının yeniden hesaplanması gerektiğinde, CPU döngüleri üzerinden RAM ticareti yapıyor olabilirsiniz.

❌ Kod çok daha karmaşık hale geliyor. Yeni ekip üyeleri her zaman bir varlığın durumunun neden bu şekilde ayrıldığını merak edeceklerdir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Composite** ağaçın paylaşılan yaprak düğümlerini **Flyweight** olarak uygulayarak bazı RAM kullanımını azaltabilirsiniz.

- **Flyweight**, birçok küçük nesne yapmanın nasıl yapılacağını gösterirken, **Facade**, bir tüm sistemini temsil eden tek bir nesne yapmanın nasıl yapılacağını gösterir.

- **Flyweight**, nesnelerin paylaşılan durumlarını yalnızca bir Flyweight nesnesine indirgeme yeteneğiniz olsaydı **Singleton**'a benzeyebilir. Ancak bu desenler arasında iki temel fark bulunmaktadır:
	- Yalnızca bir Singleton örneği olmalıdır, oysa bir Flyweight sınıfının farklı içsel durumları olan birden fazla örneği olabilir.
	- Singleton nesnesi değiştirilebilirken, Flyweight nesneleri değiştirilemez.


  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Flyweight modelinin tek bir amacı vardır: hafıza alımını en aza indirmek. Programınız RAM sıkıntısı çekmiyorsa, bu modeli bir süreliğine görmezden gelebilirsiniz.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.lang.Integer#valueOf(int)`](http://docs.oracle.com/javase/8/docs/api/java/lang/Integer.html#valueOf-int-)  (also  [`Boolean`](http://docs.oracle.com/javase/8/docs/api/java/lang/Boolean.html#valueOf-boolean-),  [`Byte`](http://docs.oracle.com/javase/8/docs/api/java/lang/Byte.html#valueOf-byte-),  [`Character`](http://docs.oracle.com/javase/8/docs/api/java/lang/Character.html#valueOf-char-),  [`Short`](http://docs.oracle.com/javase/8/docs/api/java/lang/Short.html#valueOf-short-),  [`Long`](http://docs.oracle.com/javase/8/docs/api/java/lang/Long.html#valueOf-long-)  and  [`BigDecimal`](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html#valueOf-long-int-))


**Tanımlama:** Flyweight, yeni oluşturmak yerine önbelleğe alınmış nesneleri döndüren bir oluşturma yöntemiyle tanınabilir.

 
####  Bir orman oluşturma


Bu örnekte, 1.000.000 ağaçtan oluşan bir orman oluşturacağız. Her ağaç, bazı durumlara (koordinatlar, doku vb.) sahip olan kendi nesnesiyle temsil edilecektir. Program birincil işini yapsa da doğal olarak çok fazla RAM tüketiyor.

Nedeni basit: Çok fazla ağaç nesnesi yinelenen veriler (ad, doku, renk) içeriyor. Bu nedenle Flyweight modelini uygulayabilir ve bu değerleri ayrı flyweight nesnelerinde (TreeType sınıfı) saklayabiliriz. Şimdi aynı verileri binlerce Ağaç nesnesinde depolamak yerine, belirli bir değer kümesine sahip uçucu nesnelerden birine referans vereceğiz.

Flyweight nesnelerinin yeniden kullanılmasının karmaşıklığı bir flyweight fabrikasının içinde gömülü olduğundan müşteri kodu hiçbir şeyi fark etmeyecektir.

**📁 trees**

⤵️ 📄 `trees/Tree.java` : Her ağaç için benzersiz durumu içerir

```java
package fatihes1.flyweight.example.trees;

import java.awt.*;

public class Tree {
    private int x;
    private int y;
    private TreeType type;

    public Tree(int x, int y, TreeType type) {
        this.x = x;
        this.y = y;
        this.type = type;
    }

    public void draw(Graphics g) {
        type.draw(g, x, y);
    }
}
```

⤵️ 📄 `trees/TreeType.java`: Birkaç ağaç tarafından paylaşılan durumu içerir

```java
package fatihes1.flyweight.example.trees;

import java.awt.*;

public class TreeType {
    private String name;
    private Color color;
    private String otherTreeData;

    public TreeType(String name, Color color, String otherTreeData) {
        this.name = name;
        this.color = color;
        this.otherTreeData = otherTreeData;
    }

    public void draw(Graphics g, int x, int y) {
        g.setColor(Color.BLACK);
        g.fillRect(x - 1, y, 3, 5);
        g.setColor(color);
        g.fillOval(x - 5, y - 10, 10, 10);
    }
}
```


⤵️ 📄 `trees/TreeFactory.java`

```java
package fatihes1.flyweight.example.trees;

import java.awt.*;
import java.util.HashMap;
import java.util.Map;

public class TreeFactory {
    static Map<String, TreeType> treeTypes = new HashMap<>();

    public static TreeType getTreeType(String name, Color color, String otherTreeData) {
        TreeType result = treeTypes.get(name);
        if (result == null) {
            result = new TreeType(name, color, otherTreeData);
            treeTypes.put(name, result);
        }
        return result;
    }
}
```

**📁 forest**

⤵️ 📄 `forest/Forest.java`

```java
package fatihes1.flyweight.example.forest;

import fatihes1.flyweight.example.trees.Tree;
import fatihes1.flyweight.example.trees.TreeFactory;
import fatihes1.flyweight.example.trees.TreeType;

import javax.swing.*;
import java.awt.*;
import java.util.ArrayList;
import java.util.List;

public class Forest extends JFrame {
    private List<Tree> trees = new ArrayList<>();

    public void plantTree(int x, int y, String name, Color color, String otherTreeData) {
        TreeType type = TreeFactory.getTreeType(name, color, otherTreeData);
        Tree tree = new Tree(x, y, type);
        trees.add(tree);
    }

    @Override
    public void paint(Graphics graphics) {
        for (Tree tree : trees) {
            tree.draw(graphics);
        }
    }
}
```


⤵️ 📄 `Demo.java`: İstemci Kodu

```java
package fatihes1.flyweight.example;

import fatihes1.flyweight.example.forest.Forest;

import java.awt.*;

public class Demo {
    static int CANVAS_SIZE = 500;
    static int TREES_TO_DRAW = 1000000;
    static int TREE_TYPES = 2;

    public static void main(String[] args) {
        Forest forest = new Forest();
        for (int i = 0; i < Math.floor(TREES_TO_DRAW / TREE_TYPES); i++) {
            forest.plantTree(random(0, CANVAS_SIZE), random(0, CANVAS_SIZE),
                    "Summer Oak", Color.GREEN, "Oak texture stub");
            forest.plantTree(random(0, CANVAS_SIZE), random(0, CANVAS_SIZE),
                    "Autumn Oak", Color.ORANGE, "Autumn Oak texture stub");
        }
        forest.setSize(CANVAS_SIZE, CANVAS_SIZE);
        forest.setVisible(true);

        System.out.println(TREES_TO_DRAW + " trees drawn");
        System.out.println("---------------------");
        System.out.println("Memory usage:");
        System.out.println("Tree size (8 bytes) * " + TREES_TO_DRAW);
        System.out.println("+ TreeTypes size (~30 bytes) * " + TREE_TYPES + "");
        System.out.println("---------------------");
        System.out.println("Total: " + ((TREES_TO_DRAW * 8 + TREE_TYPES * 30) / 1024 / 1024) +
                "MB (instead of " + ((TREES_TO_DRAW * 38) / 1024 / 1024) + "MB)");
    }

    private static int random(int min, int max) {
        return min + (int) (Math.random() * ((max - min) + 1));
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.png`: Çalıştırma Sonucu

![](https://refactoring.guru/images/patterns/examples/java/flyweight/OutputDemo.png)

⤵️ 📄 `OutputDemo.txt`: RAM kullanım istatistikleri

```
1000000 trees drawn
---------------------
Memory usage:
Tree size (8 bytes) * 1000000
+ TreeTypes size (~30 bytes) * 2
---------------------
Total: 7MB (instead of 36MB)
```
