---
title: "[Terraform] Azure Functions -> 503 Service Unavailable (Azure)"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "Azure", "Azure Functions"]
published: true
---

## サマリ

Terraform で Azure Functions を構築してアクセスすると 503 Service Unavailable が発生しました。  
なんだか少し可愛らしいエラー画面ですね。  

![503エラー](/images/azure-functions-503-error.png)  

そんな可愛らしさとは裏腹に、原因追及は非常に困難でした。  
さて、原因は何だったでしょうか。  

答えは.NET のバージョンが指定されていないからでした。  

## なぜ、これでプロビジョニングできたか

これが謎。  

Azure ポータルからリソースを作成しようとするとこのようなことは発生しませんでした。
しかし、Terraform で`azurerm_linux_function_app`を使用し、`dotnet_version`を指定しないでプロビジョニングすると、503 エラーが発生しました。  

旧来の`azurerm_function_app`では、プロビジョニング時にエラーが発生します。
しかし、4.0 から推奨された`azurerm_linux_function_app`では、プロビジョニングは成功します。  
プロビジョニングには成功して、実行時に 503 エラーのみを出力するため、原因追及が困難でした。  

ChatGPT に聞いてみても、回答してくれませんでした。  
これは ChatGPT が使用しているデータモデルが古いため、`azurerm_linux_function_app`の動作については理解していないからでしょう。  

---

運よく`dotnet_version`を指定したことろ、プロビジョニングが成功し、503 エラーが解消されました。  

## コード例

```tf
resource "azurerm_linux_function_app" "function_app" {
  name                       = var.function_app_name
  location                   = azurerm_resource_group.resource_group.location
  resource_group_name        = azurerm_resource_group.resource_group.name
  service_plan_id            = azurerm_service_plan.service_plan.id
  storage_account_name       = azurerm_storage_account.storage_account.name
  storage_account_access_key = azurerm_storage_account.storage_account.primary_access_key

  site_config {
    application_stack {
      dotnet_version = "6.0" // 🐙これがないと503エラーが発生する!
    }
  }

  app_settings = {
    "MY_ENV" = "my-env"
  }

  connection_string {
    name      = "MY_CONNECTION_STRING"
    type      = "MySql"
    value     = "Server=${azurerm_mariadb_server.db_server.name}.mariadb.database.azure.com;Database=${azurerm_mariadb_database.db_database.name};Uid=${var.mariadb_admin_username}@${azurerm_mariadb_server.db_server.name};Pwd=${var.mariadb_admin_password}"
  }

  virtual_network_subnet_id = azurerm_subnet.subnet.id
}
```
