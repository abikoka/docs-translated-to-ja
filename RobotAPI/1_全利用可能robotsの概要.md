# 1. 全利用可能robotsの概要

Transloadit のrobotsはお客様のファイルをインポート、変換そして格納し、各assemblyステップはひとつのrobotを使用します。各robotはファイルを用いて何を実行するか定義するお客様のassemblyステップにパラメータを設定できます。

## 1.1 Fileインポートrobots

これらのrobotsは外部リソースからお客様のファイルをインポートするために利用できます。気をつけてもらいたいのは、アップロードすることに対する特別なrobotは無いことです。アップロードするには直接に処理します。どのようにrobotが実行されるかを学ぶには[minimal integration](https://transloadit.com/docs/the-minimal-integration)をチェックしてください。

## 1.2 Video and audio conversion robots

## 1.3 Image and document conversion robots

## 1.4 Other conversion robots

## 1.5 Assembly flow logic robots

## 1.6 File export robot

# 2. File インポートrotobs

## 2.1 HTTP経由のファイルインポート

## 2.2 SFTP経由のファイルインポート

## 2.3 FTP経由のファイルインポート

## 2.4 Amazon S3からファイルインポート

"/s3/import"robotは指定したAmazon S3 bucketとpathからファイルをインポートします。与えたファイルパスから単一ファイルをインポートでき、もしくは、ディレクトリパスを与えればすべてのファイルをインポートできます。
お客様のS3クレデンシャルをこのrobotに提供する必要があるため、assembly 命令を隠すためのテンプレートと一緒に、常にこれを利用してください。
