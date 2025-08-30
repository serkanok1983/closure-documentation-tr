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

![](clojure-expression.svg)

Bu diyagram yeşil renkli sözdizimi (Okuyucu tarafından üretilen Closure veri yapısı) ve mavi renkli anlam (Closure çalışma zamanı *(runtime)* tarafından verinin nasıl anlaşıldığı) farkını göstermektedir.

Çoğu yazılı Clojure formu kendilerini döndürürler, **semboller** ve **listeler** hariç. Semboller başka bir şeye atıf için kullanılır ve işlendiklerinde, atıfta bulundukları nesneyi döndürürler. Listeler (diyagramda olduğu gibi) çağrı *(invocation)* olarak işlenir.

Diyagramda, (+ 3 4) ifadesi (+) sembolü ve iki sayıyı (3 ve 4) içeren bir liste olarak okunur. İlk öğeye (+ işaretinin bulunduğu) "fonksiyon konumu" denebilir, çağrılacak nesne için buraya bakılır. Çağrılabilir şeylerin başında fonksiyonlar gelmekle birlikte, makrolar ve bir kaç diğer nesne gibi çalışma zamanının bildiği özel operatörler de vardır.

Yukarıdaki ifadenin işlenişini ele alalım:

* 3 ve 4 kendilerini döndürür (long değerler)
* + sembolü + işlemini uygulayan bir fonksiyon döndürür
* listenin işlenmesi + fonksiyonunun 3 ve 4'ü argüman alarak çağrılması ile sonuçlanır.

Çoğu dilde hem bildirim *(statements)* hem ifadeler *(expressions)* vardır, bildirimler program durumunu etkilerken *(stateful effect)* değer döndürmezler. Clojure'da, her şey değer döndüren ifade cinsindendir. Bazı ifadelerin (çoğunun değil) yan etkileri de olur.

Şimdi Clojure'da etkileşimli olarak nasıl ifade işleyebileceğimize bakalım.

#### Kesme işareti ile işleme erteleme

Bazen özellikle semboller ve listelerin işlenmemesi kullanışlı olur. Bir sembolün atıf yaptığı şeye bakmadan sembolün kendisini almak gerekebilir.

```
user=> 'x
x
```

Bazen de bir listenin işletilecek kod değil veri değerlerinden oluşan bir liste olması gerekir.

```
user=> '(1 2 3)
(1 2 3)
```

Kesme işaretini unutursanız veri listesi kod gibi işlenemeyeceği için kafa karıştırıcı bir hata alırsınız:

```
user=> (1 2 3)
Execution error (ClassCastException) at user/eval156 (REPL:1).
class java.lang.Long cannot be cast to class clojure.lang.IFn
```

Şimdilik kesme işaretini çok düşünmeyin, ancak bu içerikte sembol ve listelerin işlenmesinden kaçınmak için kullanıldığını görebilirsiniz.

### REPL

Clojure kullandığını zamanın çoğunluğunda bir editörde veya REPL'de (*Read-Eval-Print-Loop* Oku-İşle-Yaz-Döngüsü) çalışacaksınız. REPL aşağıdaki parçalardan oluşur:

1. Clojure verisi üretmek üzere bir ifadeyi (karakter dizisi olarak) oku.
2. 1 numaralı işlemden döndürülen veriyi işle ve (Clojure verisi olarak) bir sonuç üret.
3. Üretilen sonucu veriden karaktere çevirerek yazdır.
4. Başa dön.

2 numaralı işlemde önemli bir nokta şudur, Clojure bir ifadeyi yürütmeden *(execute)* evvel muhakkak derler *(compile)*; Clojure **her zaman** JVM bytecode'a derlenir. Clojure yorumlayıcısı yoktur.

```
user=> (+ 3 4)
7
```

Yukarıdaki örnekte bir ifadenin (+ 3 4) derlenerek bir sonuç alınmasını görüyorsunuz.

#### REPL'de Keşfe Çıkmak

Çoğu REPL ortamı etkileşimli kullanımı kolaylaştırmak için bazı destekler sunar. Örneğin, birkaç özel sembol ile son üç ifadenin işleme sonuçlarını hatırlayabilir:

* `*1` (son sonuç)
* `*2` (iki ifade önceki sonuç)
* `*3` (üç ifade önceki sonuç)

```
user=> (+ 3 4)
7
user=> (+ 10 *1)
17
user=> (+ *1 *2)
24
```

İlaveten standart Clojure kütüphanesinde dahili olarak bulunan `clojure.repl` ad uzayı çok sayıda yardımcı fonksiyon sağlamaktadır. Bu kütüphaneyi yükleyerek içinde bulunan fonksiyonları mevcut bağlamımızda *(context)* kullanabilmek için şu çağrıyı yaparız:

```
(require '[clojure.repl :refer :all])
```

Şimdi REPL ortamında kullanışlı bazı ilave fonksiyonlara erişimimiz var: `doc`, `find-doc`, `\apropos`, `source` ve `dir`.

`doc` fonksiyonu bir fonksiyonun dökümantasyonunu görüntüler. `+` üzerinde çağıralım:

```
user=> (doc +)

clojure.core/+
([] [x] [x y] [x y & more])
  Returns the sum of nums. (+) returns 0. Does not auto-promote longs, will throw on overflow. See also: +'
```

`doc`fonksiyonu `+`'nın dökümantasyonunu geçerli fonksiyon imzaları dahil ekrana yazar.

Doc fonksiyonu dökümantasyon yazdıktan sonra değer olarak nil döndürür, işleme çıktısı *(evaluation output)* olarak ger ikisini de görürsünüz.

`doc`'u kendi üzerinde de çağırabiliriz:

```
user=> (doc doc)

clojure.repl/doc
([name])
Macro
  Prints documentation for a var or special form given its name
```

