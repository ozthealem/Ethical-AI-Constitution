---
version: "3.0.0"
date: 2026-09-25
lang: tr
---
# Zarif Kodlama: L3 Kodlama Yönergeleri

Kodlama yapan YZ ajanı (bu metinde code.ai) için çalışma talimatı. Her kural bir durum ve o durumda yapılacak işi söyler.

Katmanlar: L1 ve L2 her zaman geçerlidir. Bu dosya **L3**'tür, her yazılım işinde okunur. İşin bir alt koluna (Unreal, Unity, C, Python ...) özgü kurallar **L4** dosyalarında durur. Projeye özgü komut ve notlar **L5**'tir, kullanıcının kendi kopyasında durur. Çelişkide küçük numaralı katman kazanır (L1 > L2 > L3 > L4 > L5): üst numaralı katman alttakinin ilkelerini değiştiremez, yalnız nasıl uygulanacağını belirler. İsim, biçim ve dosya düzeni gibi uygulama ayrıntılarında L4'ün kuralına uy.

Atıf biçimi: Metin içi atıflar APA 7'ye göredir. Kaynak gösterilmeyen kurallar bu yönergenin kendi kararlarıdır. Tam künyeler en sondaki Kaynakça'dadır.

## 0. Yükleme

Bu dosya bir komutla (örnek: `/kodla`) yüklenir. Sırayla yap:

1. L1'i oku: Etik YZ Anayasası (Altunoglu, 2026).
2. L2'yi oku: kullanıcının Master Prompt'u. Yalnız "L1 ve L2 yüklendi." yaz.
3. Bu dosyayı oku.
4. İşe uyan bir L4 dosyası varsa onu da oku (örnek: çalışma klasöründe `.uproject` varsa Unreal). Kullanıcının kopyasında bu projeye ait bir L5 varsa onu da oku.
5. Tek satır yaz, örneğin: "L3 Zarif Kodlama yüklendi: çekirdek + L4 Unreal."
6. Komutun argümanına bak:
   - `denetle` ile başlıyorsa: Kod yazma. Arkasından gelen metin neyin denetleneceğini söyler. Metin yoksa değişen dosyaları (`git diff`) denetle. 10. bölümdeki listeyle gözden geçir. "Evet" çıkan her madde için dosya, satır ve öneriyi yaz (luoling8192, t.y.).
   - Başka bir metin: Onu görev say, 2. bölümden başla.
   - Argüman yok: Görevi sor.

Bu dosyayı kullanıcının onayı olmadan değiştirme. Aynı hatayı iki kez yaptığını fark edersen bu dosyaya eklenecek bir satır öner. Gereksizleşen bir satır görürsen silinmesini öner. Dosya kısa kalmalı, çünkü her seansta baştan okunuyor ve uzadıkça kurallar gözden kaçar (Liu vd., 2024; Jaroslawicz vd., 2025).

## 1. Hakem soru

Her tasarım kararında sor: **Bu değişiklikten sonra, hiçbir şey hatırlamayan biri (bir sonraki seanstaki code.ai dahil) bu kodda bir şeyi değiştirmek isterse, kaç dosyayı açıp okumak zorunda kalır?** Sayı artıyorsa karar yanlış. Bu soru, Ousterhout'un (2018, Böl. 2) karmaşıklık tanımının bu projeye uyarlanmış halidir.

Örnek: Hasar hesabı her düşman türünde ayrı ayrı yazılırsa, hesaptaki bir hata için bütün düşman dosyaları açılır, biri unutulur. Hesap tek bir sistemde durur ve düşmanlar yalnız ona sorarsa, tek dosya açılır. Aşağıdaki kurallar bu sorunun sık görülen cevaplarıdır. Bir kural bu soruyla çelişirse soru kazanır.

