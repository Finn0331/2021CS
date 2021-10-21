#
- [TCP/IP 與 Internet 網路：第十三章 網域名稱系統](http://www.tsnien.idv.tw/Internet_WebBook/chap13/13-0%20%e7%bf%92%e9%a1%8c.html)

### DNS message format

### 資源記錄類型(resource record types)

| record|意義| 說明|
|-------| ------| -------|
| SOA|Start of Authority ||
|A記錄|主機記錄IP addresses|RFC 1035定義，A記錄是用於名稱解析的重要記錄，它將特定的主機名對映到對應主機的IP位址上。|
|CNAME記錄|別名記錄domain name aliases RFC 1035定義，CNAME記錄用於將某個別名指向到某個A記錄上，這樣就不需要再為某個新名字另外建立一條新的A記錄。|
|AAAA記錄|IPv6主機記錄|RFC 3596定義，與A記錄對應，用於將特定的主機名對映到一個主機的IPv6位址。|
|MX |SMTP mail exchangers||
|SRV記錄|服務位置記錄 |RFC 2782定義，用於定義提供特定服務的伺服器的位置，如主機（hostname），埠（port number）等。|
|NS記錄|域名伺服器記錄name servers|用來指定該域名由哪個DNS伺服器來進行解析。 您註冊域名時，總有預設的DNS伺服器，每個註冊的域名都是由一個DNS域名伺服器來進行解析的，DNS伺服器NS記錄位址一般以以下的形式出現： ns1.domain.com、ns2.domain.com等。 簡單的說，NS記錄是指定由哪個DNS伺服器解析你的域名。|
|PTR|pointers for reverse DNS lookups||
|NAPTR記錄||RFC 3403定義，它提供了正規表示式方式去對映一個域名。NAPTR記錄非常著名的一個應用是用於ENUM查詢。|
