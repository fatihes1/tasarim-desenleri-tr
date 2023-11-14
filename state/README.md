#  Durum (State) Tasarım Deseni
 

##  💬 Amaç

Durum, bir nesnenin iç durumu değiştiğinde davranışını değiştirmesine olanak tanıyan davranışsal (behavioral) bir tasarım modelidir. Nesnenin, sınıfını değiştirmiş gibi görünür.

<div align="center">

![](https://refactoring.guru/images/patterns/content/state/state-en-2x.png)

</div>


##  🙁 Problem

Durum modeli, Sonlu Durum Makinesi (Finite-State Machine) kavramıyla yakından ilgilidir.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/state/problem1-2x.png)

*Sonlu Durum Makinesi (Finite-State Machine)*

</div>

Ana fikir, herhangi bir anda bir programın bulunabileceği sınırlı sayıda durumun bulunmasıdır. Herhangi bir benzersiz durumda program farklı davranır ve program bir durumdan diğerine anında geçiş yapabilir. Bununla beraber mevcut duruma bağlı olarak program diğer belirli durumlara geçebilir veya geçmeyebilir. Geçişler olarak adlandırılan bu anahtarlama kuralları da sonludur ve önceden belirlenmiştir.

Bu yaklaşımı nesnelere de uygulayabilirsiniz. Bir `Document` sınıfımız olduğunu hayal edin. Bir belge üç durumdan birinde olabilir: Taslak (`Draft`), Denetleme (`Moeration`) ve Yayınlandı (`Published`). Belgenin yayınlama yöntemi her durumda biraz farklı çalışır:
	- Taslak halinde olan belgeyi, denetime taşır.
	- Moderasyonda olan belgeyi herkese açık (public) hale getirir, ancak yalnızca geçerli kullanıcı yöneticiyse.
	- Yayınlandı da ise hiçbir şey yapmaz.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/state/problem2-en-2x.png)

*Bir belge nesnesinin olası durumları ve geçişleri.*

</div>

Durum makineleri genellikle nesnenin mevcut durumuna bağlı olarak uygun davranışı seçen çok sayıda koşullu ifadeden (`if` veya `switch`) oluşur. Genellikle bu "durum" yalnızca nesnenin alanlarının değerlerinin bir kümesidir. Sonlu durumlu makineleri daha önce hiç duymamış olsanız bile, muhtemelen en az bir kez durum uygulamışsınızdır. Aşağıdaki kod yapısı size bir şeyler hatırlatıyor mu?

```java
class Document is
    field state: string
    // ...
    method publish() is
        switch (state)
            "draft":
                state = "moderation"
                break
            "moderation":
                if (currentUser.role == "admin")
                    state = "published"
                break
            "published":
                // Do nothing.
                break
    // ...
```

Koşullara dayalı bir durum makinesinin en büyük zayıflığı, `Document` sınıfına giderek daha fazla durum ve duruma bağlı davranış eklemeye başladığımızda kendini gösterir. Çoğu yöntem, bir yöntemin mevcut durumuna göre uygun davranışını seçen korkunç koşullar içerecektir. Bunun gibi bir kodun bakımı çok zordur çünkü geçiş mantığındaki herhangi bir değişiklik, her yöntemde durum koşullarının değiştirilmesini gerektirebilir.

Proje geliştikçe sorun daha da büyüyor. Tasarım aşamasında tüm olası durumları ve geçişleri tahmin etmek oldukça zordur. Bu nedenle, sınırlı sayıda koşul cümlesiyle oluşturulmuş bir yalın durum makinesi, zamanla şişirilmiş bir karmaşaya dönüşebilir.


##  😊 Çözüm

Durum modeli, bir nesnenin tüm olası durumları için yeni sınıflar oluşturmanızı ve duruma özgü tüm davranışları bu sınıflara çıkarmanızı önerir.

Bağlam (context) adı verilen orijinal nesne, tüm davranışları kendi başına uygulamak yerine, mevcut durumunu temsil eden durum nesnelerinden birine bir referans saklar ve durumla ilgili tüm işleri bu nesneye devreder.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/state/solution-en-2x.png)

