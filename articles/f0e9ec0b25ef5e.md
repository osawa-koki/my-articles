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
ユーザー情報とユーザーの認証情報を Google Workspace に集約することで、ユーザーの管理を簡略化できます。  
