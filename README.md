# 如何在AWS上使用Python（Linux）

👋  來自: https://famous-grape-0b8.notion.site/AWS-Python-Linux-caa279445b45473c8bf677a5f07e175a

👋  紀錄要如何用AWS裡面的內容

👋  紀錄python如何在開好的個體上跑

👋  一些linux語法

---

### 教學連結：

[AWS新手的30天之旅 - 1. Cloud簡介 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10235549)

[連線至 EC2 的三種方法與比較 - SSH、EC2 Instance Connect、System Manager](https://www.ecloudture.com/%E9%80%A3%E7%B7%9A%E8%87%B3ec2%E7%9A%84%E4%B8%89%E7%A8%AE%E6%96%B9%E6%B3%95%E8%88%87%E6%AF%94%E8%BC%83-ssh%EF%BC%8Cec2%E5%AF%A6%E4%BE%8B%E9%80%A3%E6%8E%A5%EF%BC%8C%E7%B3%BB%E7%B5%B1%E7%AE%A1%E7%90%86/)

[第 3 堂課：檔案管理與 vim 初探](https://linux.vbird.org/linux_basic_train/centos8/unit03.php)

[Using VSCode remotely on an EC2 instance](https://medium.com/@christyjacob4/using-vscode-remotely-on-an-ec2-instance-7822c4032cff)

---

## AWS基本創建instance(個體)

1. 去EC2的介面中，點選“啟用執行個體” （相當於在雲端建立一台機器（電腦），接著要設定你要建立什麼機器）
2. 設定參數（這台機器要有什麼資源），OS的部分建議linux或ubuntu(mac或windows要收os的錢)：
    1. 執行個體類型（這台機器的配備，最簡單測試的就用t2-micro就好，如果要用GPU或是其他的話再設定別的）
    2. VPC類似你連到雲端的哪個網域（這部分不台確定），這邊預設就好
    3. 金鑰的部分給個名稱，其他參數先不用特別設定 (好的之後就會自動幫你下載下來一個金鑰)
    4. 往下走，網路設定點開後“**安全群組規則**”決定哪個IP可以連進這個機器，因此這邊要特別設定成“我的ＩＰ”，（如果之後要從家裡連進來，再把家裡的IP新增進來），**note: 0.0.0.0就是隨便的人都可以連進來**，另外這邊的名稱可以設定一下，類似設定成一個資料夾好管理的概念，把之後你的連線都放到此資料夾（設定的名稱）
    5. 設定儲存：可以選擇機器要多少的硬碟及硬碟的等級，因為是測試用，所以這邊一樣預設即可
3. 好了之後就直接建立執行個體 （亮綠色的就代表可以跑他了），到這步之後，相當於你已經在雲端**啟動**一台機器（電腦），這台機器的硬體就是剛剛設定好的參數，連到雲端的哪邊就是VPC的設定，至於要怎麼連進去以及誰可以連呢？就是金鑰和安全群組的設定。
4. 啟動好了就可以試玩看看（不用怕直接跑吧），左邊ec2儀表板，執行中個體，就可以找到你的剛剛設定的機器名字 (或直接在輸入的地方輸入你的名字ex. lab_test_luka)，點進執行個體ID之後就可以看到“連線”，點選進去之後可以看到有個範例的command（例如我的是ssh -i "test_key_luka.pem” ……）複製那行之後就可以準備進入你的機器啦！
5. 準備進入虛擬機器，開啟terminal，進入你剛剛下載好金鑰的位置（例如我的金鑰test_key_luka.pem存在桌面→work內）就是

![Untitled](如何在AWS上使用Python（Linux）/Untitled.png)

1. 成功進去之後就幫剛剛複製的command貼上（前面加sudo，ex. sudo ssh -i “test_key_luka.pem”……），接著輸入這台電腦的密碼（有yes就yes）最後看到以下畫面就代表成功進去剛剛建起來的linux機器啦！(第一次用的話別懷疑，這個terminal就可以想像成你的桌面了)
2. 接下來因為已經進入機器內（linux作業系統），因此要怎麼操作都要靠linux語法

![Untitled](如何在AWS上使用Python（Linux）/Untitled%201.png)

### 其他：

- 如果要從linux的terminal出來，可以輸入”exit”，但這樣他還是會在雲端上執行，因此要**暫停**的話可以回到剛剛aws頁面中的這個個體上，點選“執行個體狀態”，停止他
- 呈上，如果是要把這個個體給砍了（個體內所有東西都會被刪掉），就點終止
- cd ../.. 可以看到這台機器的底層架構（文件）

---

## 如何使用python

> 這邊的想法是希望先在本機上面寫好一個python檔，或是一個app，之後透過一個**媒介**放到linux上運行，因此**媒介**的選擇可以分成以下兩種：
> 
> 
> 1. Docker: 可以先在linux上下載好docker，本機上也建立好一個docker image後，把我們既有的py檔案丟到instance內，再讓linux上運行這個image。
> 
> 2. **Using VSCode remotely on an EC2 instance：**想法是直接從地端遠端連線([SSH](https://ithelp.ithome.com.tw/articles/10277498))到雲端instance上
> 

這邊我們選擇比較簡單的第2種版本：

1. 確認Python已安裝好：

```bash
**python3 --version** 
```

       若有版本代表已有python:

![Untitled](%E5%A6%82%E4%BD%95%E5%9C%A8AWS%E4%B8%8A%E4%BD%BF%E7%94%A8Python%EF%BC%88Linux%EF%BC%89%20/Untitled%202.png)

1. 建立一個資料夾，當作可以存放工作用的資料夾，輸入mkdir YOUR_FOLDER_NAME，YOUR_FOLDER_NAME我是設定work代表資料夾名稱，之後進入資料夾

```bash
mkdir work
cd work
```

1. 在本地端下載好vscode後，在vscode內下載“**Remote-SSH”**好了後就會在左下角看到綠色的icon:

![Untitled](%E5%A6%82%E4%BD%95%E5%9C%A8AWS%E4%B8%8A%E4%BD%BF%E7%94%A8Python%EF%BC%88Linux%EF%BC%89%20/Untitled%203.png)

1. 點綠色的icon，在vscode上面輸入configure(open configuration file），點第一個user/…/../config進去
2. 開始照下圖編輯吧：

![Untitled](%E5%A6%82%E4%BD%95%E5%9C%A8AWS%E4%B8%8A%E4%BD%BF%E7%94%A8Python%EF%BC%88Linux%EF%BC%89%20/Untitled%204.png)

1. 設定好了之後，回去點綠色的icon，連host，就可以看到剛剛設定的東西了！
2. 接著在本機terminal中cd目錄到你的key底下，[輸入](https://stackoverflow.com/questions/60675232/ssh-in-vscode-error-permission-denied-publickey-password)：

```bash
chmod 400 YOUR_KEY.pem
```

1. 最後切回vscode，點左下角的icon，connect host，點剛剛的aws-ec2，就可以啦
    
    左下角顯示SSH: aws-ec2代表成功了
    
    ![Untitled](%E5%A6%82%E4%BD%95%E5%9C%A8AWS%E4%B8%8A%E4%BD%BF%E7%94%A8Python%EF%BC%88Linux%EF%BC%89%20/Untitled%205.png)
    

---

最後我們隨便測試看看吧，直接open folder到剛剛的work目錄底下，new一個hellow world的檔案就直接測試吧！

## 第一種版本 (Docker):

待更
