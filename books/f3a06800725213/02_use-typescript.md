---
title: "GraphQLサーバをTypeScriptに変換してみよう！"
---

## GraphQLサーバをTypeScriptに変換してみよう

GraphQL サーバを TypeScript に変換してみましょう。  
[@apollo/serverの公式サイト](https://www.apollographql.com/docs/apollo-server/getting-started)では TypeScript での実装方法が推奨されています。  

ということで先ほど作成した GraphQL サーバを TypeScript に変換してみます。  

## TypeScriptの設定イロイロ

最初に TypeScript を設定します。  
必要なパッケージをインストールします。  

```bash
yarn add -D typescript @types/node
yarn add ts-node
```

以下のコマンドで`tsconfig.json`を作成します。  

```bash
yarn tsc --init
```

次に`package.json`の`scripts`を以下のように変更します。  

```json
{
  "scripts": {
    "dev": "nodemon --ext ts --exec ts-node ./index.ts",
    "start": "ts-node ./index.ts"
  }
}
```

また、簡単のため、モジュールモードを解除します。  

`package.json`の`type`を以下のように変更します。  

```json
{
  ...
  "type": "commonjs"
}
```

## JSスクリプトの変換

では、先ほど作成した JavaScript のスクリプトを TypeScript に変換していきます。  
`./index.ts`を作成し、以下のコードを記述します。  

```diff ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

(async () => {
+   interface Book {
+     title: string;
+     author: string;
+   }

  const typeDefs = `
    type Book {
      title: String
      author: String
    }

    type Query {
      books: [Book]
    }
  `;

-   const books = [
+   const books: Book[] = [
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
-     books: () => books,
+     books: (): Book[] => books,
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
})()
  .then(() => console.log('Server started...'))
  .catch((e) => console.error(e));
```

`Book`というデータ型を定義して、`books`という変数や、リゾルバの戻り値の型を定義しています。  

また、TLA の問題も修正しています。  
これを実行してみます。  

```bash
yarn dev
```

これでも問題なくサーバが起動できました。  
これで TypeScript での GraphQL サーバの実装の完了です。  

## 型チェックコマンドの追加

最後に、型チェックコマンドを追加します。  
`package.json`の`scripts`を以下のように変更します。  

```json
{
  "scripts": {
    "dev": "nodemon --ext ts --exec ts-node ./index.ts",
    "start": "ts-node ./index.ts",
    "type-check": "tsc --noEmit"
  }
}
```

これで以下のコマンドで型チェックができるようになりました。  

```bash
yarn type-check
```

正しく型チェックができることを確認しておきましょう。  

## このコードの問題点

このコードには問題点があります。  
GraphQL のスキーマ定義に`Book`という型を定義していますが、これと全く同じ型を TypeScript で定義しています。  

以下の部分が該当します。  

```ts
interface Book {
  title: string;
  author: string;
}

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

これは、GraphQL のスキーマ定義とによって TypeScript の型を生成することで解決できます。  
この方法についてはのちほど解説します。  
