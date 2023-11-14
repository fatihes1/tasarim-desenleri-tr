#  Hatıra (Memento) Tasarım Deseni

> Diğer Adıyla: Anlık Görüntü (Snapshot)
 

##  💬 Amaç

Memento, uygulama ayrıntılarını açıklamadan bir nesnenin önceki durumunu kaydetmenize ve geri yüklemenize olanak tanıyan davranışsal (behavioral) bir tasarım modelidir.

<div align="center">

![](https://refactoring.guru/images/patterns/content/memento/memento-en-2x.png)

</div>


##  🙁 Problem

Bir metin düzenleyici uygulaması oluşturduğunuzu düşünün. Düzenleyiciniz, basit metin düzenlemenin yanı sıra metni biçimlendirme, satır içi görseller ekleme gibi işlemleri gerçekleştirebilir.

Bir noktada kullanıcıların metin üzerinde gerçekleştirilen işlemleri geri almasına izin vermeye karar verebilirsiniz. Bu özellik, yıllar geçtikçe o kadar yaygınlaştı ki günümüzde insanlar her uygulamanın bu özelliğe sahip olmasını bekliyor. Uygulama için doğrudan yaklaşımı (direct approach) tercih ettiğinizi düşünelim. Uygulama, herhangi bir işlem yapmadan önce tüm nesnelerin durumunu kaydeder ve bir depolama alanında tutar. Daha sonra kullanıcı bir eylemi geri almaya karar verdiğinde uygulama geçmişten en son anlık görüntüyü alır ve bunu tüm nesnelerin durumunu geri yüklemek için kullanır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/problem1-en-2x.png)

*Uygulama, bir işlemi gerçekleştirmeden önce nesnelerin durumunun anlık görüntüsünü kaydeder. Bu anlık görüntü, daha sonra nesneleri önceki durumlarına geri yüklemek için kullanılabilir.*

</div>

Bu durum anlık görüntülerini (snapshot) düşünelim. Tam olarak nasıl bir tane oluştururdunuz? Muhtemelen bir nesnedeki tüm alanları gözden geçirmeniz ve değerlerini depoya kopyalamanız gerekecektir. Ancak bu yalnızca nesnenin içeriğine yönelik oldukça esnek erişim kısıtlamalarına sahip olması durumunda işe yarar. Ne yazık ki çoğu gerçek nesne, diğerlerinin içlerine bu kadar kolay göz atmasına izin vermez ve tüm önemli verileri özel alanlarda gizler.

Şimdilik bu sorunu bir kenara bırakalım ve nesnelerimizin hippiler gibi davrandığını varsayalım: açık ilişkileri tercih ediyor ve durumlarını halka açık (public) tutuyorlar. Bu yaklaşım acil sorunu çözecek ve nesnelerin durumlarının anlık görüntülerini istediğiniz zaman oluşturmanıza olanak tanıyacak olsa da, hala bazı ciddi sorunları vardır. Gelecekte, düzenleyici sınıflarından bazılarını yeniden düzenlemeye veya alanların bazılarını eklemeye veya kaldırmaya karar verebilirsiniz. Kulağa kolay geliyor ama aynı zamanda etkilenen nesnelerin durumunun kopyalanmasından sorumlu sınıfların değiştirilmesini de gerektiriyor.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/problem2-en-2x.png)

*Nesnenin özel durumunun (private state) bir kopyası nasıl oluşturulur?*

</div>

Ama dahası da var. Editörün durumunun gerçek "anlık görüntülerini" ele alalım. Hangi verileri içeriyor? En azından gerçek metni, imleç koordinatları, geçerli kaydırma konumu gibi bilgileri içermelidir. Anlık görüntü oluşturmak için bu değerleri toplamanız ve bunları bir tür kapsayıcıya koymanız gerekir.

Büyük olasılıkla, bu konteyner nesnelerinin çoğunu geçmişi temsil edecek bir listenin içinde depolayacaksınız. Bu nedenle konteynerler muhtemelen tek bir sınıfın nesneleri haline gelecektir. Sınıfın neredeyse hiç yöntemi olmayacak, ancak editörün durumunu yansıtan birçok alan olacaktır. Diğer nesnelerin anlık görüntüye veri yazmasına ve anlık görüntüden veri okumasına izin vermek için muhtemelen alanlarını herkese açık hale getirmeniz gerekir. Bu, özel olsun ya da olmasın, editörün tüm durumlarını açığa çıkarır. Diğer sınıflar, anlık görüntü sınıfındaki her küçük değişikliğe bağımlı hale gelir; aksi takdirde bu, dış sınıfları etkilemeden özel alanlar ve yöntemler içinde gerçekleşir.

