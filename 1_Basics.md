# 1. 基本事項

## 1.1 導入

### どのように稼働する？

	1. Transloaditを利用するためには、HTMLフォームを利用可能なページに、本サイトのjQuery pluginを導入しなさい
	2. Transloaditは、例えば、エンコーディングするビデオやリサイズする画像などアップロードされるファイル、そして、Amazon S3へその結果を格納するなど、処理してくれます。
	3. 処理が完了すると同時に、jQuery pluginはあなたの設置するフォームに対してtransloaditを呼び出したHiddenフィールドに結果のJSONを設定します。それから、そのフォームは通常通りにサブミットされます。
	4. あなたのバックエンドで、Transloaditフォームからの結果JSONを分析し、データベースに結果ファイルに対するURLを格納します。

上記は、一般的なワークフローです。あなた側の実装方法に合わせてカスタマイズもできます。

### Transloadit側は何を提供するか

我々は、あなたのWebアプリもしくはモバイルアプリに対するファイルアップロードや変換を管理するコンポーネントを提供します。
	- 画像やビデオやドキュメントなどをエンコーディング／リサイジング／変換を管理するためのRESTful JSON API
	- あなた側のフォームにTransloadit機能群を利用可能にするjQuery plugin
	- お客様側のバックエンドからAPI呼び出しを送る多くの言語やフレームワークに対応する開発キットそして、jQuery pluginを頼る必要はありません。

### 用語

#### (Assembly)

(assembly)は、アップロードもしくはインクルードファイルに対するファイル変換指令（assembly 命令）の成果です。各(assembly）は

## 1.2 小規模な結合

以下の段階的なガイドは、Transloaditを利用して小規模なリサイズするアップロードを実施する方法を説明します。

### Step1: アカウントを作成しなさい

signup pageで新規にアカウントを作成しなさい。自動的にあなたのAPIクレデンシャルが生成されます。それから、お客様のアカウントでログインしなさい。

### Step2: アップロードフォームを作成しなさい

お客様側のWebページで、ページ内のソースを編集してフォームのHTMLを加えなさい：

```
<form id="upload-form" action="/uploads" enctype="multipart/form-data" method="POST">
  <input type="file" name="my_file" />
  <input type="submit" value="Upload">
</form>
```

enctype属性には"multipart/form-data"を設定しなさい。そうしなければ、ブラウザはこのフォームがアップロードファイルを利用することを理解できないはずだ。
さらに、お客様側のファイルInputフィールドはNAME属性を含めなければならない。

### Step:3 jQuery plugin を加えなさい

Transloadit jQuery pluginは、いくつかの追加機能を供給します。あなたに以下を許容させるため：

	* アップロード操作中の表示
	* 遠方のAPIのクエリなし直接アップロード結果を得る
	* フォーム送信が終わる前に変換操作が完了するまで待たせる

お客様は、次のHTML/Javascriptを使って、TransloaditのjQuery pluginを追加できる:

```
<!-- You can choose jQuery version 1.9.0 or any newer version here -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
<script src="//assets.transloadit.com/js/jquery.transloadit2-v2-latest.js"></script>
<script type="text/javascript">
   $(function() {
     $('#upload-form').transloadit({
        wait: true,

        params: {
          auth: { key: "YOUR-AUTH-KEY" },
          steps: {
            resize_to_75: {
              robot: "/image/resize",
              use: ":original",
              width: 75,
              height: 75
            }
          }
        }
     });
   });
</script>
```


