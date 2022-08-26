🐱‍💻 XSS Payloads 🐱‍💻

Credits: 
https://github.com/payloadbox/xss-payload-list 
https://github.com/pgaijin66/XSS-Payloads

XSS NASIL ÖNLENİR?

Genel olarak, XSS güvenlik açıklarını etkili bir şekilde önlemenin aşağıdaki önlemlerin bir kombinasyonunu içermesi muhtemeldir:

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1-Input Filtreleme: Kullanıcı girişinin alındığı noktada, beklenen veya geçerli girişe göre mümkün olduğunca katı bir şekilde filtre uygulayın.

Encoding muhtemelen XSS savunmasının en önemli hattıdır, ancak her bağlamda XSS güvenlik açıklarını önlemek için yeterli değildir. Ayrıca, bir kullanıcıdan input alındığı noktada girişi mümkün olduğunca katı bir şekilde doğrulamanız gerekir. Eğer kullanıcının sayısal bir değer girmesi bekleniyorsa, sadece sayısal değerler girebileceği şekilde filtrelenmelidir.

Whitelisting ve Blacklisting kavramları ile de filtrelemek mümkündür. Tüm zararlı olabilecek protokolleri blackliste almaktansa, sadece istenilen protokolleri whiteliste alıp diğer girdileri etkisiz hale getirebiliriz. Bu, yeni zararlı protokoller ortaya çıktığında savunmanızın kırılmamasını sağlar ve bir blacklistten kaçmak için geçersiz değerleri gizlemeye çalışan saldırılara karşı daha az duyarlı hale getirir.


Bir diğer seçenek, DOMPurify gibi kullanıcının tarayıcısında filtreleme ve kodlama gerçekleştiren bir JavaScript kitaplığı kullanmaktır. Ne yazık ki, tüm bu kütüphanelerin zaman zaman XSS güvenlik açıkları vardır, bu nedenle bu mükemmel bir çözüm değildir.

DOMPurify, tehlikeli HTML içeren her şeyi çıkarır ve böylece XSS saldırılarını ve diğer kötü durumları önler.

let clean = DOMPurify.sanitize(dirty);

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

2-Output Encoding: HTTP yanıtlarında kullanıcı tarafından kontrol edilebilen verilerin çıktılandığı noktada, outputun yorumlanmasını önlemek için outputu encode edin Output bağlamına bağlı olarak bu, HTML, URL, JavaScript ve CSS kodlaması kombinasyonlarının uygulanmasını gerektirebilir.

![image](https://user-images.githubusercontent.com/59416511/186914969-dbd1abc7-6f2b-4c36-a46d-74e090fdef81.png)

Kullanıcı girdisini bir olay işleyicisine güvenli bir şekilde gömmek için hem JavaScript bağlamı hem de HTML bağlamı ile ilgilenmeniz gerekir. Bu nedenle, önce girişten Unicode'dan kaçmanız ve ardından HTML kodlamanız gerekir:

" <a href="#" onclick="x='This string needs two layers of escaping'">test</a> "

PHP'de htmlentities adlı varlıkları encode etmek için yerleşik bir işlev vardır. Bir HTML bağlamı içindeyken girdinizden kaçmak için bu işlevi çağırmalısınız.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

jQuery'de XSS'den nasıl kaçınılır:

JQuery'deki en yaygın XSS biçimi, kullanıcı girdisini bir jQuery seçicisine($) geçirmenizdir. Web geliştiricileri genellikle location.hash kullanır ve seçiciye iletir, bu da jQuery HTML'yi oluşturacağı için XSS'ye neden olur. jQuery bu sorunu fark etti ve girişin bir hash ile başlayıp başlamadığını kontrol etmek için seçici mantığına yama yaptı. Şimdi jQuery, yalnızca ilk karakter < ise HTML oluşturacaktır.

<script>document.write('<script>x="'+jsEscape(untrustedValue)+'";<\/script>')</script>

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

3-Uygun yanıt başlıklarını kullanın. Herhangi bir HTML veya JavaScript içermesi amaçlanmayan HTTP yanıtlarında XSS'yi önlemek için, tarayıcıların yanıtları istediğiniz şekilde yorumlamasını sağlamak için Content-Type ve X-Content-Type-Options başlıklarını kullanabilirsiniz.
 
Syntax-->  Content-Type: text/html; charset=UTF-8

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

4-İçerik Güvenliği Politikası (CSP): Son bir savunma hattı olarak, hala meydana gelen XSS güvenlik açıklarının önem derecesini azaltmak için CSP kullanabilirsiniz.

Content Security Policy (CSP), XSS atağına karşı son savunma hattıdır. XSS önlemeniz başarısız olursa, bir saldırganın yapabileceklerini kısıtlayarak XSS'yi azaltmak için CSP'yi kullanabilirsiniz.
CSP, resimler ve komut dosyaları gibi kaynakların yalnızca ana sayfayla aynı kaynaktan yüklenebileceğini belirtir. Bu nedenle, bir saldırgan bir XSS payload'ını başarıyla enjekte edebilse bile, yalnızca mevcut kaynaktan içerik yükleyebilir.
Belirli domain'leri whitelist'e aldıysak, saldırgan bu domainler üzerinden scriptler çalıştırabilir. Bu yüzden mümkünse içerikleri kendi domainlerimizde barındırmalıyız. Eğer bu mümkün değilse farklı domainlerde script çalıştırmaya izin veren nonce-based policy kullanılabilir. 

  Nonce, bir komut dosyasının veya kaynağın özniteliği olarak eklenen rastgele bir dizedir ve yalnızca rastgele dize sunucu tarafından oluşturulan dizeyle eşleşirse yürütülür. Saldırgan rastgele diziyi tahmin edemez ve bu nedenle geçerli bir nonce ile bir komut dosyası veya kaynağı çağıramaz ve bu nedenle kaynak yürütülmez.

  Bir CSP'nin komut dosyası gibi kaynakları engellemesi oldukça yaygındır. Ancak, birçok CSP, görüntü isteklerine izin verir. Bu, örneğin CSRF belirteçlerini ifşa etmek için harici sunuculara istekte bulunmak için img öğelerini sıklıkla kullanabileceğiniz anlamına gelir.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

HttpOnly Nedir:
Sunucu bu Flag’'i kullanarak tarayıcıya, Cookie'ye JavaScript tarafından erişime izin verilmemesini söylemektedir. Bir XSS saldırısında JavaScript kodları çalıştırılabilir olduğundan Cookie, JavaScript sayesinde çalınabilir.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

Web Uygulaması Güvenlik Duvarları - Bunlar bilinen saldırı dizilerini arar ve engeller. WAF'lar güvenilmezdir ve düzenli olarak yeni bypass teknikleri keşfedilmektedir. Ayrıca, WAF'ler ayrıca yalnızca istemci tarafında çalışan bir sınıf XSS güvenlik açığını da kaçırır. WAF'ler, XSS'yi, özellikle DOM Tabanlı XSS'yi önlemek için önerilmez.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