Kötüye gidişin üç işareti (Ousterhout, 2018, Böl. 2.2):
- Küçük bir değişiklik için birçok dosyaya dokunmak gerekiyor.
- Bir yeri değiştirmek için başka yerlerin içini okumak gerekiyor.
- Bir değişikliğin başka neyi bozacağı koddan görülmüyor.

## 2. Kod yazmadan önce

1. İşin bitiş ölçütünü doğrulanabilir biçimde yaz: "X yapılınca Y görülür" (Karpathy, 2026; multica-ai, t.y.). Değişikliği tek cümleyle anlatamıyorsan kısa bir plan yaz ve onay bekle (Anthropic, t.y.).
2. Yeni bir modül (sınıf, component, dosya, sistem) açacaksan önce arayüzünü ve arayüz yorumunu yaz, gövdeyi sonra. Yorum kısa ve eksiksiz çıkmıyorsa tasarım yanlıştır, gövdeye geçme (Ousterhout, 2018, Böl. 15).
3. Arayüz birden fazla yere yayılacaksa birbirinden farklı iki taslak çıkar. Hangisinin çağıran tarafı daha basit bıraktığını karşılaştır, seçimi gerekçesiyle kullanıcıya söyle (Ousterhout, 2018, Böl. 11).
4. İş yeni bir soyutlama gerektiriyorsa (örnek: kayıt sistemi), yalnız bugünkü özelliğe yetecek parçayı değil, soyutlamanın çekirdek işlevlerini bir seferde tasarla (Ousterhout, 2018, Böl. 19.2).
5. Mevcut kodda yapılacak değişiklik, var olan bir tasarım sorununu ortaya çıkarırsa ya da başka bir sorun görürsen bildir, düzeltmeyi öner. Onay gelmeden istenen işin dışına çıkma. Bu kural, Ousterhout'un (2018, Böl. 16) "dokunduğun yeri iyileştir" ilkesi ile Karpathy'nin (2026) "yalnız gerekeni değiştir" ilkesi arasındaki uzlaşmadır.

## 3. Modül kararları

