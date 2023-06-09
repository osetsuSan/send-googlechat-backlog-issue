# Backlog期限日、期限切れの課題をデイリーでGoogleChatへ送信

## 実装の背景
デイリーでBacklogのチェックとチャットへの通知を手動でやっていたが、
休みとか多忙の為安定して告知ができなくなるケースも発生してきたため、
GAS(GoogleAppsScript)を利用し、定期実行を行えればと考えて実装してみました。

## 処理の目的
Backlogで登録されている課題の中で当日期限または期限が超過している課題を抽出し、毎日件数をGoogleChatへ送信

また、期限のチェックの他にステータスが”**未対応**”のままになっている課題も同時に取得し通知

## 実装の流れ
### 1. BacklogのAPIキーを取得
  - Backlogにログインし、画面右上にある自身のアイコンをクリックし**個人設定**をクリック
  - 個人設定画面が表示されたら左カラムにある**API**をクリック
  - 新しいAPIキーを発行の項目が表示されます。メモの項目に用途などを入力し**登録**ボタンをクリック
  - 登録ボタンを押すとAPIキーが発行されるので、APIキーを忘れないようにメモをする。
### 2. GoogleChatのAPIキーを取得
  - GoogleChatの画面で通知したいスペースを開きます。
  - スペースの設定メニューを開き**アプリと統合**をクリック（2023年4月現在）
  - ポップアップウィンドウが開き、連携しているアプリが表示されます。ポップアップウィンドウ下にある**Webhookを管理**ボタンをクリック
  - Webhookを追加ボタンが表示されるので、通知を行う**名前**と**アイコン**を設定し保存
  - 保存後GoogleChatのAPIキーが表示されるので忘れないようにメモをする。
### 3. GASの設定を行う
  - https://script.google.com/home
  へアクセスし、画面左上の**新しいプロジェクト**をクリック
  - スクリプト編集画面に遷移してしまうが、まだスクリプトを入力せず画面左下の*歯車*のアイコンをクリック
  - プロジェクトの設定画面が表示されます。画面一番下にある**スクリプト プロパティ**にAPIキーを設定していきます。
  ```
  プロパティ：BACKLOG_API_KEY
  値　　　　：BacklogのAPIキー
  
  プロパティ：BACKLOG_PROJECT_ID
  値　　　　：Backlogのプロジェクト設定画面のURLにあるプロジェクトID
  
  プロパティ：BACKLOG_SPACE_FQDN
  値　　　　：Backlogのドメイン（https://含まない）
  ```
### 4. GASのスクリプトを作成
  - GASのスクリプト画面へ遷移し、スクリプトを設定（send_googlechat_backlog.gas）
  
  ※【】に囲まれている箇所は各自の環境で書き換えてください。
  - 実行を行い、Backlogの課題内容をもとに期限切れの件数が取得しGoogleChatへ送信されているか確認を行います。
### 5. GAS実行のタイミングを設定
  - スクリプトに問題がなければ、定時実行の設定を行います。GAS画面左側にある時計のアイコンをクリック
  - トリガー画面が表示されます。画面右下の**トリガーを追加**をクリックし下記設定を行います。
  ```
  実行する関数：task
  イベントのソースを選択：時間手動型
  時間ベースのトリガーのタイプを選択：日付ベースのタイマー
  時刻を選択：通知したい時間帯を設定してください。（選択した時間内で実行されます。正確な時間の設定はできません。）
  エラー通知設定：任意の設定
  ```
### 6. 動作検証
  - トリガー設定後、設定した時間帯にGoogleChatへ通知されるか確認をします。

## 最後に
土日祝日にChat通知してしまうと社内でクレームになりそうだったので、

土日祝日判定はどうしようか悩んでいるところ以下のURLを発見し、参考にさせていただきました！

https://auto-worker.com/blog/?p=2494

ありがとうございました！