*Belge, işi bir durum nesnesine devreder.*

</div>

Bağlamı başka bir duruma geçirmek için etkin durum nesnesini bu yeni durumu temsil eden başka bir nesneyle değiştirin. Bu ancak tüm durum sınıflarının aynı arayüzü izlemesi ve bağlamın kendisinin bu arayüz aracılığıyla bu nesnelerle çalışması durumunda mümkündür.

Bu yapı **Strateji (Strategy)** modeline benzeyebilir ancak önemli bir fark vardır. Durum modelinde, belirli durumlar birbirlerinin farkında olabilir ve bir durumdan diğerine geçişleri başlatabilir, oysa stratejiler neredeyse hiçbir zaman birbirini bilmez.

## 🚙 Gerçek Dünya Örneği


Akıllı telefonunuzdaki düğmeler ve anahtarlar, cihazın mevcut durumuna bağlı olarak farklı davranır:
- Telefonun kilidi açıkken düğmelere basmak çeşitli işlevlerin yürütülmesine yol açar.

- Telefon kilitliyken herhangi bir düğmeye basıldığında kilit açma ekranı açılır.

- Telefonun şarjı azaldığında herhangi bir tuşa basıldığında şarj ekranı görüntülenir.


##  ⚙️ Yapı

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/state/structure-en-2x.png)

</div>

1. **Bağlam (Context)**, somut durum nesnelerinden birine bir referansı saklar ve duruma özgü tüm işleri ona devreder. Bağlam durum nesnesi ile durum arayüzü aracılığıyla iletişim kurar. Bağlam, kendisine yeni bir durum nesnesi iletmek için bir ayarlayıcıyı ortaya çıkarır.

2. **Durum (State)** arayüzü duruma özgü yöntemleri bildirir. Bu yöntemler tüm somut durumlar için anlamlı olmalıdır çünkü bazı durumlarınızın hiçbir zaman çağrılmayacak işe yaramaz yöntemlere sahip olmasını istemezsiniz.

3. **Concrete States**, duruma özgü yöntemler için kendi uygulamalarını sağlar. Benzer kodun birden fazla durumda tekrarlanmasını önlemek için, bazı ortak davranışları kapsayan ara soyut sınıflar sağlayabilirsiniz.
Durum nesneleri, bağlam nesnesine bir geri referans depolayabilir. Bu referans aracılığıyla durum, bağlam nesnesinden gerekli bilgileri alabilir ve durum geçişlerini başlatabilir.

4. **Concrete Subscribers**, yayıncının yayınladığı bildirimlere yanıt olarak bazı eylemler gerçekleştirir. Yayıncının somut sınıflara bağlanmaması için bu sınıfların tümü aynı arayüzü uygulamalıdır.


##  💻 Sözde Kod (Pseudocode)

Bu örnekte **Durum (State)** modeli, mevcut oynatma durumuna bağlı olarak medya oynatıcının aynı kontrollerinin farklı davranmasına olanak tanır.

<div align="center">