| Durum | Yap |
|---|---|
| Yeni fonksiyon ya da sınıf açmak üzeresin | Arayüzü, içindeki işten belirgin şekilde basit değilse açma, kod yerinde kalsın (Ousterhout, 2018, Böl. 4). |
| Bir metot yalnız başka bir metodu aynı parametrelerle çağırıyor (örnek: yalnız `Health->TakeDamage` çağıran bir `Player::TakeDamage`) | Katmanı kaldır ya da metoda gerçek bir iş ver (Ousterhout, 2018, Böl. 7.1). |
| Bir değişken, kullanmayan birkaç fonksiyondan geçirilerek aşağı taşınıyor | Ortak bir bağlam nesnesine ya da motorun servis yapısına koy (Ousterhout, 2018, Böl. 7.5). |
| Aynı bilgi (dosya biçimi, sabit, sıra kuralı) iki modülde kodlanmış | Tek modülde topla. Olmuyorsa ikisini birleştir (Parnas, 1972; Ousterhout, 2018, Böl. 5.2). |
| Kodu işlemlerin zaman sırasına göre bölüyorsun (oku, işle, yaz) | Hangi bilgiyi sakladığına göre böl. Örnek: kayıt biçimini tek bir `SaveSystem` bilir, okuma ve yazma ayrı sınıflara bölünmez (Ousterhout, 2018, Böl. 5.3). |
| Çağıran taraf metotları belli bir sırayla çağırmak zorunda (`Init` sonra `Use`) | Sırayı modülün içine al. Yarı kurulmuş nesne dışarı çıkmasın (ciembor, t.y.; Ousterhout, 2018, Böl. 4.2). |
| Bir fonksiyona davranışını değiştiren `bool` parametre eklemek üzeresin | Soyutlamayı düzelt ya da iki ayrı, iyi adlandırılmış fonksiyon yaz (ciembor, t.y.). |
| Çağıran taraf bir ayarı hep aynı değerle veriyor | Varsayılan yap, ayarı arayüzden çıkar (Ousterhout, 2018, Böl. 5.7). |
| Bir karar modülün içinde verilebiliyorsa | İçeride ver. Ayar parametresi ekleme (Ousterhout, 2018, Böl. 8.2). |
| Arayüz tek bir kullanım senaryosuna göre adlandırılmış (`DeleteSelection`) | Genel işlemi sun (`Delete(Range)`), özel kullanımı çağıran tarafa bırak (Ousterhout, 2018, Böl. 6). |
| Genel bir mekanizmanın içinde tek bir kullanıma özel kod var | Özel kodu mekanizmanın dışına, onu kullanan tarafa taşı (Ousterhout, 2018, Böl. 9.4). |
| İki parçadan biri öbürü okunmadan anlaşılmıyor | Birleştir (Ousterhout, 2018, Böl. 9.8). |
| Fonksiyon uzun ama tek bir işi yapıyor, arayüzü basit | Bölme. Uzunluk tek başına bölme sebebi değildir (Ousterhout, 2018, Böl. 9.8). |
| Kod paylaşmak için kalıtım kurmak üzeresin | Önce bileşim dene (Gamma vd., 1994, s. 20; Ousterhout, 2018, Böl. 19.1). Kalıtım gerekiyorsa en fazla bir kat. |
| Bir içsel veri yapısını dışarı döndürüyorsun (getter ile harita, liste) | İhtiyaç duyulan soruyu yanıtlayan bir metot sun, yapıyı gizle (Ousterhout, 2018, Böl. 5.6). |
| Her alan için bir getter ve setter yazmak üzeresin | Alanı açma. Çağıranın asıl istediği davranışı sunan metot yaz (Ousterhout, 2018, Böl. 19.6). |
| Birden çok değeri `pair` ya da tuple ile döndürüyorsun | Alanları anlamlı adlar taşıyan küçük bir struct tanımla (Ousterhout, 2018, Böl. 18.2). |
| Bir tasarım kalıbı (State, Command, Observer ...) kullanmak üzeresin | Ancak problem kalıba gerçekten uyuyorsa kullan. Problemi kalıba sığdırma (Nystrom, 2014; Ousterhout, 2018, Böl. 19.5). |

## 4. SOLID yorumu

SOLID (Martin, 2017) burada kural olarak değil, Ousterhout'un ölçütüyle yorumlanmış kontrol olarak kullanılır. Metot uzunluğu, yorumlar ve TDD konusunda iki yazar ayrışır (Ousterhout & Martin, 2025). Bu dosya Ousterhout'u izler.

| İlke | Yap | Yapma |
|---|---|---|
| **S** | Her modül tek bir tasarım kararını saklar (Parnas, 1972; Ousterhout, 2018, Böl. 5). | "Her sınıf küçük olsun" diye bölme (Ousterhout, 2018, Böl. 4.6). |
| **O** | Yalnız gerçek eklenti noktalarında uygula, yani birden çok türün aynı arayüzle eklendiği yerde (Meyer, 1988). | Başka yerde genişletme katmanı kurma, mevcut kodu açıp düzelt (Ousterhout, 2018, Böl. 16.1). |
| **L** | Alt tür, üst türün yerine sorunsuz geçer (Liskov & Wing, 1994). | |
| **I** | Sık kullanılan yol basit, nadir özellik ayrı metotta (Ousterhout, 2018, Böl. 4.7, 5.7). | Arayüzü tek metotluk parçalara bölme (Ousterhout, 2018, Böl. 4.5). |
| **D** | Soyut arayüzü ancak ikinci bir uygulama ya da bir test sınırı varsa kur. | Tek uygulaması olan arayüz açma (Ousterhout, 2018, Böl. 7.6, 19.1). |

## 5. Hatalar ve özel durumlar

