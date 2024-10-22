---
title: "Google Colabの準備"
---

# Google Colabとは

Google Colabは、Googleが提供する無料のPython実行環境です。  
機械学習やデータ分析を行うためのプラットフォームです。  

公式サイトでは、以下のように説明されています。  

> Colab is a hosted Jupyter Notebook service that requires no setup to use and provides free access to computing resources, including GPUs and TPUs. Colab is especially well suited to machine learning, data science, and education.
> Ref: https://colab.google/

同じくGoogleが開発しているGeminiに和訳してもらいました。  

> Colabは、セットアップなしで使用でき、GPUやTPUなどのコンピューティングリソースに無料でアクセスできるホスト型のJupyter Notebookサービスです。Colabは特に機械学習、データサイエンス、教育に適しています。

非常に簡単に機械学習を実行することができます。  
PC上にエディタやコンパイラ・インタプリタなどの開発環境を用意する必要がありません。  
ブラウザ上ですぐに実行できます。  

また、GPUやTPUなどのコンピューティングリソースに無料でアクセスできます。  
上限はありますが、それでも無料で利用できるのは非常にありがたいです。  

## Google Colabの準備

プログラムを実行するためにGoogle Colabを準備しましょう。  
<https://colab.research.google.com/>へアクセスし、`ノートブックを新規作成`をクリックしてください。  

以下のような画面が表示されます。  

![Google Colabの新規作成](/images/google-colab-new.png)  

これで、Google Colabの準備が完了です。  

試しに、`print("Hello, World!")`を実行してみましょう。  

以下のコードを書き、実行ボタンをクリックしてください。  
ランタイムの割り当てを聞かれます。  
特に理由がなければ、`GPU`を選択してください。  
GPUはCPUよりも高速に処理を行うことができます。  
機械学習では、GPUを使うことがほとんどです。  

```py
print("Hello World!!!")
```

以下のように表示されます。  

![Google Colabの実行結果](/images/google-colab-hello-world.png)  

正常に表示されたら、Google Colabの準備は完了です。  

## ファイルのアップロード

ファイルタブをクリックすると、ファイル一覧が階層構造で表示されます。  

![Google Colabのファイル一覧](/images/google-colab-files.png)  

初期状態では、以下のディレクトリが存在します。  

- `.config`
- `sample_data`

コンテキストクリック(右クリック)すると、メニューが表示されます。  
ファイルのアップロードや新規フォルダの作成ができます。  

## 注意点

Google Colabは、Googleが提供するサービスです。  
Googleの規約に抵触する行為はしないようにしましょう。  
<https://research.google.com/colaboratory/faq.html#disallowed-activities>に記載されている禁止事項を守りましょう。  

アップロードしたファイルは、ランタイムが終了すると削除されます。  
重要なファイルは、自分でダウンロードしておくようにしましょう。  
