# learnning-of-SQL
## 指定文字数分左埋め/最大値＋1
→LPAD（MAX関数（カラム名）＋１, 桁数, 埋める数)

INSERT INTO tbl_member(member_id,member_name,sex,birthday,enrollment_date,quit_flg,update_date)  
SELECT lpad(max(member_id) + 1,4,'0'),'木曽義仲', '1', '1998-08-16', '2020-08-12', 0, '2020-08-12'
FROM tbl_member;

# CROSS JOIN
結合条件が存在しない場合にテーブル同士を結合することが出来る

# 結合禁止の制約(現場では意外と多い制約らしい)
コンシューマ向けのサービスのようにデータ件数が大きい場合はRDBに処理を任せないほうがいい
→サーバーの拡張がしづらいから
→処理件数が膨大になるほど動作が遅くなる
結果的にこのような制約が生まれる。
制約ない方が早いことも多いので、ケースによっては廃止するよう進言すべき

# OUTER JOIN
→LEFT, RIGHTがある
　OUTER は省略可
 テーブル同士の結合に用いるが、結合先がNULL値であっても全件を結合することが出来る。
 INNER　JOINは結合先のカラムで共通する値しか結合しない。
 
 # USING
USING句は、ON句の略記法。
結合する条件が同じ名前のフィールド名であれば、USINGで結合条件を指定することができる。
USINGの引数に指定したフィールド名で、テーブル間のリレーションを作成します。

次の構文は意味的には同じ。

### USING を使った構文
SELECT * FROM A LEFT JOIN B USING (Ｆ1)

### ON 条件式を使った構文
SELECT * FROM A LEFT JOIN B ON A.F1=B.F1
フィールド名をカンマ( , )で区切って条件式を複数指定することも可能。

### USING を使った構文
SELECT * FROM A LEFT JOIN B USING (F1, F2, F3)

### ON 条件式を使った構文
SELECT * FROM A LEFT JOIN B ON A.F1=B.F1 AND A.F2=B.F2 AND A.F3=B.F3,...

# HAVING句の実行順序
画像参照


# GROUP BYとPERTITION　BYの違い
いずれもテーブルを指定されたキーで分割する

ex)
SELECT member, team, age ,
       RANK() OVER(PARTITION BY team ORDER BY age DESC) rn,
       DENSE_RANK() OVER(PARTITION BY team ORDER BY age DESC) dense_rn,
       ROW_NUMBER() OVER(PARTITION BY team ORDER BY age DESC) row_num
  FROM Members
ORDER BY team, rn;

GROUP BY:クエリ全体を変更する

PARTITION BY:ウィンドウ関数に対してのみ動作する

# 分析関数（ウインドウ関数）と集合関数
## 分析関数とは
集合関数と同じ集計動作をそれぞれの行に範囲を制限して実行するもの
## 集合関数とは
GROUP BY とともに使用する関数（SUM,AVG,MAXなど）

＜実行例＞
集合関数
SELECT COUNT(*) FROM test_orders;

        COUNT(*)
----------------
               6

分析関数
SELECT order_id, item, COUNT(*) OVER () FROM test_orders;

  ORDER_ID ITEM       COUNT(*)OVER()
---------- ---------- --------------
      1001 Apple                   6
      1005 Banana                  6
      1010 Banana                  6
      1021 Apple                   6
      1025 Apple                   6
      1026 Apple                   6


## OVER句
以下の三つの方法で集計対象の範囲指定が行える。
記述しなかった場合は全行が集計対象範囲となる。
* Partition By指定
* Order By指定
* Window (Frame)指定

## DISTINCTかGROUP BYか
目的に対する挙動はどちらも同じなので、それほどこだわる必要はない。
強いて言うならば、
* 単に重複を除いた結果をそのまま出すだけの場合はDISTINCT句
* まとめた結果に対して何らかの処理を加える必要がある場合はGROUP BY句
という使い分けができる

## 既存の日付データから、年月のみを抽出する
date_formatを使えばいい。sqlserverにはないので注意。sqlserverではformat。

ex)
date_format(tp.purchase_date, '%Y年%m月')
→2018年5月
みたいに表示できる

## count関数の中で条件を指定する
countは非null値を持つ値をすべて数える。
ので、下記のように or nullという条件を加えないとテーブル上には値が存在しているのですべて非null値としてカウントされてしまう。
or nullの条件を加えることで、男性の場合のみTRUEとなり要件通りカウント関数を用いることが出来る。

ex)
男性の数だけ数えたい場合
sex:1(男性)、2(女性)
count(tm.sex=1 OR NULL)

参考： 
* MYSQL リファレンス　https://dev.mysql.com/doc/refman/5.6/ja/comparison-operators.html#function_coalesce
* はじめてのSQL　https://www.udemy.com/course/standard-sql-for-beginners/learn/lecture/9507796#notes
* JOINに関する記事https://qiita.com/ngron/items/db4947fb0551f21321c0
* SQL全般
https://rfs.jp/sb/sql
* HAVING句とWHERE句
https://www.sejuku.net/blog/73003
* GROUP BYとPATITION BY http://mickindex.sakura.ne.jp/database/db_gb_pb.html
* 分析関数についてhttps://qiita.com/tlokweng/items/fc13dc30cc1aa28231c5
* count関数について
https://qiita.com/zb185423/items/f20b21ca041989410b5f
