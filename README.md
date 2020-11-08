# simple-websockets-chat-app

## Requirements
* wscat: 4.0.1

## プロジェクト構成

```
.
├── README.md                   
├── onconnect                   <-- ハンドシェイク確立
├── ondisconnect                <-- ハンドシェイク削除
├── sendmessage                 <-- メッセージ送信
└── template.yaml               <-- template.yaml
                                    AWSリソースの定義ファイル。LambdaやAPI Gateway, DynamoDB等の定義を記述
```


## デプロイ方法

1. パッケージング
```
sam package --s3-bucket <任意のS3バケット> --output-template-file packaged.yaml

(例)
sam package --s3-bucket spa-test-bucket-gray --output-template-file packaged.yaml
```

2. デプロイ
https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/tutorial-lambda-sam-deploy.html
```
sam deploy --template-file packaged.yaml --stack-name <Stack名> --capabilities CAPABILITY_IAM

(例)
sam deploy --template-file packaged.yaml --stack-name sample-websocket --capabilities CAPABILITY_IAM
```

## ローカルからWebSocket の検証

2. Websocketとハンドシェイクを確立します。
``` bash
$ wscat -c wss://{YOUR-API-ID}.execute-api.{YOUR-REGION}.amazonaws.com/{STAGE}
```
3. デプロイしたSendMessageから「テスト」を実行するとhello world文字列がハンドシェイクを確立したクライアントに
   メッセージを送信できます。
   
   
``` bash
(例)
1.  「テスト」に以下のJSONを用意し実行します。
{
  "endpoint": "<API Gatewayのwebsocketのエンドポイント>",
  "message": "hello world"
}

2. ハンドシェイクを確立しているクライアントにメッセージが届きます。
$ wscat -c wss://{YOUR-API-ID}.execute-api.{YOUR-REGION}.amazonaws.com/prod
connected (press CTRL+C to quit)
< hello world
```

(補足) クライアント側からWebsocket通信する場合は以下のようなメッセージを送ることでやりとりできます
```
> {"action":"sendmessage", "data":"hello world"}
< hello world
```

4. スタックの削除
```
aws cloudformation delete-stack --stack-name sample-websocket --region ap-northeast-1
```