Görünüşe göre bir çıkmaza girmişiz: Ya sınıfların tüm iç ayrıntılarını açığa çıkararak onları çok kırılgan hale getirirsiniz ya da durumlarına erişimi kısıtlayarak anlık görüntü oluşturmayı imkansız hale getirirsiniz. "Geri al" işlemini uygulamanın başka bir yolu var mı?


##  😊 Çözüm


Az önce yaşadığımız tüm sorunlar kapsüllemenin (encapsulation) bozulmasından kaynaklanıyor. Bazı nesneler yapmaları gerekenden daha fazlasını yapmaya çalışır. Bir eylemi gerçekleştirmek için gereken verileri toplamak amacıyla, bu nesnelerin gerçek eylemi gerçekleştirmesine izin vermek yerine diğer nesnelerin özel alanlarını istila ederler.

Memento modeli, durum anlık görüntülerinin oluşturulmasını o durumun gerçek sahibine, yani yaratıcı nesneye devreder. Dolayısıyla, editörün durumunu “dışarıdan” kopyalamaya çalışan diğer nesneler yerine, kendi durumuna tam erişime sahip olduğu için editör sınıfının kendisi anlık görüntüyü oluşturabilir.

Desen, nesnenin durumunun kopyasının hatıra (memento) adı verilen özel bir nesnede saklanmasını önerir. Hatıranın içeriğine, onu üreten nesne dışında başka hiçbir nesne erişemez. Diğer nesnelerin, anlık görüntünün meta verilerinin (oluşturulma zamanı, gerçekleştirilen işlemin adı vb.) getirilmesine izin verebilecek, ancak anlık görüntüde yer alan orijinal nesnenin durumunu getiremeyen sınırlı bir arayüz kullanarak hatıralarla iletişim kurması gerekir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/solution-en-2x.png)

*Oluşturan, hatıraya tam erişime sahipken bakıcı yalnızca meta verilere erişebilir.*

</div>

Bu kadar kısıtlayıcı bir politika, hatıraları genellikle bakıcı (caretakers) olarak adlandırılan diğer nesnelerin içinde saklamanıza olanak tanır. Bakıcı, hatıra ile yalnızca sınırlı arayüz aracılığıyla çalıştığından, hatıranın içinde saklanan duruma müdahale edemez. Aynı zamanda, yaratıcının hatıranın içindeki tüm alanlara erişimi vardır ve bu da onun istediği zaman önceki durumuna geri dönmesine olanak sağlar.

Metin düzenleyici örneğimizde, bakıcı görevi görecek ayrı bir memento sınıfı oluşturabiliriz. Editörün bir işlemi yürütmek üzere olduğu her seferde, bakıcının içinde saklanan bir memento yığını büyüyecektir. Hatta bu yığını uygulamanın kullanıcı arayüzünde oluşturup daha önce gerçekleştirilen işlemlerin geçmişini kullanıcıya görüntüleyebilirsiniz.

Bir kullanıcı geri alma işlemini tetiklediğinde, geçmiş, yığından en son hatırayı alır ve onu geri alma talebinde bulunarak editöre geri iletir. Editör hatıraya tam erişime sahip olduğundan hatıradan alınan değerlerle kendi durumunu değiştirir.


##  ⚙️ Yapı

#### İç içe geçmiş sınıflara dayalı uygulama

Desenin klasik uygulaması, birçok popüler programlama dilinde (C++, C# ve Java gibi) mevcut olan iç içe geçmiş sınıfların desteğine dayanır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/structure1-2x.png)

</div>

1. **Originator** sınıfı, kendi durumunun anlık görüntülerini üretebildiği gibi gerektiğinde anlık görüntülerden durumunu da geri yükleyebilir.

2. **Memento**, yaratıcının durumunun anlık görüntüsü görevi gören bir değer nesnesidir. Hatıranın değişmez hale getirilmesi ve verilerin yapıcı aracılığıyla yalnızca bir kez iletilmesi yaygın bir uygulamadır.

3. **Bakıcı (Caretaker)** yalnızca yaratıcının durumunu "ne zaman" ve "neden" yakalayacağını değil, aynı zamanda durumun ne zaman geri getirilmesi gerektiğini de bilir.
Bir bakıcı, bir yığın hatıra saklayarak, yaratıcının geçmişini takip edebilir. Yaratıcının tarihte geriye gitmesi gerektiğinde, bakıcı yığından en üstteki hatırayı alır ve onu yaratıcının restorasyon yöntemine iletir.

4. Bu uygulamada, hatıra sınıfı, oluşturucunun içine yerleştirilmiştir. Bu, özel olarak bildirilmiş olsalar bile, yaratıcının hatıranın alanlarına ve yöntemlerine erişmesine olanak tanır. Öte yandan, bakıcının hatıra alanlarına ve yöntemlerine çok sınırlı erişimi vardır, bu da hatıraları bir yığın halinde saklamasına ancak durumlarını değiştirmemesine olanak tanır.

#### Ara arayüze dayalı uygulama

İç içe geçmiş sınıfları desteklemeyen programlama dilleri için uygun alternatif bir uygulama var (evet, PHP, senden bahsediyoruz ☹️ ).

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/structure2-2x.png)

