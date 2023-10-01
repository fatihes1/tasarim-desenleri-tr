# Tasarım Desenleri (Design Patterns)
![](https://refactoring.guru/images/refactoring/content/refactoring-2x.png)
## Tasarım Deseni (Design Pattern) Nedir?
Tasarım desenleri, yazılım tasarımında sıkça karşılaşılan sorunlara tipik çözümlerdir. Bunlar, kodunuzdaki tekrar eden tasarım sorunlarını çözmek için özelleştirebileceğiniz önceden hazırlanmış şablonlar gibi düşünülebilir.

 Hazır işlevler veya kütüphanelerde olduğu gibi bir deseni bulup programınıza kopyalayamazsınız. Desen, belirli bir kod parçası değil, belirli bir problemi çözmek için genel bir kavramdır. Desen ayrıntılarını takip edebilir ve kendi programınızın gerçeklerine uygun bir çözümü uygulayabilirsiniz.

Desenler, algoritmalarla sıklıkla karıştırılır, çünkü her iki kavram da bilinen bazı sorunlara tipik çözümleri tanımlar. Bir algoritma her zaman bir hedefi başarmak için belirli bir dizi eylemi tanımlarken, bir desen daha yüksek seviyede bir çözümün açıklamasıdır. Aynı desenin farklı programlara uygulanan kodu farklı olabilir.

Bir algoritma ve yemek tarifi birbirine benzer: her ikisi de bir hedefe ulaşmak için net adımlar içerir. Öte yandan, bir desen daha çok bir plana benzer: sonucu ve özelliklerini görebilirsiniz, ancak uygulamanın tam sırası size bağlıdır.

### Desen Nelerden Oluşur?
Desen, genellikle birçok bağlamda yeniden üretilebilmeleri için çok formel bir şekilde açıklanır. Aşağıda genellikle bir desen açıklamasında bulunan bölümler şunlardır:

-  Desenin Amacı (Intent): Desenin problemi ve çözümü kısaca tanımlar.
- Motivasyon (Motivation): Problemi daha fazla açıklar ve desen tarafından mümkün kılınan çözümü detaylandırır.
-  Sınıf Yapısı (Structure of Classes): Desenin her bir bölümünü ve bunların nasıl ilişkilendiğini gösterir.
-  Popüler Bir Programlama Dilinde Kod Örneği (Code Example): Desenin arkasındaki fikri daha iyi anlamanıza yardımcı olacak şekilde popüler bir programlama dilinde kod örneği sunar.

Bazı desen katalogları, desenin uygulanabilirliği, uygulama adımları ve diğer desenlerle ilişkiler gibi diğer kullanışlı detayları listeler.

## Desenlerin Tarihi
Desenlerin kim tarafından icat edildiği sorusu, iyi ama çokta doğru olmayan bir sorudur. Tasarım desenleri özgün ve karmaşık kavramlar değillerdir - aksine, desenler nesne tabanlı tasarımın (OOP - object-oriented design) yaygın sorunlarına tipik çözümlerdir. Bir çözüm farklı projelerde tekrar tekrar kullanıldığında, bu çözüme nihayet bir isim verilir ve çözüm detaylı bir şekilde açıklanır. İşte temelde bir desenin nasıl keşfedildiği bu şekilde açıklanabilir.

Desenlerin kavramı ilk kez Christopher Alexander tarafından "[A Pattern Language: Towns, Buildings, Construction](https://www.amazon.com/-/dp/0195019199)" adlı kitapta tanımlanmıştır. Bu kitap, kentsel çevreyi tasarlama "dili"ni açıklar. Bu dilin birimleri kalıplar yani desenlerdir. Desenler, yüksek pencerelerin nasıl olması gerektiğini, bir binanın kaç katlı olması gerektiğini, bir mahalledeki yeşil alanların ne kadar büyük olması gerektiğini vb. açıklayabilir.

Bu fikir, Erich Gamma, John Vlissides, Ralph Johnson ve Richard Helm adlı dört yazar tarafından benimsendi. 1994 yılında, nesne tabanlı programlamaya tasarım desenleri kavramını uyguladıkları "[Design Patterns: Elements of Reusable Object-Oriented Software](https://www.amazon.com/gp/product/0201633612/)" adlı kitabı yayımladılar. Kitap, nesne tabanlı tasarımın çeşitli sorunlarını çözen 23 deseni içeriyordu ve çok kısa sürede en çok satanlar listesine girdi. Uzun adı nedeniyle, insanlar ona başlangıçta "dörtlü çetenin kitabı" (he book by the **g**ang **o**f **f**our) dediler ve bu terim kısa sürede sadece "**GoF** kitabı" olarak kısaltıldı.

O zamandan beri, onlarca başka nesne tabanlı desen keşfedildi. "Desen yaklaşımı" (pattern approach), diğer programlama alanlarında da çok popüler hale geldi, bu nedenle şimdi nesne tabanlı tasarımın dışında da birçok başka desen bulunmaktadır.

## Neden Desenleri (Patterns) Öğrenmeliyim?
Gerçek şu ki, bir programcı olarak birçok yıl boyunca hiçbir desen hakkında bilgi sahibi olmadan çalışmayı başarabilirsiniz. Birçok insan sadece bunu yapar. Ancak o durumda bile, belki de habersiz olarak bazı desenleri uyguluyor olabilirsiniz. Peki, neden zaman harcamalısınız?

- Tasarım desenleri, yazılım tasarımında karşılaşılan yaygın sorunlara yönelik denenmiş ve test edilmiş çözümlerin bir araç kutusudur. Bu sorunlarla hiç karşılaşmasanız bile, desenleri bilmek hala faydalıdır, çünkü size nesne tabanlı tasarım ilkelerini kullanarak çeşitli sorunları nasıl çözeceğinizi öğretir.

- Tasarım desenleri, sizin ve takım arkadaşlarınızın daha verimli iletişim kurabilmesi için ortak bir dil tanımlar. 'Aa, bunun için sadece bir Singleton kullan.,' diyebilirsiniz ve herkes önerinizin arkasındaki fikri anlar. Deseni ve desenin adını biliyorsanız bir singleton'ın ne olduğunu açıklamaya gerek yoktur. 

## Desenlerin Eleştirisi (Criticism of Patterns)

Desenleri henüz eleştirmemiş olanlar sadece tembel insanlar gibi görünüyor. Desen kullanımına karşı en yaygın argümanlara bir göz atalım.

**Zayıf Bir Programlama Dili İçin Önem Arz Edebilir**
Genellikle desenlere olan ihtiyaç, insanların gerekli soyutlama seviyesine sahip olmayan bir programlama dili veya teknolojiyi seçtiğinde ortaya çıkar. Bu durumda, desenler, dile çok ihtiyaç duyulan süper yetenekler kazandıran bir yapışkan haline gelir.
Örneğin, Strateji deseni (Strategy pattern), çoğu modern programlama dilinde basit bir lambda işlevle uygulanabilir.

**Verimsiz Çözümler**
Desenler, zaten yaygın olarak kullanılan yaklaşımları sistemleştirmeye çalışır. Bu birleştirme, birçok kişi tarafından bir dogma olarak görülür ve desenleri projelerinin bağlamına uyarlamadan "to the letter" uygularlar.

**Gereksiz Kullanım**
> *"If all you have is a hammer, everything looks like a nail."* (Abraham Maslow)
> 
> Türkçe Karşılığı: *"Eğer elinizde sadece bir çekici varsa, her şey bir çivi gibi görünür."*

Bu, desenlerle yeni tanışan birçok acemi programcıyı rahatsız eden bir sorundur. Desenleri öğrendikten sonra, onları her yerde uygulamaya çalışırlar, hatta daha basit bir kodun işi gayet iyi yapacağı durumlarda bile.

## Desenlerin Sınıflandırılması
Tasarım desenleri; karmaşıklıkları, ayrıntı seviyeleri ve tasarlanan tüm sisteme uygulanabilirliği açısından farklılık gösterir. Yol yapımına benzer bir sistemle açıklamayı seviyorum:  Ya birkaç trafik ışığı yerleştirerek ya da yayalar için yer altı geçitleri inşa ederek çok seviyeli bir kavşağı daha güvenli hale getirebilirsiniz.

En temel ve düşük seviyeli desenler genellikle idiyomlar (idioms) olarak adlandırılır. Genellikle yalnızca tek bir programlama diline uygulanırlar.

En evrensel ve yüksek seviyeli desenler ise mimari (architectural) desenlerdir. Geliştiriciler bu desenleri neredeyse istedikleri herhangi bir dilde uygulayabilirler. Diğer desenlerden farklı olarak, direkt olarak tüm bir uygulamanın mimarisini tasarlamak için kullanılabilirler.






