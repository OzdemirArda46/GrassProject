# GrassFX (gfx) Kütüphane Kılavuzu

Bu doküman, GrassFX kütüphanesinde yer alan tüm makroları, tip tanımlarını ve GrassFX fonksiyonlarını referans tabloları hâlinde listeler. Tüm örnek ve prototipler GrassFX soyutlama katmanına aittir.

## 1\. Makrolar (`#define`) ve Tip Tanımları (`typedef`)

| Tür | Dönüş Tipi / Tanım | Prototip / İfade | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- | :---- |
| **Typedef** | `typedef void (*Gfx_Callback)` | `typedef void (*Gfx_Callback)(void *data, Evas_Object *object, void *event_info);` | Standart geri çağrı (callback) fonksiyon imzasını tanımlar. | \- `data`: Kullanıcı verisi (`void *`, `NULL` olabilir). \- `object`: Olayı tetikleyen nesne (`Evas_Object *`). \- `event_info`: Olaya özgü ek veri (`void *`, `NULL` olabilir). |
| **Makro** | `void` | `#define GFX_CALLBACK(func_name) void func_name(void *data, Evas_Object *object, void *event_info)` | `Gfx_Callback` imzasına uygun geri çağrı fonksiyonu tanımlamayı kısaltır. | \- `func_name`: Oluşturulacak fonksiyonun adı (`void *data`, `Evas_Object *object`, `void *event_info` parametrelerini alır). |

---

## 2\. Genel Fonksiyonlar (Başlatma, Çalıştırma, Kapatma)

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `int` | `int gfx_init(int argc, char **argv);` | Kütüphaneyi ve altyapıyı başlatır. Başarısız olursa negatif değer döner. | \- `argc`: Komut satırı argüman sayısı (`int`). \- `argv`: Komut satırı argüman dizisi (`char **`). |
| `int` | `int gfx_run(void);` | Ana uygulama döngüsünü başlatır ve pencere kapatılana kadar bloklar. | Yok (`void`). Her zaman `0` döner. |
| `int` | `int gfx_exit(void);` | Çalışan ana döngüden çıkış yapar. | Yok (`void`). Her zaman `0` döner. |
| `int` | `int gfx_shutdown(void);` | Başlatılan kaynakları temizler ve kütüphaneyi kapatır. | Yok (`void`). Kalan başlatma sayacını (`int`) döner. |

---

## 3\. Ana Döngü (Main Loop) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_start_mainloop(void);` | Alt seviye olay döngüsünü doğrudan başlatır. | Yok (`void`). |
| `double` | `double gfx_get_looptime(void);` | Ana döngünün o anki zaman damgasını saniye cinsinden döndürür. | Yok (`void`). |

---

## 4\. Zamanlayıcı (Timer) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Ecore_Timer *` | `Ecore_Timer *gfx_add_timer(double in_seconds, Ecore_Task_Cb func, const void *data);` | Belirtilen saniye aralığında tetiklenen zamanlayıcı ekler. `func` `NULL` ise `NULL` döner. | \- `in_seconds`: Tetiklenme süresi (saniye, `double`). \- `func`: Çağrılacak görev fonksiyonu (`Ecore_Task_Cb`; `ECORE_CALLBACK_RENEW` veya `ECORE_CALLBACK_CANCEL` döner). \- `data`: Fonksiyona aktarılacak veri (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_del_timer(Ecore_Timer *timer);` | Var olan zamanlayıcıyı siler. | \- `timer`: Silinecek zamanlayıcı işaretçisi (`Ecore_Timer *`). |
| `void` | `void gfx_timer_set_interval(Ecore_Timer *timer, double in_seconds);` | Zamanlayıcının çalışma aralığını günceller. | \- `timer`: Hedef zamanlayıcı (`Ecore_Timer *`). \- `in_seconds`: Yeni aralık süresi (saniye, `double`). |
| `double` | `double gfx_timer_get_interval(const Ecore_Timer *timer);` | Zamanlayıcının mevcut aralığını saniye cinsinden döner (`timer` `NULL` ise `0.0`). | \- `timer`: Sorgulanacak zamanlayıcı (`const Ecore_Timer *`). |
| `void` | `void gfx_timer_delay(Ecore_Timer *timer, double add);` | Zamanlayıcının bir sonraki tetiklenmesine belirtilen süreyi ekler. | \- `timer`: Hedef zamanlayıcı (`Ecore_Timer *`). \- `add`: Eklenecek gecikme süresi (saniye, `double`). |
| `void` | `void gfx_timer_freeze(Ecore_Timer *timer);` | Zamanlayıcıyı geçici olarak durdurur (dondurur). | \- `timer`: Dondurulacak zamanlayıcı (`Ecore_Timer *`). |
| `void` | `void gfx_timer_thaw(Ecore_Timer *timer);` | Dondurulmuş zamanlayıcıyı kaldığı yerden devam ettirir. | \- `timer`: Devam ettirilecek zamanlayıcı (`Ecore_Timer *`). |

---