</div>

1. Yuvalanmış sınıfların yokluğunda, bakıcıların yalnızca açıkça beyan edilen bir aracı arayüz aracılığıyla bir hatıra ile çalışabileceği ve yalnızca hatıranın meta verileriyle ilgili yöntemlerin bildirileceği bir kural oluşturarak hatıra alanlarına erişimi kısıtlayabilirsiniz.

2. Öte yandan, yaratıcılar memento sınıfında bildirilen alanlara ve yöntemlere erişerek doğrudan bir memento nesnesiyle çalışabilirler. Bu yaklaşımın dezavantajı, hatıranın tüm üyelerini kamuya açıklamanız gerekmesidir.

#### Daha sıkı kapsülleme ile uygulama

Hatıra yoluyla diğer sınıfların yaratıcının durumuna erişmesine en ufak bir şans bile bırakmak istemediğinizde yararlı olan başka bir uygulama daha var.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/structure3-2x.png)

</div>

1. Bu uygulama, birden fazla yaratıcı ve hatıra türüne sahip olmanıza olanak tanır. Her yaratıcı, karşılık gelen bir hatıra sınıfıyla çalışır. Ne yaratıcılar ne de hatıralar durumlarını kimseye ifşa etmez.

2. Bakıcıların (Caretakers) artık hatıralarda saklanan durumu değiştirmeleri açıkça kısıtlanmıştır. Üstelik restorasyon yöntemi artık hatıra sınıfında tanımlandığı için bakıcı sınıfı, yaratıcıdan bağımsız hale geliyor.

3. Her hatıra (memento), onu üreten yaratıcıya bağlanır. Oluşturucu, durumunun değerleriyle birlikte kendisini hatıranın yapıcısına aktarır. Bu sınıflar arasındaki yakın ilişki sayesinde, bir hatıra, yaratıcısının uygun ayarlayıcıları tanımlamış olması koşuluyla, yaratıcısının durumunu geri yükleyebilir.


##  💻 Sözde Kod (Pseudocode)

Bu örnek, karmaşık metin düzenleyicisinin durumunun anlık görüntülerini depolamak ve gerektiğinde bu anlık görüntülerden daha önceki bir durumu geri yüklemek için Komut modelinin yanında Memento modelini kullanır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/memento/example-2x.png)

*Metin düzenleyicinin durumunun anlık görüntüleri kaydediliyor.*

</div>

Komut nesneleri bakıcı görevi görür. Komutlarla ilgili işlemleri yürütmeden önce editörün hatırasını alırlar. Bir kullanıcı en son komutu geri almaya çalıştığında, editör kendisini önceki duruma döndürmek için o komutta saklanan hatırayı kullanabilir.

Hatıra sınıfı herhangi bir genel alanı, alıcıyı veya ayarlayıcıyı bildirmez. Bu nedenle hiçbir nesne içeriğini değiştiremez. Hatıralar, onları oluşturan düzenleyici nesneye bağlıdır. Bu, bir hatıranın, verileri düzenleyici nesnesindeki ayarlayıcılar aracılığıyla ileterek bağlantılı düzenleyicinin durumunu geri yüklemesine olanak tanır. Hatıralar belirli düzenleyici nesnelere bağlı olduğundan, uygulamanızın merkezi bir geri alma yığınıyla birkaç bağımsız düzenleyici penceresini desteklemesini sağlayabilirsiniz.

```java
// Oluşturucu, zaman içinde değişebilecek önemli verilere sahip olan
// originator'ü temsil eder. Ayrıca durumu bir memento içinde kaydetmek
// için bir yöntem tanımlar ve bu durumu ondan geri yüklemek için başka bir yöntem tanımlar.
class Editor is
    private field text, curX, curY, selectionWidth

    method setText(text) is
        this.text = text

    method setCursor(x, y) is
        this.curX = x
        this.curY = y

    method setSelectionWidth(width) is
        this.selectionWidth = width

    // Mevcut durumu bir memento içinde kaydeder.
    method createSnapshot():Snapshot is
        // Memento, değiştirilemez bir nesnedir; bu nedenle
        // originator, durumunu memento'nun yapıcı parametrelerine aktarır.
        return new Snapshot(this, text, curX, curY, selectionWidth)

// Memento sınıfı, editörün önceki durumunu saklar.
class Snapshot is
    private field editor: Editor
    private field text, curX, curY, selectionWidth

    constructor Snapshot(editor, text, curX, curY, selectionWidth) is
        this.editor = editor
        this.text = text
        this.curX = x
        this.curY = y
        this.selectionWidth = selectionWidth

    // Belirli bir noktada, bir memento nesnesi kullanılarak
    // editörün önceki bir durumu geri yüklenebilir.
    method restore() is
        editor.setText(text)
        editor.setCursor(curX, curY)
        editor.setSelectionWidth(selectionWidth)

// Bir komut nesnesi bir bakıcı olarak hareket edebilir. Bu durumda,
// komut, originator'ün durumunu değiştirmeden önce bir memento alır.
// Geri alma istendiğinde, originator'ün durumunu bir memento'dan geri yükler.
class Command is
    private field backup: Snapshot

    method makeBackup() is
        backup = editor.createSnapshot()

    method undo() is
        if (backup != null)
            backup.restore()
    // ...

```


