# はじめようApache Icebergハンズオン
<img src="image/banner.jpg">

## ハンズオン概要
これからApache Icebergを学びたい人向けの実践的なハンズオンです。コンテナが動く端末が1台あれば始められます。
```shell
docker-compose up --build
```

以下のようなJupyterベースのコンテンツを通じてIcebergの機能と仕組みが学べます。
![](https://storage.googleapis.com/zenn-user-upload/dda9998a7f59-20240310.png)

### 対象者
これからApache Icebergを学びたい人
### 前提知識
このハンズオンは、Apache Icebergの基本的な概念と使い方を初めて学ぶ方を対象としています。  
ハンズオンを実施するために特別な前提知識は必要ありませんが、以下のような経験やスキルがあるとより理解が深まります。

- SQLの基本的な知識
- PythonやScalaなどのプログラミング言語の基礎
- Apache SparkやHadoopなどのビッグデータ処理フレームワークの概要理解
- データレイク、データウェアハウス、ETLなどのデータ管理の基本概念への理解

上記の知識や経験がない場合でも、このハンズオンを通してApache Icebergの基本を学ぶことができます。　　

ハンズオンの中で、必要な概念や用語については補足説明を加えています。  
ビッグデータ処理や分散システムの詳細な知識は必須ではありませんが、これらの技術への関心や基礎的な理解があると、Apache Icebergの特徴やメリットをより深く理解することができるでしょう。
## コンテンツ
以下のコンテンツが用意されています。Notebook間は依存せず、どのNotebookからでも始められるように作ってありますが、初めての人はChapter1から進めることをオススメします。

- Chapter1 - Hello Iceberg World：細かい仕組みの理解は一旦脇に置き、Icebergの世界観をざっくり体験します
- Chapter2 - Understanding The Iceberg Architecture Basic：Icebergのアーキテクチャの基本を理解します

## ハンズオン環境
Icebergの実験に必要なコンポーネントがdocker-compose.ymlに纏まっており、簡単に実験環境を構築できます。  
  
**【注意】この環境はあくまでも実験用です。本番向けには非機能面を考慮した適切な設計の検討が必要です**
  
docker-composeには主に以下のコンポーネントが含まれています。

- [Apache Spark](https://spark.apache.org/)
    - Icebergを試すクエリエンジンとして利用します
- [Jupyter Notebook](https://jupyter.org/)
    - Sparkを操作するインターフェースとして利用します
- [minio](https://min.io/)
    - Amazon S3互換のオブジェクトストレージです。ローカルでS3みたいなストレージを構築できます
- [Iceberg Catalog REST Server](https://github.com/tabular-io/iceberg-rest-image)
    - Icebergの構成要素の一つです

全体としては以下の構成になります。
![](https://storage.googleapis.com/zenn-user-upload/2588ec6fe4d4-20240306.jpg)
## 環境構築
macとwindowsのwsl(Ubuntu 20.04)で動作を確認しています。
### コンテナ立ち上げ
#### docker-compose
```shell
docker-compose up --build
```

※`docker-compose.yml`内にAWSのクレデンシャルを指定するように見える箇所がありますが、これはminioのために必要な設定で、編集は不要です(本ハンズオンではS3を使用しません)
### JupyterのUIを開いてみよう
ブラウザから`localhost:8888`にアクセスしてください。無事に環境が構築できていれば、JupyterのUIが表示されるはずです。
ハンズオンの手順はNotebookとして配置されており、ハンズオンではこれらを利用していきます。
![](https://storage.googleapis.com/zenn-user-upload/1c425e0c9366-20240310.png)

### MinIOのUIを開いてみよう
続いて`localhost:9001`にアクセスするとMinIOのログイン画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/c3ad32996e93-20240303.png)

UsernameとPasswordは`docker-compose.yml`に記載の通り、Username=admin, Password=passwordです。(適宜変更してください)

ログインすると以下の画面が表示されます。(warehouseというバケットが最初から用意されています)
![](https://storage.googleapis.com/zenn-user-upload/ba13e16faccd-20240303.png)
### Apache Icebergとは？
[Apache Iceberg](https://iceberg.apache.org/)は、2017年にNetflixが開発した、大規模なデータセットに最適化されたOpen Table Formatの一種です。  
オブジェクトストレージやHDFS上のデータをTrinoやSparkなどのエンジン/ツールで操作する基盤を運用する中で突き当たる機能的、性能的な限界を突破するために生まれました。  
大量のデータを扱う際の複雑さとパフォーマンスの問題を解決する様々な特徴を備えています。  

Open Table Format登場の背景やユースケースについては以下を参照してください。  
[データレイクの新しいカタチ：Open Table Formatの紹介](https://bering.hatenadiary.com/entry/2023/07/17/235246)

**もちろん、Icebergが活躍するのはNetflixのような莫大なデータを抱えているデータ基盤ばかりではありません。**  
HDFSやAmazon S3のようなオブジェクトストレージにデータを貯めて、TrinoやAmazon Athenaからクエリするような基盤を構築して、最初は便利だったものの、用途が複雑化、大規模化するに従って様々な運用上の課題に突き当たった経験はありませんか？  
ストリーミングやCDCのユースケースで、KafkaやFlinkのデータを蓄積して、操作する基盤の構築に悩んだ経験はありませんか？  
そのような場面で、Apache Icebergは役に立つ可能性があります。

Icebergの詳細を知りたい方は以下をご参照ください。  
[Iceberg 公式 Doc](https://iceberg.apache.org/docs/latest/)  
[Apache Iceberg とは何か](https://bering.hatenadiary.com/entry/2023/09/24/175953)
## Spacial Thanks
本ハンズオンは、TabularのSpark + Iceberg Quickstart Imageを活用しています。
https://github.com/tabular-io/docker-spark-iceberg/tree/main