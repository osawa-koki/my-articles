---
title: "簡単なGraphQLサーバを作成してみよう！"
---

## はじめに

このチャプターではNode.jsを用いて最も簡単なGraphQLサーバを実装してみます。  
以下の環境が用意されていることを想定します。  

| name | version |
| --- | --- |
| Node.js | >= 18.x |
| yarn | >= 12.x.x |

## Node.jsプロジェクトの作成

まずは、Node.jsプロジェクトを作成します。  

```bash
# ディレクトリの作成と移動。
mkdir <project-name> && cd <project-name>

# Node.jsプロジェクトの初期化。 
yarn init -y
```

これで、`package.json`が作成されます。  
当プログラムはモジュールモードで実行するため、`"type": "module"`を追加しておきます。  

```json
{
  ...
  "type": "module"
}
```

## 必要なパッケージのインストール

次にGraphQLサーバを実装するために必要なパッケージをインストールします。  

```bash
yarn add @apollo/server graphql
```

`graphql`はGraphQLに関するパッケージで、`@apollo/server`はGraphQLサーバを実装するためのパッケージです。  

---

また、ホットリロードを実現するために`nodemon`をインストールします。  
これで、ファイルの変更を検知して自動的にサーバを再起動してくれます。  

```bash
yarn add -D nodemon
```

## yarnのスクリプトを設定

`package.json`の`scripts`に以下のスクリプトを追加します。  

```json
{
  "scripts": {
    "dev": "nodemon ./index.js",
    "start": "node ./index.js"
  }
}
```

`dev`はホットリロードを実行するためのスクリプトで、`start`は通常の起動を行うためのスクリプトです。  
開発時は`dev`を、本番環境では`start`を実行するようにします。  

## JSスクリプトの作成

では、早速GraphQLサーバを実装していきましょう。  
`./index.js`を作成し、以下のコードを記述します。  

```js
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

const typeDefs = `
  type Book {
    title: String
    author: String
  }

  type Query {
    books: [Book]
  }
`;

const books = [
  {
    title: 'The Awakening',
    author: 'Kate Chopin',
  },
  {
    title: 'City of Glass',
    author: 'Paul Auster',
  },
];

const resolvers = {
  Query: {
    books: () => books,
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
});

console.log(`🚀  Server ready at: ${url}`);
```

コードの中身については、後ほど説明します。  
では、実際にサーバを起動してみましょう。  

```bash
yarn dev
```

以下のようなログが出力されれば成功です。  

```bash
🚀  Server ready at: http://localhost:4000/
```

では、ブラウザで`http://localhost:4000/`にアクセスしてみましょう。  

![GraphQL sandbox](/images/graphql-server-step-by-step_sandbox.webp)  

表示されているクエリをそのまま実行してみましょう！  
正しくデータが取得できていることがわかります！  

## クエリについて

サンプルでは以下のクエリが実行されています。  

```graphql
query ExampleQuery {
  books {
    title
  }
}
```

これは、`books`を取得するクエリです。  
また、取得対象フィールドとして`title`を指定しています。  
したがって、以下のようなレスポンスが返ってきます。  

```json
{
  "data": {
    "books": [
      {
        "title": "The Awakening"
      },
      {
        "title": "City of Glass"
      }
    ]
  }
}
```

では、`author`も取得してみましょう。  

```graphql
query ExampleQuery {
  books {
    title
    author
  }
}
```

すると、以下のようなレスポンスが返ってきます。  

```json
{
  "data": {
    "books": [
      {
        "title": "The Awakening",
        "author": "Kate Chopin"
      },
      {
        "title": "City of Glass",
        "author": "Paul Auster"
      }
    ]
  }
}
```

指定したフィールドが取得できていることがわかります。  

## Query・Mutation・Subscription

GraphQLには、`Query`・`Mutation`・`Subscription`という3つの型があります。  

| name | description |
| --- | --- |
| Query | データの取得 |
| Mutation | データの更新 |
| Subscription | データの購読 |

REST APIでは、`GET`・`POST`・`PUT`・`DELETE`という4つのメソッドがありますが、GraphQLでは、`Query`・`Mutation`・`Subscription`という3つの型でそれぞれの役割を担っています。  

GETメソッドは、`Query`に相当し、データの取得を行います。  
POST・PUT・DELETEメソッドは、`Mutation`に相当し、データの更新を行います。  
また、`Subscription`は、データの購読を行います。  
こちらは、内部的にWebSocketを使用してリアルタイムにデータを取得することができます。  

## スクリプトの解説

最初にコードで使用するパッケージをインポートしています。  

少し前までは、`apollo-server`パッケージを使用していましたが、現在は`@apollo/server`パッケージを使用するようになっています。  
2023年の10月までは`apollo-server`パッケージもサポートされていますが、今後は`@apollo/server`パッケージを使用するようにしましょう。  

また、`startStandaloneServer`は`@apollo/server/standalone`パッケージに含まれています。  
これは、`ApolloServer`を簡単に起動するための関数です。  

```js
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';
```

次に、GraphQLのスキーマを定義しています。  
ここでは、`Book`という型と、`Query`という型を定義しています。  

GraphQLで使用できる型は、以下のようなものがあります。  

- `Int`
- `Float`
- `String`
- `Boolean`
- `ID`

プロパティとデータ型は、`:`で区切って、`Property: DataType`のように定義します。  
また、配列を定義する場合は、`[]`を使用します。  

`!`を付けると、必須項目になります。  

```js
const typeDefs = `
  type Book {
    title: String
    author: String
  }

  type Query {
    books: [Book]
  }
`;
```

ここでは、実際のデータを定義しています。  
通常はデータベースから取得することになると思いますが、今回はサンプルなので、配列でそのまま定義しています。  

```js
const books = [
  {
    title: 'The Awakening',
    author: 'Kate Chopin',
  },
  {
    title: 'City of Glass',
    author: 'Paul Auster',
  },
];

```

次に、リゾルバを定義しています。  
リゾルバとは、クエリを実行する際に呼び出される関数のことです。  
ここでは、`books`を取得するクエリを定義しています。  

フィルタ条件などを指定する場合には、引数を取る関数を定義して、その中でフィルタを行うようにしますが、ここではフィルタを行わないので、空の引数を受け取る関数を定義しています。  

```js
const resolvers = {
  Query: {
    books: () => books,
  },
};

```

ここで、サーバをオブジェクトを作成しています。  
引数にはデータ型を定義した`typeDefs`と、リゾルバを定義した`resolvers`を指定します。  

```js
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

```

最後に、サーバを起動しています。  
  
```js
const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
});

console.log(`🚀  Server ready at: ${url}`);
```
