# OpenSSL 指令練習與紀錄

### 下載網址

[OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries)  
[https://indy.fulgan.com/SSL/](https://indy.fulgan.com/SSL/)  

### 一些參考網址

[Creating Your Own SSL Certificate Authority (and Dumping Self Signed Certs)](https://datacenteroverlords.com/2012/03/01/creating-your-own-ssl-certificate-authority/)  
[[Windows] 透過 OpenSSL 產製金鑰檔與憑證檔](http://fannys23.pixnet.net/blog/post/30619452-%5Bwindows%5D-%E9%80%8F%E9%81%8E-openssl-%E7%94%A2%E8%A3%BD%E9%87%91%E9%91%B0%E6%AA%94%E8%88%87%E6%86%91%E8%AD%89%E6%AA%94)  
[Create a .pfx/.p12 certificate file using OpenSSL](https://www.ssl.com/how-to/create-a-pfx-p12-certificate-file-using-openssl/)  
[openssl 指令 (續) windows 的 pfx、p12 篇](https://ssorc.tw/927)

### 產生CA[自簽憑證]

* 產生[私鑰] rootCA.key
> openssl genrsa -out rootCA.key 2048

* 轉檔產出[自簽公開憑證] rootCA.pem  
將被要求填入一些識別與檢示用資訊
> openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem 

* 轉檔：rootCA.key + rootCA.pem → rootCA.pfx  
轉檔：[私鑰] + [自簽公開憑證] → pfx_file
> openssl pkcs12 -export -out rootCA.pfx -inkey rootCA.key -in rootCA.pem

### 產生加簽[憑證]

* 產生[私鑰] device.key  
用於設備等應用
> openssl genrsa -out device.key 2048

* 產生[憑證請求檔] device.csr  
用以申請加簽憑證  
將被要求填入一些識別與檢示用資訊
> openssl req -new -key device.key -out device.csr

* 簽發[憑證] device.crt  
用CA[私鑰]加簽[憑證請求檔]產出[憑證]  
簽發：[憑證請求檔] + [CA私鑰] + [CA憑證] → 由CA加簽的[憑證]
> openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 500 -sha256

* 轉檔：device.key + device.crt → device.pfx  
轉檔：[私鑰] + [憑證] → pfx_file
> openssl pkcs12 -export -out device.pfx -inkey device.key -in device.crt

註：一般自簽憑證不可用於各項應用，需再製造各類應用的加簽[憑證]。  比如用於設備(device)或簽署發行應用軟體，如：ClickOnce等等。
