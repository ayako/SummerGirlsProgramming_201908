# Microsoft Cognitive Services を利用した 画像識別アプリ 開発 (201908 版: 犬種判定(Dogs Classification))

"人工知能 API" [Microsoft Azure Cognitive Services](https://www.microsoft.com/cognitive-services/) を使うと、画像分析を行うエンジンをノーコーディングで利用、作成できます。

[Custom Vision Service](https://azure.microsoft.com/ja-jp/services/cognitive-services/custom-vision-service/) は、ご自分で用意した画像をアップロードしてタグ付け、学習させることで、画像の分類 (Classification) や 画像に写っているモノの抽出 (Object Detection) を行うエンジンを簡単に作成でき、Web API として利用できます。また TensorFlow / CoreML / ONNX、または Docker コンテナー向けに Export して利用することもできます。

ここでは、Custom Vision で画像識別エンジンを作成し、Web アプリ (C# または HTML&JavaScript) からアクセスして利用する方法を紹介します。

> アプリの動作は、[オンラインアプリサンプル](http://cogservicesvisionsamples201906.azurewebsites.net/CustomVisionClassification-Dog) でご確認ください。


## 目次

0. [準備](#準備)
1. [Custom Vision で画像識別エンジンを作成](#1-custom-vision-で画像識別エンジンを作成)
2. Web アプリから Custom Vision にアクセス
    - [HTML&JavaScript 版](#htmljavascript-版)
    - [C# 版](#c-版)


## 準備

- Azure サブスクリプション
    - ハンズオンの講師から、Azure サブスクリプションを利用できるマイクロソフトアカウントをお受け取りください。
- ハンズオン用の画像とサンプルコード一式(ZIP)のダウンロードと展開
    - 以下のリンクをクリックしてダウンロードします。
    - <a href="handson_files.zip" download="handson_files.zip">handson_files.zip</a> 
    - ダウンロードしたら、右クリックして **[全て展開]** をクリックします。
    - <img src="doc_images/handson_01.png" width="400">
    - **圧縮(ZIP形式)フォルダーの展開** のダイアログで **[展開]** をクリックして、展開します。
    - <img src="doc_images/handson_02.png" width="400">

## 手順

### 1. Custom Vision で画像識別エンジンを作成

ブラウザーから [Custom Vision のポータル (https://www.customvision.ai)](https://www.customvision.ai) にアクセスします。
**[SIGN IN]** をクリックし、マイクロソフトアカウントでサインインします。

<img src="doc_images/handson_customvision_01.png" width="600">

**[NEW PROJECT]** をクリックし、画像識別エンジンを新規に作成するダイアログを開きます。

<img src="doc_images/handson_customvision_02.png" width="600">

**Create New Project** ダイアログで、**Name** にお好きな名前を入力します。Project Type は **Classification** を選択し、Classification は **Multiclass**　を選択します。**[Create Project]** をクリックして、新規プロジェクトを作成します。

<img src="doc_images/handson_customvision_03d.png" width="600">

プロジェクトが作成されると詳細画面が表示されます。 **Add Images**　をクリックして、画像識別エンジンに学習させる画像をアップロードするダイアログを開きます。

<img src="doc_images/handson_customvision_04d.png" width="600">

ダウンロードして展開しておいたディレクトリーにある画像を使用していきます。まずは **images\beagle** フォルダーにある画像をすべて選択して **[開く]** をクリックします。

<img src="doc_images/handson_customvision_05d.png" width="600">

**My Tags** に **Beagle** と入力し、**[Upload Files]** をクリックします。

<img src="doc_images/handson_customvision_06d.png" width="600">

これで *Beagle のタグが付いた画像がアップロードできました。**[Done]** をクリックしてダイアログを閉じます。

<img src="doc_images/handson_customvision_07d.png" width="600">

同様に、他の画像もタグをつけてアップロードします。

| 画像フォルダー名 | タグ (犬の品種名) |
|:------------|:---------------------|
| beagle | ビーグル |
| bernese-mountain-dog | バーニーズマウンテンドッグ |
| chihuahua | チワワ |
| eskimo-dog | エスキモードッグ |
| german-shepherd | ジャーマンシェパード |
| golden-retriever | ゴールデンレトリバー |
| maltese-dog | マルチーズ |

画像のアップロードが終了したら、上部バーの **Train** をクリックして、画像をエンジンに学習させます。

<img src="doc_images/handson_customvision_08d.png" width="600">

**Training Type** は **Fast Training** を選択し、**Train** をクリックして学習させます。

<img src="doc_images/handson_customvision_09d.png" width="600">

学習が終了すると、**Performance** のページに学習結果が表示されます。このエンジンをテストするには、上部バーから **Quick Test** をクリックします。

<img src="doc_images/handson_customvision_10d.png" width="600">

**Browse local files** をクリックして、ダウンロードしたコンテンツの [test_images\Dog](test_images\Dog) フォルダーにある画像をひとつ選択します。画像の犬種が正しく識別されたら OK です。

<img src="doc_images/handson_customvision_11d.png" width="600">

> 必要に応じて、画像の追加→学習→テスト を繰り返します

**Performance** の画面で、上部バーから **Publish** をクリックして、Web API から利用できるように発行を行います。

<img src="doc_images/handson_customvision_12d.png" width="600">

**Publish Name** のダイアログで **Publish** をクリックして発行します。

<img src="doc_images/handson_customvision_13.png" width="600">

Publish が完了すると **Prediction URL** が表示されるので、クリックして作成した画像判別エンジンにアクセスする URL の情報を表示します。

<img src="doc_images/handson_customvision_14.png" width="600">

*If you have an image fIle* の下に表示されている URL と *Prediction-Key* の横に表示されている Prediction Key をコピーして、ローカルに保存しておきます。

<img src="doc_images/handson_customvision_15.png" width="600">


### 2. Web アプリ (HTML&JavaScript) から Custom Vision にアクセス

#### HTML&JavaScript 版

ダウンロードして展開しておいたディレクトリーにあるサンプルコードを使用していきます。 **code_samples\JavaScript** フォルダーを右クリックして **[Visual Studio で開く]** を選択して、Visual Studio で開きます。

Visual Studio の画面右側に表示される **ソリューションエクスプローラー** から **[script]** フォルダの中にある **customvision_script.js** をクリックして表示します。

18,19　行目の predictionKey と endpoint の設定箇所をローカルに保存しておいた URL と Prediction Key で書き換えます。

```
    var predictionKey = "ローカルに保存しておいたPrediction Key";
    var endpoint = "ローカルに保存しておいたURL";
```

上部バーの保存ボタンをクリックして、customvision_script.js を保存します。エクスプローラーでサンプルコードのフォルダーを再度表示し、**[Custom Vision]** をダブルクリックしてブラウザーで開きます。

<img src="doc_images/handson_customvision_16.png" width="600">

**[ファイルを選択]** をクリックして、**Test** フォルダーにある画像をひとつ選択します。画像が識別されて犬種情報が表示されたら完了です。

<img src="doc_images/handson_customvision_17d.png" width="600">


#### C# 版

ダウンロードして展開しておいたディレクトリーにあるサンプルコードを使用していきます。 **code_samples\CSharp\CogServicesVisionSamples_201906.sln** をダブルクリックして Visual Studio で開きます。

Visual Studio の画面右側に表示される **ソリューションエクスプローラー** から **[Pages]** フォルダの中にある
**CustomVisionClassification.cshtml.cs** をクリックして開きます。

<img src="doc_images/handson_customvision_18.png" width="600">

26~29　行目の predictionKey と endpoint の設定箇所をローカルに保存しておいた URL と Prediction Key で書き換えます。

```
    private const string cvPredictionKey = "YOUR_CUSTOMVISION_PREDICTION_KEY";
    private const string cvEndpoint = "https://YOUR_LOCATION.api.cognitive.microsoft.com";
    private const string cvProjectId = "YOUR_CUSTOMVISION_PROJECTID";
    private const string cvPublishName = "YOUR_CUSTOMVISION_PROJECT_PUBLISHNAME";
```

保存しておいた URL が以下のようになっている場合、

<pre>https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/a0000000-0000-0000-aaaa-000000000000/classify/iterations/Iteration1/image </pre>

 - YOUR_LOCATION -> southcentralus
 - YOUR_CUSTOMVISION_PROJECTID -> a0000000-0000-0000-aaaa-000000000000
 - YOUR_CUSTOMVISION_PROJECT_PUBLISHNAME -> Iteration1

となります。

上部バーの ▶ をクリックして、ビルド＆アプリの起動を行います。

<img src="doc_images/handson_customvision_19.png" width="600">

ブラウザーが起動して、Web アプリの画面が表示されます。上部バーから **Custom Vision Classification** をクリックします。

<img src="doc_images/handson_customvision_20.png" width="600">

*Custom Vision Classification* の画面で、**[ファイルを選択]** をクリックして、**Test** フォルダーにある画像をひとつ選択します。**[Analyze]** をクリックすると画像が識別されて犬種情報が表示されたら完了です。

<img src="doc_images/handson_customvision_21d.png" width="600">
