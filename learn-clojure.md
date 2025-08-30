# Clojure

## Sözdizimi *(Syntax)*

### Yazılı değerler *(Literals)*

Aşağıda Clojure'da bulunan temel tiplerin *(primitives)* yazılı temsil *(literal representation)* örneklerini bulabilirsiniz.

**;** karakteri satır sonuna kadar yorum oluşturur. Yaygın bir uygulama olarak yorum bölüm başlıklarını belirlemek için çok sayıda noktalı virgül kullanılabilir, ancak bu teknik bir zorunluluk değildir.

#### Sayısal tipler

```
42      ; integer
-1.5    ; floating point
22/7    ; ratio
```

Tamsayılar uygun değer aralığında ise 64-bit sabit hassasiyetli *(fixed precision)* tamsayılar olarak, aksi halde isteğe göre hassasiyetli *(arbitrary precision)* tamsayılar olarak okunur. İsteğe göre hassasiyeti tercih etmek için sayının sonuna **N** eklenebilir. Clojure ayrıca sekizli (0 önekli), onaltılı (0x önekli) ve özel tabanlı (taban ve r önekli, ör. ikili *(binary)* taban için 2r) tamsayılar için Java sözdizimini destekler. Ayrıca pay ve paydanın bir araya getirilmesi ile oran *(ratio)* tipi oluşturularak kullanıma sunulmuştur.

Küsüratlı *(kayan noktalı/floating point)* değerler çift-hassasiyetli, yahut **M** soneki ile isteğe göre hassasiyetli 64-bit *float*lar olarak okunur. Üslü yazım *(exponential notation)* ayrıca desteklenir. ##Inf,##-Inf ve ##NaN  özel sembolleri sırası ile artı sonsuz, eksi sonsuz ve "sayı değil" değerlerini temsil eder.

#### Karakter tipleri

```
"hello"   ; string
\e        ; character
#"[0-9]+" ; regular expression
```

Metin değerleri *(string)* çift tırnak içinde yazılır ve birden fazla satır alabilir. Tek karakterler ters eğik çizgi *(backslash)* ile temsil edilir. Özel isimli birkaç karakter: `\newline` `\space` `\tab` vs. Unicode karakterler `\uNNNN` veya `\oNNN` gibi sekizli olarak temsil edilebilir.

Düzenli ifadeler *(literal regular expressions)* # ile başlayan metinler olarak yazılır. Bunlar java.util.regex.Pattern nesnelerine çevrilirler.

#### Semboller ve İsimler

```
map             ; symbol
+               ; symbol - most punctuation allowed
clojure.core/+  ; namespaced symbol
nil             ; null value
true false      ; booleans
:alpha          ; keyword
:release/alpha  ; keyword with namespace
```

Semboller harf, sayı ve noktalama işaretlerinden oluşturulur ve fonksiyon, değer, ad uzayı *(namespace)* gibi bir başka nesneye işaret etmek *(refer to)* için kullanılırlar. Sembollerin ait olduğu ad uzayları da bir slash ile birlikte sembol isminde belirtilebilir.

Farklı değer tipleri olarak okunan üç özel sembol vardır - ==nil== boş değer *(null value)*, ==true== ve ==false== ise mantıksal *(boolean)* değerlerdir.

Anahtar kelimeler iki nokta üst üste ile başlar ve değer olarak kendilerini döndürürler. Clojure'da genellikle sıralama veya öznitelik değerleri *(enumerated values / attribute values)* olarak kullanılırlar.

#### Koleksiyonlar *(Literal collections)*

Clojure söz diziminde dört koleksiyon tipini de yazabiliyoruz:

```
'(1 2 3)      ; list
[1 2 3]       ; vector
#{1 2 3}      ; set
{:a 1, :b 2}  ; map
```

Şu aşamada bu dört veri yapısını birleşik veri üretmek için kullanabileceğimizi bilmemiz yeterlidir.

### İşleme *(Evaluation)*

Clojure'un ifadeleri *(expressions)* nasıl okuyup değerlere dönüştürdüğünü *(evaluation)* inceleyelim.

#### Geleneksel İşleme (Java)

