# Stable-Diffusion-Tutorial
[教學] 使用 Stable Diffusion 訓練自己的資料集

## 需要安裝的清單
1. [Git 連結](https://git-scm.com/)
2. [Python 3.10.6](https://www.python.org/downloads/release/python-3106/)

> 請務必安裝 Python 3.10.6版本。

## 安裝基本環境
基本上 Git 與 Python 的安裝只要下一步即可，需要注意的是， Python 在安裝的時候需要把 `Add Python to environment variables`，若不確之前是否已經安裝過 Python 可以先使用下面指令做查詢，輸入完後要回傳 3.10.6 才是正確的版本，否則在安裝套件過程中會有些許版本問題需要處理，若之前安裝的版本太新或太舊，直接去控制台解除安裝後重裝即可。

```css!
python --version
git --version
```

## Web Ui 安裝

首先先在一個你想安裝的資料夾位置在路徑位置輸入 CMD ，就可以在該資料夾裡面開啟終端機。
![](https://i.imgur.com/NioCgEx.png)

並在終端機裡面輸入下面的指令把作者的 Github repository 克隆下來
```link!
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```

完成後可以看到該位置出現 `stable-diffusion-webui` 的資料夾，並進去該資料夾裡面。
![](https://i.imgur.com/NpBGYsC.png)

對 webui-user.bat 點選右鍵 -> 編輯，將下列的代碼取代先前的參數。

```bat!
@echo off

set PYTHON=
set GIT=
set VENV_DIR=
set COMMANDLINE_ARGS=--deepdanbooru

call webui.bat

```

在 stable-diffusion-webui 的資料夾裡面，一樣在路徑輸入 cmd 開啟終端機，並在終端機內輸入下方代碼安裝 Python 套件。

```text!
pip install -r ./requirements.txt
```

> 若 Python 的指令終端機找不到，代表沒有安裝環境變數給 Python，可以先去 Python 的安裝檔做 Modify 的方式重新設定環境變數。

完成後直接開啟 webui-user.bat 即可接續安裝 stable-diffusion-webui 所使用的套件。

安裝完成後終端機會出現下圖的網址，點擊網址就可以進入 web-ui 的介面使用。

![](https://i.imgur.com/iZjqPwI.png)

## 模型使用方式

需要先下載別人訓練完的模型進行參考，這邊使用 [Anything v3.0](https://huggingface.co/Linaqruf/anything-v3.0/tree/main) 的 `anything-v3-full.safetensors` 進行接下來的示範。

> 備註: 使用不同的模型會讓你接下來的產圖、訓練都會有不同風格，因此可以先找你喜歡的模型進行使用。

把下載好的模型放置剛剛使用 git clone 下來的資料夾裡面 `~\stable-diffusion-webui\models\Stable-diffusion` 就完成模型的放置了。

![](https://i.imgur.com/9u35CHt.png)

## Web Ui 生成產圖
開啟 `http://127.0.0.1:7860` 的網址，進入 Web Ui ，在左上角的清單可以選擇剛剛所放的模型，若未出現模型請先點旁邊的重新整理符號，若依然沒出現則代表模型位置放錯。

![](https://i.imgur.com/GO9MKuv.png)

下方圖片簡易介紹各區塊的用途。

![](https://i.imgur.com/IcedDAM.png)

參數調整區塊簡易的進行介紹:
* Sampling method
    * 主要是針對圖像採樣方式不同，聽起來很像是幹話但如果隊採樣的技術有興趣，可以打開清單看各種算法。
    * 建議使用的採樣 Euler a 與 DDIM 。
* Sampling steps
    * 採樣的次數，可以簡易了解若該參數愈大可以畫出更細緻圖片，對顯示卡的要求也更高。
* Width, Height
    * 設定圖片的長與寬，這也需要注意顯示卡的記憶體空間，若產出圖片大小太大很容易會出現顯示卡記憶體空間不足。
* Batch count
    * 在這邊的意思是，要生成幾次圖片。
* Batch size
    * 在 1 次 batch count ，要生成幾張圖片。
* CFG Scale
    * 數字愈小愈接近原本訓練的圖片，反之則愈不接近原本訓練的圖片。

> Batch 補充: count 為 2, size 為 3 則代表，這一次的圖片生成總共會是 2 次，並且每次生成 3 張圖片，因此會產出 6 張圖片。
> 這邊的參數調整並不是都愈大愈好，都需要依照原本的訓練模型進行調整，當然也要依照你的顯示卡能力調整。

接下來就可以在咒語的位置輸入你想看到的圖片樣子，請務必使用英文做咒語的輸入，輸入完後就可以點擊生成圖片。

## 訓練自己的 AI

### Create hypernetwork
接下來把 txt2img 的頁面切換至 Train 的頁面，進行前置作業。
在 Create hypernetwork 的 Name 輸入你對這個接下來的模型名稱，並在 Modules 全都進行勾選，最後勾選 Use Dropout 參數為 0, 0.05, 0.15 。
![](https://i.imgur.com/ck75U5q.png)

### Preprocess images

接著到 Preprocess images 的頁面設定圖片的預處理，先在圖片來源資料夾的路徑放置你想訓練的圖片，並在輸出資料夾給一個乾淨的空間作輸出，輸出資料夾的路徑會是我們最終要訓練的模型的位置，影像的 Width 與 Height 基本上也是愈大愈好，但這個的先決條件是顯示卡的記憶體空間要足夠，否則接下來的訓練會很容易看到 out of memory 的字眼，下方參數為建議大小的常見設置，3080 10GB 我是用 512x512，最後按下 Create hypernetwork。

* 256 x 256
* 416 x 416
* 512 x 512
* 604 x 604

![](https://i.imgur.com/cnkx7xq.png)

### Train

到 Train 的頁面進行訓練的參數調整，其中特別要注意的是 Batch size 與圖片大小(Width, Height)。

* Batch Size: 代表著每一次的訓練所使用的圖片數量，該數值若為 1 則代表每訓練 1 次圖片就更新經驗一次，若數值為 5 則代表每看完 5 張圖片做一次經驗的更新，若有興趣可以去看一些關於 DL 的論文了解，為了簡易說法就沒講得很細了。
* 圖片大小: 基本上這個值是愈大愈好，但根據你的顯示卡記憶體空間通常會決定的 size 的大小。

我的顯示卡是 3080 下面是我在訓練時的配置。
* batch_size: 4
* width, height: 416x416

![](https://i.imgur.com/wO12Ybk.png)

最後就是對訓練的次數調整。
* Max Steps: 可以調 10,000 做預設，基本上這個參數預大可以讓經驗迭代的次數更多，但最終都會在一個 loss 值不上不下。
![](https://i.imgur.com/NdrDdUe.png)

設定完後就可以直接進行 Hypernetwork 訓練。

### 使用 Hypernotwork
訓練完網路會在 `stable-diffusion-webui\textual_inversion\<日期>\<你的網路名稱>\hypernetworks`，可以根據你訓練的保存點選擇你喜歡的風格，而表現最好的網路會直接放在 `stable-diffusion-webui\models\hypernetworks\{你的網路名稱}.pt`的位置

並到 Settings 保存你想放上去 hypernetwork 風格，接下來在去 txt2img 就可以生成你剛剛訓練的風格了。

![](https://i.imgur.com/PXXorgC.png)
