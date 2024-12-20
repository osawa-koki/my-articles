---
title: "IAM Identity Centerを用いて、Google Workspaceの認証情報を用いてAWSにサインインする"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "iam", "sso"]
published: true
---

## 概要

AWSのIAM Identity Centerを用いて、Google Workspaceの認証情報を用いてAWSにサインインする方法を紹介します。  
静的な認証情報を使わずに、Google Workspaceの認証情報を使ってAWSにサインインすることで、セキュリティを向上させることができます。  
ユーザー情報とユーザーの認証情報をGoogle Workspaceに集約することで、ユーザーの管理を簡略化することができます。  
