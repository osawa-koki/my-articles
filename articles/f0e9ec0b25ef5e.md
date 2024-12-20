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
