---
title: "GraphQLサーバをTypeScriptに変換してみよう！"
---

## GraphQLサーバをTypeScriptに変換してみよう

GraphQLサーバをTypeScriptに変換してみましょう！  
[@apollo/serverの公式サイト](https://www.apollographql.com/docs/apollo-server/getting-started)ではTypeScriptでの実装方法が推奨されています。  

ということで先ほど作成したGraphQLサーバをTypeScriptに変換してみます。  

## TypeScriptの設定イロイロ

最初にTypeScriptの設定を行います。  
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
本当はモジュールモードで実行したいのですが、、、  

`package.json`の`type`を以下のように変更します。  

```json
{
  ...
  "type": "commonjs"
}
```

## JSスクリプトの変換

では、先ほど作成したJavaScriptのスクリプトをTypeScriptに変換していきます。  
`./index.ts`を作成し、以下のコードを記述します。  

```diff_ts
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

また、TLAの問題に関しても修正しています。  
これを実行してみます！  

```bash
yarn dev
```

これでも問題なくサーバが起動できました！  
これでTypeScriptでのGraphQLサーバの実装ができました！  

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

これで以下のコマンドで型チェックができるようになりました！  

```bash
yarn type-check
```

正しく型チェックができることを確認しておきましょう！  

## このコードの問題点

お気づきの方もいるかもしれませんが、このコードには問題点があります。  
GraphQLのスキーマ定義に`Book`という型を定義していますが、これと全く同じ型をTypeScriptで定義しています。  

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

これは、GraphQLのスキーマ定義とによってTypeScriptの型を生成することで解決できます。  
この方法についてはのちほど解説します。  
