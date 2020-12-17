# Linux筆記

## 檔案系統架構

| 目錄        | 功能 |
| :---------- | ---- |
| /bin, /sbin | /bin 主要放置一般使用者可以操作的指令，/sbin 放置系統管理員可以操作的指令。連結到 /usr/bin，/usr/sbin |
| /boot       | 主要放置開機相關檔案 |
| /dev        | 放置 device 裝置檔案，包話滑鼠鍵盤等 |
| /etc        | 主要放置系統檔案 |
| /home, /root       | /home 主要是一般帳戶的家目錄，/root 為系統管理者的家目錄 |
| /lib, /lib64      | 主要為系統函式庫和核心函式庫，若是 64 位元則放在 /lib64。連結到 /usr/lib, /usr/lib64 |
| /proc       | 將記憶體內的資料做成檔案類型 |
| /sys       | 與 /proc 類似，但主要針對硬體相關參數 |
| /usr      | /usr 全名為 unix software resource 縮寫，放置系統相關軟體、服務（注意不是 user 的縮寫喔！） |
| /var       | 全名為 variable，放置一些變數或記錄檔 |
| /var /log | 所有服務的錯誤訊息紀錄，要Debug先看這邊 |
|/tmp      | 全名為 temporary，放置暫存檔案 |
| /media, /mnt      | 放置隨插即用的裝置慣用目錄， /mnt 為管理員/使用者手動掛上（mount）的目錄 |
| /opt       | 全名為 optional，通常為第三方廠商放置軟體處 |
| /run      | 系統進行服務軟體運作管理處 |
| /srv     | 通常是放置開發的服務（service），如：網站服務 www 等 |

## 基礎指令

### pwd
print work directory，印出目前工作目錄

### mv
move (rename) files，移動檔案或是重新命名檔案

### touch
用來更新已存在文件的 timestamp 時間戳記或是新增空白檔案

### cat
將檔案內容印出在終端機上

### more
將檔案內容一頁頁印在終端機上，與cat相似，只是有分頁

### tail
顯示檔案最後幾行內容

### file
檢查檔案類型

### ln
建立連結（捷徑），通常搭配 -s (symbolic) 參數使用，如果沒加 -s 就是預設硬連結，
當對連結中的檔案內容進行變更時，原本的檔案也會跟著改變，
軟硬連結的差別在於硬連結會完整複製原始檔案的程式碼放在檔案系統中，軟連結則是一個映像檔（不佔空間）而已

### df
查看硬碟空間的指令，

會有 Used 使用掉的硬碟空間（KB）和 Available 剩下多少空間的資訊（KB）

### mount
將硬碟或者是光碟、軟碟接掛上系統的指令。在 Linux 下面，每一個裝置都是一個檔案（或目錄），存放在 /dev 目錄下面，例如：
```
sudo mount -t iso9660 -o ro /dev/cdrom /mnt/cdrom
```
這裡將 /dev/cdrom 這個光碟機掛載到 /mnt/cdrom 這個目錄，
第一個參數 -t iso9660 是指定光碟的檔案格式，-o ro 參數是指定這個設備是唯讀的（read only）

### fdisk
對硬碟進行分割，建立或刪除partition

### mke2fs
這是用來將磁碟格式化成 Linux 系統檔的指令，新加上去的硬碟分割完後還要用 mke2fs 才能正常使用。

### who
檢查目前在系統上有哪些使用者的指令，也可以使用 w 這個指令來取代。



