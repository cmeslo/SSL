# [SSL](http://www.cnblogs.com/guogangj/p/4118605.html)

## SSL
Secure Sockets Layer,現在應該叫"TLS",但由於習慣問題,我們還是叫"SSL"比較多.http協議默認情況下是不加密內容的,這樣就很可能在內容傳播的時候被別人監聽到,對於安全性要求較高的場合,必須要加密,https就是帶加密的http協議,而https的加密是基於SSL的,它執行的是一個比較下層的加密,也就是說,在加密前,你的服務器程序在幹嘛,加密後也一樣在幹嘛,不用動,這個加密對用戶和開發者來說都是透明的.More:[[維基百科](https://en.wikipedia.org/wiki/Transport_Layer_Security)]

### OpenSSL
簡單地說,OpenSSL是SSL的一個實現,SSL只是一種規範.理論上來說,SSL這種規範是安全的,目前的技術水平很難破解,但SSL的實現就可能有些漏洞,如著名的"心臟出血".OpenSSL還提供了一大堆強大的工具軟件,強大到90%我們都用不到.

## 證書標準
### X.509
這是一種證書標準,主要定義了證書中應該包含哪些內容.其詳情可以參考RFC5280,SSL使用的就是這種證書標準.

## 編碼格式
同樣的X.509證書,可能有不同的編碼格式,目前有以下兩種編碼格式.

### PEM - Privacy Enhanced Mail
打開看文本格式,以"-----BEGIN..."開頭, "-----END..."結尾,內容是BASE64編碼.
查看PEM格式證書的信息:```openssl x509 -in certificate.pem -text -noout```
Apache和*NIX服務器偏向於使用這種編碼格式.

### DER - Distinguished Encoding Rules
打開看是二進制格式,不可讀.
查看DER格式證書的信息:```openssl x509 -in certificate.der -inform der -text -noout```
Java和Windows服務器偏向於使用這種編碼格式.

## 相關的文件擴展名
這是比較誤導人的地方,雖然我們已經知道有PEM和DER這兩種編碼格式,但文件擴展名並不一定就叫"PEM"或者"DER",常見的擴展名除了PEM和DER還有以下這些,它們除了編碼格式可能不同之外,內容也有差別,但大多數都能相互轉換編碼格式.

### CRT
CRT應該是certificate的三個字母,其實還是證書的意思,常見於*NIX系統,有可能是PEM編碼,也有可能是DER編碼,大多數應該是PEM編碼,相信你已經知道怎麼辨別.

### CER
還是certificate,還是證書,常見於Windows系統,同樣的,可能是PEM編碼,也可能是DER編碼,大多數應該是DER編碼.

### KEY
通常用來存放一個公鑰或者私鑰,並非X.509證書,編碼同樣的,可能是PEM,也可能是DER.
查看KEY的辦法:```openssl rsa -in mykey.key -text -noout```
如果是DER格式的話,同理應該這樣了:```openssl rsa -in mykey.key -text -noout -inform der```

### CSR - Certificate Signing Request
即證書籤名請求,這個並不是證書,而是向權威證書頒發機構獲得簽名證書的申請,其核心內容是一個公鑰(當然還附帶了一些別的信息),在生成這個申請的時候,同時也會生成一個私鑰,私鑰要自己保管好.做過iOS APP的朋友都應該知道是怎麼向蘋果申請開發者證書的吧.
查看的辦法:openssl req -noout -text -in my.csr (如果是DER格式的話照舊加上-inform der,這裡不寫了)

### PFX/P12 - predecessor of PKCS#12
對*nix服務器來說,一般CRT和KEY是分開存放在不同文件中的,但Windows的IIS則將它們存在一個PFX文件中,(因此這個文件包含了證書及私鑰)這樣會不會不安全？應該不會,PFX通常會有一個"提取密碼",你想把裡面的東西讀取出來的話,它就要求你提供提取密碼,PFX使用的時DER編碼,如何把PFX轉換為PEM編碼？
```openssl pkcs12 -in for-iis.pfx -out for-iis.pem -nodes```
這個時候會提示你輸入提取代碼. for-iis.pem就是可讀的文本.
生成pfx的命令類似這樣:```openssl pkcs12 -export -in certificate.crt -inkey privateKey.key -out certificate.pfx -certfile CACert.crt```

其中CACert.crt是CA(權威證書頒發機構)的根證書,有的話也通過-certfile參數一起帶進去.這麼看來,PFX其實是個證書密鑰庫.

### JKS - Java Key Storage
這是Java的專利,跟OpenSSL關係不大,利用Java的一個叫"keytool"的工具,可以將PFX轉為JKS,當然了,keytool也能直接生成JKS,不過在此就不多表了.

## 證書編碼的轉換
### PEM轉為DER
```openssl x509 -in cert.crt -outform der -out cert.der```

### DER轉為PEM
```openssl x509 -in cert.crt -inform der -outform pem -out cert.pem```

(提示:要轉換KEY文件也類似,只不過把x509換成rsa,要轉CSR的話,把x509換成req...)

## 獲得證書
### 向權威證書頒發機構申請證書
用這命令生成一個csr: ```openssl req -newkey rsa:2048 -new -nodes -keyout my.key -out my.csr```
把csr交給權威證書頒發機構,權威證書頒發機構對此進行簽名,完成.保留好csr,當權威證書頒發機構頒發的證書過期的時候,你還可以用同樣的csr來申請新的證書,key保持不變.

### 或者生成自簽名的證書
```openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem```
在生成證書的過程中會要你填一堆的東西,其實真正要填的只有Common Name,通常填寫你服務器的域名,如"yourcompany.com",或者你服務器的IP地址,其它都可以留空的.
生產環境中還是不要使用自簽的證書,否則瀏覽器會不認,或者如果你是企業應用的話能夠強制讓用戶的瀏覽器接受你的自簽證書也行.向權威機構要證書通常是要錢的,但現在也有免費的,僅僅需要一個簡單的域名驗證即可.有興趣的話查查"沃通數字證書".

[source](http://www.cnblogs.com/guogangj/p/4118605.html)
