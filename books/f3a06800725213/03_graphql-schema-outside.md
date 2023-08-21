---
title: "GraphQLスキーマを外部ファイル化する！"
---

## GraphQLスキーマを外部ファイル化する

先ほどはGraphQLのスキーマをソースコード内で直接定義しました。  

```ts
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

可読性の観点やGitの差分を見やすくするために、スキーマを外部ファイル化することが多いです。  
ということで、スキーマを外部ファイル化してみましょう！  

<https://the-guild.dev/graphql/tools/docs/schema-loading>  

## 必要なパッケージのインストール

スキーマを外部ファイル化するために、以下のパッケージをインストールします。  

```bash
yarn add @graphql-tools/graphql-file-loader @graphql-tools/load @graphql-tools/schema
```

これで、外部ファイルに定義したスキーマの読み込み、スキーマのGraphQLサーバへの適用ができるようになりました！  

## スキーマの外部ファイル化

まずは、スキーマを外部ファイル化します。  

`./schemas/query.graphql`を作成し、以下の内容を記述します。  

```graphql
type Query {
  books: [Book]
}
```

`./schemas/book.graphql`を作成し、以下の内容を記述します。  

```graphql
type Book {
  title: String
  author: String
}
```

次にソースコードにベタ書きされている箇所を削除して、外部ファイルを読み込むようにします。  

```ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

+ import { GraphQLFileLoader } from '@graphql-tools/graphql-file-loader';
+ import { loadSchemaSync } from '@graphql-tools/load';
+ import { addResolversToSchema } from '@graphql-tools/schema';

(async () => {
  interface Book {
    title: string;
    author: string;
  }

+ const schema = loadSchemaSync('./schemas/**/*.graphql', {
+   loaders: [new GraphQLFileLoader()],
+ });

-  const typeDefs = `
-   type Book {
-     title: String
-     author: String
-   }
-
-   type Query {
-     books: [Book]
-   }
- `;

  const books: Book[] = [
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
      books: (): Book[] => books,
    },
  };

+ const schemaWithResolvers = addResolversToSchema({ schema, resolvers });
+ const server = new ApolloServer({ schema: schemaWithResolvers });

- const server = new ApolloServer({
-   typeDefs,
-   resolvers,
- });

  const { url } = await startStandaloneServer(server, {
    listen: { port: 4000 },
  });

  console.log(`🚀  Server ready at: ${url}`);
})()
  .then(() => console.log('Server started...'))
  .catch((e) => console.error(e));
```

この状態で、`yarn dev`を実行してみましょう！  
今までと同じようにGraphQLサーバが起動することが確認できると思います！  
