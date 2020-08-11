---
title: "PostgreSQL 9.3以降でセキュリティを疎かにすると、任意のコード実行できちゃうので気をつけよう"
date: "2020-01-31"
layout: "post"
---

はい。

PostgreSQL 9.3から、COPYコマンドにある機能が追加されているようです。これは、コマンドの実行結果をレコードに挿入したり、レコードの結果をコマンドの標準入力として実行したりできるものです。詳しくは公式サイトの9.2と9.3の説明を見比べてみてください。

9.2のCOPY [https://www.postgresql.org/docs/9.2/sql-copy.html](https://www.postgresql.org/docs/9.2/sql-copy.html)  
9.3のCOPY [https://www.postgresql.org/docs/9.3/sql-copy.html](https://www.postgresql.org/docs/9.3/sql-copy.html)

聡明な読者の皆さんはお気づきかと思いますが、**外部からPostgreSQLにログインさえできれば**、これを使って任意のコマンドが実行できるのです(ただしコマンドはPostgreSQL実行ユーザで実行されるので、そのユーザの権限で実行できる範囲に留まります)。

これは仕様なので、脆弱性と言えるのかどうか...

[https://nvd.nist.gov/vuln/detail/CVE-2019-9193](https://nvd.nist.gov/vuln/detail/CVE-2019-9193)

https://medium.com/greenwolf-security/authenticated-arbitrary-command-execution-on-postgresql-9-3-latest-cd18945914d5

詳しく説明しているブログ

とにかく、

**PostgreSQLのポートを閉じていたり、PostgreSQLでユーザのログインできるIPアドレスをlocalhostに絞っていたりすれば、防げる話**です。

## マジで不必要なポートは閉じましょう。

必要最低限のポートだけを開けましょう。

次の投稿では、セキュリティを意識したサーバの建て方を書きます、きっと。