```mermaid
flowchart LR
    SC["Source Code"]:::plain
    C(["Compiler"]):::proc
    JVM(["JVM"]):::vm
    E((Effect)):::eff

    %% oklar
    SC -- characters --> C
    C  -- bytecode   --> JVM
    JVM --> E

    %% üst not
    CU["compilation unit = file or class"]:::note
    CU -.-> C

    %% stiller
    classDef plain fill:#ffffff,stroke:#666,stroke-width:1px,color:#111;
    classDef proc  fill:#e9f0ff,stroke:#3a7,stroke-width:1px,color:#111;
    classDef vm    fill:#fff1d6,stroke:#b88,stroke-width:1px,color:#111;
    classDef eff   fill:#ffe6f5,stroke:#c06,stroke-width:3px,color:#111;
    classDef note  fill:transparent,stroke:transparent,color:#777,font-size:12px;
  ```
Java'da, kaynak kodu (.java uzantılı dosyalar) derleyici (javac) tarafından karakterler olarak okunur ve Java Sanal Makinesi (JVM) tarafından işlenebilen bytecode'a (.class uzantılı dosyalar) dönüştürülür.

#### Clojure'da İşleme

```mermaid
flowchart LR
    SC["Source Code"]:::plain
    R(["Reader"]):::reader
    C(["Compiler"]):::proc
    JVM(["JVM"]):::vm
    E((Effect)):::eff
    U((You)):::you

    %% oklar
    SC -- characters --> R
    R  -- data structures --> C
    C  -- bytecode        --> JVM
    JVM --> E
    U -.-> R

    %% üst not
    CU["compilation unit = expression"]:::note
    CU -.-> R

    %% stiller
    classDef plain  fill:#ffffff,stroke:#666,stroke-width:1px,color:#111;
    classDef reader fill:#e7ffef,stroke:#2a6,stroke-width:1px,color:#111;
    classDef proc   fill:#e9f0ff,stroke:#3a7,stroke-width:1px,color:#111;
    classDef vm     fill:#fff1d6,stroke:#b88,stroke-width:1px,color:#111;
    classDef eff    fill:#ffe6f5,stroke:#c06,stroke-width:3px,color:#111;
    classDef you    fill:#f3f3f3,stroke:#888,stroke-width:1px,color:#111;
    classDef note   fill:transparent,stroke:transparent,color:#777,font-size:12px;
```

Clojure'da, kaynak kodu Okuyucu tarafından karakter karakter okunur. Okuyucu kaynağı .clj uzantılı dosyalardan yahut etkileşimli olarak *(interactively)* ifade serileri şeklinde okuyabilir. Okuyucu Clojure verisi üretir. Ardından Clojure derleyicisi JVM için bytecode'u üretir.

Burada iki önemli nokta var:

1. Kaynak kodunun ölçü birimi **Clojure ifadesi** olup, Clojure kaynak dosyası değildir. Kaynak dosyaları da REPL'de etkileşimli olarak girilmiş Clojure ifadelerinden farksız olarak ifade dizileri biçiminde okunur.
2. Okuyucu ve Derleyiciyi bu şekilde ayırmak makrolara yer açan bir yaklaşımdır. Makrolar (veri olarak) kod alıp (veri olarak) kod üreten özel fonksiyonlardır. Yukarıdaki veri işleme modelinde makro genişlemesi için bir döngünün nereye eklenebileceğini fark ettiniz mi?

#### Yapı ve Anlam *(Structure vs Semantics)*

Bir Clojure ifadesini ele alalım:

![](clojure-expression(2).svg)

Bu diyagram yeşil renkli sözdizimi (Okuyucu tarafından üretilen Closure veri yapısı) ve mavi renkli anlam (Closure çalışma zamanı *(runtime)* tarafından verinin nasıl anlaşıldığı) farkını göstermektedir.

Çoğu yazılı Clojure formu kendilerini döndürürler, **semboller** ve **listeler** hariç. Semboller başka bir şeye atıf için kullanılır ve işlendiklerinde, atıfta bulundukları nesneyi döndürürler. Listeler (diyagramda olduğu gibi) çağrı *(invocation)* olarak işlenir.

Diyagramda, (+ 3 4) ifadesi (+) sembolü ve iki sayıyı (3 ve 4) içeren bir liste olarak okunur. 