#  Arabulucu (Mediator) Tasarım Deseni

> Diğer Adıyla: Aracı (Intermediary), Denetleyici (Controller)


##  💬 Amaç

Mediator, nesneler arasındaki kaotik bağımlılıkları azaltmanıza olanak tanıyan davranışsal bir tasarım modelidir. Desen, nesneler arasındaki doğrudan iletişimi kısıtlar ve onları yalnızca bir aracı nesne aracılığıyla işbirliği yapmaya zorlar.


![](https://refactoring.guru/images/patterns/content/mediator/mediator-2x.png)


##  🙁 Problem

Müşteri profilleri oluşturmak ve düzenlemek için bir iletişim kutunuzun olduğunu varsayalım. Metin alanları, onay kutuları, düğmeler gibi çeşitli form kontrollerinden oluşur.


![](https://refactoring.guru/images/patterns/diagrams/mediator/problem1-en-2x.png)

*Uygulama geliştikçe kullanıcı arayüzünün öğeleri arasındaki ilişkiler kaotik hale gelebilir.*

Form öğelerinden bazıları diğerleriyle etkileşime girebilir. Örneğin, "Bir köpeğim var" onay kutusunun seçilmesi, köpeğin adının girilmesi için gizli bir metin alanının ortaya çıkmasına neden olabilir. Başka bir örnek, verileri kaydetmeden önce tüm alanların değerlerini doğrulaması gereken (validation) gönder düğmesidir.

![](https://refactoring.guru/images/patterns/diagrams/mediator/problem2-2x.png)

*Elementlerin diğer elementlerle birçok ilişkisi olabilir. Bu nedenle, bazı öğelerde yapılan değişiklikler diğerlerini etkileme ihtimali yüksektir.*

Bu mantığın doğrudan form öğelerinin kodunun içine uygulanmasını sağlayarak, bu öğelerin sınıflarının uygulamanın diğer formlarında yeniden kullanılmasını çok daha zorlaştırırsınız. Örneğin, köpeğin metin alanına bağlı olduğundan bu onay kutusu sınıfını başka bir formda kullanamazsınız. Profil formunun oluşturulmasında yer alan tüm sınıfları kullanabilir veya hiçbirini kullanamazsınız.

##  😊 Çözüm

Mediator modeli, birbirinden bağımsız kılmak istediğiniz bileşenler arasındaki tüm doğrudan iletişimi kesmeniz gerektiğini önerir. Bunun yerine, bu bileşenlerin, çağrıları uygun bileşenlere yönlendiren özel bir aracı nesneyi çağırarak dolaylı olarak işbirliği yapması gerekir. Sonuç olarak bileşenler, düzinelerce meslektaşına bağlanmak yerine yalnızca tek bir aracı sınıfa bağımlıdır.

Profil düzenleme formu içeren örneğimizde, diyalog sınıfının kendisi aracı olarak hareket edebilir. Büyük olasılıkla, diyalog sınıfı zaten tüm alt öğelerinin farkındadır, dolayısıyla bu sınıfa yeni bağımlılıklar eklemenize bile gerek kalmayacaktır.

![](https://refactoring.guru/images/patterns/diagrams/mediator/solution1-en-2x.png)

*Kullanıcı arayüzü öğeleri, aracı nesne aracılığıyla dolaylı olarak iletişim kurmalıdır.*

En önemli değişiklik asıl form öğelerinde meydana gelir. Gönder düğmesini ele alalım. Önceden, bir kullanıcı düğmeye her tıkladığında, tüm bireysel form öğelerinin değerlerini doğrulamak zorundaydı. Artık tek işi, tıklama hakkında iletişim kutusunu bilgilendirmektir. Bu bildirimi aldıktan sonra iletişim kutusunun kendisi doğrulamaları gerçekleştirir veya görevi ayrı ayrı öğelere aktarır. Böylece, bir düzine form öğesine bağlı olmak yerine düğme yalnızca diyalog sınıfına bağımlıdır.

Daha da ileri gidebilir ve tüm diyalog türleri için ortak arayüzü oluşturarak bağımlılığı daha da gevşetebilirsiniz. Arayüz, tüm form öğelerinin, bu öğelerin başına gelen olaylar hakkında iletişim kutusunu bilgilendirmek için kullanabileceği bildirim yöntemini tanımlar. Bu nedenle, gönder düğmemiz artık bu arayüzü uygulayan herhangi bir iletişim kutusuyla çalışabilmelidir.

Bu şekilde Mediator modeli, çeşitli nesneler arasındaki karmaşık ilişkiler ağını tek bir aracı nesne içinde kapsüllemenize olanak tanır. Bir sınıfın bağımlılığı ne kadar az olursa, o sınıfı değiştirmek, genişletmek veya yeniden kullanmak o kadar kolay olur.

## 🚙 Gerçek Dünya Örneği

![](https://refactoring.guru/images/patterns/diagrams/mediator/live-example-2x.png)

*Uçak pilotları, uçağını bir sonraki kimin indireceğine karar verirken birbirleriyle doğrudan konuşmazlar. Tüm iletişim kontrol kulesinden geçer.*

Havaalanı kontrol alanına yaklaşan veya ayrılan uçak pilotları birbirleriyle doğrudan iletişim kurmazlar. Bunun yerine, uçak pistine yakın bir yerde yüksek bir kulede oturan bir hava trafik kontrolörüyle konuşurlar. Hava trafik kontrolörü olmasaydı, pilotların havaalanı yakınındaki her uçaktan haberdar olması ve düzinelerce pilottan oluşan bir komiteyle iniş önceliklerini tartışması gerekecekti. Bu muhtemelen uçak kazası istatistiklerini oldukça artırırdı.

Kulenin tüm uçuşu kontrol etmesine gerek yok. Yalnızca terminal alanındaki kısıtlamaları uygulamak için mevcuttur çünkü oradaki ilgili aktörlerin sayısı bir pilot için çok fazla olabilir.

##  ⚙️ Yapı

![](https://refactoring.guru/images/patterns/diagrams/mediator/structure-2x.png)

1. **Bileşenler (Components)**, bazı iş mantığını içeren çeşitli sınıflardır. Her bileşenin, arabulucu arayüzünün türüyle bildirilen bir arabulucuya referansı vardır. Bileşen, aracının gerçek sınıfının farkında değildir; bu nedenle, bileşeni farklı bir aracıya bağlayarak diğer programlarda yeniden kullanabilirsiniz.

2. **Mediator** arayüzü, genellikle tek bir bildirim yöntemini içeren bileşenlerle iletişim yöntemlerini bildirir. Bileşenler, kendi nesneleri de dahil olmak üzere herhangi bir bağlamı bu yöntemin argümanları olarak iletebilir. Ancak bu yalnızca alıcı bileşen ile gönderenin sınıfı arasında hiçbir bağlantı oluşmayacak şekilde yapılabilir.

3. **Concrete Mediators** çeşitli bileşenler arasındaki ilişkileri kapsar. Somut aracılar genellikle yönettikleri tüm bileşenlere referanslar tutar ve hatta bazen yaşam döngülerini bile yönetirler.

4. Bileşenler diğer bileşenlerin farkında olmamalıdır. Bir bileşenin içinde veya bileşenin başına önemli bir şey gelirse, yalnızca arabulucuya bilgi vermelidir. Arabulucu bildirimi aldığında göndereni kolayca tanımlayabilir ve bu da karşılığında hangi bileşenin tetiklenmesi gerektiğine karar vermek için yeterli olabilir.
Bir bileşenin bakış açısından her şey tamamen kara bir kutuya benzer. Gönderen, isteğini kimin yerine getireceğini bilmiyor ve alıcı da isteği ilk etapta kimin gönderdiğini bilmiyor.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte, **Mediator** modeli çeşitli kullanıcı arayüzü sınıfları arasındaki karşılıklı bağımlılıkları ortadan kaldırmanıza yardımcı olur: düğmeler, onay kutuları ve metin etiketleri.


![](https://refactoring.guru/images/patterns/diagrams/mediator/example-2x.png)

*Kullanıcı arayüzü iletişim kutusu sınıflarının yapısı.*

Kullanıcı tarafından tetiklenen bir öğe, olması gerektiği gibi görünse bile diğer öğelerle doğrudan iletişim kurmaz. Bunun yerine, öğenin yalnızca aracısına olay hakkında bilgi vermesi ve bu bildirimle birlikte bağlamsal bilgileri iletmesi gerekir.

Bu örnekte, kimlik doğrulama iletişim kutusunun tamamı aracı görevi görür. Somut unsurların nasıl işbirliği yapması gerektiğini bilir ve dolaylı iletişimlerini kolaylaştırır. Bir olay hakkında bir bildirim alındığında, iletişim kutusu olayı hangi öğenin ele alması gerektiğine karar verir ve çağrıyı buna göre yönlendirir.

```java
// Mediator arabirimi, bileşenlerin çeşitli olayları aracılığıyla aracıyı
// bilgilendirmek için kullandığı bir yöntemi bildirir. Aracı bu
// olaylara tepki verebilir ve yürütme işlemini diğer bileşenlere aktarabilir.
interface Mediator is
    method notify(sender: Component, event: string)

// Somut aracı sınıfı. Bireysel bileşenler arasındaki karmaşık bağlantı ağı
// çözülmüş ve aracıya taşınmıştır.
class AuthenticationDialog implements Mediator is
    private field title: string
    private field loginOrRegisterChkBx: Checkbox
    private field loginUsername, loginPassword: Textbox
    private field registrationUsername, registrationPassword,
                  registrationEmail: Textbox
    private field okBtn, cancelBtn: Button

    constructor AuthenticationDialog() is
        // Tüm bileşen nesnelerini oluştururken mevcut aracıyı
        // bağlantıları kurmak için oluşturucularına iletiyor.

    // Bir bileşenle bir şey olduğunda, aracıyı bilgilendirir. Bir bildirim alındığında,
    // aracı kendi başına bir şey yapabilir veya isteği başka bir bileşene iletebilir.
    method notify(sender, event) is
        if (sender == loginOrRegisterChkBx and event == "check")
            if (loginOrRegisterChkBx.checked)
                title = "Oturum Aç"
                // 1. Oturum açma formu bileşenlerini göster.
                // 2. Kayıt formu bileşenlerini gizle.
            else
                title = "Kayıt Ol"
                // 1. Kayıt formu bileşenlerini göster.
                // 2. Oturum açma formu bileşenlerini gizle.

        if (sender == okBtn && event == "click")
            if (loginOrRegister.checked)
                // Giriş kimlik bilgilerini kullanarak bir kullanıcı bulmaya çalış.
                if (!found)
                    // Giriş alanının üstünde bir hata mesajı göster.
            else
                // 1. Kayıt alanlarındaki verileri kullanarak bir kullanıcı hesabı oluştur.
                // 2. Bu kullanıcıyı oturum açar.
                // ...

// Bileşenler, aracı arabirimini kullanarak bir aracıyla iletişim kurar.
// Bu sayede aynı bileşenleri farklı bağlayıcı nesnelerle bağlayarak farklı bağlamlarda kullanabilirsiniz.
class Component is
    field dialog: Mediator

    constructor Component(dialog) is
        this.dialog = dialog

    method click() is
        dialog.notify(this, "click")

    method keypress() is
        dialog.notify(this, "keypress")

// Somut bileşenler birbirleriyle iletişim kurmazlar. Tek bir iletişim kanalları vardır,
// o da aracıya bildirim göndermektir.
class Button extends Component is
    // ...

class Textbox extends Component is
    // ...

class Checkbox extends Component is
    method check() is
        dialog.notify(this, "check")
    // ...

```


##  💡Uygulanabilirlik


**🐞 Diğer sınıflara sıkı sıkıya bağlı oldukları için bazı sınıfları değiştirmek zor olduğunda Mediator modelini kullanın.**

⚡️ Desen, belirli bir bileşende yapılan değişiklikleri bileşenlerin geri kalanından yalıtarak, sınıflar arasındaki tüm ilişkileri ayrı bir sınıfa çıkarmanıza olanak tanır.

----------------

**🐞 Bir bileşeni, diğer bileşenlere çok bağımlı olduğundan farklı bir programda yeniden kullanamadığınızda bu modeli kullanın.**

⚡️ Mediator'ı uyguladıktan sonra tek tek bileşenler diğer bileşenlerden habersiz hale gelir. Bir aracı nesne aracılığıyla dolaylı da olsa birbirleriyle hâlâ iletişim kurabiliyorlardı. Bir bileşeni farklı bir uygulamada yeniden kullanmak için ona yeni bir aracı sınıfı sağlamanız gerekir.

----------------

**🐞 Çeşitli bağlamlarda bazı temel davranışları yeniden kullanmak için kendinizi tonlarca bileşen alt sınıfı oluştururken bulduğunuzda Mediator'ı kullanın.**

⚡️ Bileşenler arasındaki tüm ilişkiler aracının içinde yer aldığından, bileşenleri kendileri değiştirmek zorunda kalmadan, yeni aracı sınıfları sunarak bu bileşenlerin işbirliği yapması için tamamen yeni yollar tanımlamak kolaydır.
  

##  📝 Nasıl Uygulanır?

1. Daha bağımsız olunca fayda sağlayacak sıkı sıkıya bağlı bir sınıf grubunu tanımlayın (bu sınıfların daha kolay bakım veya daha basit yeniden kullanımı için).

2. Arabulucu arayüzünü tanımlayın ve arabulucular ile çeşitli bileşenler arasındaki istenen iletişim protokolünü tanımlayın. Çoğu durumda, bileşenlerden bildirim almak için tek bir yöntem yeterlidir.
Bu arayüz, bileşen sınıflarını farklı bağlamlarda yeniden kullanmak istediğinizde kritiktir. Bileşen, arabulucusu ile genel arayüz aracılığıyla çalıştığı sürece, bileşeni arabulucunun farklı bir uygulaması ile ilişkilendirebilirsiniz.

3. Somut arabulucu sınıfını uygulayın yani implement edin. Arabulucu içindeki tüm bileşenlere referansları saklamayı düşünün. Bu şekilde, arabulucunun yöntemlerinden herhangi bir bileşeni çağırabilirsiniz.

4. Bir adım daha ileri giderek arabulucuyu bileşen nesnelerinin oluşturulmasından ve yok edilmesinden sorumlu yapabilirsiniz. Bu durumda, arabulucu bir **fabrika (factory)** veya bir **cephe (facade)** gibi görünebilir.

5. Bileşenlerin bir referansını arabulucu nesnesine saklamalıdır. Bağlantı genellikle bileşenin yapıcısında, arabulucu nesnesinin bir argüman olarak iletilmesi ile kurulur.

6. Bileşenlerin kodunu değiştirin, böylece diğer bileşenlerin yöntemlerini çağırmak yerine arabulucunun bildirim yöntemini çağırırlar. Diğer bileşenleri çağırma işlevini arabulucu sınıfına çıkarın. Bu kodu arabulucu o bileşenden bildirim aldığında her seferinde çalıştırın.


##  ⚖️ Artıları ve Eksileri

✅ Bir programın çeşitli bileşenleri arasındaki bağlantıyı azaltabilirsiniz.

✅ Tek Sorumluluk İlkesi (Single Responsibility Principle): Çeşitli bileşenler arasındaki iletişimi tek bir yere toplayarak anlaşılmasını ve bakımını kolaylaştırabilirsiniz.

✅ Açık/Kapalı Prensibi (Open/Closed Principle): Gerçek bileşenleri değiştirmek zorunda kalmadan yeni aracılar tanıtabilirsiniz.

✅ Tek tek bileşenleri daha kolay yeniden kullanabilirsiniz.

❌ Zamanla bir arabulucu bir Tanrı Nesnesine ([God Object](https://en.wikipedia.org/wiki/God_object)) dönüşebilir.

##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Sorumluluk Zinciri**,  **Command**,  **Mediator**  ve  **Observer**  desenleri, isteklerin göndericileri ve alıcıları arasında bağlantı kurmanın çeşitli yollarını ele alır:
    
    -   **Sorumluluk Zinciri**, bir talebi potansiyel alıcılardan oluşan dinamik bir zincir boyunca, içlerinden biri bunu yerine getirene kadar sırayla iletir.
    -   **Command**, gönderenler ve alıcılar arasında tek yönlü bağlantılar kurar.
    -   **Mediator**, gönderenler ve alıcılar arasındaki doğrudan bağlantıları ortadan kaldırarak onları bir aracı nesne aracılığıyla dolaylı olarak iletişim kurmaya zorlar.
    -   **Observer**, alıcıların dinamik olarak alma isteklerine abone olmalarını ve abonelikten çıkmalarını sağlar.

- **Facade** ve **Mediator**'ın benzer görevleri vardır: birbirine sıkı sıkıya bağlı birçok sınıf arasında işbirliğini organize etmeye çalışırlar.
	- **Facade**, nesnelerin alt sistemine basitleştirilmiş bir arayüz tanımlar, ancak herhangi bir yeni işlevsellik sunmaz. Alt sistemin kendisi cepheden habersizdir. Alt sistemdeki nesneler doğrudan iletişim kurabilir.
	- **Mediator**, sistemin bileşenleri arasındaki iletişimi merkezileştirir. Bileşenler yalnızca aracı nesneyi bilir ve doğrudan iletişim kurmaz.

- **Mediator** ve **Observer** arasındaki fark çoğu zaman anlaşılması zordur. Çoğu durumda bu modellerden herhangi birini uygulayabilirsiniz; ancak bazen ikisini aynı anda uygulayabilirsiniz. Bunu nasıl yapabileceğimize bakalım:
	- Mediator'ın temel amacı, bir dizi sistem bileşeni arasındaki karşılıklı bağımlılıkları ortadan kaldırmaktır. Bunun yerine, bu bileşenler tek bir aracı nesneye bağımlı hale gelir. Observer'ın amacı, bazı nesnelerin diğerlerinin astı gibi davrandığı nesneler arasında dinamik tek yönlü bağlantılar kurmaktır.
	- Arabulucu modelinin Observer'a dayanan popüler bir uygulaması vardır. Arabulucu nesnesi yayıncı rolünü oynar ve bileşenler, arabulucunun etkinliklerine abone olan ve bu etkinliklerden aboneliği iptal eden aboneler olarak hareket eder. Mediator bu şekilde uygulandığında Observer'a çok benzeyebilir.
	- Kafanız karıştığında Mediator modelini başka yollarla da uygulayabileceğinizi unutmayın. Örneğin, tüm bileşenleri kalıcı olarak aynı aracı nesneye bağlayabilirsiniz. Bu uygulama Observer'a benzemeyecek ancak yine de Mediator modelinin bir örneği olacaktır.
	- Şimdi tüm bileşenlerin yayıncı haline geldiği ve birbirleri arasında dinamik bağlantılara izin verdiği bir program hayal edin. Merkezi bir aracı nesne olmayacak, yalnızca dağıtılmış bir gözlemci kümesi olacak.
  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Mediator modelinin Java kodundaki en popüler kullanımı, bir uygulamanın GUI bileşenleri arasındaki iletişimi kolaylaştırmaktır. Mediator'ın eşanlamlısı MVC modelinin Controller kısmıdır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`java.util.Timer`](http://docs.oracle.com/javase/8/docs/api/java/util/Timer.html)  (tüm  `scheduleXXX()`  methodları)
-   [`java.util.concurrent.Executor#execute()`](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html#execute-java.lang.Runnable-)
-   [`java.util.concurrent.ExecutorService`](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html)  (`invokeXXX()`  ve  `submit()`  methodları)
-   [`java.util.concurrent.ScheduledExecutorService`](http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledExecutorService.html)  (Tüm  `scheduleXXX()`  methodları)
-   [`java.lang.reflect.Method#invoke()`](http://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#invoke-java.lang.Object-java.lang.Object...-)
 
####  Not uygulaması

Bu örnek, birçok GUI öğesinin bir arabulucunun yardımıyla işbirliği yapacak ancak birbirlerine bağlı olmayacak şekilde nasıl organize edileceğini gösterir.

**📁 components**: Meslektaş sınıfları

⤵️ 📄 `components/Component.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;

/**
 * Common component interface.
 */
public interface Component {
    void setMediator(Mediator mediator);
    String getName();
}
```

⤵️ 📄 `components/AddButton.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;
import fatihes1.mediator.example.mediator.Note;

import javax.swing.*;
import java.awt.event.ActionEvent;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class AddButton extends JButton implements Component {
    private Mediator mediator;

    public AddButton() {
        super("Add");
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void fireActionPerformed(ActionEvent actionEvent) {
        mediator.addNewNote(new Note());
    }

    @Override
    public String getName() {
        return "AddButton";
    }
}
```


⤵️ 📄 `components/DeleteButton.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;

import javax.swing.*;
import java.awt.event.ActionEvent;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class DeleteButton extends JButton  implements Component {
    private Mediator mediator;

    public DeleteButton() {
        super("Del");
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void fireActionPerformed(ActionEvent actionEvent) {
        mediator.deleteNote();
    }

    @Override
    public String getName() {
        return "DelButton";
    }
}
```

⤵️ 📄 `components/Filter.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;
import fatihes1.mediator.example.mediator.Note;

import javax.swing.*;
import java.awt.event.KeyEvent;
import java.util.ArrayList;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class Filter extends JTextField implements Component {
    private Mediator mediator;
    private ListModel listModel;

    public Filter() {}

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void processComponentKeyEvent(KeyEvent keyEvent) {
        String start = getText();
        searchElements(start);
    }

    public void setList(ListModel listModel) {
        this.listModel = listModel;
    }

    private void searchElements(String s) {
        if (listModel == null) {
            return;
        }

        if (s.equals("")) {
            mediator.setElementsList(listModel);
            return;
        }

        ArrayList<Note> notes = new ArrayList<>();
        for (int i = 0; i < listModel.getSize(); i++) {
            notes.add((Note) listModel.getElementAt(i));
        }
        DefaultListModel<Note> listModel = new DefaultListModel<>();
        for (Note note : notes) {
            if (note.getName().contains(s)) {
                listModel.addElement(note);
            }
        }
        mediator.setElementsList(listModel);
    }

    @Override
    public String getName() {
        return "Filter";
    }
}
```

⤵️ 📄 `components/List.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;
import fatihes1.mediator.example.mediator.Note;

import javax.swing.*;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
@SuppressWarnings("unchecked")
public class List extends JList implements Component {
    private Mediator mediator;
    private final DefaultListModel LIST_MODEL;

    public List(DefaultListModel listModel) {
        super(listModel);
        this.LIST_MODEL = listModel;
        setModel(listModel);
        this.setLayoutOrientation(JList.VERTICAL);
        Thread thread = new Thread(new Hide(this));
        thread.start();
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    public void addElement(Note note) {
        LIST_MODEL.addElement(note);
        int index = LIST_MODEL.size() - 1;
        setSelectedIndex(index);
        ensureIndexIsVisible(index);
        mediator.sendToFilter(LIST_MODEL);
    }

    public void deleteElement() {
        int index = this.getSelectedIndex();
        try {
            LIST_MODEL.remove(index);
            mediator.sendToFilter(LIST_MODEL);
        } catch (ArrayIndexOutOfBoundsException ignored) {}
    }

    public Note getCurrentElement() {
        return (Note)getSelectedValue();
    }

    @Override
    public String getName() {
        return "List";
    }

    private class Hide implements Runnable {
        private List list;

        Hide(List list) {
            this.list = list;
        }

        @Override
        public void run() {
            while (true) {
                try {
                    Thread.sleep(300);
                } catch (InterruptedException ex) {
                    ex.printStackTrace();
                }
                if (list.isSelectionEmpty()) {
                    mediator.hideElements(true);
                } else {
                    mediator.hideElements(false);
                }
            }
        }
    }
}
```

⤵️ 📄 `components/SaveButton.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;

import javax.swing.*;
import java.awt.event.ActionEvent;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class SaveButton extends JButton implements Component {
    private Mediator mediator;

    public SaveButton() {
        super("Save");
    }

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void fireActionPerformed(ActionEvent actionEvent) {
        mediator.saveChanges();
    }

    @Override
    public String getName() {
        return "SaveButton";
    }
}
```

⤵️ 📄 `components/TextBox.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;

import javax.swing.*;
import java.awt.event.KeyEvent;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class TextBox extends JTextArea implements Component {
    private Mediator mediator;

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void processComponentKeyEvent(KeyEvent keyEvent) {
        mediator.markNote();
    }

    @Override
    public String getName() {
        return "TextBox";
    }
}
```

⤵️ 📄 `components/Title.java`

```java
package fatihes1.mediator.example.components;

import fatihes1.mediator.example.mediator.Mediator;

import javax.swing.*;
import java.awt.event.KeyEvent;

/**
 * Concrete components don't talk with each other. They have only one
 * communication channel–sending requests to the mediator.
 */
public class Title extends JTextField implements Component {
    private Mediator mediator;

    @Override
    public void setMediator(Mediator mediator) {
        this.mediator = mediator;
    }

    @Override
    protected void processComponentKeyEvent(KeyEvent keyEvent) {
        mediator.markNote();
    }

    @Override
    public String getName() {
        return "Title";
    }
}
```

**📁 mediator**

⤵️ 📄 `mediator/Mediator.java`: Ortak aracı arayüzünü tanımlar

```java
package fatihes1.mediator.example.mediator;

import fatihes1.mediator.example.components.Component;

import javax.swing.*;

/**
 * Common mediator interface.
 */
public interface Mediator {
    void addNewNote(Note note);
    void deleteNote();
    void getInfoFromList(Note note);
    void saveChanges();
    void markNote();
    void clear();
    void sendToFilter(ListModel listModel);
    void setElementsList(ListModel list);
    void registerComponent(Component component);
    void hideElements(boolean flag);
    void createGUI();
}
```

⤵️ 📄 `mediator/Editor.java`: Concrete mediator

```java
package fatihes1.mediator.example.mediator;

import fatihes1.mediator.example.components.*;
import fatihes1.mediator.example.components.Component;
import fatihes1.mediator.example.components.List;

import javax.swing.*;
import javax.swing.border.LineBorder;
import java.awt.*;

/**
 * Concrete mediator. All chaotic communications between concrete components
 * have been extracted to the mediator. Now components only talk with the
 * mediator, which knows who has to handle a request.
 */
public class Editor implements Mediator {
    private Title title;
    private TextBox textBox;
    private AddButton add;
    private DeleteButton del;
    private SaveButton save;
    private List list;
    private Filter filter;

    private JLabel titleLabel = new JLabel("Title:");
    private JLabel textLabel = new JLabel("Text:");
    private JLabel label = new JLabel("Add or select existing note to proceed...");
  
    /**
     * Here the registration of components by the mediator.
     */
    @Override
    public void registerComponent(Component component) {
        component.setMediator(this);
        switch (component.getName()) {
            case "AddButton":
                add = (AddButton)component;
                break;
            case "DelButton":
                del = (DeleteButton)component;
                break;
            case "Filter":
                filter = (Filter)component;
                break;
            case "List":
                list = (List)component;
                this.list.addListSelectionListener(listSelectionEvent -> {
                    Note note = (Note)list.getSelectedValue();
                    if (note != null) {
                        getInfoFromList(note);
                    } else {
                        clear();
                    }
                });
                break;
            case "SaveButton":
                save = (SaveButton)component;
                break;
            case "TextBox":
                textBox = (TextBox)component;
                break;
            case "Title":
                title = (Title)component;
                break;
        }
    }

    /**
     * Various methods to handle requests from particular components.
     */
    @Override
    public void addNewNote(Note note) {
        title.setText("");
        textBox.setText("");
        list.addElement(note);
    }

    @Override
    public void deleteNote() {
        list.deleteElement();
    }

    @Override
    public void getInfoFromList(Note note) {
        title.setText(note.getName().replace('*', ' '));
        textBox.setText(note.getText());
    }

    @Override
    public void saveChanges() {
        try {
            Note note = (Note) list.getSelectedValue();
            note.setName(title.getText());
            note.setText(textBox.getText());
            list.repaint();
        } catch (NullPointerException ignored) {}
    }

    @Override
    public void markNote() {
        try {
            Note note = list.getCurrentElement();
            String name = note.getName();
            if (!name.endsWith("*")) {
                note.setName(note.getName() + "*");
            }
            list.repaint();
        } catch (NullPointerException ignored) {}
    }

    @Override
    public void clear() {
        title.setText("");
        textBox.setText("");
    }

    @Override
    public void sendToFilter(ListModel listModel) {
        filter.setList(listModel);
    }

    @SuppressWarnings("unchecked")
    @Override
    public void setElementsList(ListModel list) {
        this.list.setModel(list);
        this.list.repaint();
    }

    @Override
    public void hideElements(boolean flag) {
        titleLabel.setVisible(!flag);
        textLabel.setVisible(!flag);
        title.setVisible(!flag);
        textBox.setVisible(!flag);
        save.setVisible(!flag);
        label.setVisible(flag);
    }

    @Override
    public void createGUI() {
        JFrame notes = new JFrame("Notes");
        notes.setSize(960, 600);
        notes.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        JPanel left = new JPanel();
        left.setBorder(new LineBorder(Color.BLACK));
        left.setSize(320, 600);
        left.setLayout(new BoxLayout(left, BoxLayout.Y_AXIS));
        JPanel filterPanel = new JPanel();
        filterPanel.add(new JLabel("Filter:"));
        filter.setColumns(20);
        filterPanel.add(filter);
        filterPanel.setPreferredSize(new Dimension(280, 40));
        JPanel listPanel = new JPanel();
        list.setFixedCellWidth(260);
        listPanel.setSize(320, 470);
        JScrollPane scrollPane = new JScrollPane(list);
        scrollPane.setPreferredSize(new Dimension(275, 410));
        listPanel.add(scrollPane);
        JPanel buttonPanel = new JPanel();
        add.setPreferredSize(new Dimension(85, 25));
        buttonPanel.add(add);
        del.setPreferredSize(new Dimension(85, 25));
        buttonPanel.add(del);
        buttonPanel.setLayout(new FlowLayout());
        left.add(filterPanel);
        left.add(listPanel);
        left.add(buttonPanel);
        JPanel right = new JPanel();
        right.setLayout(null);
        right.setSize(640, 600);
        right.setLocation(320, 0);
        right.setBorder(new LineBorder(Color.BLACK));
        titleLabel.setBounds(20, 4, 50, 20);
        title.setBounds(60, 5, 555, 20);
        textLabel.setBounds(20, 4, 50, 130);
        textBox.setBorder(new LineBorder(Color.DARK_GRAY));
        textBox.setBounds(20, 80, 595, 410);
        save.setBounds(270, 535, 80, 25);
        label.setFont(new Font("Verdana", Font.PLAIN, 22));
        label.setBounds(100, 240, 500, 100);
        right.add(label);
        right.add(titleLabel);
        right.add(title);
        right.add(textLabel);
        right.add(textBox);
        right.add(save);
        notes.setLayout(null);
        notes.getContentPane().add(left);
        notes.getContentPane().add(right);
        notes.setResizable(false);
        notes.setLocationRelativeTo(null);
        notes.setVisible(true);
    }
}
```

⤵️ 📄 `mediator/Note.java`: Bir not sınıfı

```java
package fatihes1.mediator.example.mediator;

/**
 * Note class.
 */
public class Note {
    private String name;
    private String text;

    public Note() {
        name = "New note";
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setText(String text) {
        this.text = text;
    }

    public String getName() {
        return name;
    }

    public String getText() {
        return text;
    }

    @Override
    public String toString() {
        return name;
    }
}
```

⤵️ 📄 `Demo.java`: Initialization code

```java
package fatihes1.mediator.example;

import fatihes1.mediator.example.components.*;
import fatihes1.mediator.example.mediator.Editor;
import fatihes1.mediator.example.mediator.Mediator;

import javax.swing.*;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    public static void main(String[] args) {
        Mediator mediator = new Editor();

        mediator.registerComponent(new Title());
        mediator.registerComponent(new TextBox());
        mediator.registerComponent(new AddButton());
        mediator.registerComponent(new DeleteButton());
        mediator.registerComponent(new SaveButton());
        mediator.registerComponent(new List(new DefaultListModel()));
        mediator.registerComponent(new Filter());

        mediator.createGUI();
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.png`: Çalışma Çıktısı

![](https://refactoring.guru/images/patterns/examples/java/mediator/OutputDemo.png)

