# データベース Overview
Webアプリケーション開発において、取得したデータを適切に保存し、必要なデータを適切に取り出して利用しなければならない場面はよくあります。データベースは、そうした場面で非常に有用な技術ですが、データを適切に取り扱うためにはデータベースがどのようなことに配慮して作られているか理解する必要があります。このOverviewでは、データベースに関する基本的な仕組みについて取り扱います。

## データベースとは
1. データの集合体
   * 事前に決めたルールに従って保存されたデータの集合体のこと
2. DBMS(Database Management System)
   * DBMSはいわゆるDB製品とほぼ同義と考えても良い

## DBMSが備える3つの主要機能
DBMS(Database Management System)は、基本的に以下の3つの機能を備えている

1. データの管理
   * データの集合体をどのような方法・形式で管理するか
2. アクセス手段
   * データベースに保存されているデータをどのように取り出すか
3. トランザクション管理
   * 新たなデータを挿入したり、不要なデータを削除したりするなど、データ操作を行う場合に、どのように読み書きを制御するか

## データベースの種類とデータ管理方法の違い
データベースは製品設計の考え方の違いにより、データの管理方法が複数存在する

1. リレーショナルデータベース
   * ある関係をもつ複数のデータを組(タプル)として扱う、表形式で表現できる
   * 主な製品: PostgreSQL, MySQL, MariaDB, Oracle Database, Microsoft SQL Server
2. ドキュメント指向データベース
   * JSONやXMLなど階層構造をもつデータをドキュメントとして扱う
   * リレーショナルデータベースは、保存できるデータ定義(スキーマ)が必要になるが、ドキュメント指向データベースはJSONデータなどをそのまま保存できるため、事前にデータ定義(スキーマ)を行う必要性がない点で、スキーマレスと言われる
   * 主な製品: MongoDB, Apache CouchDB
3. Key-Valueデータベース
   * 1つのキーに1つの値(バリュー)をデータとして扱う
   * 主な製品: Redis, Apache Cassandra
4. グラフ指向データベース
   * データの関係性を扱うデータベース。ノード、エッジ、プロパティの3要素から構成されるデータを扱う
   * 主な製品: neo4j