Neyin çağıracağımızdan emin olmadığımızda, belli bir metin yahut düzenli idadeyle eşleşen fonksiyonları bulmak için `\apropos`komutunu kullanabiliriz.

```
user=> (apropos "+")
(clojure.core/+ clojure.core/+')
```

`find-doc` ile aramamızı dökümantasyon metinlerini *(docstring)* içerecek şekilde genişletebiliriz:

```
user=> (find-doc "trim")

clojure.core/subvec
([v start] [v start end])
  Returns a persisten vector of the items in vector from start (inclusive) to end (exclusive). If end is not supplied, defaults to (count vector). This operation is O(1) and very fast, as the resulting vector shares structure with the original and no trimming is done.

clojure.string/trim
([s])
  Removes whitespace from both ends of string.

clojure.string/trim-newline
([s])
  Removes all trailing newline \n or return \r characters from string. Similar to Perl's chomp.

clojure.string/triml
([s])
  Removes whitespace from the left side of string.

clojure.string/trimr
([s])
  Removes whitespace from the right side of string.
```

Belli bir aduzayında yer alan tüm fonksiyonların listesini görmek istediğimizde, `dir` foksiyonunu kullanabiliriz. Burada `clojure.repl` aduzayında kullanalım:

```
user=> (dir clojure.repl)

apropos
demunge
dir
dir-fn
doc
find-doc
pst
root-cause
set-break-handler!
source
source-fn
stack-element-str
thread-stopper
```

Ve son olarak, çalışma zamanı tarafında erilişebilen herhangi bir fonksiyonun yalnızca dökümantasyonunu değil, kaynak kodunu da görebiliriz:

```
user=> (source dir)

(defmacro dir
  "Prints a sorted directory of public vars in a namespace"
  [nsname]
  `(doseq [v# (dir-fn 'nsname)]
    (println v#)))
```

Kullandığınız fonksiyonların dökümantasyon metinleri ve kaynak kodlarını incelemek iyi bir alışkanlık olur. Bizzat Clojure kütüphane fonksiyonlarının uygulamasını  *(implementation)* keşfetmek dili ve kullanımını öğrenmek için mükemmel bir yoldur.

[Clojure Cheatsheet](https://clojure.org/api/cheatsheet)'in bir kopyasını açık bulundurmanız da faydalı olabilir. Bu belge standart kütüphane fonksiyonlarını kategorize eder ve iyi bir referanstır.

### Clojure temelleri

#### def

REPL ortamında ifade işlerken, bir parça veriyi sonrası için kaydetmek faydalı olabilir. Bunu `def` ile yapabiliriz:

```
user=> (def x 7)
#'user/x
```

`def` mevcut ad uzayında bir sembolü (x) bir değer (7) ile eşleyen bir özel formdur. Bu eşlemeye `var` denir. Uygulamada çoğu Clojure kodunda varlar sabit bir değer veya fonksiyona referans verir, ancak REPL'de çalışırken bir kolaylık olarak var tanımlamak ve yeniden tanımlamak yaygındır.

Yukarıdaki dönüş değerinin `#'user/x` olduğuna dikkat edin - bu bir varın yazılı temsilidir: `#'` ardından ad uzayı ve sembol. `\user` varsayılan ad uzayıdır.

Sembollerin neye referans verdiğine bakılarak işlendiğini hatırlayalım, sembolü kullanarak değeri döndürebiliriz:

```
user=> (+ x x)
14
```

#### Yazdırmak

Yeni bir programlama dili öğrenirken en çok yapılan şeylerden biri de değerleri yazdırmaktır. Clojure bunun için bazı fonksiyonlar sunar:

|              | İnsanlar için | Veri olarak okunabilir |
|--------------|---------------|------------------------|
| Satır başı   | println       | prn                    |
| Aynı satır   | print         | pr                     |

İnsan-okuyabilir formlar özel yazım karakterlerini (satırbaşı ve sekme gibi) basılı formlarına çevirir ve metinlerdeki tırnak işaretlerini atlarlar. Fonksiyonlarda hata ayıklamak *(debug)* veya REPL'de bir değer yazdırmak için genellikle `println` kullanırız. `println` istenen sayıda argüman alır ve ekrana yazarken her bir argümanın arasına boşluk ekler.

```
user=> (println "What is this:" (+ 1 2))
What is this: 3
```

println fonksiyonunun yan efektleri (yazdırma) vardır ve sonuç olarak nil döndürür.

Yukarıdaki "What is this:"'in tırnak işaretsiz yazdırıldığına ve bu hali ile Okuyucunun veri olarak tekrar okuyabileceği bir metin olmadığına dikkat edin.

Bu amaç doğrultusunda, veri olarak yazdırmak için prn kullanılır:

```
user=> (prn "one\n\ttwo")
"one\n\ttwo"
```

Şimdi yazdırılan sonuç Okuyucu tarafından tekrar okunabilir formda. Bağlama göre, insan-okuyabilir formu veya veri formunu tercih edebilirsiniz.

### Bilginizi sınayın

1. REPL kullanarak, 7654 ile 1234 toplamını hesaplayınız.
2. Cebirsel ifadeyi Clojure ifadesi olarak yazınız: ( 7 + 3 * 4 + 5 ) / 10.
3. REPL dökümantasyon fonksiyonlarını kullanarak `rem` ve `mod` fonksiyonlarının dökümantasyonlarını bulunuz. Dökümantasyona dayalı olarak verilen ifadelerin sonuçlarını karşılaştırınız.
4. `find-doc` kullanarak son REPL istisnasının *(exception)* yığın izini *(stack trace)* veren fonksiyonu bulunuz. 


---


## Fonksiyonlar