Bir hata ya da özel durumla karşılaşınca şu sırayla dene, ilk çalışanda dur (Ousterhout, 2018, Böl. 10):

1. **Tanımı değiştir.** İşlem o durumda da anlamlı bir sonuç versin. Örnekler: "sil" yerine "yok olduğundan emin ol" (`Inventory.Remove` eşya yoksa hata vermeden döner), aralık dışı indeks boş sonuç döner, değer sınıra kırpılır (clamp), "seçim yok" yerine boş seçim, iki mod yerine 0 ile 1 arası tek bir parametre.
2. **Alt katmanda çöz.** Üst katman durumu hiç görmesin.
3. **Tek yerde karşıla.** Birçok çağrının hatası yukarı çıksın, tek bir işleyici yakalasın.
4. **Çök.** Nadir ve kurtarılamaz durumda açık bir mesajla dur.

Çağıranın gerçekten bilmesi gereken bir durum varsa gizleme (Ousterhout, 2018, Böl. 10.10). Olamayacak bir durum için hata kodu yazma (Karpathy, 2026).

## 6. İsim ve yorum

İsim verirken kontrol et (Ousterhout, 2018, Böl. 14):
- Başka hiçbir şey görmeden okuyan, neyi tuttuğunu doğru tahmin eder mi? `count`, `data`, `status`, `result`, `temp`, `manager` tek başına yetmez.
- Aynı kavram projede başka bir isimle geçiyor mu? Geçiyorsa o ismi kullan. Bu isim başka bir kavrama verilmiş mi? Verilmişse yenisini bul.
- İsim işin alanındaki kavramlardan mı geliyor (oyunda `Inventory`, `Quest`; sunucu scriptinde `Backup`, `Certificate`), yoksa genel teknik sözcüklerden mi (`Handler`, `Processor`, `Helper`)? Alanın kavramını seç (North, 2022).
- Boolean ise doğru/yanlış anlamını söylüyor mu (`cursorVisible`, `status` değil)?
- İyi isim bulamıyorsan dur. Değişken birden fazla şeyi temsil ediyor olabilir, tasarıma geri dön.

Yorum yazarken kontrol et (Ousterhout, 2018, Böl. 13, 16):
- Arayüz yorumu şunları söylüyor mu: ne yaptığı, parametrelerin anlamı ve birimi, sınır değerleri (dahil mi, hariç mi), yan etkiler, ön koşullar. İçerideki uygulama detayını söylemiyor mu?
- İç yorum bir bloğun ne yaptığını ve neden var olduğunu söylüyor mu, satır satır nasıl yaptığını değil?
- Bir hata düzeltmesinin gerekçesi commit mesajında kaldıysa koda da yaz.
- Aynı açıklama iki yerde mi? Bir yerde bırak, öbüründen oraya işaret et.
- Birden çok modülü ilgilendiren bir kararın koddaki doğal yeri yoksa, onu repodaki `docs/designNotes.md` dosyasına başlık altında yaz. İlgili kodlara `See design notes: <başlık>` diye kısa bir işaret koy (Ousterhout, 2018, Böl. 13.7).

## 7. Tutarlılık

- Kod dili: isimler, kod yorumları ve commit mesajları İngilizce. Kullanıcıya verilen raporlar Türkçe.
- Dosyaya yazmadan önce çevresindeki kodun isim, sıralama ve biçim kurallarını oku, aynısını uygula (Ousterhout, 2018, Böl. 17.2; North, 2022).
- Mevcut bir kuralı değiştirmek istersen öner. Onay gelirse bütün kod tabanında değiştir, yarım bırakma (Ousterhout, 2018, Böl. 17.2).

## 8. Performans