![](https://refactoring.guru/images/patterns/diagrams/state/example-2x.png)

*Durum nesneleriyle nesne davranışını değiştirme örneği.*

</div>

Oyuncunun ana nesnesi her zaman oynatıcı için işin çoğunu gerçekleştiren bir durum nesnesine bağlıdır. Bazı eylemler, oynatıcının mevcut durum nesnesini bir başkasıyla değiştirir; bu da oynatıcının kullanıcı etkileşimlerine tepki verme şeklini değiştirir.

```java
// AudioPlayer sınıfı bir bağlam (context) olarak görev yapar. Ayrıca, 
// ses çaların mevcut durumunu temsil eden durum sınıflarının örneğine bir 
// başvuruyu korur.
class AudioPlayer is
    field state: State
    field UI, volume, playlist, currentSong

    constructor AudioPlayer() is
        this.state = new ReadyState(this)

        // Bağlam, kullanıcı girişini bir durum nesnesine iletmektedir. Elbette,
        // sonucun hangi durumun şu anda etkin olduğuna bağlıdır, çünkü her 
        // durum girişi farklı şekilde işleyebilir.
        UI = new UserInterface()
        UI.lockButton.onClick(this.clickLock)
        UI.playButton.onClick(this.clickPlay)
        UI.nextButton.onClick(this.clickNext)
        UI.prevButton.onClick(this.clickPrevious)

    // Diğer nesneler ses çaların etkin durumunu değiştirebilmelidir.
    method changeState(state: State) is
        this.state = state

    // UI yöntemleri yürütme işlemini etkin duruma iletilir.
    method clickLock() is
        state.clickLock()
    method clickPlay() is
        state.clickPlay()
    method clickNext() is
        state.clickNext()
    method clickPrevious() is
        state.clickPrevious()

    // Bir durum, bağlam üzerinde bazı hizmet yöntemlerini çağırabilir.
    method startPlayback() is
        // ...
    method stopPlayback() is
        // ...
    method nextSong() is
        // ...
    method previousSong() is
        // ...
    method fastForward(time) is
        // ...
    method rewind(time) is
        // ...


// Temel durum sınıfı, tüm somut durumların uygulaması gereken yöntemleri bildirir
// ve ayrıca durumla ilişkilendirilmiş bağlam nesnesine bir geri başvuru sağlar.
// Durumlar, bağlamı başka bir duruma geçirmek için geri başvuruyu kullanabilir.
abstract class State is
    protected field player: AudioPlayer

    // Bağlam, kendisini durum yapıcısı aracılığıyla iletir. Bu, bir durumun ihtiyaç 
    // duyulursa bazı kullanışlı bağlam verilerini almasına yardımcı olabilir.
    constructor State(player) is
        this.player = player

    abstract method clickLock()
    abstract method clickPlay()
    abstract method clickNext()
    abstract method clickPrevious()


// Somut durumlar, bağlamın durumu ile ilişkilendirilen çeşitli davranışları uygular.
class LockedState extends State is

    // Kilitli bir çaların kilidini açtığınızda, iki durumdan birini
    // varsayabilir.
    method clickLock() is
        if (player.playing)
            player.changeState(new PlayingState(player))
        else
            player.changeState(new ReadyState(player))

    method clickPlay() is
        // Kilitli olduğundan hiçbir şey yapma.

    method clickNext() is
        // Kilitli olduğundan hiçbir şey yapma.

    method clickPrevious() is
        // Kilitli olduğundan hiçbir şey yapma.


// Ayrıca bağlamdaki durum geçişlerini tetikleyebilirler.
class ReadyState extends State is
    method clickLock() is
        player.changeState(new LockedState(player))

    method clickPlay() is
        player.startPlayback()
        player.changeState(new PlayingState(player))

    method clickNext() is
        player.nextSong()

    method clickPrevious() is
        player.previousSong()


class PlayingState extends State is
    method clickLock() is
        player.changeState(new LockedState(player))

    method clickPlay() is
        player.stopPlayback()
        player.changeState(new ReadyState(player))

    method clickNext() is
        if (event.doubleclick)
            player.nextSong()
        else
            player.fastForward(5)

    method clickPrevious() is
        if (event.doubleclick)
            player.previous()
        else
            player.rewind(5)
```


##  💡Uygulanabilirlik


**🐞 Mevcut durumuna bağlı olarak farklı davranan bir nesneniz varsa, durum sayısı çok fazlaysa ve duruma özgü kod sık sık değişiyorsa Durum modelini kullanın.**

⚡️ Desen, duruma özgü tüm kodları bir dizi farklı sınıfa çıkarmanızı önerir. Sonuç olarak, birbirinden bağımsız olarak yeni durumlar ekleyebilir veya mevcut durumları değiştirebilirsiniz, böylece bakım maliyetini azaltabilirsiniz.

----------------

**🐞 Sınıfın alanlarının mevcut değerlerine göre sınıfın nasıl davranacağını değiştiren çok büyük koşullarla kirlenmiş bir sınıfınız olduğunda bu modeli kullanın.**

⚡️ Durum modeli, bu koşulların dallarını karşılık gelen durum sınıflarının yöntemlerine çıkarmanıza olanak tanır. Bunu yaparken, ana sınıfınızdan duruma özgü kodda yer alan geçici alanları ve yardımcı yöntemleri de temizleyebilirsiniz.

----------------

**🐞 Koşullu tabanlı bir durum makinesinin benzer durumları ve geçişleri arasında çok sayıda yinelenen kodunuz olduğunda Durumu kullanın.**

⚡️ Durum modeli, durum sınıflarının hiyerarşilerini oluşturmanıza ve ortak kodu soyut temel sınıflara çıkararak çoğaltmayı azaltmanıza olanak tanır.


##  📝 Nasıl Uygulanır?

1. Bağlam olarak hangi sınıfın hareket edeceğine karar verin. Halihazırda duruma bağlı koda sahip olan mevcut bir sınıf veya duruma özgü kod birden fazla sınıfa dağıtılmışsa yeni bir sınıf olabilir.

2. Durum arayüzünü bildirin. Bağlamda bildirilen tüm yöntemleri yansıtabilse de yalnızca duruma özgü davranışlar içerebilenleri hedefleyin.

3. Her gerçek durum için durum arayüzünden türetilen bir sınıf oluşturun. Daha sonra bağlamın yöntemlerinin üzerinden geçin ve bu durumla ilgili tüm kodları yeni oluşturulan sınıfınıza ayırın.
Kodu durum sınıfına taşırken, bunun bağlamın özel üyelerine bağlı olduğunu keşfedebilirsiniz. Birkaç geçici çözüm vardır:
	- Bu alanları veya yöntemleri herkese açık (public) hale getirin.
	- Çıkardığınız davranışı bağlamda genel bir yönteme dönüştürün ve onu durum sınıfından çağırın. Bu yöntem pek güzel değildir ama hızlıdır ve bunu daha sonra istediğiniz zaman düzeltebilirsiniz.
	- Programlama diliniz yuvalama sınıflarını (nesting classes) destekliyorsa, durum sınıflarını bağlam sınıfına yuvalayın.

4. Bağlam sınıfında, durum arayüzü tipinde bir referans alanı ve bu alanın değerinin geçersiz kılınmasına (overriding) izin veren bir genel ayarlayıcı ekleyin.

5. Bağlam yönteminin üzerinden tekrar geçin ve boş durum şartlarını, durum nesnesinin karşılık gelen yöntemlerine yapılan çağrılarla değiştirin.

6. Bağlamın durumunu değiştirmek için durum sınıflarından birinin örneğini oluşturun ve onu bağlama aktarın. Bunu bağlamın kendisinde, çeşitli durumlarda veya istemcide yapabilirsiniz. Bu nerede yapılırsa yapılsın, sınıf somutlaştırdığı somut durum sınıfına bağımlı hale gelir.

##  ⚖️ Artıları ve Eksileri

✅  Açık/Kapalı Prensibi (Open/Closed Principle): Belirli durumlarla ilgili kodu ayrı sınıflar halinde düzenleyin.

✅ Tek Sorumluluk İlkesi (Open/Closed Principle): Belirli durumlarla ilgili kodu ayrı sınıflar halinde düzenleyin.

✅ Hacimli durum makinesi koşullarını ortadan kaldırarak bağlam kodunu basitleştirin.

❌ Bir durum makinesinin yalnızca birkaç durumu varsa veya nadiren değişiyorsa modeli uygulamak aşırıya kaçabilir.


##  🔀 Diğer Tasarım Desenleri İlişkileri

- **Köprü (Bridge)**, **Durum (State)**, **Strateji (Strategy)** (ve bir dereceye kadar **Adapter**) çok benzer yapılara sahiptir. Aslında tüm bu desenler, işi başka nesnelere devreden kompozisyona dayanıyor. Ancak hepsi farklı sorunları çözüyor. Desen yalnızca kodunuzu belirli bir şekilde yapılandırmak için kullanılan bir tarif değildir. Ayrıca desenin çözdüğü sorunu diğer geliştiricilere iletebilir.

- **Durum (State)**, **Stratejinin (Strategy)** bir uzantısı olarak düşünülebilir. Her iki model de kompozisyona dayalıdır: Bazı işleri yardımcı nesnelere devrederek bağlamın davranışını değiştirirler. Strateji bu nesneleri tamamen bağımsız ve birbirlerinden habersiz hale getirir. Ancak Durum, somut durumlar arasındaki bağımlılıkları kısıtlamaz ve bağlamın durumunu istediği gibi değiştirmelerine izin verir.

  
##  👾 Kod Örnekleri

###  Java

**Kullanım örneği:** Durum modeli, Java'da büyük `switch` tabanlı durum makinelerini nesnelere dönüştürmek için yaygın olarak kullanılır.

Aşağıda standart Java kitaplıklarından bazı bileşik örnekler verilmiştir:

-   [`javax.faces.lifecycle.LifeCycle#execute()`](http://docs.oracle.com/javaee/7/api/javax/faces/lifecycle/Lifecycle.html#execute-javax.faces.context.FacesContext-)  ( [`FacesServlet` ile kontrol edilir](http://docs.oracle.com/javaee/7/api/javax/faces/webapp/FacesServlet.html): davranış, JSF yaşam döngüsünün mevcut aşamasına (durumuna) bağlıdır))

**Tanımlama:** Durum modeli, nesnelerin durumuna bağlı olarak davranışlarını değiştiren yöntemlerle tanınabilir. Bu durumun, durum nesnelerinin kendisi de dahil olmak üzere diğer nesneler tarafından kontrol edilebileceğini veya değiştirilebileceğini görürseniz tanımlamayı onaylayabilirsiniz.

 
####  Bir medya oynatıcının arayüzü

Bu örnekte Durum modeli, aynı medya oynatıcı kontrollerinin geçerli oynatma durumuna bağlı olarak farklı davranmasına olanak tanır. Oynatıcının ana sınıfı, oynatıcının işlerinin çoğunu gerçekleştiren bir durum nesnesine referans içerir. Bazı eylemler durum nesnesinin bir başkasıyla değiştirilmesiyle sonuçlanabilir, bu da oyuncunun kullanıcı etkileşimlerine tepki verme şeklini değiştirir.


**📁 states**

⤵️ 📄 `states/State.java`: Ortak durum arayüzü

```java
package fatihes1.state.example.states;

import fatihes1.state.example.ui.Player;

/**
 * Common interface for all states.
 */
public abstract class State {
    Player player;

    /**
     * Context passes itself through the state constructor. This may help a
     * state to fetch some useful context data if needed.
     */
    State(Player player) {
        this.player = player;
    }

    public abstract String onLock();
    public abstract String onPlay();
    public abstract String onNext();
    public abstract String onPrevious();
}
```

⤵️ 📄 `states/LockedState.java`

```java
package fatihes1.state.example.states;

import fatihes1.state.example.ui.Player;

/**
 * Concrete states provide the special implementation for all interface methods.
 */
public class LockedState extends State {

    LockedState(Player player) {
        super(player);
        player.setPlaying(false);
    }

    @Override
    public String onLock() {
        if (player.isPlaying()) {
            player.changeState(new ReadyState(player));
            return "Stop playing";
        } else {
            return "Locked...";
        }
    }

    @Override
    public String onPlay() {
        player.changeState(new ReadyState(player));
        return "Ready";
    }

    @Override
    public String onNext() {
        return "Locked...";
    }

    @Override
    public String onPrevious() {
        return "Locked...";
    }
}
```

⤵️ 📄 `states/ReadyState.java`

```java
package fatihes1.state.example.states;

import fatihes1.state.example.ui.Player;

/**
 * They can also trigger state transitions in the context.
 */
public class ReadyState extends State {

    public ReadyState(Player player) {
        super(player);
    }

    @Override
    public String onLock() {
        player.changeState(new LockedState(player));
        return "Locked...";
    }

    @Override
    public String onPlay() {
        String action = player.startPlayback();
        player.changeState(new PlayingState(player));
        return action;
    }

    @Override
    public String onNext() {
        return "Locked...";
    }

    @Override
    public String onPrevious() {
        return "Locked...";
    }
}
```

⤵️ 📄 `states/PlayingState.java`

```java
package fatihes1.state.example.states;

import fatihes1.state.example.ui.Player;

public class PlayingState extends State {

    PlayingState(Player player) {
        super(player);
    }

    @Override
    public String onLock() {
        player.changeState(new LockedState(player));
        player.setCurrentTrackAfterStop();
        return "Stop playing";
    }

    @Override
    public String onPlay() {
        player.changeState(new ReadyState(player));
        return "Paused...";
    }

    @Override
    public String onNext() {
        return player.nextTrack();
    }

    @Override
    public String onPrevious() {
        return player.previousTrack();
    }
}
```

**📁 ui**

⤵️ 📄 `ui/Player.java`: Oyuncu birincil kodu

```java
package fatihes1.state.example.ui;

import fatihes1.state.example.states.ReadyState;
import fatihes1.state.example.states.State;

import java.util.ArrayList;
import java.util.List;

public class Player {
    private State state;
    private boolean playing = false;
    private List<String> playlist = new ArrayList<>();
    private int currentTrack = 0;

    public Player() {
        this.state = new ReadyState(this);
        setPlaying(true);
        for (int i = 1; i <= 12; i++) {
            playlist.add("Track " + i);
        }
    }

    public void changeState(State state) {
        this.state = state;
    }

    public State getState() {
        return state;
    }

    public void setPlaying(boolean playing) {
        this.playing = playing;
    }

    public boolean isPlaying() {
        return playing;
    }

    public String startPlayback() {
        return "Playing " + playlist.get(currentTrack);
    }

    public String nextTrack() {
        currentTrack++;
        if (currentTrack > playlist.size() - 1) {
            currentTrack = 0;
        }
        return "Playing " + playlist.get(currentTrack);
    }

    public String previousTrack() {
        currentTrack--;
        if (currentTrack < 0) {
            currentTrack = playlist.size() - 1;
        }
        return "Playing " + playlist.get(currentTrack);
    }

    public void setCurrentTrackAfterStop() {
        this.currentTrack = 0;
    }
}
```

⤵️ 📄 `ui/UI.java`: Oyuncunun GUI'si

```java
package fatihes1.state.example.ui;

import javax.swing.*;
import java.awt.*;

public class UI {
    private Player player;
    private static JTextField textField = new JTextField();

    public UI(Player player) {
        this.player = player;
    }

    public void init() {
        JFrame frame = new JFrame("Test player");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JPanel context = new JPanel();
        context.setLayout(new BoxLayout(context, BoxLayout.Y_AXIS));
        frame.getContentPane().add(context);
        JPanel buttons = new JPanel(new FlowLayout(FlowLayout.CENTER));
        context.add(textField);
        context.add(buttons);

        // Context delegates handling user's input to a state object. Naturally,
        // the outcome will depend on what state is currently active, since all
        // states can handle the input differently.
        JButton play = new JButton("Play");
        play.addActionListener(e -> textField.setText(player.getState().onPlay()));
        JButton stop = new JButton("Stop");
        stop.addActionListener(e -> textField.setText(player.getState().onLock()));
        JButton next = new JButton("Next");
        next.addActionListener(e -> textField.setText(player.getState().onNext()));
        JButton prev = new JButton("Prev");
        prev.addActionListener(e -> textField.setText(player.getState().onPrevious()));
        frame.setVisible(true);
        frame.setSize(300, 100);
        buttons.add(play);
        buttons.add(stop);
        buttons.add(next);
        buttons.add(prev);
    }
}
```



⤵️ 📄 `Demo.java`: Initialization kodu

```java
package fatihes1.state.example;

import fatihes1.state.example.ui.Player;
import fatihes1.state.example.ui.UI;

/**
 * Demo class. Everything comes together here.
 */
public class Demo {
    public static void main(String[] args) {
        Player player = new Player();
        UI ui = new UI(player);
        ui.init();
    }
}
```

**🖨️ Çıktılar**

⤵️ 📄 `OutputDemo.png`: Çalışma Çıktısı

![](https://refactoring.guru/images/patterns/examples/java/state/OutputDemo.png)