## 5\. Idler ve Poller Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Ecore_Idler *` | `Ecore_Idler *gfx_add_idler(Ecore_Task_Cb func, const void *data);` | Ana döngü boşta (idle) kaldığında çalışacak görev ekler. `func` `NULL` ise `NULL` döner. | \- `func`: Çağrılacak görev fonksiyonu (`Ecore_Task_Cb`). \- `data`: Fonksiyona aktarılacak veri (`const void *`, `NULL` olabilir). |
| `void *` | `void *gfx_del_idler(Ecore_Idler *idler);` | Idler görevini siler ve oluşturulurken verilen kullanıcı verisini döner. | \- `idler`: Silinecek idler işaretçisi (`Ecore_Idler *`). |
| `Ecore_Poller *` | `Ecore_Poller *gfx_add_poller(Ecore_Poller_Type type, int interval, Ecore_Task_Cb func, const void *data);` | Belirli aralıklarla yoklama (polling) yapacak görev ekler. `func` `NULL` ise `NULL` döner. | \- `type`: Poller türü (`ECORE_POLLER_CORE`). \- `interval`: Tetiklenme periyodu (2'nin katları önerilir, `int`). \- `func`: Çağrılacak fonksiyon (`Ecore_Task_Cb`). \- `data`: Kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void *` | `void *gfx_del_poller(Ecore_Poller *poller);` | Poller görevini siler ve ilişkili kullanıcı verisini döner. | \- `poller`: Silinecek poller işaretçisi (`Ecore_Poller *`). |

---

## 6\. İş Parçacığı (Thread) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Ecore_Thread *` | `Ecore_Thread *gfx_run_thread(Ecore_Thread_Cb func_blocking, Ecore_Thread_Cb func_end, Ecore_Thread_Cb func_cancel, const void *data);` | Arka planda bloklayıcı işlem yürüten iş parçacığı başlatır. `func_blocking` `NULL` ise `NULL` döner. | \- `func_blocking`: Arka planda çalışacak ana fonksiyon (`Ecore_Thread_Cb`). \- `func_end`: Normal bitişte ana döngüde çağrılacak fonksiyon (`Ecore_Thread_Cb`, `NULL` olabilir). \- `func_cancel`: İptal durumunda çağrılacak fonksiyon (`Ecore_Thread_Cb`, `NULL` olabilir). \- `data`: Paylaşılan veri (`const void *`, `NULL` olabilir). |
| `Ecore_Thread *` | `Ecore_Thread *gfx_thread_run_feedback(Ecore_Thread_Cb func_heavy, Ecore_Thread_Notify_Cb func_notify, Ecore_Thread_Cb func_end, Ecore_Thread_Cb func_cancel, const void *data, Eina_Bool try_no_queue);` | Çalışırken ana döngüye ara bildirim (feedback) gönderebilen iş parçacığı başlatır. | \- `func_heavy`: Arka planda çalışacak yoğun işlem (`Ecore_Thread_Cb`). \- `func_notify`: Bildirim geldiğinde ana döngüde çalışacak fonksiyon (`Ecore_Thread_Notify_Cb`, `NULL` olabilir). \- `func_end`: Bitiş fonksiyonu (`Ecore_Thread_Cb`, `NULL` olabilir). \- `func_cancel`: İptal fonksiyonu (`Ecore_Thread_Cb`, `NULL` olabilir). \- `data`: Kullanıcı verisi (`const void *`, `NULL` olabilir). \- `try_no_queue`: Kuyruğa almadan doğrudan çalıştırma denemesi (`EINA_TRUE` / `EINA_FALSE`). |
| `Eina_Bool` | `Eina_Bool gfx_thread_cancel(Ecore_Thread *th);` | Çalışan iş parçacığının iptal edilmesini ister. | \- `th`: İptal edilecek iş parçacığı (`Ecore_Thread *`). Başarılıysa `EINA_TRUE`, değilse `EINA_FALSE` döner. |
| `void` | `void gfx_thread_add_localdata(Ecore_Thread *th, const char *key, void *value, Eina_Free_Cb cb, Eina_Bool direct);` | İş parçacığına anahtar-değer biçiminde yerel veri ekler. | \- `th`: Hedef iş parçacığı (`Ecore_Thread *`). \- `key`: Veri anahtarı (`const char *`). \- `value`: Saklanacak veri işaretçisi (`void *`). \- `cb`: Veri serbest bırakılırken çağrılacak temizleyici (`Eina_Free_Cb`, `NULL` olabilir). \- `direct`: Doğrudan atama modu (`EINA_TRUE` / `EINA_FALSE`). |

---

## 7\. Harici Süreç (Process Execution) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Ecore_Exe *` | `Ecore_Exe *gfx_execute_run(const char *exe_command, const void *data);` | Harici bir komutu veya programı alt süreç olarak başlatır. | \- `exe_command`: Çalıştırılacak kabuk komutu (`const char *`). \- `data`: Süreçle ilişkilendirilecek kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `Ecore_Exe *` | `Ecore_Exe *gfx_execute_run_cb(const char *exe_command, Ecore_Exe_Cb func, const void *data);` | Harici komutu başlatır ve süreç sonlandığında `func` geri çağrısını tetikler. | \- `exe_command`: Çalıştırılacak komut (`const char *`). \- `func`: Süreç bitiminde çağrılacak fonksiyon (`Ecore_Exe_Cb`). \- `data`: Fonksiyona iletilecek kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_execute_terminate(Ecore_Exe *exe);` | Çalışan alt sürece sonlandırma sinyali (`SIGTERM`) gönderir. | \- `exe`: Hedef süreç işaretçisi (`Ecore_Exe *`). |
| `void` | `void gfx_execute_kill(Ecore_Exe *exe);` | Çalışan alt süreci zorla sonlandırır (`SIGKILL`). | \- `exe`: Hedef süreç işaretçisi (`Ecore_Exe *`). |
| `pid_t` | `pid_t gfx_execute_get_pid(const Ecore_Exe *exe);` | Alt sürecin işletim sistemi süreç kimliğini (PID) döner (`exe` `NULL` ise `-1`). | \- `exe`: Sorgulanacak süreç (`const Ecore_Exe *`). |
| `void` | `void gfx_execute_free(Ecore_Exe *exe);` | Süreç yapısına ayrılan belleği serbest bırakır (süreci öldürmez). | \- `exe`: Serbest bırakılacak süreç işaretçisi (`Ecore_Exe *`). |

---

## 8\. Dosya Tanımlayıcı (FD) ve Olay İşleyici (Event Handler) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Ecore_Fd_Handler *` | `Ecore_Fd_Handler *gfx_add_fdhandler(int fd, Ecore_Fd_Handler_Flags flags, Ecore_Fd_Cb func, const void *data, Ecore_Fd_Cb bufd_func, const void *bufd_data);` | Bir dosya tanımlayıcısını (soket, pipe vb.) okuma/yazma/hata olayları için ana döngüye bağlar. | \- `fd`: İzlenecek dosya tanımlayıcısı (`int`, `>= 0`). \- `flags`: İzleme bayrakları (`ECORE_FD_READ`, `ECORE_FD_WRITE`, `ECORE_FD_ERROR`). \- `func`: Olay gerçekleştiğinde çalışacak fonksiyon (`Ecore_Fd_Cb`). \- `data`: `func` için veri (`const void *`, `NULL` olabilir). \- `bufd_func`: Tamponlanmış veri kontrol fonksiyonu (`Ecore_Fd_Cb`, `NULL` olabilir). \- `bufd_data`: `bufd_func` için veri (`const void *`, `NULL` olabilir). |
| `void *` | `void *gfx_del_fdhandler(Ecore_Fd_Handler *fd_handler);` | Dosya tanımlayıcı dinleyicisini kaldırır ve kullanıcı verisini döner. | \- `fd_handler`: Silinecek dinleyici (`Ecore_Fd_Handler *`). |
| `Ecore_Event_Handler *` | `Ecore_Event_Handler *gfx_add_eventhandler(int type, Ecore_Event_Handler_Cb func, const void *data);` | Belirli bir sistem/uygulama olay türü için dinleyici ekler. | \- `type`: Olay tipi kimliği (`int`, `>= 0`). \- `func`: Olay işleyici fonksiyon (`Ecore_Event_Handler_Cb`). \- `data`: Fonksiyona aktarılacak veri (`const void *`, `NULL` olabilir). |

---

## 9\. Tuval (Canvas) ve Temel Nesne Yönetimi

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas *` | `Evas *gfx_object_get_evas(const Evas_Object *object);` | Nesnenin bağlı olduğu çizim tuvalini (`Evas *`) döndürür. | \- `object`: Kaynak nesne (`const Evas_Object *`). |
| `void` | `void gfx_object_show(Evas_Object *object);` | Nesneyi ekranda görünür hâle getirir. | \- `object`: Gösterilecek nesne (`Evas_Object *`). |
| `void` | `void gfx_object_hide(Evas_Object *object);` | Nesneyi ekrandan gizler (silmez). | \- `object`: Gizlenecek nesne (`Evas_Object *`). |
| `void` | `void gfx_object_del(Evas_Object *object);` | Nesneyi siler ve belleğini serbest bırakır. | \- `object`: Silinecek nesne (`Evas_Object *`). |
| `void` | `void gfx_object_raise(Evas_Object *object);` | Nesneyi çizim katmanında en üste taşır. | \- `object`: Üste alınacak nesne (`Evas_Object *`). |
| `void` | `void gfx_object_lower(Evas_Object *object);` | Nesneyi çizim katmanında en alta taşır. | \- `object`: Alta alınacak nesne (`Evas_Object *`). |
| `void` | `void gfx_object_set_size_hintweight(Evas_Object *object, double x, double y);` | Yerleşim kutularında nesnenin yatay ve dikey genişleme ağırlığını ayarlar. | \- `object`: Hedef nesne (`Evas_Object *`). \- `x`: Yatay ağırlık (`0.0` sabit, `EVAS_HINT_EXPAND` / `1.0` genişler). \- `y`: Dikey ağırlık (`0.0` sabit, `EVAS_HINT_EXPAND` / `1.0` genişler). |
| `void` | `void gfx_object_set_size_hintalign(Evas_Object *object, double x, double y);` | Nesnenin kendisine ayrılan alan içindeki hizalamasını veya doldurma davranışını ayarlar. | \- `object`: Hedef nesne (`Evas_Object *`). \- `x`: Yatay hizalama (`0.0` sol, `0.5` orta, `1.0` sağ, `EVAS_HINT_FILL` / `-1.0` doldur). \- `y`: Dikey hizalama (`0.0` üst, `0.5` orta, `1.0` alt, `EVAS_HINT_FILL` / `-1.0` doldur). |

---

## 10\. Geometri, Konum, Renk ve Kırpma (Clipping)

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_object_move(Evas_Object *object, Evas_Coord x, Evas_Coord y);` | Nesneyi tuval üzerinde belirtilen koordinata taşır. | \- `object`: Taşınacak nesne (`Evas_Object *`). \- `x`: Yatay konum (piksel, `Evas_Coord`). \- `y`: Dikey konum (piksel, `Evas_Coord`). |
| `void` | `void gfx_object_resize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Nesnenin genişlik ve yüksekliğini değiştirir. | \- `object`: Boyutlandırılacak nesne (`Evas_Object *`). \- `w`: Genişlik (piksel, `Evas_Coord`). \- `h`: Yükseklik (piksel, `Evas_Coord`). |
| `void` | `void gfx_object_get_geometry(Evas_Object *object, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | Nesnenin mevcut konum ve boyut bilgilerini alır. | \- `object`: Sorgulanacak nesne (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Çıktı işaretçileri (`Evas_Coord *`, istenmeyenler `NULL` geçilebilir). |
| `void` | `void gfx_object_set_color(Evas_Object *object, int r, int g, int b, int a);` | Nesnenin RGBA rengini ayarlar (önceden çarpılmış alfa formatı). | \- `object`: Hedef nesne (`Evas_Object *`). \- `r`, `g`, `b`: Kırmızı, yeşil, mavi değerleri (`0`–`255`). \- `a`: Şeffaflık/alfa değeri (`0` tam şeffaf – `255` tam opak). |
| `void` | `void gfx_object_get_color(const Evas_Object *object, int *r, int *g, int *b, int *a);` | Nesnenin mevcut RGBA renk bileşenlerini okur. | \- `object`: Sorgulanacak nesne (`const Evas_Object *`). \- `r`, `g`, `b`, `a`: Çıktı işaretçileri (`int *`, `NULL` olabilir). |
| `void` | `void gfx_object_set_minsize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Nesne için en küçük boyut ipucunu belirler. | \- `object`: Hedef nesne (`Evas_Object *`). \- `w`: Minimum genişlik (`Evas_Coord`). \- `h`: Minimum yükseklik (`Evas_Coord`). |
| `void` | `void gfx_object_set_maxsize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Nesne için en büyük boyut ipucunu belirler. | \- `object`: Hedef nesne (`Evas_Object *`). \- `w`: Maksimum genişlik (`Evas_Coord`). \- `h`: Maksimum yükseklik (`Evas_Coord`). |
| `void` | `void gfx_object_set_clip(Evas_Object *object, Evas_Object *clip);` | Nesnenin çizimini başka bir nesnenin sınırları içine kırpar (maskeler). | \- `object`: Kırpılacak nesne (`Evas_Object *`). \- `clip`: Maske görevi görecek nesne (`Evas_Object *`). |
| `void` | `void gfx_object_unset_clip(Evas_Object *object);` | Nesne üzerindeki kırpma maskesini kaldırır. | \- `object`: Kırpması kaldırılacak nesne (`Evas_Object *`). |

---

## 11\. Temel Tuval Nesneleri (Primitifler, Ham Görsel, Metin ve Olaylar)

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_rectangle(Evas *canvas);` | Tuval üzerine düz dikdörtgen nesnesi ekler. | \- `canvas`: Hedef tuval (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_rawimage(Evas *canvas);` | Tuval üzerine ham Evas resim nesnesi ekler. | \- `canvas`: Hedef tuval (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_text(Evas *canvas);` | Tuval üzerine tek satırlık temel metin nesnesi ekler. | \- `canvas`: Hedef tuval (`Evas *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_textblock(Evas *canvas);` | Tuval üzerine biçimlendirilebilir çok satırlı metin bloğu ekler. | \- `canvas`: Hedef tuval (`Evas *`). |
| `void` | `void gfx_object_set_imagefile(Evas_Object *object, const char *file, const char *key);` | Ham resim nesnesine dosya kaynağını yükler. | \- `object`: Ham resim nesnesi (`Evas_Object *`). \- `file`: Görsel dosya yolu (`const char *`). \- `key`: Arşiv/Eet içindeki grup anahtarı (`const char *`, normal dosyalar için `NULL`). |
| `void` | `void gfx_object_set_imagefilled(Evas_Object *object, Eina_Bool filled);` | Ham resmin nesne boyutunu otomatik doldurup doldurmayacağını ayarlar. | \- `object`: Ham resim nesnesi (`Evas_Object *`). \- `filled`: `EINA_TRUE` (otomatik doldur) veya `EINA_FALSE`. |
| `void` | `void gfx_object_set_imagesize(Evas_Object *object, int w, int h);` | Ham resmin iç piksel boyutunu ayarlar. | \- `object`: Ham resim nesnesi (`Evas_Object *`). \- `w`, `h`: Piksel genişlik ve yükseklik (`int`). |
| `void` | `void gfx_object_get_imagesize(Evas_Object *object, int *w, int *h);` | Ham resmin iç piksel boyutunu okur. | \- `object`: Ham resim nesnesi (`Evas_Object *`). \- `w`, `h`: Boyut çıktı işaretçileri (`int *`). |
| `Eina_Bool` | `Eina_Bool gfx_object_set_textfont(Evas_Object *object, const char *font, Evas_Font_Size size);` | Ham metin nesnesinin yazı tipini ve puntosunu belirler. | \- `object`: Metin nesnesi (`Evas_Object *`). \- `font`: Yazı tipi adı (`const char *`, örn. `"Sans"`). \- `size`: Yazı boyutu (`Evas_Font_Size`, `int`). |
| `void` | `void gfx_object_set_rawtext(Evas_Object *object, const char *text);` | Ham metin nesnesinin içeriğini değiştirir. | \- `object`: Metin nesnesi (`Evas_Object *`). \- `text`: Yazılacak metin (`const char *`). |
| `const char *` | `const char *gfx_object_get_rawtext(const Evas_Object *object);` | Ham metin nesnesindeki mevcut metni döndürür. | \- `object`: Metin nesnesi (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_object_set_textblockmarkup(Evas_Object *object, const char *text);` | Textblock nesnesine işaretleme (markup) destekli metin atar. | \- `object`: Textblock nesnesi (`Evas_Object *`). \- `text`: Biçimlendirilmiş metin (`const char *`). |
| `void` | `void gfx_object_add_eventcallback(Evas_Object *object, Evas_Callback_Type type, Evas_Object_Event_Cb func, const void *data);` | Nesneye düşük seviye giriş/tuval olayı (fare, klavye vb.) bağlar. | \- `object`: Hedef nesne (`Evas_Object *`). \- `type`: Olay tipi (`EVAS_CALLBACK_MOUSE_DOWN`, `EVAS_CALLBACK_KEY_DOWN` vb.). \- `func`: Olay geri çağrı fonksiyonu (`Evas_Object_Event_Cb`). \- `data`: Kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_object_add_smartcallback(Evas_Object *object, const char *event, Evas_Smart_Cb func, const void *data);` | Widget veya akıllı nesneye yüksek seviye sinyal geri çağrısı ekler. | \- `object`: Hedef widget/nesne (`Evas_Object *`). \- `event`: Sinyal adı (`const char *`, örn. `"clicked"`, `"changed"`). \- `func`: Geri çağrı fonksiyonu (`Evas_Smart_Cb` veya `Gfx_Callback`). \- `data`: Kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_object_set_focus(Evas_Object *object, Eina_Bool focus);` | Nesneye klavye odağı verir veya odağı kaldırır. | \- `object`: Hedef nesne (`Evas_Object *`). \- `focus`: `EINA_TRUE` (odak ver) veya `EINA_FALSE` (odağı kaldır). |

---

## 12\. Pencere (Window) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_new_feature_window(const char *name, const char *title);` | Standart arka planlı hazır pencere oluşturur. | \- `name`: Pencere iç adı (`const char *`). \- `title`: Pencere başlık çubuğu metni (`const char *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_new_standard_window(Evas_Object *parent, const char *name, Elm_Win_Type type);` | Belirtilen türde temel bir pencere nesnesi oluşturur. | \- `parent`: Üst pencere (`Evas_Object *`, ana pencere için `NULL`). \- `name`: Pencere iç adı (`const char *`). \- `type`: Pencere türü (`ELM_WIN_BASIC`, `ELM_WIN_DIALOG_BASIC`, `ELM_WIN_SPLASH` vb.). |
| `Evas_Object *` | `Evas_Object *gfx_object_create_window(Evas_Object *parent, const char *name, Elm_Win_Type type, const char *title, Evas_Coord x, Evas_Coord y);` | Pencereyi oluşturur, başlığını ve otomatik silme özelliğini ayarlar, boyutlandırır ve ekranda gösterir. | \- `parent`: Üst pencere (`Evas_Object *`, `NULL` olabilir). \- `name`: Pencere iç adı (`const char *`). \- `type`: Pencere türü (`Elm_Win_Type`). \- `title`: Pencere başlığı (`const char *`). \- `x`: Pencere genişliği (`Evas_Coord`). \- `y`: Pencere yüksekliği (`Evas_Coord`). |
| `int` | `int gfx_window_set_title(Evas_Object *object, const char *title);` | Pencerenin başlık metnini değiştirir. Başarılıysa `0`, hata varsa `-1` döner. | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `title`: Yeni başlık metni (`const char *`). |
| `int` | `int gfx_window_set_autodel(Evas_Object *object, Eina_Bool autodel);` | Pencere kapatma düğmesine basıldığında otomatik silinmesini ayarlar (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `autodel`: `EINA_TRUE` veya `EINA_FALSE`. |
| `int` | `int gfx_window_set_center(Evas_Object *object, Eina_Bool h, Eina_Bool v);` | Pencereyi ekranda yatay ve/veya dikey olarak ortalar (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `h`: Yatay ortalama (`EINA_TRUE` / `EINA_FALSE`). \- `v`: Dikey ortalama (`EINA_TRUE` / `EINA_FALSE`). |
| `int` | `int gfx_window_set_minsize(Evas_Object *object, int w, int h);` | Pencerenin taban/minimum boyutunu ayarlar (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `w`: Genişlik (`int`). \- `h`: Yükseklik (`int`). |
| `int` | `int gfx_window_set_fullscreen(Evas_Object *object, Eina_Bool full);` | Pencerenin tam ekran modunu açar veya kapatır (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `full`: `EINA_TRUE` (tam ekran) veya `EINA_FALSE`. |
| `int` | `int gfx_window_set_active(Evas_Object *object);` | Pencereyi öne getirip etkin pencere yapar (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). |
| `int` | `int gfx_window_set_iconified(Evas_Object *object, Eina_Bool iconified);` | Pencereyi simge durumuna küçültür veya geri yükler (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `iconified`: `EINA_TRUE` (küçült) veya `EINA_FALSE`. |
| `int` | `int gfx_window_set_sticky(Evas_Object *object, Eina_Bool sticky);` | Pencerenin tüm sanal masaüstlerinde görünmesini ayarlar (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `sticky`: `EINA_TRUE` veya `EINA_FALSE`. |
| `int` | `int gfx_window_proportion_scale(Evas_Object *object, Evas_Object *subobject);` | Alt nesneyi pencere boyutuna bağlı olarak otomatik ölçeklenecek şekilde ekler (`0` / `-1`). | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `subobject`: Pencereye bağlanacak kapsayıcı/widget (`Evas_Object *`). |
| `void` | `void gfx_window_set_autohide(Evas_Object *object, Eina_Bool autohide);` | Pencere kapatıldığında silmek yerine otomatik gizlenmesini ayarlar. | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `autohide`: `EINA_TRUE` veya `EINA_FALSE`. |
| `void` | `void gfx_window_set_borderless(Evas_Object *object, Eina_Bool borderless);` | Pencere kenarlıklarını ve başlık çubuğunu gizler veya gösterir. | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `borderless`: `EINA_TRUE` (kenarlıksız) veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_borderless(const Evas_Object *object);` | Pencerenin kenarlıksız olup olmadığını döndürür. | \- `object`: Pencere nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_window_set_shaped(Evas_Object *object, Eina_Bool shaped);` | Pencerenin şekilli (şeffaf maskeli) pencere modunu ayarlar. | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `shaped`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_shaped(const Evas_Object *object);` | Pencerenin şekilli modda olup olmadığını döndürür. | \- `object`: Pencere nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_window_set_alpha(Evas_Object *object, Eina_Bool alpha);` | Pencere için alfa kanalı (yarı saydamlık) desteğini açar/kapatır. | \- `object`: Pencere nesnesi (`Evas_Object *`). \- `alpha`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_window_get_alpha(const Evas_Object *object);` | Pencerenin alfa kanalı desteğinin açık olup olmadığını döndürür. | \- `object`: Pencere nesnesi (`const Evas_Object *`). |

---

## 13\. Arayüz Bileşenleri (Widget Oluşturma Fonksiyonları)

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_button(Evas_Object *parent);` | Tıklanabilir buton bileşeni oluşturur. | \- `parent`: Üst nesne/pencere (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_checkbutton(Evas_Object *parent);` | İşaret kutusu (checkbox) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_radiobutton(Evas_Object *parent);` | Seçenek düğmesi (radio button) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_entry(Evas_Object *parent);` | Metin giriş kutusu (entry) oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_slider(Evas_Object *parent);` | Kaydırma çubuğu (slider) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_spinner(Evas_Object *parent);` | Sayısal değer artırıp azaltma (spinner) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_combobox(Evas_Object *parent);` | Açılır seçim kutusu (combobox) oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_label(Evas_Object *parent);` | Metin etiketi (label) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_icon(Evas_Object *parent);` | Tema/standart simge (icon) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_image(Evas_Object *parent);` | Yüksek seviye resim (image) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_progressbar(Evas_Object *parent);` | İlerleme çubuğu (progressbar) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_bubble(Evas_Object *parent);` | Bilgi balonu (bubble) kapsayıcısı oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_clock(Evas_Object *parent);` | Dijital saat bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_calendar(Evas_Object *parent);` | Takvim bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_separator(Evas_Object *parent);` | Ayırıcı çizgi (separator) bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_popup(Evas_Object *parent);` | Açılır uyarı/diyalog kutusu (popup) oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |

---

## 14\. Ortak Nesne İçerik, Metin ve Durum Yönetimi

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_object_set_text(Evas_Object *object, const char *text);` | Widget'ın ana metnini (buton etiketi, başlık vb.) ayarlar. | \- `object`: Hedef widget (`Evas_Object *`). \- `text`: Atanacak metin (`const char *`). |
| `const char *` | `const char *gfx_object_get_text(const Evas_Object *object);` | Widget'ın ana metnini döndürür. | \- `object`: Sorgulanacak widget (`const Evas_Object *`). |
| `void` | `void gfx_object_set_content(Evas_Object *object, Evas_Object *content);` | Kapsayıcı widget'ın ana içeriğini ayarlar. | \- `object`: Kapsayıcı widget (`Evas_Object *`). \- `content`: İçerik nesnesi (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_get_content(const Evas_Object *object);` | Kapsayıcı widget'ın mevcut içerik nesnesini döndürür. | \- `object`: Kapsayıcı widget (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_image_set_file(Evas_Object *object, const char *file, const char *group);` | Elementary resim widget'ına görsel dosyası yükler. | \- `object`: Resim widget'ı (`Evas_Object *`). \- `file`: Dosya yolu (`const char *`). \- `group`: Edje/Eet grup adı (`const char *`, `NULL` olabilir). |
| `Eina_Bool` | `Eina_Bool gfx_icon_set_standard(Evas_Object *object, const char *name);` | Simge widget'ına standart tema simgesi atar. | \- `object`: Simge widget'ı (`Evas_Object *`). \- `name`: Standart simge adı (`const char *`, örn. `"home"`, `"close"`). |
| `void` | `void gfx_object_set_parttext(Evas_Object *object, const char *part, const char *text);` | Widget'ın belirli bir tema parçasının metnini ayarlar. | \- `object`: Hedef widget (`Evas_Object *`). \- `part`: Parça adı (`const char *`, örn. `"default"`, `"info"`). \- `text`: Metin (`const char *`). |
| `void` | `void gfx_object_set_partcontent(Evas_Object *object, const char *part, Evas_Object *content);` | Widget'ın belirli bir tema parçasına alt nesne yerleştirir. | \- `object`: Hedef widget (`Evas_Object *`). \- `part`: Parça adı (`const char *`, örn. `"icon"`). \- `content`: Yerleştirilecek nesne (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_set_policy(unsigned int policy, int value);` | Genel uygulama politikasını ayarlar (ör. son pencere kapanınca çıkış). | \- `policy`: Politika türü (örn. `ELM_POLICY_QUIT`). \- `value`: Politika değeri (örn. `ELM_POLICY_QUIT_LAST_WINDOW_CLOSED`). |
| `void` | `void gfx_object_set_disabled(Evas_Object *object, Eina_Bool disabled);` | Widget'ı kullanıcı etkileşimine kapatır veya açar. | \- `object`: Hedef widget (`Evas_Object *`). \- `disabled`: `EINA_TRUE` (devre dışı) veya `EINA_FALSE` (etkin). |

---

## 15\. Entry, Check, Radio ve Slider Bileşen Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `void` | `void gfx_entry_set_text(Evas_Object *object, const char *text);` | Entry metin kutusunun içeriğini ayarlar. | \- `object`: Entry nesnesi (`Evas_Object *`). \- `text`: Yazılacak metin (`const char *`). |
| `const char *` | `const char *gfx_entry_get_text(const Evas_Object *object);` | Entry metin kutusundaki metni döndürür. | \- `object`: Entry nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_entry_set_singleline(Evas_Object *object, Eina_Bool single_line);` | Entry'nin tek satır mı çok satır mı çalışacağını belirler. | \- `object`: Entry nesnesi (`Evas_Object *`). \- `single_line`: `EINA_TRUE` (tek satır) veya `EINA_FALSE`. |
| `void` | `void gfx_entry_set_password(Evas_Object *object, Eina_Bool password);` | Entry'yi karakterleri gizleyen parola moduna alır veya çıkarır. | \- `object`: Entry nesnesi (`Evas_Object *`). \- `password`: `EINA_TRUE` (parola modu) veya `EINA_FALSE`. |
| `void` | `void gfx_check_set_state(Evas_Object *object, Eina_Bool checked);` | İşaret kutusunun seçili durumunu ayarlar. | \- `object`: Check nesnesi (`Evas_Object *`). \- `checked`: `EINA_TRUE` (seçili) veya `EINA_FALSE` (seçili değil). |
| `Eina_Bool` | `Eina_Bool gfx_check_get_state(const Evas_Object *object);` | İşaret kutusunun seçili olup olmadığını döndürür. | \- `object`: Check nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_radio_set_statevalue(Evas_Object *object, int value);` | Radio butonunun kendi temsil ettiği tam sayı değerini belirler. | \- `object`: Radio nesnesi (`Evas_Object *`). \- `value`: Bu seçeneğe atanacak değer (`int`). |
| `void` | `void gfx_radio_set_value(Evas_Object *object, int value);` | Radio grubunda aktif seçili değeri değiştirir. | \- `object`: Gruptaki herhangi bir radio nesnesi (`Evas_Object *`). \- `value`: Seçilecek radio değeri (`int`). |
| `int` | `int gfx_radio_get_value(const Evas_Object *object);` | Radio grubunda o an seçili olan değeri döndürür (`object` `NULL` ise `-1`). | \- `object`: Radio nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_radio_add_group(Evas_Object *object, Evas_Object *group);` | Radio butonunu başka bir radio butonunun grubuna dahil eder. | \- `object`: Gruba eklenecek radio (`Evas_Object *`). \- `group`: Hedef grubun ana radio nesnesi (`Evas_Object *`). |
| `void` | `void gfx_radio_set_pointervalue(Evas_Object *object, int *valuep);` | Seçili radio değerinin otomatik güncelleneceği dış `int` değişkeni bağlar. | \- `object`: Radio nesnesi (`Evas_Object *`). \- `valuep`: Değişken adresi (`int *`). |
| `void` | `void gfx_slider_set_minmax(Evas_Object *object, double min, double max);` | Kaydırma çubuğunun alt ve üst sınır değerlerini ayarlar. | \- `object`: Slider nesnesi (`Evas_Object *`). \- `min`: Minimum değer (`double`). \- `max`: Maksimum değer (`double`). |
| `void` | `void gfx_slider_set_unitformat(Evas_Object *object, const char *format);` | Kaydırma çubuğu üzerindeki birim metni biçimini (`printf` tarzı) ayarlar. | \- `object`: Slider nesnesi (`Evas_Object *`). \- `format`: Biçim dizgesi (`const char *`, örn. `"%1.0f %%"`). |
| `void` | `void gfx_slider_set_value(Evas_Object *object, double value);` | Kaydırma çubuğunun mevcut değerini ayarlar. | \- `object`: Slider nesnesi (`Evas_Object *`). \- `value`: Yeni değer (`double`). |
| `double` | `double gfx_slider_get_value(const Evas_Object *object);` | Kaydırma çubuğunun mevcut değerini döndürür (`object` `NULL` ise `0.0`). | \- `object`: Slider nesnesi (`const Evas_Object *`). |

---

## 16\. Kutu Yerleşimi (Box) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_box(Evas_Object *parent);` | Alt nesneleri yatay veya dikey sıralayan kutu (box) oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_box_set_horizontal(Evas_Object *object, Eina_Bool horizontal);` | Kutunun dizilim yönünü yatay veya dikey olarak ayarlar. | \- `object`: Box nesnesi (`Evas_Object *`). \- `horizontal`: `EINA_TRUE` (yatay) veya `EINA_FALSE` (dikey). |
| `Eina_Bool` | `Eina_Bool gfx_box_get_horizontal(const Evas_Object *object);` | Kutunun yatay modda olup olmadığını döndürür. | \- `object`: Box nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_box_pack_start(Evas_Object *object, Evas_Object *child);` | Alt nesneyi kutunun en başına ekler. | \- `object`: Box nesnesi (`Evas_Object *`). \- `child`: Eklenecek nesne (`Evas_Object *`). |
| `void` | `void gfx_box_pack_end(Evas_Object *object, Evas_Object *child);` | Alt nesneyi kutunun en sonuna ekler. | \- `object`: Box nesnesi (`Evas_Object *`). \- `child`: Eklenecek nesne (`Evas_Object *`). |
| `void` | `void gfx_box_pack_before(Evas_Object *object, Evas_Object *child, Evas_Object *before);` | Alt nesneyi belirtilen referans nesnenin öncesine ekler. | \- `object`: Box nesnesi (`Evas_Object *`). \- `child`: Eklenecek nesne (`Evas_Object *`). \- `before`: Referans nesne (`Evas_Object *`). |
| `void` | `void gfx_box_pack_after(Evas_Object *object, Evas_Object *child, Evas_Object *after);` | Alt nesneyi belirtilen referans nesnenin sonrasına ekler. | \- `object`: Box nesnesi (`Evas_Object *`). \- `child`: Eklenecek nesne (`Evas_Object *`). \- `after`: Referans nesne (`Evas_Object *`). |
| `void` | `void gfx_box_unpack(Evas_Object *object, Evas_Object *child);` | Belirtilen alt nesneyi kutudan çıkarır (silmez). | \- `object`: Box nesnesi (`Evas_Object *`). \- `child`: Çıkarılacak nesne (`Evas_Object *`). |
| `void` | `void gfx_box_unpack_all(Evas_Object *object);` | Kutudaki tüm alt nesneleri kutudan çıkarır (silmez). | \- `object`: Box nesnesi (`Evas_Object *`). |
| `void` | `void gfx_box_set_homogeneous(Evas_Object *object, Eina_Bool homogeneous);` | Kutudaki tüm öğelere eşit alan dağıtılıp dağıtılmayacağını ayarlar. | \- `object`: Box nesnesi (`Evas_Object *`). \- `homogeneous`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_box_get_homogeneous(const Evas_Object *object);` | Kutunun homojen modda olup olmadığını döndürür. | \- `object`: Box nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_box_set_padding(Evas_Object *object, Evas_Coord horizontal, Evas_Coord vertical);` | Kutu içindeki öğeler arası yatay ve dikey boşluğu (piksel) ayarlar. | \- `object`: Box nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Boşluk miktarı (`Evas_Coord`). |
| `void` | `void gfx_box_get_padding(Evas_Object *object, Evas_Coord *horizontal, Evas_Coord *vertical);` | Kutunun öğeler arası boşluk değerlerini okur. | \- `object`: Box nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Çıktı işaretçileri (`Evas_Coord *`). |
| `void` | `void gfx_box_set_align(Evas_Object *object, double horizontal, double vertical);` | Kutu içindeki öğelerin genel hizalamasını ayarlar. | \- `object`: Box nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Hizalama oranı (`0.0`–`1.0`). |
| `void` | `void gfx_box_get_align(Evas_Object *object, double *horizontal, double *vertical);` | Kutunun genel hizalama değerlerini okur. | \- `object`: Box nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Çıktı işaretçileri (`double *`). |
| `Eina_List *` | `Eina_List *gfx_box_get_children(const Evas_Object *object);` | Kutu içindeki alt nesnelerin listesini döndürür. | \- `object`: Box nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_box_clear(Evas_Object *object);` | Kutudaki tüm alt nesneleri çıkarır ve siler. | \- `object`: Box nesnesi (`Evas_Object *`). |
| `void` | `void gfx_box_set_layout(Evas_Object *object, Evas_Object_Box_Layout func, const void *data, Eina_Free_Cb free_data);` | Kutuya özel yerleşim algoritması atar. | \- `object`: Box nesnesi (`Evas_Object *`). \- `func`: Yerleşim fonksiyonu (`Evas_Object_Box_Layout`). \- `data`: Yerleşim verisi (`const void *`, `NULL` olabilir). \- `free_data`: Veri temizleyici (`Eina_Free_Cb`, `NULL` olabilir). |

---

## 17\. Izgara (Grid) ve Tablo (Table) Yerleşim Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_grid(Evas_Object *parent);` | Sanal koordinat sistemine dayalı ızgara (grid) kapsayıcısı oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_grid_set_size(Evas_Object *object, int w, int h);` | Izgaranın sanal sütun ve satır çözünürlüğünü belirler (varsayılan `100x100`). | \- `object`: Grid nesnesi (`Evas_Object *`). \- `w`, `h`: Sanal genişlik ve yükseklik birimleri (`int`). |
| `void` | `void gfx_grid_get_size(const Evas_Object *object, int *w, int *h);` | Izgaranın sanal boyutlarını okur. | \- `object`: Grid nesnesi (`const Evas_Object *`). \- `w`, `h`: Çıktı işaretçileri (`int *`). |
| `void` | `void gfx_grid_add_pack(Evas_Object *object, Evas_Object *subobject, int x, int y, int w, int h);` | Alt nesneyi ızgara üzerindeki sanal koordinat ve boyuta yerleştirir. | \- `object`: Grid nesnesi (`Evas_Object *`). \- `subobject`: Eklenecek nesne (`Evas_Object *`). \- `x`, `y`: Başlangıç hücresi (`int`). \- `w`, `h`: Kaplayacağı hücre genişlik ve yüksekliği (`int`). |
| `void` | `void gfx_grid_unpack(Evas_Object *object, Evas_Object *subobject);` | Alt nesneyi ızgaradan çıkarır (silmez). | \- `object`: Grid nesnesi (`Evas_Object *`). \- `subobject`: Çıkarılacak nesne (`Evas_Object *`). |
| `void` | `void gfx_grid_set_pack(Evas_Object *subobject, Evas_Coord x, Evas_Coord y, Evas_Coord w, Evas_Coord h);` | Izgaraya eklenmiş bir alt nesnenin konum ve boyutunu günceller. | \- `subobject`: Izgara içindeki alt nesne (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Yeni sanal koordinat ve boyutlar (`Evas_Coord`). |
| `void` | `void gfx_grid_get_pack(Evas_Object *subobject, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | Alt nesnenin ızgara üzerindeki konum ve boyutunu okur. | \- `subobject`: Izgara içindeki alt nesne (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Çıktı işaretçileri (`Evas_Coord *`). |
| `Eina_List *` | `Eina_List *gfx_grid_get_children(const Evas_Object *object);` | Izgaradaki tüm alt nesnelerin listesini döndürür. | \- `object`: Grid nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_grid_clear(Evas_Object *object, Eina_Bool clear);` | Izgarayı boşaltır; `clear` `EINA_TRUE` ise alt nesneleri de siler. | \- `object`: Grid nesnesi (`Evas_Object *`). \- `clear`: `EINA_TRUE` (nesneleri sil) veya `EINA_FALSE` (sadece çıkar). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_table(Evas_Object *parent);` | Satır ve sütun hücrelerinden oluşan tablo (table) kapsayıcısı oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_table_set_align(Evas_Object *object, double horizontal, double vertical);` | Tablo içeriğinin hizalamasını ayarlar. | \- `object`: Table nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Hizalama oranı (`0.0`–`1.0`). |
| `void` | `void gfx_table_get_align(const Evas_Object *object, double *horizontal, double *vertical);` | Tablo hizalama değerlerini okur. | \- `object`: Table nesnesi (`const Evas_Object *`). \- `horizontal`, `vertical`: Çıktı işaretçileri (`double *`). |
| `Evas_Object *` | `Evas_Object *gfx_table_get_child(const Evas_Object *object, int col, int row);` | Belirtilen sütun ve satırdaki alt nesneyi döndürür. | \- `object`: Table nesnesi (`const Evas_Object *`). \- `col`: Sütun indeksi (`int`). \- `row`: Satır indeksi (`int`). |
| `void` | `void gfx_table_clear(Evas_Object *object, Eina_Bool clear);` | Tabloyu temizler; `clear` `EINA_TRUE` ise alt nesneleri de siler. | \- `object`: Table nesnesi (`Evas_Object *`). \- `clear`: `EINA_TRUE` veya `EINA_FALSE`. |
| `void` | `void gfx_table_set_homogeneous(Evas_Object *object, Eina_Bool homogeneous);` | Tablo hücrelerinin eşit boyutta olup olmayacağını belirler. | \- `object`: Table nesnesi (`Evas_Object *`). \- `homogeneous`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_table_get_homogeneous(const Evas_Object *object);` | Tablonun homojen modda olup olmadığını döndürür. | \- `object`: Table nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_table_pack(Evas_Object *object, Evas_Object *subobject, int col, int row, int colspan, int rowspan);` | Alt nesneyi tablonun belirtilen hücresine yerleştirir. | \- `object`: Table nesnesi (`Evas_Object *`). \- `subobject`: Eklenecek nesne (`Evas_Object *`). \- `col`, `row`: Sütun ve satır konumu (`int`). \- `colspan`, `rowspan`: Yatay ve dikey birleştirilecek hücre sayısı (`int`, en az `1`). |
| `void` | `void gfx_table_set_pack(Evas_Object *subobject, int col, int row, int colspan, int rowspan);` | Tablodaki alt nesnenin hücre konumunu ve yayılımını günceller. | \- `subobject`: Tablo içindeki alt nesne (`Evas_Object *`). \- `col`, `row`, `colspan`, `rowspan`: Yeni hücre değerleri (`int`). |
| `void` | `void gfx_table_get_pack(Evas_Object *subobject, int *col, int *row, int *colspan, int *rowspan);` | Tablodaki alt nesnenin hücre bilgilerini okur. | \- `subobject`: Tablo içindeki alt nesne (`Evas_Object *`). \- `col`, `row`, `colspan`, `rowspan`: Çıktı işaretçileri (`int *`). |
| `void` | `void gfx_table_set_padding(Evas_Object *object, Evas_Coord horizontal, Evas_Coord vertical);` | Tablo hücreleri arasındaki yatay ve dikey boşluğu ayarlar. | \- `object`: Table nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Boşluk (piksel, `Evas_Coord`). |
| `void` | `void gfx_table_get_padding(Evas_Object *object, Evas_Coord *horizontal, Evas_Coord *vertical);` | Tablo hücre boşluklarını okur. | \- `object`: Table nesnesi (`Evas_Object *`). \- `horizontal`, `vertical`: Çıktı işaretçileri (`Evas_Coord *`). |
| `void` | `void gfx_table_unpack(Evas_Object *object, Evas_Object *subobject);` | Alt nesneyi tablodan çıkarır (silmez). | \- `object`: Table nesnesi (`Evas_Object *`). \- `subobject`: Çıkarılacak nesne (`Evas_Object *`). |

---

## 18\. Kaydırma Alanı (Scroller) ve Çerçeve (Frame) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_scroller(Evas_Object *parent);` | Kaydırılabilir (scrollable) kapsayıcı oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_scroller_set_policy(Evas_Object *object, Elm_Scroller_Policy policy_h, Elm_Scroller_Policy policy_v);` | Yatay ve dikey kaydırma çubuklarının görünürlük politikasını ayarlar. | \- `object`: Scroller nesnesi (`Evas_Object *`). \- `policy_h`, `policy_v`: `ELM_SCROLLER_POLICY_AUTO`, `ELM_SCROLLER_POLICY_ON`, `ELM_SCROLLER_POLICY_OFF`. |
| `void` | `void gfx_scroller_get_policy(const Evas_Object *object, Elm_Scroller_Policy *policy_h, Elm_Scroller_Policy *policy_v);` | Mevcut kaydırma çubuğu politikalarını okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `policy_h`, `policy_v`: Çıktı işaretçileri (`Elm_Scroller_Policy *`). |
| `void` | `void gfx_scroller_set_region(Evas_Object *object, Evas_Coord x, Evas_Coord y, Evas_Coord w, Evas_Coord h);` | Belirtilen dikdörtgen bölgeyi görünür alana kaydırır. | \- `object`: Scroller nesnesi (`Evas_Object *`). \- `x`, `y`, `w`, `h`: Bölge koordinat ve boyutları (`Evas_Coord`). |
| `void` | `void gfx_scroller_get_region(const Evas_Object *object, Evas_Coord *x, Evas_Coord *y, Evas_Coord *w, Evas_Coord *h);` | O an görünür olan kaydırma bölgesini okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `x`, `y`, `w`, `h`: Çıktı işaretçileri (`Evas_Coord *`). |
| `void` | `void gfx_scroller_get_position(const Evas_Object *object, Evas_Coord *x, Evas_Coord *y);` | Mevcut kaydırma konumunu (`x`, `y`) okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `x`, `y`: Konum çıktı işaretçileri (`Evas_Coord *`). |
| `void` | `void gfx_scroller_set_pagesize(Evas_Object *object, Evas_Coord w, Evas_Coord h);` | Kaydırma alanının sayfa boyutlarını ayarlar. | \- `object`: Scroller nesnesi (`Evas_Object *`). \- `w`, `h`: Sayfa genişlik ve yüksekliği (`Evas_Coord`). |
| `void` | `void gfx_scroller_get_pagesize(const Evas_Object *object, Evas_Coord *w, Evas_Coord *h);` | Kaydırma alanının sayfa boyutlarını okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `w`, `h`: Çıktı işaretçileri (`Evas_Coord *`). |
| `void` | `void gfx_scroller_get_currentpage(const Evas_Object *object, int *hp, int *vp);` | Mevcut yatay ve dikey sayfa indekslerini okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `hp`, `vp`: Yatay ve dikey sayfa çıktı işaretçileri (`int *`). |
| `void` | `void gfx_scroller_show_page(Evas_Object *object, int hp, int vp);` | Belirtilen yatay ve dikey sayfaya kaydırır. | \- `object`: Scroller nesnesi (`Evas_Object *`). \- `hp`, `vp`: Hedef yatay ve dikey sayfa indeksleri (`int`). |
| `void` | `void gfx_scroller_set_bounce(Evas_Object *object, Eina_Bool h_bounce, Eina_Bool v_bounce);` | Yatay ve dikey kaydırmada kenar sekme (bounce) efektini açar veya kapatır. | \- `object`: Scroller nesnesi (`Evas_Object *`). \- `h_bounce`, `v_bounce`: `EINA_TRUE` veya `EINA_FALSE`. |
| `void` | `void gfx_scroller_get_bounce(const Evas_Object *object, Eina_Bool *h_bounce, Eina_Bool *v_bounce);` | Yatay ve dikey kenar sekme efektlerinin açık olup olmadığını okur. | \- `object`: Scroller nesnesi (`const Evas_Object *`). \- `h_bounce`, `v_bounce`: Çıktı işaretçileri (`Eina_Bool *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_frame(Evas_Object *parent);` | Başlıklı çerçeve (frame) kapsayıcı bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_frame_set_autocollapse(Evas_Object *object, Eina_Bool autocollapse);` | Çerçeveye tıklandığında otomatik katlanıp açılma özelliğini ayarlar. | \- `object`: Frame nesnesi (`Evas_Object *`). \- `autocollapse`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_frame_get_autocollapse(const Evas_Object *object);` | Çerçevede otomatik katlanma özelliğinin açık olup olmadığını döndürür. | \- `object`: Frame nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_frame_set_collapse(Evas_Object *object, Eina_Bool collapse);` | Çerçeve içeriğini katlar veya açar. | \- `object`: Frame nesnesi (`Evas_Object *`). \- `collapse`: `EINA_TRUE` (katlı) veya `EINA_FALSE` (açık). |
| `Eina_Bool` | `Eina_Bool gfx_frame_get_collapse(const Evas_Object *object);` | Çerçevenin o an katlı olup olmadığını döndürür. | \- `object`: Frame nesnesi (`const Evas_Object *`). |

---

## 19\. Genlist ve Background Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_genlist_add_item(Evas_Object *object, const Elm_Genlist_Item_Class *itemclass, const void *data, Elm_Object_Item *parent, Elm_Genlist_Item_Type flags, Evas_Smart_Cb function, const void *function_data);` | Genlist bileşenine yeni öğe ekler. | \- `object`: Genlist nesnesi (`Evas_Object *`). \- `itemclass`: Öğe sınıf tanımı (`const Elm_Genlist_Item_Class *`). \- `data`: Öğe kullanıcı verisi (`const void *`, `NULL` olabilir). \- `parent`: Ağaç öğeleri için üst öğe (`Elm_Object_Item *`, kök öğeler için `NULL`). \- `flags`: Öğe türü (`ELM_GENLIST_ITEM_NONE`, `ELM_GENLIST_ITEM_TREE` vb.). \- `function`: Seçim geri çağrısı (`Evas_Smart_Cb`, `NULL` olabilir). \- `function_data`: Geri çağrı kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_genlist_clear(Evas_Object *object);` | Genlist bileşenindeki tüm öğeleri temizler. | \- `object`: Genlist nesnesi (`Evas_Object *`). |
| `Elm_Genlist_Item_Class *` | `Elm_Genlist_Item_Class *gfx_genlist_add_itemclass(void);` | Yeni bir genlist öğe sınıfı yapısı ayırır ve döndürür. | Yok (`void`). |
| `void` | `void gfx_genlist_free_itemclass(Elm_Genlist_Item_Class *itemclass);` | Ayrılmış genlist öğe sınıfı yapısını serbest bırakır. | \- `itemclass`: Öğe sınıfı işaretçisi (`Elm_Genlist_Item_Class *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_background(Evas_Object *parent);` | Arka plan (bg) nesnesi oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_background_set_file(Evas_Object *object, const char *file, const char *group);` | Arka plan kaynağı olarak görsel veya Edje dosyası yükler. | \- `object`: Background nesnesi (`Evas_Object *`). \- `file`: Dosya yolu (`const char *`). \- `group`: Dosya içindeki grup adı (`const char *`, `NULL` olabilir). |
| `void` | `void gfx_background_get_file(const Evas_Object *object, const char **file, const char **group);` | Arka planın mevcut dosya yolunu ve grup adını okur. | \- `object`: Background nesnesi (`const Evas_Object *`). \- `file`, `group`: Çıktı işaretçileri (`const char **`). |
| `void` | `void gfx_background_set_option(Evas_Object *object, Elm_Bg_Option option);` | Arka plan görselinin nasıl yerleştirileceğini/ölçekleneceğini ayarlar. | \- `object`: Background nesnesi (`Evas_Object *`). \- `option`: `ELM_BG_OPTION_CENTER`, `ELM_BG_OPTION_SCALE`, `ELM_BG_OPTION_TILE`, `ELM_BG_OPTION_STRETCH`. |
| `Elm_Bg_Option` | `Elm_Bg_Option gfx_background_get_option(const Evas_Object *object);` | Mevcut arka plan yerleşim seçeneğini döndürür. | \- `object`: Background nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_background_set_color(Evas_Object *object, int r, int g, int b);` | Arka planın düz RGB rengini ayarlar. | \- `object`: Background nesnesi (`Evas_Object *`). \- `r`, `g`, `b`: Kırmızı, yeşil, mavi değerleri (`0`–`255`). |
| `void` | `void gfx_background_get_color(const Evas_Object *object, int *r, int *g, int *b);` | Arka planın mevcut RGB renk bileşenlerini okur. | \- `object`: Background nesnesi (`const Evas_Object *`). \- `r`, `g`, `b`: Çıktı işaretçileri (`int *`). |
| `void` | `void gfx_background_set_loadsize(Evas_Object *object, int w, int h);` | Arka plan görseli için yükleme/çözümleme boyut ipucunu ayarlar. | \- `object`: Background nesnesi (`Evas_Object *`). \- `w`, `h`: Yükleme genişlik ve yüksekliği (`int`). |

---

## 20\. Menü (Menu) Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_menu(Evas_Object *parent);` | Menü bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_item(Evas_Object *object, Elm_Object_Item *parent, const char *icon, const char *label, Evas_Smart_Cb func, const void *data);` | Menüye veya üst menü öğesinin altına yeni öğe ekler. | \- `object`: Menü nesnesi (`Evas_Object *`). \- `parent`: Üst öğe (`Elm_Object_Item *`, kök için `NULL`). \- `icon`: Simge adı (`const char *`, `NULL` olabilir). \- `label`: Öğe etiket metni (`const char *`). \- `func`: Seçim geri çağrısı (`Evas_Smart_Cb`, `NULL` olabilir). \- `data`: Geri çağrı kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_separator(Evas_Object *object, Elm_Object_Item *parent);` | Menüye ayırıcı çizgi öğesi ekler. | \- `object`: Menü nesnesi (`Evas_Object *`). \- `parent`: Üst öğe (`Elm_Object_Item *`, kök için `NULL`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_add_submenu(Evas_Object *object, Elm_Object_Item *parent, const char *label);` | Menü veya üst öğe altında alt menü kapsayıcı öğesi ekler. | \- `object`: Menü nesnesi (`Evas_Object *`). \- `parent`: Üst öğe (`Elm_Object_Item *`, kök için `NULL`). \- `label`: Alt menü etiket metni (`const char *`). |
| `void` | `void gfx_menu_move(Evas_Object *object, Evas_Coord x, Evas_Coord y);` | Menüyü belirtilen koordinatlara taşır. | \- `object`: Menü nesnesi (`Evas_Object *`). \- `x`, `y`: Hedef koordinatlar (`Evas_Coord`). |
| `void` | `void gfx_menu_close(Evas_Object *object);` | Açık olan menüyü kapatır. | \- `object`: Menü nesnesi (`Evas_Object *`). |
| `Elm_Object_Item *` | `Elm_Object_Item *gfx_menu_get_selected(const Evas_Object *object);` | O an seçili olan menü öğesini döndürür. | \- `object`: Menü nesnesi (`const Evas_Object *`). |
| `const Eina_List *` | `const Eina_List *gfx_menu_get_items(const Evas_Object *object);` | Kök seviyedeki menü öğelerinin listesini döndürür. | \- `object`: Menü nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_menu_get_itemobject(const Elm_Object_Item *item);` | Menü öğesine bağlı widget nesnesini döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemselected(Elm_Object_Item *item, Eina_Bool selected);` | Menü öğesinin seçili durumunu ayarlar. | \- `item`: Menü öğesi (`Elm_Object_Item *`). \- `selected`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_menu_get_itemdisabled(const Elm_Object_Item *item);` | Menü öğesinin devre dışı olup olmadığını döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemicon(Elm_Object_Item *item, const char *icon);` | Menü öğesinin simge adını ayarlar. | \- `item`: Menü öğesi (`Elm_Object_Item *`). \- `icon`: Simge adı (`const char *`). |
| `const char *` | `const char *gfx_menu_get_itemicon(const Elm_Object_Item *item);` | Menü öğesinin simge adını döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_set_itemlabel(Elm_Object_Item *item, const char *label);` | Menü öğesinin etiket metnini ayarlar. | \- `item`: Menü öğesi (`Elm_Object_Item *`). \- `label`: Etiket metni (`const char *`). |
| `const char *` | `const char *gfx_menu_get_itemlabel(const Elm_Object_Item *item);` | Menü öğesinin etiket metnini döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `Eina_Bool` | `Eina_Bool gfx_menu_get_itemselected(const Elm_Object_Item *item);` | Menü öğesinin seçili olup olmadığını döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `const Eina_List *` | `const Eina_List *gfx_menu_get_subitems(const Elm_Object_Item *item);` | Menü öğesinin alt öğelerinin listesini döndürür. | \- `item`: Menü öğesi (`const Elm_Object_Item *`). |
| `void` | `void gfx_menu_del_item(Elm_Object_Item *item);` | Menü öğesini siler. | \- `item`: Menü öğesi (`Elm_Object_Item *`). |

---

## 21\. Flip, Hover, Inwin, Layout, Map, Panel, Panes ve Photocam Fonksiyonları

| Dönüş Tipi | Fonksiyon Prototipi | İşlevi | Parametreler ve Alabileceği Değerler |
| :---- | :---- | :---- | :---- |
| `Evas_Object *` | `Evas_Object *gfx_object_add_flip(Evas_Object *parent);` | Ön ve arka yüz arasında animasyonlu geçiş sağlayan flip kapsayıcısı oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_flip_go(Evas_Object *object, Elm_Flip_Mode mode);` | Belirtilen animasyon moduyla flip geçişini başlatır. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `mode`: Flip animasyon modu (`ELM_FLIP_ROTATE_XZ_CENTER_AXIS`, `ELM_FLIP_CUBE_LEFT`, `ELM_FLIP_PAGE_LEFT` vb.). |
| `void` | `void gfx_flip_go_to(Evas_Object *object, Eina_Bool front, Elm_Flip_Mode mode);` | Belirtilen modla doğrudan ön veya arka yüze geçiş yapar. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `front`: `EINA_TRUE` (ön yüz) veya `EINA_FALSE` (arka yüz). \- `mode`: Flip modu (`Elm_Flip_Mode`). |
| `void` | `void gfx_flip_set_interaction(Evas_Object *object, Elm_Flip_Interaction mode);` | Flip bileşeninde kullanıcı etkileşimini (kaydırma/sürükleme vb.) ayarlar. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `mode`: `ELM_FLIP_INTERACTION_NONE`, `ELM_FLIP_INTERACTION_ROTATE`, `ELM_FLIP_INTERACTION_CUBE`, `ELM_FLIP_INTERACTION_PAGE`. |
| `void` | `void gfx_flip_set_swipedirection(Evas_Object *object, Elm_Flip_Direction dir, Eina_Bool enabled);` | Belirtilen yönde kaydırma etkileşimini açar veya kapatır. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `dir`: `ELM_FLIP_DIRECTION_UP`, `ELM_FLIP_DIRECTION_DOWN`, `ELM_FLIP_DIRECTION_LEFT`, `ELM_FLIP_DIRECTION_RIGHT`. \- `enabled`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_flip_get_swipedirection(Evas_Object *object, Elm_Flip_Direction dir);` | Belirtilen yönde kaydırma etkileşiminin açık olup olmadığını döndürür. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `dir`: Flip yönü (`Elm_Flip_Direction`). |
| `void` | `void gfx_flip_set_swipehitsize(Evas_Object *object, Elm_Flip_Direction dir, double siz);` | Bir yön için kaydırma algılama alanı oranını (`0.0`–`1.0`) ayarlar. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `dir`: Flip yönü (`Elm_Flip_Direction`). \- `siz`: Algılama oranı (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_flip_get_swipehitsize(Evas_Object *object, Elm_Flip_Direction dir);` | Bir yön için kaydırma algılama alanı oranını döndürür. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `dir`: Flip yönü (`Elm_Flip_Direction`). |
| `void` | `void gfx_flip_set_perspective(Evas_Object *object, Evas_Coord foc, Evas_Coord x, Evas_Coord y);` | Flip perspektifi için odak uzaklığını ve kaçış noktası koordinatlarını ayarlar. | \- `object`: Flip nesnesi (`Evas_Object *`). \- `foc`: Odak uzaklığı (`Evas_Coord`). \- `x`, `y`: Kaçış noktası koordinatları (`Evas_Coord`). |
| `Evas_Object *` | `Evas_Object *gfx_flip_get_selectedchild(const Evas_Object *object);` | Flip'in `"front"` parçasında o an ayarlı içerik nesnesini döndürür. | \- `object`: Flip nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_hover(Evas_Object *parent);` | Hedef nesneye bağlı açılır hover bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_hover_set_target(Evas_Object *object, Evas_Object *target);` | Hover bileşeninin bağlanacağı hedef nesneyi ayarlar. | \- `object`: Hover nesnesi (`Evas_Object *`). \- `target`: Hedef nesne (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_hover_get_target(const Evas_Object *object);` | Hover bileşeninin hedef nesnesini döndürür. | \- `object`: Hover nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_inwin(Evas_Object *parent);` | Pencere içi (inwin) katmanlı diyalog kapsayıcısı oluşturur. | \- `parent`: Üst pencere (`Evas_Object *`). |
| `void` | `void gfx_inwin_set_content(Evas_Object *object, Evas_Object *content);` | Inwin kapsayıcısı içinde gösterilecek içerik nesnesini ayarlar. | \- `object`: Inwin nesnesi (`Evas_Object *`). \- `content`: İçerik nesnesi (`Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_inwin_get_content(const Evas_Object *object);` | Inwin kapsayıcısı içindeki içerik nesnesini döndürür. | \- `object`: Inwin nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_layout(Evas_Object *parent);` | Edje/tema yerleşim kapsayıcı bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_layout_set_file(Evas_Object *object, const char *file, const char *group);` | Layout bileşenine Edje dosyası ve grup yükler. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `file`: Edje dosya yolu (`const char *`). \- `group`: Grup adı (`const char *`). |
| `void` | `void gfx_layout_get_file(Evas_Object *object, const char **file, const char **group);` | Layout bileşeninde yüklü dosya yolunu ve grup adını okur. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `file`, `group`: Çıktı işaretçileri (`const char **`). |
| `Eina_Bool` | `Eina_Bool gfx_layout_set_theme(Evas_Object *object, const char *klass, const char *group, const char *style);` | Layout bileşenine tema sınıfı, grubu ve stili uygular. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `klass`: Tema sınıfı (`const char *`). \- `group`: Tema grubu (`const char *`). \- `style`: Tema stili (`const char *`). |
| `void` | `void gfx_layout_signal_emit(Evas_Object *object, const char *emission, const char *source);` | Layout'un altındaki Edje nesnesine sinyal gönderir. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `emission`: Sinyal adı (`const char *`). \- `source`: Sinyal kaynağı (`const char *`). |
| `void` | `void gfx_layout_add_signalcallback(Evas_Object *object, const char *emission, const char *source, Edje_Signal_Cb func, const void *data);` | Layout tarafından yayılan bir sinyal için geri çağrı kaydeder. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `emission`: Sinyal adı deseni (`const char *`). \- `source`: Kaynak deseni (`const char *`). \- `func`: Sinyal geri çağrısı (`Edje_Signal_Cb`). \- `data`: Kullanıcı verisi (`const void *`, `NULL` olabilir). |
| `void` | `void gfx_layout_del_signalcallback(Evas_Object *object, const char *emission, const char *source, Edje_Signal_Cb func);` | Layout'a kayıtlı sinyal geri çağrısını kaldırır. | \- `object`: Layout nesnesi (`Evas_Object *`). \- `emission`: Sinyal adı deseni (`const char *`). \- `source`: Kaynak deseni (`const char *`). \- `func`: Sinyal geri çağrısı (`Edje_Signal_Cb`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_map(Evas_Object *parent);` | Etkileşimli harita bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_map_set_zoom(Evas_Object *object, int zoom);` | Harita bileşeninin yakınlaştırma (zoom) seviyesini ayarlar. | \- `object`: Map nesnesi (`Evas_Object *`). \- `zoom`: Yakınlaştırma seviyesi (`int`). |
| `int` | `int gfx_map_get_zoom(const Evas_Object *object);` | Harita bileşeninin mevcut yakınlaştırma seviyesini döndürür. | \- `object`: Map nesnesi (`const Evas_Object *`). |
| `Eina_Bool` | `Eina_Bool gfx_map_set_source(Evas_Object *object, const char *type, const char *source);` | Belirtilen kaynak türü için harita sağlayıcısını ayarlar. | \- `object`: Map nesnesi (`Evas_Object *`). \- `type`: Kaynak türü (`"tile"`, `"route"` veya `"name"`, `const char *`). \- `source`: Kaynak adı (`const char *`). |
| `const char *` | `const char *gfx_map_get_source(const Evas_Object *object, const char *type);` | Belirtilen harita kaynak türü için mevcut kaynak adını döndürür. | \- `object`: Map nesnesi (`const Evas_Object *`). \- `type`: Kaynak türü (`"tile"`, `"route"` veya `"name"`, `const char *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_panel(Evas_Object *parent);` | Açılır/kapanır kayar panel bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_panel_set_orient(Evas_Object *object, Elm_Panel_Orient orient);` | Panelin yönünü/açılma yönünü ayarlar. | \- `object`: Panel nesnesi (`Evas_Object *`). \- `orient`: `ELM_PANEL_ORIENT_TOP`, `ELM_PANEL_ORIENT_BOTTOM`, `ELM_PANEL_ORIENT_LEFT`, `ELM_PANEL_ORIENT_RIGHT`. |
| `Elm_Panel_Orient` | `Elm_Panel_Orient gfx_panel_get_orient(const Evas_Object *object);` | Panelin mevcut yönünü döndürür. | \- `object`: Panel nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_panel_set_hidden(Evas_Object *object, Eina_Bool hidden);` | Panel içeriğini gizler veya gösterir. | \- `object`: Panel nesnesi (`Evas_Object *`). \- `hidden`: `EINA_TRUE` (gizli) veya `EINA_FALSE` (görünür). |
| `Eina_Bool` | `Eina_Bool gfx_panel_get_hidden(const Evas_Object *object);` | Panelin gizli olup olmadığını döndürür. | \- `object`: Panel nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_panel_toggle(Evas_Object *object);` | Panelin gizli/görünür durumunu tersine çevirir. | \- `object`: Panel nesnesi (`Evas_Object *`). |
| `void` | `void gfx_panel_set_scrollable(Evas_Object *object, Eina_Bool scrollable);` | Panelde kaydırma davranışını açar veya kapatır. | \- `object`: Panel nesnesi (`Evas_Object *`). \- `scrollable`: `EINA_TRUE` veya `EINA_FALSE`. |
| `Eina_Bool` | `Eina_Bool gfx_panel_get_scrollable(const Evas_Object *object);` | Panelin kaydırılabilir olup olmadığını döndürür. | \- `object`: Panel nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_panes(Evas_Object *parent);` | Bölünmüş görünüm (panes) kapsayıcı bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `void` | `void gfx_panes_set_horizontal(Evas_Object *object, Eina_Bool horizontal);` | Panes bölünmesinin yatay mı dikey mi olacağını ayarlar. | \- `object`: Panes nesnesi (`Evas_Object *`). \- `horizontal`: `EINA_TRUE` (yatay bölünme) veya `EINA_FALSE` (dikey bölünme). |
| `Eina_Bool` | `Eina_Bool gfx_panes_get_horizontal(const Evas_Object *object);` | Panes bölünmesinin yatay olup olmadığını döndürür. | \- `object`: Panes nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_leftsize(Evas_Object *object, double size);` | Sol/üst panelin göreli boyut oranını (`0.0`–`1.0`) ayarlar. | \- `object`: Panes nesnesi (`Evas_Object *`). \- `size`: Boyut oranı (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_panes_get_leftsize(const Evas_Object *object);` | Sol/üst panelin göreli boyut oranını döndürür. | \- `object`: Panes nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_rightsize(Evas_Object *object, double size);` | Sağ/alt panelin göreli boyut oranını (`0.0`–`1.0`) ayarlar. | \- `object`: Panes nesnesi (`Evas_Object *`). \- `size`: Boyut oranı (`double`, `0.0`–`1.0`). |
| `double` | `double gfx_panes_get_rightsize(const Evas_Object *object);` | Sağ/alt panelin göreli boyut oranını döndürür. | \- `object`: Panes nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_panes_set_fixed(Evas_Object *object, Eina_Bool fixed);` | Panes ayırıcı konumunu sabitler veya sürüklenebilir yapar. | \- `object`: Panes nesnesi (`Evas_Object *`). \- `fixed`: `EINA_TRUE` (sabit) veya `EINA_FALSE` (sürüklenebilir). |
| `Eina_Bool` | `Eina_Bool gfx_panes_get_fixed(const Evas_Object *object);` | Panes ayırıcısının sabit olup olmadığını döndürür. | \- `object`: Panes nesnesi (`const Evas_Object *`). |
| `Evas_Object *` | `Evas_Object *gfx_object_add_photocam(Evas_Object *parent);` | Büyük görselleri yakınlaştırma ve kaydırma ile görüntülemek için photocam bileşeni oluşturur. | \- `parent`: Üst nesne (`Evas_Object *`). |
| `Eina_Error` | `Eina_Error gfx_photocam_set_file(Evas_Object *object, const char *file);` | Photocam bileşenine görsel dosyası yükler. | \- `object`: Photocam nesnesi (`Evas_Object *`). \- `file`: Görsel dosya yolu (`const char *`). |
| `const char *` | `const char *gfx_photocam_get_file(const Evas_Object *object);` | Photocam bileşeninde yüklü görsel dosya yolunu döndürür. | \- `object`: Photocam nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_photocam_set_zoom(Evas_Object *object, double zoom);` | Photocam bileşeninin yakınlaştırma çarpanını ayarlar. | \- `object`: Photocam nesnesi (`Evas_Object *`). \- `zoom`: Yakınlaştırma çarpanı (`double`). |
| `double` | `double gfx_photocam_get_zoom(const Evas_Object *object);` | Photocam bileşeninin mevcut yakınlaştırma çarpanını döndürür. | \- `object`: Photocam nesnesi (`const Evas_Object *`). |
| `void` | `void gfx_photocam_get_imagesize(const Evas_Object *object, int *w, int *h);` | Photocam'da yüklü görselin orijinal piksel boyutlarını okur. | \- `object`: Photocam nesnesi (`const Evas_Object *`). \- `w`, `h`: Boyut çıktı işaretçileri (`int *`). |

&nbsp;