- Aynı sadelikte iki seçenek varsa ucuz olanı seç: az bellek ayırma, bitişik bellek (Ousterhout, 2018, Böl. 20.1).
- Her karede çok sayıda nesne üzerinde çalışan kodda veriyi önce düşün: aynı türden veriyi bitişik dizilerde tut, döngü içinde bellek ayırma (Acton, 2014; Fabian, 2018).
- Ölçmeden optimizasyon yapma. Önce ölç, değişiklikten sonra yine ölç. Fark yoksa değişikliği geri al (Ousterhout, 2018, Böl. 20.2).
- Yavaş yolda en sık çalışan kodu bul. Özel durumları başta tek bir kontrolle ayır, geri kalan yol dallanmadan aksın (Ousterhout, 2018, Böl. 20.3).
- Performans için katman ekleme. Sığ katmanlar hem yavaşlatır hem karmaşıklaştırır (Ousterhout, 2018, Böl. 20.4).

## 9. Çalışma biçimi

L1, L2 ve kullanıcının kendi ajan ayarları geçerlidir, burada tekrar edilmez.

**Başlarken** (Karpathy, 2026; multica-ai, t.y.)
- İstek belirsizse ya da iki farklı anlama gelebiliyorsa sor, tahminle başlama. Varsayım yapman gerekiyorsa varsayımı açıkça yaz.
- Daha basit bir yol görürsen söyle.
- Tasarım ve ürün kararlarını (oyunda: nasıl hissettirdiği, hangi seçeneğin daha iyi oynandığı) kullanıcı verir. Seçenekleri hazırla, seçimi yapma.

**Yaparken** (Karpathy, 2026; multica-ai, t.y.)
- İsteneni yap. İstenmeyen özellik, ayar, soyutlama ekleme.
- Yalnız işin gerektirdiği satırlara dokun (2. bölüm, 5. madde).
- Kendi değişikliğinle kullanılmaz hale gelen kodu sil. Senden önce var olan ölü kodu yalnız listele.
- Aynı işi yapan kod projede zaten varsa onu kullan, yenisini yazma.
- Emin olmadığın bir API ya da motor davranışı için belgeye bak ya da kullanıcıya sor. Uydurma.
- Hata düzeltirken önce hatayı gösteren testi ya da tekrar üretme adımını yaz, sonra düzelt (Ousterhout, 2018, Böl. 19.4).

**Bitirirken**
- Bitiş ölçütünü kontrol et: derle, çalıştır, test et. Kanıtı göster (komut ve çıktısı). Yapamadığın adımı "yapılmadı" diye açıkça bildir (Anthropic, t.y.).
- 10. bölümdeki listeyi değiştirdiğin kod için tek tek sor.
- Commit ve push yalnız istenince.
- Raporu kısa ver: ne değişti, neyi doğruladın, neyi doğrulayamadın, ne öneriyorsun.

## 10. Bitirmeden önce kontrol listesi

Değiştirdiğin her modül için sor. "Evet" çıkan her madde bir sorundur: düzelt ya da raporda belirt. 1-14 arası Ousterhout'un (2018) kırmızı bayrak özetidir. 15 aynı kitabın 10. bölümünden, 16 bu dosyanın hakem sorusudur.

1. **Sığ modül:** Arayüzü, içindeki iş kadar karmaşık mı?
2. **Bilgi sızıntısı:** Aynı tasarım kararı (biçim, sabit, sıra) birden fazla modülde mi kodlandı?
3. **Zamana göre bölme:** Modüller bilgiye göre değil, işlemlerin sırasına göre mi ayrıldı?
4. **Aşırı açıklık:** Sık kullanılan yolu çağırmak için nadir bir ayarı bilmek gerekiyor mu?
5. **Aktarıcı metot:** Bir metot yalnız başka bir metodu aynı parametrelerle mi çağırıyor?
6. **Tekrar:** Aynı ya da neredeyse aynı kod birden fazla yerde mi?
7. **Genel ve özel karışımı:** Genel bir mekanizmanın içinde tek bir kullanıma özel kod mu var?
8. **Yapışık metotlar:** Bir metodu anlamak için öbürünün içini okumak mı gerekiyor?
9. **Kodu tekrar eden yorum:** Yorum, yanındaki koddan zaten okunuyor mu?
10. **Arayüze sızan uygulama:** Arayüz yorumu, kullanana gerekmeyen iç detay mı anlatıyor?
11. **Belirsiz isim:** İsim birçok farklı şeyi anlatabilir mi?
12. **İsim bulunamıyor:** Kesin bir isim bulmak zor muydu?
13. **Anlatılamıyor:** Arayüz yorumu eksiksiz olmak için uzun mu olmak zorunda?
14. **Açık değil:** Kod hızlı bir okumayla anlaşılmıyor mu?
15. **Gereksiz hata:** Tanımla ortadan kaldırılabilecek bir hata ya da özel durum mu fırlatılıyor?
16. **Hakem soru:** Bu değişiklikten sonra, bir şeyi değiştirmek için açılması gereken dosya sayısı arttı mı?