##  💡Uygulanabilirlik


**🐞 Nesnenin önceki durumunu geri yükleyebilmek için nesnenin durumunun anlık görüntülerini oluşturmak istediğinizde Memento modelini kullanın.**

⚡️ Memento modeli, özel alanlar da dahil olmak üzere bir nesnenin durumunun tam kopyalarını oluşturmanıza ve bunları nesneden ayrı olarak saklamanıza olanak tanır. Çoğu kişi bu modeli "geri alma" kullanım durumu sayesinde hatırlasa da, işlemlerle uğraşırken de bu model vazgeçilmezdir (örneğin, bir işlemi hata durumunda geri almanız gerekiyorsa).

----------------

**🐞 Nesnenin alanlarına/alıcılarına/ayarlayıcılarına doğrudan erişim, kapsüllemeyi ihlal ettiğinde bu modeli kullanın.**

⚡️ Memento, nesnenin durumunun anlık görüntüsünü oluşturmaktan kendisini sorumlu kılar. Başka hiçbir nesne anlık görüntüyü okuyamaz, bu da orijinal nesnenin durum verilerinin güvenli ve emniyetli olmasını sağlar.
  

##  📝 Nasıl Uygulanır?

Nasıl Uygulanır

1.  Hangi sınıfın yaratıcı rolünü oynayacağını belirleyin. Programın bu türden tek bir merkezi nesneyi mi yoksa birden fazla küçük nesneyi mi kullandığını bilmek önemlidir.
    
2.  Memento sınıfını oluşturun. Sırayla, originator sınıfında bildirilen alanları yansıtan bir dizi alanı taımlayın.
    
3.  Memento sınıfını değişmez (immutable) hale getirin. Bir memento yalnızca bir kez, yapıcı yöntemi aracılığıyla verileri kabul etmelidir. Sınıfta ayarlayıcı olmamalıdır.
    
4.  Programlama diliniz gömülü sınıfları destekliyorsa, memento sınıfını yaratıcı içine gömün. Aksi takdirde, memento sınıfından boş bir arabirim çıkarın ve diğer nesnelerin mementoya başvurmak için bu arabirimi kullanmasını sağlayın. Arabirime bazı meta veri işlemleri ekleyebilirsiniz, ancak yaratıcının durumunu ortaya çıkarmayan işlemler eklemeyin.
    
5.  Memento üretebilme yöntemi ekleyin. Yaratıcı, durumunu memento'ya bir veya birden çok memento yapıcısının argümanı aracılığıyla aktarmalıdır.
    
6.  Yöntemin dönüş türü, önceki adımda çıkardığınız arabirim olmalıdır (eğer çıkardıysanız). Alt yapıda, memento üretme yöntemi doğrudan memento sınıfı ile çalışmalıdır.
    
7.  Yaratıcı sınıfının durumunu geri yüklemek için bir yöntem ekleyin. Bir memento nesnesini bir argüman olarak kabul etmelidir. Önceki adımda bir arabirim çıkardıysanız, bu arabirimin türünü parametre türü yapın. Bu durumda, gelen nesneyi memento sınıfına dönüştürmeniz gerekecektir, çünkü yaratıcı nesnenin bu nesneye tam erişime ihtiyacı vardır.
    
8.  Bakıcı, bir komut nesnesini, bir geçmişi veya tamamen farklı bir şeyi temsil edip etmediğine bakılmaksızın, ne zaman yeni memento talep etmesi gerektiğini, nasıl depolayacağını ve belirli bir memento ile yaratıcı ne zaman geri yüklemesi gerektiğini bilmelidir.
    
9.  Bakıcılar ve yaratıcı arasındaki bağlantı, memento sınıfına taşınabilir. Bu durumda, her memento, onu oluşturan yaratıcıya bağlı olmalıdır. Geri yükleme yöntemi de memento sınıfına taşınacaktır. Ancak bu, memento sınıfının yaratıcı tarafından sağlanan bir durumun üzerine yazılmasına yönelik yeterli ayarlayıcılar sunuyorsa anlamlı olur.

