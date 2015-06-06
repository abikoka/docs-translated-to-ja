# 6. ファイル出力robots

## 6.1 /s3/store bot を用いたAmazon S3への出力

入力ファイルをAmazon S3 bucketへ出力。お客様がAmazon S3ｗ初めて利用するならば、こちらの[お客様のS3 bucketを利用する](https://transloadit.com/docs/use-your-own-s3-bucket)のチュートリアルを参照。

お客様のS3 bucketの結果情報ファイルに対するURLはTransloadit結果情報のJSONの中に返されるだろう。

```
【重要】Transloaditの結果情報のURLは、フォーム http://{bucket}.s3.amazonaws.com/path/to/fileを利用し、このことは、お客様のbucket名がDNS-compliant(DNS準拠)でなければならない。これはbucket名に大文字を使わないことも含んでいる。これに関わるより多くの情報は[Amazonの推奨](http://docs.amazonwebservices.com/AmazonS3/latest/dev/BucketRestrictions.html)を参照ください。英数字ではない文字列はアンダースコアに置き換わり、スペースはダッシュ(-)に置き換わることに注意してください。もし、お客様の既存のS3 bucket名が大文字やDNS準拠にしない場合、robotの"url_prefix"パラメータを利用して結果情報のURLを書き換るようにしてください。

```

お客様は、bucketに対して権限設定をつかする必要があります。Transloaditはbucketに正常にアクセスできるようにするためです。こちらにお客様が利用できるIAMポリシーのサンプルがあります。Transroditを利用してS3 bucketに対してファイルを出力するためのminimun required permissionsを含んでいます。お客様のアプリケーションに依存するより多くの権限設定（特に表示に関わる権限設定）が要求されるだろう。

状況に応じて、BUCKET_NAMEとSIDやResourceの値を変更してください。また、このポリシーはすべてのお客様側のユーザに対してminimum required permissionを承諾するだろう（"Principal"の値はAWS: ”*”).分割するAmzon IAM ユーザを生成することをアドバイスします。そして、"Principal"の値に対してそのユーザARN（[ここ](https://console.aws.amazon.com/iam/home#users)のユーザの"Summary"タブで見つけることができる）を利用します。より詳細な情報は[ここ](http://docs.aws.amazon.com/AmazonS3/latest/dev/AccessPolicyLanguage_UseCases_s3_a.html)で見つけられます

```
{
    "Statement": [
        {
            "Sid": "transloaditUpload",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Principal": {
                "AWS": "*"
            },
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::BUCKET_NAME/*"
        },
        {
            "Sid": "transloaditList",
            "Action": [
                "s3:ListBucket"
            ],
            "Principal": {
                "AWS": "*"
            },
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::BUCKET_NAME"
        }
    ]
}
```
SIDの値は、後でルールをお客様に認識してもらうために指定します。好きなように名付けることができます。
ポリシーは２つのパートで分けさせる必要があります。理由は、その他のアクションはbucketの中のオブジェクトに対する権限を要求するの対して、ListBucketアクションはbucketに対する権限を要求します。オブジェクトを対象にする際、Resourceパラメータ中の後続のスラッシュとアスタリスクをあり、スラッシュとアスタリスクを省略します。
注意して欲しいのは、robotのaclパラメータ"bucket-default"の値を与えるならば、それからお客様のbucketポリシーの"s3:PutObjectAcl"権限を必要としません。