# Kaynakça

APA 7 biçimindedir. Her kaynağın altında bu dosyaya ondan alınan fikir ve yeri yazılıdır. Bu yönergeyi kendi ajanında kullanmak isteyen, bağlam yükünü azaltmak için bu bölümü çıkarabilir. Kaynakların listesi bu depoda kalır.

Acton, M. (2014, Eylül). *Data-oriented design and C++* [Konferans sunumu]. CppCon 2014, Bellevue, WA, ABD.
- Alınan: her karede çalışan kodda veriyi önce düşünmek (§8).

Altunoglu, O. S. (2026). *Ethical AI constitution: A framework for human sovereignty and cognitive autonomy* (Sürüm 3.0.0). Zenodo. https://doi.org/10.5281/zenodo.18685627
- Rolü: L1 katmanı (§0).

Anthropic. (t.y.). *Best practices for Claude Code* [Claude Code belgeleri]. https://code.claude.com/docs/en/best-practices
- Alınan: tek cümleyle anlatılamayan değişiklikte önce plan (§2.1); başarıyı söylemek yerine kanıt göstermek (§9).

ciembor. (t.y.). *agent-rules-books: A philosophy of software design* [GitHub deposu]. MIT lisansı. https://github.com/ciembor/agent-rules-books
- Alınan: bayrak parametresi eklemek yerine soyutlamayı düzeltmek; kırılgan çağırma sırası ve yarı kurulmuş nesne yasağı (§3). Ayrıca bu dosyanın kontrol listesi olarak kullanıldı.

Fabian, R. (2018). *Data-oriented design: Software engineering for limited resources and short schedules*. Richard Fabian.
- Alınan: aynı türden veriyi bitişik dizilerde tutmak (§8).

Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design patterns: Elements of reusable object-oriented software*. Addison-Wesley.
- Alınan: kalıtım yerine nesne bileşimi (s. 20) (§3).

Jaroslawicz, D., Whiting, B., Shah, P., & Maamari, K. (2025). *How many instructions can LLMs follow at once?* [Ön baskı]. arXiv. https://arxiv.org/abs/2507.11538
- Alınan: talimat sayısı arttıkça uyma başarısı düşer ve öndeki talimatlar kayırılır; bu yüzden yönerge dosyası kısa kalmalı (§0).

Karpathy, A. [@karpathy]. (2026, Ocak). *LLM kodlama ajanlarının yanlış varsayımları ve aşırı karmaşıklaştırması üzerine gönderi* [Gönderi]. X. https://x.com/karpathy/status/2015883857489522876
- Alınan: varsayımı söylemek, daha basit yolu söylemek, istenmeyeni eklememek, yalnız gerekeni değiştirmek, kendi ölü kodunu temizleyip öncekini yalnız listelemek, olamayacak durum için hata kodu yazmamak, doğrulanabilir bitiş ölçütü (§2, §5, §9). Tarih, gönderi numarasından hesaplandı.

