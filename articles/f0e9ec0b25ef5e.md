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

### 2. IAM Identity Centerの設定

IAM Identity Center のページに移動します。  
「Settings」「アイデンティティソース」の「アクション」から「アイデンティティソースを変更」をクリックします。  

![アイデンティティソースの変更](/images/update-identity-source.png)  

次に、アイデンティティソースを選択する画面が表示されます。  
「外部 ID プロバイダー」を選択します。  

![アイデンティティソースの選択](/images/select-identity-source.png)  

「外部アイデンティティプロバイダーを設定」という画面が表示されます。  

![外部アイデンティティプロバイダーの設定](/images/setting-external-idp.png)  

この情報を用いて、Google Workspace の設定をします。  
一旦、AWS 側の操作はここでストップして、Google Workspace の設定に移ります。  

### 3. Google Workspaceの設定

Google Workspace の管理コンソールにログインします。  
<https://admin.google.com/>  

「アプリ」→「ウェブアプリとモバイルアプリ」を選択し、「アプリを追加」から「カスタム SAML アプリの追加」をクリックします。 　
![カスタム SAML アプリの追加](/images/create-custom-saml-app.png)  

アプリに関する情報を入力します。  

![アプリ情報の入力](/images/setting-custom-app-detail.png)  

アプリに関する情報を入力したら、「次へ」をクリックします。  
メタデータをダウンロードします。  

![メタデータのダウンロード](/images/download-idp-metadata.png)  

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

属性のマッピングを行います。  

| Google Workspaceの属性 | AWS側の属性名 | 説明 |
| --- | --- | --- |
| Primary Email | email | AWSユーザーのメールアドレスとして使用 |
| First Name | givenName | AWSユーザーの「名」にマッピング |
| Last Name | familyName | AWSユーザーの「姓」にマッピング |
| Primary Email | userName | AWSユーザー名として使用（推奨） |

![属性のマッピング](/images/setting-attribute-mapping.png)  

最後に、このアプリを有効化します。  
サービスのステータスをオンにします。  

![アプリの有効化](/images/custom-saml-app-service-status.png)  

### 4. SSOログインしてみる (失敗する)

では、Google Workspace の SSO ログインを試してみましょう。  
ただし、まだ SSO 用のユーザを作成していないため、ログインに失敗します。  

IAM Identity Center のコンソール画面から「Settings」へ移動し、「アイデンティティソース」内の「AWS access portal URL」をクリックします。  
Google による認証画面へ遷移します。  
ログインに利用する Google アカウントを選択し、ログインします。  

以下のようなエラーが表示されるはずです。  

![IAM Identity Centerのエラー](/images/iam-identity-center-signin-failure.png)  

これは、IAM Identity Center にユーザーが登録されていないためです。  
では、ユーザーを登録してみましょう。  

---

余談です。  
IdP initiated SSO も可能です。  
先ほど作成したカスタム SAML アプリを選択し、アプリの詳細画面にアクセスします。  
「SAML ログインをテスト」をクリックして、IdP initiated SSO を試してみましょう。  
![IdP initiated SSO](/images/idp-initiated-saml-signin.png)  

こちらもまだユーザーが登録されていないため、ログインに失敗します。  

### 5. ユーザーの登録

IAM Identity Center のコンソール画面から「Users」へ移動し、「ユーザーを追加」をクリックします。  

![ユーザーの追加](/images/iam-identity-center-users.png)  

ユーザー情報を入力します。  
大切なのは、ユーザー名です。  
ユーザー名は属性マッピングで指定したものと一致している必要があります。  
先ほどは「Primary Email」を「userName」としてマッピングしていたため、ユーザー名はメールアドレスとして入力します。  
Google Workspace のユーザー名が`default@example.com`であれば、その認証情報を利用した AWS のユーザー名も`default@example.com`となります。  

今回は簡単のため、グループでの管理は行いません。  
そのまま進み、「ユーザーを追加」をクリックします。  

ユーザの作成が完了したら、先ほどと同様に Google Workspace の SSO ログインを試してみましょう。  
先ほどと異なり、今回はログインに成功します。  

`AWS アクセスポータルに移動しました`とのメッセージが表示されれば成功です。  

![AWSアクセスポータル](/images/aws-access-portal.png)  

### 6. 許可セットの設定

ユーザーを追加したら、許可セットを設定します。  
これによって、ユーザーがどのような権限を持つかを設定できます。  

「IAM Identity Center」の「許可セット」を開きます。 　
「許可セットを作成」をクリックします。  

![許可セットの作成](/images/create-permission-set.png)  

今回は簡単のため、「事前定義された許可セット」から「AdministratorAccess」を選択します。 　
許可セットの詳細はデフォルトのままで問題ありません。  
「作成」をクリックします。  
これで、許可セットの作成が完了しました。  

![許可セットの作成完了](/images/permission-sets-list.png)  

### 7. ユーザーへの許可セットの割り当て

許可セットをユーザーに割り当てます。  
作成したユーザーを選択し、「AWS アカウント」をクリックします。  

![AWSアカウントへのアクセス](/images/assign-account.png)  

「アカウントを割り当てる」をクリックします。

対象のアカウントと許可セットを選択し、「Assign」をクリックします。  

![許可セットの割り当て (アカウント)](/images/assign-account-target-account.png)  
![許可セットの割り当て (許可セット)](/images/assign-account-target-permission-sets.png)  

割り当てが完了したら、ユーザーの詳細画面に以下のような表示がされます。  

![AWSアカウントへのアクセス](/images/access-to-aws-account.png)  

では、Google Workspace の SSO ログインを試してみましょう。  
以下のように、対象のアカウントと許可セットが表示されれば成功です。  

![AWSアカウントへのアクセス](/images/aws-access-portal-granted.png)  

対象のアカウントの許可セットをクリックして、ログインします。  
正しくログインできれば成功です。  
通常の AWS マネジメントコンソールにログインできます。  
