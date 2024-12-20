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

### 2. メタデータファイルのダウンロード

<https://admin.google.com/ac/security/ssocert>にアクセスし、メタデータファイルをダウンロードします。  

![メタデータファイルのダウンロード](/images/download-idp-metadata.png)  