上記1以外は、リレーショナルデータベースではないことから、[NoSQL](https://ja.wikipedia.org/wiki/NoSQL)と呼ばれることもある<br>
近年では、リレーショナルデータベースがもつデータの信頼性とNoSQLの性能を両立しようとする[NewSQL](https://ja.wikipedia.org/wiki/NewSQL)と呼ばれるDBも出てきている

## データへのアクセス手段
1. リレーショナルデータベース
   * SQL
     * SQLには、ANSI/ISOで決められた標準SQLと呼ばれるものがある。標準SQLは、決められた年をつけて表記され、現在の最新は「SQL:2023」
     * 各リレーショナルDB製品には、標準SQLに沿いながらも、製品独自のSQLが実装されている。そのため、アプリケーションで利用しているリレーショナルDBを別のリレーショナルDBに変更する場合、アプリケーションが発行している全てのSQLが移行先のリレーショナルDBでも動作するか確認する必要がある。
2. 非リレーショナルデータベース(NoSQL)
   * 独自クエリ
     * データの管理方法により、データへのアクセス方法が異なるため、SQLのような共通言語ではなく、製品ごとに規定された独自クエリを使用する

SQLを使うリレーショナルデータベースであっても差異があることが多く、非リレーショナルデータベース(NoSQL)は独自クエリになるため、アプリケーションのバックエンドに利用するデータベースをアプリケーション構築後に別のデータベースに移行するのは容易ではない。そのため、バックエンドに利用するデータベース選定は慎重に検討する必要がある。

## トランザクションとは
アプリケーションが意味のある処理を実行する際に必要な複数のデータベースへの処理単位

  * 例えば、AさんからBさんに10万円を送金する処理は、Aさんの口座から10万円減額し、Bさんの口座を10万円増額する、という全ての処理が完了しないと「送金」という意図した操作が成立しない。
  * トランザクションは、意図した操作を成立させるために必要な複数の処理を束ねたような概念。

## トランザクションとACID特性
トランザクションの中でも、特にお金の処理などビジネスデータを扱う場合、データ不整合やデータ消失を起こすと重大な事故になってしまう。そうしたことが起きないように、トランザクションでは以下の4つの性質が保証されるべきとされている。

 * **A**tomicity(原子性)
   * 処理を確定する(コミット) or 確定しない(ロールバック)の二者択一になるようにする
 * **C**onsistency(一貫性・整合性)
   * 事前に定義されたルールに違反するデータの入力を拒否する
 * **I**solation(独立性・分離性)
   * 他のトランザクションの影響を受けないようにする
 * **D**urability(永続性)
   * 実行された処理結果は失われない、永続的に保存される

ACID特性は、上記の4つの特性の英単語の頭文字から作られた単語(参考: [Wikipedia](https://ja.wikipedia.org/wiki/ACID_(%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF%E7%A7%91%E5%AD%A6)))

一般的に、リレーショナルデータベースは、ACID特性が保証されているが、非リレーショナルデータベース(NoSQL)ではACID特性が緩められていることがある。

* リレーショナルデータベースは、一般的にACID特性が保証されているため、データを安全に扱うことができるが、ACID特性を保証するためには様々な処理を実行する必要があるため、NoSQLに比べると相対的に速度が遅いと言われる。

* 非リレーショナルデータベース(NoSQL)は、リレーショナルデータベースに比べて、大規模データを高速に処理することができると言われるが、NoSQLがACID特性の一部を緩めることで、大規模データの処理性能を効率的に実施している部分がある。アプリケーション開発者は、アプリケーションで扱うデータを守る上で、ACID特性の一部が緩められていても問題がないか検討し、必要であれば保証されていない特性をアプリケーション側で補う必要があるが、考慮する必要性が無ければ、ACID特性を緩めることで、高速性等の恩恵を受けることができる。

## 原子性(Atomicity)とは
* 例: AさんからBさんに10万円送金する場合

    1. Aさんの口座を10万円減額する
    2. Bさんの口座を10万円増額する

仮に、1の処理を実行した後、2の処理が実行できなかった場合、Aさんの口座だけが減額されるトラブルが発生することになる

原子性は、こうしたトラブルが発生しないよう、例えば2の処理が失敗した場合、1の処理も無かったことになることによって、意図した処理が実行されるか(コミット)、処理が全く実行されないか(ロールバック)、どちらかになるように保証する特性

* コミット(commit)
  
  トランザクション内で実行してきた処理を確定すること

* ロールバック(rollback)

  トランザクション内で実行してきた処理を全て破棄し、トランザクション開始時点の状態に戻すこと

## 一貫性･整合性(Consistency)とは
* 例: 口座の金額は0円以上である

仮に、残高が5千円の口座から、1万円の引落を実行した場合、口座残高はマイナス5千円となり、「口座の金額は0円以上である」というルールに違反することになるため、このケースでは1万円の引落の処理は、データベース側でブロックされ実行されない

一貫性･整合性は、データベースに保存される情報が、設計者の想定していない状態にならないよう、あらかじめ設定されたルール(≒業務仕様)に適合するように保証することができる特性

* 制約の例
   * NOT NULL制約

     NULLデータを許容しない(データが必ずあることを保証する)

   * UNIQUE制約

     UNIQUE制約を設定した列のデータは、必ず一意になる(その列のデータは列内で重複しないことを保証する)

    テーブル内のレコードを一意に特定するために使われる主キー(Primary Key、PKと記載される)と言われるカラムは、NOT NULL制約とUNIQUE制約の2つの制約を設定することで実現されている

## 独立性・分離性(Isolation)とは
データベースは、組織内などで情報共有のために使われるため、同時に複数のアクセス(トランザクション)を処理する必要がある。独立性･分離性は、複数トランザクションを同時実行しても、複数トランザクションを1つずつ逐次実行した時と同じ結果になるように保証する特性。

### ロック(lock)とは
複数のトランザクションを同時実行する際に、処理対象データが他のトランザクションの影響を受けないよう、処理に必要になる期間データを占有する機能。独立性・分離性を保証する上で必要な機能の1つ。

* 例: AさんからBさんに10万円送金する処理(トランザクション1)と、BさんからCさんに5万円送金する処理(トランザクション2)を同時実行する場合
  1. トランザクション1がAさんの口座をロックし10万円減額し20万円にする
    <br />![lock_01](./image/lock_01.svg)
  2. トランザクション2がBさんの口座をロックし5万円減額し15万円にする
    <br />![lock_02](./image/lock_02.svg)
  3. トランザクション1がBさんの口座を10万増額しようとするが、トランザクション2が口座をロック中のため待機
    <br />![lock_03](./image/lock_03.svg)
  4. トランザクション2がCさんの口座をロックし5万円増額し10万円にする
    <br />![lock_04](./image/lock_04.svg)
  5. トランザクション2が処理結果を保存し、BさんとCさんの口座ロックを解除する
    <br />![lock_05](./image/lock_05.svg)
  6. トランザクション1がBさんの口座をロックし10万増額し25万円にする
    <br />![lock_06](./image/lock_06.svg)
  7. トランザクション1が処理結果を保存し、AさんとBさんの口座ロックを解除する
    <br />![lock_07](./image/lock_07.svg)

上記例の場合、Bさんの口座は、5万円の減額処理と10万円の増額処理が競合したが、トランザクション2がBさんの口座を2の時点でロックするため、トランザクション2 → トランザクション1の順番で処理が実施される。この結果は、トランザクション1を先に全て実行し、その後トランザクション2を実行した場合と同じになるので、独立性･分離性が担保できている。

### デッドロック(deadlock)とは
デッドロックとは、お互いのロック解除を待つことでお互いの処理が永久に進まなくなる状態のこと。例えば、トランザクション1がトランザクション2のロック解除を待ち、トランザクション2がトランザクション1のロック解除を待ってしまう様な状態になると、トランザクション1もトランザクション2も処理が完了せずロック解除に至らないため、ロックが永久に解除されず処理が永久に終了しなくなってしまう。

* 例: AさんがBさんに10万円送金する処理(トランザクション1)と、BさんがAさんに5万円送金する処理(トランザクション2)を同時実行する場合
  1. トランザクション1がAさんの口座をロックし10万円減額し20万円にする
    <br />![dead_lock_01](./image/dead_lock_01.svg)
  2. トランザクション2がBさんの口座をロックし5万円減額し15万円にする
    <br />![dead_lock_02](./image/dead_lock_02.svg)
  3. トランザクション1がBさんの口座を10万増額しようとするが、トランザクション2が口座をロック中のため待機
    <br />![dead_lock_03](./image/dead_lock_03.svg)
  4. トランザクション2がAさんの口座を5万増額しようとするが、トランザクション1が口座をロック中のため待機
    <br />![dead_lock_04](./image/dead_lock_04.svg)

上記例は、トランザクション1はBさんの口座のロック解除を待つ必要があるが、それにはトランザクション2が終了する必要があり、トランザクション2はAさんの口座のロック解除を待つしか無いが、それにはトランザクション1が終了する必要があり、処理が永久に進まなくなる。そのため、データベースには、デッドロック状態を検知し、デッドロックとなったトランザクションの片方を強制中断させることで、デッドロックを解消する機能が備えられている。

デッドロックによって強制中断したトランザクションは、データベースによって再度トランザクションが実行されることはないので、アプリケーション側でデッドロックによる中断を検知したらトランザクションを再実行する必要がある。デッドロックは、複数トランザクションを同時実行するのであれば必ず発生するため、アプリケーション作成時にデッドロックが起きる想定で設計しておく必要がある。

### 分離レベル(isolation level)とは
トランザクション間の分離･独立性をどのぐらい緩めるか4段階で定義したもの。

|分離レベルの名称|分離レベルの詳細|ダーティーリード|ノンリピータブルリード|ファンタムリード|
|:-|:-|:-:|:-:|:-:|
|READ UNCOMMITTED|他のトランザクションの確定してない(コミットされていない)データを読み取れる|起きる|起きる|起きる|
|READ COMMITTED  |他のトランザクションが確定した(コミットした)データを読み取れる|-|起きる|起きる|
|REPEATABLE READ |トランザクション内では同じデータは何度読み取っても同じデータになる|-|-|起きる|
|SERIALIZABLE    |複数トランザクションを同時実行しても1つずつ逐次実行した時と同じ結果になる|-|-|-|

SERIALIZABLE以外の分離レベルでは、分離性･独立性を緩めることになるため、分離性･独立性に違反する現象が発生することを理解する必要がある(データベースの実装によって上記表とは異なるケースもある。例えば、MySQLはREPEATABLE READでもファンタムリードは発生しない実装になっている)

* ダーティーリード(dirty read)
   
   トランザクション1がデータAを更新した後、トランザクション2がデータAを読み取った状態で、トランザクション1が実行されなければ(ロールバックされる)、トランザクション2が読み取ったデータは実在しないデータを読み取ることになってしまう現象。
   
   取得データを元に何らかの処理を行う場合は、実在しないデータを元に処理を実施してしまう状況になるため、意図しない結果を出力してしまう恐れがある。

  
* ノンリピーダブルリード(non-repeatable read)

  トランザクション1がデータAを読み取ったあと、トランザクション2がデータAを変更して確定(コミット)し、トランザクション1が再度データAを読み取った場合、1回目の読み取りと2回目の読み取り結果が異なってしまう現象。
  
  読み取るタイミングによってデータ異なってしまうため、アプリケーションの実装によっては、意図しない結果を出力してしまう恐れがある。

* ファンタムリード(phantom read)
  
  トランザクション1が条件Aで検索してデータを読み取ると、データα・βが取得できるとする。トランザクション2が、条件Aに該当するデータγを追加し確定(コミット)した場合、トランザクション1が再び条件Aで検索してデータを読み取ると、データα・β以外に、1回目の読み取りでは存在してなかった、トランザクション2が追加したデータγも読み取ってしまうような現象。
  
  登録データ数などを根拠に処理を行うような実装などで意図しない結果を出力してしまう恐れがある。

高い分離レベルを実現するためには、ロックをかけてデータを保護するため必要があるため、同時実行速度が犠牲になる。一方で低い分離レベルは、ロックをかける必要がなくなるため、同時実行時の速度が向上する。データベース製品ごとにどのように分離レベルが実装されているかを理解しつつ、上記のような現象が発生するリスクを許容できるかアプリケーション設計者がよく検討した上で、データの安全性よりも実行速度を重視したい場合、分離レベルを低くすることも1つの選択肢になる。

## 永続性(Durability)とは
永続性とは、トランザクションが確定(コミット)したら、それ以降にデータが失われないように保証する特性。

トランザクションにおいて、処理が全て完了し確定(コミット)する場合、確定(コミット)が成功したら、それ以降は確定されたデータは永久に消えない状態になっている必要がある。

しかし、一般的に永続ディスク(ハードディスク・SSDなど)への書き込みは低速で時間のかかる処理とされている。そのため、LinuxなどのOSは、書き込み処理を高速化するため、永続ディスクよりも高速に書き込みが可能なキャッシュ領域にデータを書き込むことでアプリケーションに対しては書き込み処理完了とするような実装になっている。そして、キャッシュ領域から永続ディスクへは、一定間隔でまとめて書き込みを行う(フラッシュ処理)実装になっているが、キャッシュ領域で使われるのは、揮発性のメモリー(RAM)であるため、電源断が生じるとデータが消失してしまう。

仮に、確定(コミット)が成功したかの根拠となるデータの書き込み成功が、OSが用意しているキャッシュ(揮発性メモリー)領域への書き込みだった場合、電源断などの障害によってデータが消失する可能性があり、永続性の原則に違反する状況が発生してしまう。データベースでは、こうした状況を防ぐため確定(コミット)する際に、OSのキャッシュ領域ではなく、永続ディスクへの書き込みになるように制御するような機能が備わっている。

## まとめ

### データを扱うことの難しさを知る
データを確実に保存し、必要な場面で確実に利用するためには、様々なことに注意を払う必要があります。アプリケーション開発者は、これまで見てきたようなデータに関して起きうる全ての状況を想定しながら、アプリケーションの設計・実装していく必要がありますが、データに関して起きうる全ての事象に対応したアプリケーションを1から実装するのは非現実的です(いわゆる[車輪の再発明](https://ja.wikipedia.org/wiki/%E8%BB%8A%E8%BC%AA%E3%81%AE%E5%86%8D%E7%99%BA%E6%98%8E))。

### データベースを学び利用する
データベースを学ぶことは、データを扱う上で起きうる事象を学ぶことができ、アプリケーション開発の中でデータを扱う際に何に配慮すれば良いか見えて来ると思います。また、データベースを利用することで、データを扱う上で起きうる事象への配慮をデータベースに任せることができれば、アプリケーション開発者はアプリケーションに求められる本質的な機能の実装に集中でき、より良いアプリケーション開発を行うことができるようになると思います。

### 必要な機能を備えたデータベース製品を選定する
昨今、様々なデータベース製品がリリースされています。それらの製品ごとにメリット･デメリット、得意･不得意な領域が存在します。データの安全性を重視するのか、それとも速度を重視するかなど、アプリケーションごとにデータに対する要求の方向性は変わってきますが、求める方向性に合致するデータベース製品を選定できるようになるためにも、データベースの知識を持つことが重要だと思います。