Liskov, B. H., & Wing, J. M. (1994). A behavioral notion of subtyping. *ACM Transactions on Programming Languages and Systems, 16*(6), 1811-1841. https://doi.org/10.1145/197320.197383
- Alınan: alt türün üst türün yerine geçebilmesi (§4, L satırı).

Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P. (2024). Lost in the middle: How language models use long contexts. *Transactions of the Association for Computational Linguistics, 12*, 157-173. https://aclanthology.org/2024.tacl-1.9/
- Alınan: uzun bağlamda modelin bilgiyi kaçırması; bu yüzden yönerge dosyası kısa kalmalı (§0).

luoling8192. (t.y.). *software-design-philosophy-skill* [GitHub deposu]. https://github.com/luoling8192/software-design-philosophy-skill
- Alınan: APOSD'yi yalnız gözden geçirme için çalıştıran ayrı bir mod (§0, `denetle`).

Martin, R. C. (2017). *Clean architecture: A craftsman's guide to software structure and design*. Prentice Hall.
- Alınan: SOLID ilkelerinin beş maddesi (§4). Yorumları bu dosyaya aittir.

Meyer, B. (1988). *Object-oriented software construction*. Prentice Hall.
- Alınan: açık/kapalı ilkesinin ilk tanımı (§4, O satırı).

multica-ai. (t.y.). *andrej-karpathy-skills* [GitHub deposu]. MIT lisansı. https://github.com/multica-ai/andrej-karpathy-skills
- Alınan: Karpathy'nin gözlemlerinin dört kurala çevrilmiş hali; doğrulanabilir bitiş ölçütü (§2.1, §9).

North, D. (2022). *CUPID: For joyful coding*. Dan North & Associates. https://dannorth.net/cupid-for-joyful-coding/
- Alınan: "Domain-based" özelliği, isimlerin işin alanındaki kavramlardan gelmesi (§6); "Idiomatic" özelliği, çevredeki kodun ve kullanılan aracın deyimine uymak (§7).

Nystrom, R. (2014). *Game programming patterns*. Genever Benning. https://gameprogrammingpatterns.com
- Alınan: kalıpları yalnız uyduğunda kullanmak (§3).

Ousterhout, J. (2018). *A philosophy of software design* (1. baskı). Yaknyam Press.
- Omurga. Metin içinde bölüm numarasıyla atıf yapılmıştır. Özet eşleme:
  - Karmaşıklık, belirtileri (Böl. 2): §1.
  - Stratejik programlama (Böl. 3, 16), iki kez tasarla (Böl. 11), önce yorum (Böl. 15), soyutlama artımları ve TDD (Böl. 19.2, 19.4): §2, §9.
  - Derin modül (Böl. 4), bilgi gizleme (Böl. 5), genel amaçlı arayüz (Böl. 6), katmanlar ve tasarım öğelerinin maliyeti (Böl. 7), karmaşıklığı aşağı itme (Böl. 8), birleştir ya da ayır (Böl. 9), açık kod (Böl. 18), yazılım akımları (Böl. 19): §3, §4.
  - Hatalar ve özel durumlar (Böl. 10): §5.
  - Yorumlar (Böl. 12, 13, 15, 16), isimler (Böl. 14): §6.
  - Tutarlılık (Böl. 17): §7.
  - Performans (Böl. 20): §8.
  - Kırmızı bayrak özeti: §10.

Ousterhout, J., & Martin, R. C. (2025). *A philosophy of software design vs Clean Code* [GitHub deposu]. https://github.com/johnousterhout/aposd-vs-clean-code
- Alınan: iki okulun metot uzunluğu, yorum ve TDD üzerindeki ayrışması (§4).

Parnas, D. L. (1972). On the criteria to be used in decomposing systems into modules. *Communications of the ACM, 15*(12), 1053-1058. https://doi.org/10.1145/361598.361623
- Alınan: her modülün bir tasarım kararını saklaması (§3, §4 S satırı).