##  ⚖️ Artıları ve Eksileri

✅ Kapsüllemeyi ihlal etmeden nesnenin durumunun anlık görüntülerini oluşturabilirsiniz.

✅ Sorumlunun, oluşturucunun durumunun geçmişini tutmasına izin vererek, oluşturucunun kodunu basitleştirebilirsiniz.


❌ İstemciler çok sık hatıra oluşturursa uygulama çok fazla RAM tüketebilir.

❌ Bakıcılar, eski hatıraları yok edebilmek için yaratıcının yaşam döngüsünü takip etmelidir.

❌ PHP, Python ve JavaScript gibi çoğu dinamik programlama dili, hatıra içindeki durumun değişmeden kalacağını garanti edemez.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- Geri alma işlemini gerçekleştirirken **Command** ve **Memento**'yu birlikte kullanabilirsiniz. Bu durumda, komutlar bir hedef nesne üzerinde çeşitli işlemlerin gerçekleştirilmesinden sorumludur; hatıralar ise bir komut yürütülmeden hemen önce o nesnenin durumunu kaydeder.

- Mevcut yineleme durumunu yakalamak ve gerekirse geri almak için **Memento**'yu **Iterator** ile birlikte kullanabilirsiniz.

- Bazen **Prototip**, **Memento**'ya daha basit bir alternatif olabilir. Bu, durumunu geçmişte depolamak istediğiniz nesnenin oldukça basit olması ve dış kaynaklara bağlantıları olmaması veya bağlantıların yeniden kurulmasının kolay olması durumunda işe yarar.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Memento ilkesine Java'da oldukça yaygın olan serileştirme kullanılarak ulaşılabilir. Bir nesnenin durumunun anlık görüntüsünü almanın tek ve en etkili yolu olmasa da, yine de durumu oluşturan yedeklerin depolanmasına izin verirken, oluşturucunun yapısını diğer nesnelerden korur.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   All  [`java.io.Serializable`](http://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)  implementation'ları Memento'yu simüle edebilir..
-   All  [`javax.faces.component.StateHolder`](http://docs.oracle.com/javaee/7/api/javax/faces/component/StateHolder.html)  implementation'ları.
 
####  Şekil düzenleyici ve karmaşık geri alma/yeniden yapma

Bu grafik düzenleyici, ekrandaki şekillerin renginin ve konumunun değiştirilmesine olanak tanır. Bununla beraber herhangi bir değişiklik geri alınabilir ve tekrarlanabilir.

"Geri alma", Memento ve Komut kalıpları arasındaki işbirliğine dayanır. Düzenleyici, gerçekleştirilen komutların geçmişini izler. Herhangi bir komutu çalıştırmadan önce yedekleme yapar ve onu komut nesnesine bağlar. Yürütmeden sonra, yürütülen komutu geçmişe iter.

Bir kullanıcı geri alma talebinde bulunduğunda, editör geçmişten yeni bir komut alır ve durumu bu komutun içinde tutulan yedekten geri yükler. Kullanıcı başka bir geri alma talebinde bulunursa, editör geçmişten aşağıdaki komutu alır ve bu şekilde devam eder.

Geri alınan komutlar, kullanıcı ekrandaki şekillerde bazı değişiklikler yapana kadar geçmişte tutulur. Bu, geri alınan komutların yeniden yapılması için çok önemlidir.

**📁 editor**

⤵️ 📄 `editor/Editor.java`: Editor kodu

```java
package fatihes1.memento.example.editor;

import fatihes1.memento.example.commands.Command;
import fatihes1.memento.example.history.History;
import fatihes1.memento.example.history.Memento;
import fatihes1.memento.example.shapes.CompoundShape;
import fatihes1.memento.example.shapes.Shape;

import javax.swing.*;
import java.io.*;
import java.util.Base64;

public class Editor extends JComponent {
    private Canvas canvas;
    private CompoundShape allShapes = new CompoundShape();
    private History history;

    public Editor() {
        canvas = new Canvas(this);
        history = new History();
    }

    public void loadShapes(Shape... shapes) {
        allShapes.clear();
        allShapes.add(shapes);
        canvas.refresh();
    }

    public CompoundShape getShapes() {
        return allShapes;
    }

    public void execute(Command c) {
        history.push(c, new Memento(this));
        c.execute();
    }

    public void undo() {
        if (history.undo())
            canvas.repaint();
    }

    public void redo() {
        if (history.redo())
            canvas.repaint();
    }

    public String backup() {
        try {
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(baos);
            oos.writeObject(this.allShapes);
            oos.close();
            return Base64.getEncoder().encodeToString(baos.toByteArray());
        } catch (IOException e) {
            return "";
        }
    }

    public void restore(String state) {
        try {
            byte[] data = Base64.getDecoder().decode(state);
            ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(data));
            this.allShapes = (CompoundShape) ois.readObject();
            ois.close();
        } catch (ClassNotFoundException e) {
            System.out.print("ClassNotFoundException occurred.");
        } catch (IOException e) {
            System.out.print("IOException occurred.");
        }
    }
}
```

⤵️ 📄 `editor/Canvas.java`: Canvas kodu

```java
package fatihes1.memento.example.editor;

import fatihes1.memento.example.commands.ColorCommand;
import fatihes1.memento.example.commands.MoveCommand;
import fatihes1.memento.example.shapes.Shape;

import javax.swing.*;
import javax.swing.border.Border;
import java.awt.*;
import java.awt.event.*;
import java.awt.image.BufferedImage;

class Canvas extends java.awt.Canvas {
    private Editor editor;
    private JFrame frame;
    private static final int PADDING = 10;

    Canvas(Editor editor) {
        this.editor = editor;
        createFrame();
        attachKeyboardListeners();
        attachMouseListeners();
        refresh();
    }

    private void createFrame() {
        frame = new JFrame();
        frame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        frame.setLocationRelativeTo(null);

        JPanel contentPanel = new JPanel();
        Border padding = BorderFactory.createEmptyBorder(PADDING, PADDING, PADDING, PADDING);
        contentPanel.setBorder(padding);
        contentPanel.setLayout(new BoxLayout(contentPanel, BoxLayout.Y_AXIS));
        frame.setContentPane(contentPanel);

        contentPanel.add(new JLabel("Select and drag to move."), BorderLayout.PAGE_END);
        contentPanel.add(new JLabel("Right click to change color."), BorderLayout.PAGE_END);
        contentPanel.add(new JLabel("Undo: Ctrl+Z, Redo: Ctrl+R"), BorderLayout.PAGE_END);
        contentPanel.add(this);
        frame.setVisible(true);
        contentPanel.setBackground(Color.LIGHT_GRAY);
    }

    private void attachKeyboardListeners() {
        addKeyListener(new KeyAdapter() {
            @Override
            public void keyPressed(KeyEvent e) {
                if ((e.getModifiers() & KeyEvent.CTRL_MASK) != 0) {
                    switch (e.getKeyCode()) {
                        case KeyEvent.VK_Z:
                            editor.undo();
                            break;
                        case KeyEvent.VK_R:
                            editor.redo();
                            break;
                    }
                }
            }
        });
    }

    private void attachMouseListeners() {
        MouseAdapter colorizer = new MouseAdapter() {
            @Override
            public void mousePressed(MouseEvent e) {
                if (e.getButton() != MouseEvent.BUTTON3) {
                    return;
                }
                Shape target = editor.getShapes().getChildAt(e.getX(), e.getY());
                if (target != null) {
                    editor.execute(new ColorCommand(editor, new Color((int) (Math.random() * 0x1000000))));
                    repaint();
                }
            }
        };
        addMouseListener(colorizer);

        MouseAdapter selector = new MouseAdapter() {
            @Override
            public void mousePressed(MouseEvent e) {
                if (e.getButton() != MouseEvent.BUTTON1) {
                    return;
                }

                Shape target = editor.getShapes().getChildAt(e.getX(), e.getY());
                boolean ctrl = (e.getModifiers() & ActionEvent.CTRL_MASK) == ActionEvent.CTRL_MASK;

                if (target == null) {
                    if (!ctrl) {
                        editor.getShapes().unSelect();
                    }
                } else {
                    if (ctrl) {
                        if (target.isSelected()) {
                            target.unSelect();
                        } else {
                            target.select();
                        }
                    } else {
                        if (!target.isSelected()) {
                            editor.getShapes().unSelect();
                        }
                        target.select();
                    }
                }
                repaint();
            }
        };
        addMouseListener(selector);


        MouseAdapter dragger = new MouseAdapter() {
            MoveCommand moveCommand;

            @Override
            public void mouseDragged(MouseEvent e) {
                if ((e.getModifiersEx() & MouseEvent.BUTTON1_DOWN_MASK) != MouseEvent.BUTTON1_DOWN_MASK) {
                    return;
                }
                if (moveCommand == null) {
                    moveCommand = new MoveCommand(editor);
                    moveCommand.start(e.getX(), e.getY());
                }
                moveCommand.move(e.getX(), e.getY());
                repaint();
            }

            @Override
            public void mouseReleased(MouseEvent e) {
                if (e.getButton() != MouseEvent.BUTTON1 || moveCommand == null) {
                    return;
                }
                moveCommand.stop(e.getX(), e.getY());
                editor.execute(moveCommand);
                this.moveCommand = null;
                repaint();
            }
        };
        addMouseListener(dragger);
        addMouseMotionListener(dragger);
    }

    public int getWidth() {
        return editor.getShapes().getX() + editor.getShapes().getWidth() + PADDING;
    }

    public int getHeight() {
        return editor.getShapes().getY() + editor.getShapes().getHeight() + PADDING;
    }

    void refresh() {
        this.setSize(getWidth(), getHeight());
        frame.pack();
    }

    public void update(Graphics g) {
        paint(g);
    }

    public void paint(Graphics graphics) {
        BufferedImage buffer = new BufferedImage(this.getWidth(), this.getHeight(), BufferedImage.TYPE_INT_RGB);
        Graphics2D ig2 = buffer.createGraphics();
        ig2.setBackground(Color.WHITE);
        ig2.clearRect(0, 0, this.getWidth(), this.getHeight());

        editor.getShapes().paint(buffer.getGraphics());

        graphics.drawImage(buffer, 0, 0, null);
    }
}
```

**📁 history**

⤵️ 📄 `history/History.java`: Geçmiş, komutları ve mementoları saklar

```java
package fatihes1.memento.example.history;

import fatihes1.memento.example.commands.Command;

import java.util.ArrayList;
import java.util.List;

public class History {
    private List<Pair> history = new ArrayList<Pair>();
    private int virtualSize = 0;

    private class Pair {
        Command command;
        Memento memento;
        Pair(Command c, Memento m) {
            command = c;
            memento = m;
        }

        private Command getCommand() {
            return command;
        }

        private Memento getMemento() {
            return memento;
        }
    }

    public void push(Command c, Memento m) {
        if (virtualSize != history.size() && virtualSize > 0) {
            history = history.subList(0, virtualSize - 1);
        }
        history.add(new Pair(c, m));
        virtualSize = history.size();
    }

    public boolean undo() {
        Pair pair = getUndo();
        if (pair == null) {
            return false;
        }
        System.out.println("Undoing: " + pair.getCommand().getName());
        pair.getMemento().restore();
        return true;
    }

    public boolean redo() {
        Pair pair = getRedo();
        if (pair == null) {
            return false;
        }
        System.out.println("Redoing: " + pair.getCommand().getName());
        pair.getMemento().restore();
        pair.getCommand().execute();
        return true;
    }

    private Pair getUndo() {
        if (virtualSize == 0) {
            return null;
        }
        virtualSize = Math.max(0, virtualSize - 1);
        return history.get(virtualSize);
    }

    private Pair getRedo() {
        if (virtualSize == history.size()) {
            return null;
        }
        virtualSize = Math.min(history.size(), virtualSize + 1);
        return history.get(virtualSize - 1);
    }
}
```

⤵️ 📄 `history/Memento.java`: Memento Sınıfı

```java
package fatihes1.memento.example.history;

import fatihes1.memento.example.editor.Editor;

public class Memento {
    private String backup;
    private Editor editor;

    public Memento(Editor editor) {
        this.editor = editor;
        this.backup = editor.backup();
    }

    public void restore() {
        editor.restore(backup);
    }
}
```

**📁 commands**

⤵️ 📄 `commands/Command.java`: Temel komut (command) sınıfı

```java
package fatihes1.memento.example.commands;

public interface Command {
    String getName();
    void execute();
}
```

⤵️ 📄 `commands/ColorCommand.java`: Seçilen şeklin rengini değiştirir

```java
package fatihes1.memento.example.commands;

import fatihes1.memento.example.editor.Editor;
import fatihes1.memento.example.shapes.Shape;

import java.awt.*;

public class ColorCommand implements Command {
    private Editor editor;
    private Color color;

    public ColorCommand(Editor editor, Color color) {
        this.editor = editor;
        this.color = color;
    }

    @Override
    public String getName() {
        return "Colorize: " + color.toString();
    }

    @Override
    public void execute() {
        for (Shape child : editor.getShapes().getSelected()) {
            child.setColor(color);
        }
    }
}
```

⤵️ 📄 `commands/MoveCommand.java`: Seçilen şekli taşır

```java
package fatihes1.memento.example.commands;

import fatihes1.memento.example.editor.Editor;
import fatihes1.memento.example.shapes.Shape;

public class MoveCommand implements Command {
    private Editor editor;
    private int startX, startY;
    private int endX, endY;

    public MoveCommand(Editor editor) {
        this.editor = editor;
    }

    @Override
    public String getName() {
        return "Move by X:" + (endX - startX) + " Y:" + (endY - startY);
    }

    public void start(int x, int y) {
        startX = x;
        startY = y;
        for (Shape child : editor.getShapes().getSelected()) {
            child.drag();
        }
    }

    public void move(int x, int y) {
        for (Shape child : editor.getShapes().getSelected()) {
            child.moveTo(x - startX, y - startY);
        }
    }

    public void stop(int x, int y) {
        endX = x;
        endY = y;
        for (Shape child : editor.getShapes().getSelected()) {
            child.drop();
        }
    }

    @Override
    public void execute() {
        for (Shape child : editor.getShapes().getSelected()) {
            child.moveBy(endX - startX, endY - startY);
        }
    }
}
```

**📁 shapes**: Çeşitli şekiller

⤵️ 📄 `shapes/Shape.java`

```java
package fatihes1.memento.example.shapes;

import java.awt.*;
import java.io.Serializable;

public interface Shape extends Serializable {
    int getX();
    int getY();
    int getWidth();
    int getHeight();
    void drag();
    void drop();
    void moveTo(int x, int y);
    void moveBy(int x, int y);
    boolean isInsideBounds(int x, int y);
    Color getColor();
    void setColor(Color color);
    void select();
    void unSelect();
    boolean isSelected();
    void paint(Graphics graphics);
}
```

⤵️ 📄 `shapes/BaseShape.java`

```java
package fatihes1.memento.example.shapes;

import java.awt.*;

public abstract class BaseShape implements Shape {
    int x, y;
    private int dx = 0, dy = 0;
    private Color color;
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
    public void drag() {
        dx = x;
        dy = y;
    }

    @Override
    public void moveTo(int x, int y) {
        this.x = dx + x;
        this.y = dy + y;
    }

    @Override
    public void moveBy(int x, int y) {
        this.x += x;
        this.y += y;
    }

    @Override
    public void drop() {
        this.x = dx;
        this.y = dy;
    }

    @Override
    public boolean isInsideBounds(int x, int y) {
        return x > getX() && x < (getX() + getWidth()) &&
                y > getY() && y < (getY() + getHeight());
    }

    @Override
    public Color getColor() {
        return color;
    }

    @Override
    public void setColor(Color color) {
        this.color = color;
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

⤵️ 📄 `shapes/Circle.java`

```java
package fatihes1.memento.example.shapes;

import java.awt.*;

public class Circle extends BaseShape {
    private int radius;

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

    @Override
    public void paint(Graphics graphics) {
        super.paint(graphics);
        graphics.drawOval(x, y, getWidth() - 1, getHeight() - 1);
    }
}
```

⤵️ 📄 `shapes/Dot.java`

```java
package fatihes1.memento.example.shapes;

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

⤵️ 📄 `shapes/Rectangle.java`

```java
package fatihes1.memento.example.shapes;

import java.awt.*;

public class Rectangle extends BaseShape {
    private int width;
    private int height;

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

⤵️ 📄 `shapes/CompoundShape.java`

```java
package fatihes1.memento.example.shapes;

import java.awt.*;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CompoundShape extends BaseShape {
    private List<Shape> children = new ArrayList<>();

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
    public void drag() {
        for (Shape child : children) {
            child.drag();
        }
    }

    @Override
    public void drop() {
        for (Shape child : children) {
            child.drop();
        }
    }

    @Override
    public void moveTo(int x, int y) {
        for (Shape child : children) {
            child.moveTo(x, y);
        }
    }

    @Override
    public void moveBy(int x, int y) {
        for (Shape child : children) {
            child.moveBy(x, y);
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
    public void setColor(Color color) {
        super.setColor(color);
        for (Shape child : children) {
            child.setColor(color);
        }
    }

    @Override
    public void unSelect() {
        super.unSelect();
        for (Shape child : children) {
            child.unSelect();
        }
    }

    public Shape getChildAt(int x, int y) {
        for (Shape child : children) {
            if (child.isInsideBounds(x, y)) {
                return child;
            }
        }
        return null;
    }

    public boolean selectChildAt(int x, int y) {
        Shape child = getChildAt(x,y);
        if (child != null) {
            child.select();
            return true;
        }
        return false;
    }

    public List<Shape> getSelected() {
        List<Shape> selected = new ArrayList<>();
        for (Shape child : children) {
            if (child.isSelected()) {
                selected.add(child);
            }
        }
        return selected;
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


⤵️ 📄 `Demo.java`: Initialization kodu

```java
package fatihes1.memento.example;

import fatihes1.memento.example.editor.Editor;
import fatihes1.memento.example.shapes.Circle;
import fatihes1.memento.example.shapes.CompoundShape;
import fatihes1.memento.example.shapes.Dot;
import fatihes1.memento.example.shapes.Rectangle;

import java.awt.*;

public class Demo {
    public static void main(String[] args) {
        Editor editor = new Editor();
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

⤵️ 📄 `OutputDemo.png`: Çalışma Çıktısı

![](https://refactoring.guru/images/patterns/examples/java/memento/OutputDemo.png)