## 檔案權限設定
![dFJMHXC[1]](https://raw.githubusercontent.com/shownlin/MarkDownNote/master/img/dFJMHXC%5B1%5D.png)

第一欄：使用者權限
由 10 個字元組成，第一個字元表示檔案型態（- 為檔案，d 表示目錄，1 表示連結檔案）。
字元 2、3、4 表示檔案擁有者的存取權限。
字元 5、6、7 表示檔案擁有者所屬群組成員的存取權限。
字元 8、9、10 表示其他使用者的存取權限

舉例來說 -rwxrwxr--，代表這是一格檔案，擁有者和群組都具備讀取、寫入和執行權限，其他使用者只擁有讀取權限

第二欄：檔案數量

第三欄：擁有者

第四欄：群組

第五欄：檔案大小

第六欄：檔案建立時間

第七欄：檔案名稱

### chmod
修改檔案權限，例如將權限設為 rw-rw-r--：
```
chmod 664 README.md
```

將檔案的使用者和群組加入執行權限：
```
chmod ug+x README.md
```
其中u代表user，g代表group

### chown
修改檔案擁有者與群組

## 系統管理

### sudo
使用最高權限（superuser）執行指令，會要求輸入自己密碼，使用上必須非常小心

### su
su 指令可以讓一般的 Linux 使用者輸入 root 密碼取得 root 權限，暫時取得 root 權限的使用者就如同 root 一樣可以對系統進行各種變更動作

### kill
根據 Process ID 指定要終止程式，加上-9可以立即強制執行：
```
kill -9 PID
```

### killall
直接使用程式的名稱來指定要終止的程式，例如：
```
killall hello.py
```

### ps
查看執行中的程序，是Process Status的縮寫，你可以配合其參數aux，系統將會列出連同系統服務的程式：
```
ps  -aux
```

### top
與 ps 相同，但是 ps 是顯示執行指令當下的瞬間情況，top 則是即時顯示目前動態的process情況

### free
用來查看記憶體（ram）使用情形，雖然top也會顯示，但是沒有 free 準確，以 KB 為單位

### & 與 [Ctrl]+[z]
背景執行，例如：
```
find  "/"  -name  httpd &
```
這一行命令，表示將尋找 httpd 這個檔案的指令放置到背景中執行的意思。
或是先執行了某個程式，再按下 [Ctrl]+[z] 發出「掛起（暫停）」的 signal，將程序放到背景中並且暫時停止活動， 可以再透過bg繼續於背景執行。
可以搭配fg做喚回的動作。

### fg
fg 是將後台運行的程式叫回前台（螢幕）顯示，在終端模式中輸入 fg 即可。
fg 是後進先出，最後掛到後台的最先出來。
當然，如果當時並沒有程式在執行的話，系統會告訴你並無執行中的程式（no such job）。

### bg
如果想要讓process在後台執行，但是忘記在指令最後面鍵入&，此時可以先用[Ctrl]+[z]掛起程序，在輸入 bg 讓程序在後台執行。

## 網路功能

### ifconfig
查詢目前我們這個系統的網路卡的狀況的指令，可以查詢 IP、子遮罩網路及網路卡的硬體資訊等等

### ping
傳送 ICMP 封包去要求對方主機回應是否存在於網路環境中，例如：
```bash
[root@www ~]# ping -c 3 168.95.1.1
PING 168.95.1.1 (168.95.1.1) 56(84) bytes of data.
64 bytes from 168.95.1.1: icmp_seq=1 ttl=245 time=15.4 ms
64 bytes from 168.95.1.1: icmp_seq=2 ttl=245 time=10.0 ms
64 bytes from 168.95.1.1: icmp_seq=3 ttl=245 time=10.2 ms
```
上面顯示如下：

64 bytes：表示這次傳送的 ICMP 封包大小為 64 bytes 這麼大，這是預設值， 在某些特殊場合中，例如要搜索整個網路內最大的 MTU 時，可以使用 -s 2000 之類的數值來取代；

icmp_seq=1：ICMP 所偵測進行的次數，第一次編號為 1 ；

ttl=243：TTL 與 IP 封包內的 TTL 是相同的，每經過一個帶有 MAC 的節點 (node) 時，例如 router, bridge 時， TTL 就會減少一，預設的 TTL 為 255 ， 你可以透過 -t 150 之類的方法來重新設定預設 TTL 數值；

time=15.4 ms：回應時間，單位有 ms(0.001秒)及 us(0.000001秒)， 一般來說，越小的回應時間，表示兩部主機之間的網路連線越良好！

### traceroute
追蹤兩部主機之間通過的各個節點 (node) 通訊狀況的好壞，透過設定TTL，每經過一個節點就減 1 
的方式來觀察每個節點的反應時間，詳細參閱[維基百科](https://w.wiki/qus)。

### netstat
在本機上面以自己的程式監測自己的 port，觀察port有沒有被打開，搭配 -a 參數可以列出所有連接埠，包含 listening 與 non listening。

其中一欄 stat 標示出目前該port的使用狀況，主要的狀態含有：

| 狀態       | 說明                                                         |
| ---------- | ------------------------------------------------------------ |
| ESTABLISED | 已建立連線的狀態，表示兩台機器正在通訊中。                   |
| SYN_SENT   | 發出主動連線 (SYN 標誌) 的連線封包，要訪問其它電腦的服務時首先要發個同步訊號給該port，此時狀態為SYN_SENT |
| SYN_RECV   | 接收到一個要求連線的主動連線封包                             |
| FIN_WAIT1  | 該插槽服務(socket)已中斷，該連線正在斷線當中                 |
| FIN_WAIT2  | 該連線已掛斷，但正在等待對方主機回應斷線確認的封包           |
| TIME_WAIT  | 該連線已掛斷，但 socket 還在網路上等待結束，我方主動調用close()斷開連接，收到對方確認後狀態變為TIME_WAIT。 |
| LISTEN     | 通常用在服務的監聽 port ，如果是服務程式的話，指的是等待從任何遠端的客戶端發來連接請求。|

###  iptables
如果要打開或關閉某個port，需要使用 iptables 這個防火牆指令。
例如：
```
iptables -A INPUT -p tcp -i eth0 –dport 80 -j ACCEPT
```
說明：允許 tcp 通訊協定 80 port 連進主機
如果是要關掉，把ACCEPT改成DROP

