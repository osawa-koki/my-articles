---
title: "IAM Identity Centerを用いて、Google Workspaceの認証情報を用いてAWSにサインインする"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "iam", "sso"]
published: true
---

## 概要

AWS の IAM Identity Center を用いて、Google Workspace の認証情報を用いて AWS にサインインする方法を紹介します。  
静的な認証情報を使わずに、Google Workspace の認証情報を使って AWS にサインインすることで、セキュリティを向上させることができます。  
また、ユーザー情報とユーザーの認証情報を Google Workspace に集約することで、ユーザーの管理を簡略化できます。  

## 手順

### 1. IAM Identity Centerの有効化

AWS のマネジメントコンソールにログインします。  
検索バーに「IAM Identity Center」と入力し、IAM Identity Center のページに移動します。  
IAM Identity Center を有効化します。  

![IAM Identity Centerの有効化](/images/iam-identity-center-init.png)  

### 2. IdPの設定

IAM Identity Center のページに移動します。  
「Settings」「アイデンティティソース」の「アクション」から「アイデンティティソースを変更」をクリックします。  

![アイデンティティソースの変更](/images/update-identity-source.png)  

次に、アイデンティティソースを選択する画面が表示されます。  
「外部 ID プロバイダー」を選択します。  

![アイデンティティソースの選択](/images/select-identity-source.png)  

「外部アイデンティティプロバイダーを設定」という画面が表示されます。  

![外部アイデンティティプロバイダーの設定](/images/setting-external-idp.png)  

この情報を用いて、Google Workspace の設定を行います。  

### 3. Google Workspaceの設定

Google Workspace の管理コンソールにログインします。  
<https://admin.google.com/>  

「アプリ」→「ウェブアプリとモバイルアプリ」を選択し、「アプリを追加」から「カスタム SAML アプリの追加」をクリックします。 　
![カスタム SAML アプリの追加](/images/create-custom-saml-app.png)  

アプリに関する情報を入力します。  

![アプリ情報の入力](/images/setting-custom-app-overview.png)  

アプリに関する情報を入力したら、「次へ」をクリックします。  
メタデータをダウンロードします。  

![メタデータのダウンロード](/images/download-metadata.png)  

サービスプロバイダの詳細を入力します。 　
AWS の IAM Identity Center の画面に戻り、各情報を入力します。  

| Google Workspace | AWS IAM Identity Center |
| --- | --- |
| ACS の URL | IAM Identity Center Assertion Consumer Service (ACS) の URL |
| エンティティ ID | IAM Identity Center 発行者 URL |
| 開始 URL | AWS access portal サインイン URL |

![サービスプロバイダの詳細](/images/setting-service-provider.png)  

その他の項目はデフォルトのままで問題ありません。  

![サービスプロバイダの詳細 - その他の項目](/images/setting-service-provider-other.png)  

最後に属性のマッピングを行います。  

| Google Workspaceの属性 | AWS側の属性名 | 説明 |
| --- | --- | --- |
| Primary Email | email | AWSユーザーのメールアドレスとして使用 |
| First Name | givenName | AWSユーザーの「名」にマッピング |
| Last Name | familyName | AWSユーザーの「姓」にマッピング |
| Primary Email | userName | AWSユーザー名として使用（推奨） |

![属性のマッピング](/images/setting-attribute-mapping.png)  

### 4. SSOログインしてみる (失敗する)

では、Google Workspace の SSO ログインを試してみましょう。  
ただし、まだSSO用のユーザを作成していないため、ログインに失敗します。  

IAM Identity Centerのコンソール画面から「Settings」へ移動し、「アイデンティティソース」内の「AWS access portal URL」をクリックします。 　
Googleによる認証画面へ遷移します。  
ログインに利用する Google アカウントを選択し、ログインします。  

以下のようなエラーが表示されるはずです。  

![IAM Identity Centerのエラー](/images/iam-identity-center-signin-failure.png)  

これは、IAM Identity Center にユーザーが登録されていないためです。  
では、ユーザーを登録してみましょう。  
