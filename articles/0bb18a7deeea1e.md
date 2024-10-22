---
title: "Dynamo DB - Python | Float vs Decimal"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "dynamodb"]
published: true
---

## DynamoDB - Python | Float vs Decimal

DynamoDB では数値型として N(Number / Decimal)という型があります。  
しかしながら、JSON での表現は Float となります。  

これにより、データ型不整合エラーが発生します。  

これを回避するためには、DynamoDB 用のデータの保持には Decimal を使用し、JSON 出力する際には Float に変換する必要があります。  

## Float - Decimal (JSON)

```py
import json
from decimal import Decimal

def decimal_default_proc(obj):
    if isinstance(obj, Decimal):
        return float(obj)
    raise TypeError

# JSONの読み込み時にDecimalに変換する
def json_loads(json_str):
    return json.loads(json_str, parse_float=Decimal)

# JSONの出力時にFloatに変換する
def json_dumps(json_obj):
    return json.dumps(json_obj, default=decimal_default_proc)
```
