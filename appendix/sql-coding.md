# DevGuideline(APL) 別紙1.SQLコーディング規約ドラフト版

# 目次

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->
- [DevGuideline(APL) ドラフト版](#DevGuideline(APL)-font-colorredドラフト版font)
- [目次](#目次)
- [1. はじめに](#1-はじめに)
  - [1.1 本書の目的](#11-本書の目的)
  - [1.2 商標表示](#12-本書の位置付け)
- [2. SQLコーディング規約適用範囲](#2-はじめに)
  - [2.1 SQLコーディング規約対象範囲](#21-SQLコーディング規約対象範囲)
  - [2.2 SQL使用規定範囲](#22-SQL使用規定範囲)
  - [2.3 対象SQL文](#23-対象SQL文)
- [3. コーディングスタイル](#3-コーディングスタイル)
  - [3.1 SQLコーディングスタイル](#31-SQLコーディングスタイル)
- [4. 共通コーディングルール](#4-共通コーディングルール)
  - [4.1 共通コーディングルール](#41-共通コーディングルール)
  - [4.2 禁止事項](#42-禁止事項)
- [5. 問合せ処理コーディングルール](#5-問合せ処理コーディングルール)
  - [5.1 抽出項目記述（SELECT句内）のコーディングルール](#51-抽出項目記述（SELECT句内）のコーディングルール)
  - [5.2 条件句、結合処理でのコーディングルール](#52-条件句、結合処理でのコーディングルール)
  - [5.3 索引を利用する際のコーディングルール](#53-索引を利用する際のコーディングルール)
  - [5.4 ソートに関するコーディングルール](#54-ソートに関するコーディングルール)
- [6. 更新処理コーディングルール](#6-更新処理コーディングルール)
  - [6.1 更新処理共通ルール](#61-更新処理共通ルール)
  - [6.2 データ新規登録処理（INSERT）のコーディングルール](#62-データ新規登録処理（INSERT）のコーディングルール)
  - [6.3 データ更新処理（UPDATE）のコーディングルール](#63-データ更新処理（UPDATE）のコーディングルール)
  - [6.4 データ削除処理(DELETE)のコーディングルール](#64-データ削除処理(DELETE)のコーディングルール)
  <!-- /code_chunk_output -->

# 1. はじめに

## 1.1 本書の目的
本書の目的
SQLでコーディングする際のルール、推奨、及び迷ったときの指針を提供するものである。
主に、java言語内で使用するSQLを想定している。

## 1.2 商標表示

Javaは、米国及びその他の国における米国Sun Microsystems, Inc.の商標または登録商標です。
PostgreSQLは、PostgreSQLの米国およびその他の国における商標または登録商標です。
その他，本書に掲載している社名，製品名及びシステム名は，各社の登録商標または商標です。

# 2. SQLコーディング規約適用範囲
本章では、SQLコーディング規約の適用範囲に関して記述する。
本書に従って作成したSQLに対して更にパフォーマンスを求める場合、DB管理グループに相談の上、SQLに対してパフォーマンスチューニングを実施すること。その結果、本書の記述から外れたSQLとなった場合、例外として本書に準拠する必要はない。
RDBMSはPostgreSQL（バージョンxx）を前提とし、PostgreSQL上で動作するSQLのコーディング規約に関して記述する。

# 2.1 SQLコーディング規約対象範囲
本節では、PostgreSQLデータベースコマンドの分類の中で、本規約が対象とする範囲に関して明記する。
本規約対象のコマンドを下表の通りまとめる。
|コマンド | 説明 | 規約の対象
| - | - | :-: |
DDL文（データ定義言語）|これらのコマンドは表、宣言型制約、ストアドプロシジャー、トリガーなどの</br>データベースオブジェクト全体の作成・変更・削除などを行う際に使用する。|	×
DML文（データ操作言語）|これらのコマンドはSQL問合せ処理、</br>SQL更新処理などのデータベース表に対する</br>レコード検索、挿入、更新、削除等の操作を行う。|○
トランザクション制御文|トランザクションの開始、終了、チェックポイント、ロールバック、コミット等行う。|×
EXPORT/IMPORTなど|ユーティリティでの使用コマンド|×
サードパーティー製品によるSQLまたはコマンド|-|×
psql|PostgreSQLのターミナル型フロントエンドです。 対話的に問い合わせを入力し、</br>それをPostgreSQLに対して発行して、結果を確認することができます。 </br>また、ファイルから入力を読み込むことも可能です。 <br>さらに、スクリプトの記述を簡便化したり、様々なタスクを自動化したりする、</br>いくつものメタコマンドとシェルに似た各種の機能を備えています。|×

## 2.2 SQL使用規定範囲
前述のSQLコーディング規約対象範囲である「DML文」の使用を以下のように規定している。</br>
使用規定箇所 | 使用箇所 | 規約の記述箇所</br>
| - | - | - |
java言語内 |永続層（Factory）でのJDBC APIでのDBアクセス処理として使用 | SQL文の組立に関しては本書</br>SQLを使用した実装方法（SQL文定義方法、使用方法）は「開発ガイドライン（APL）」 |トランザクション管理に関しても、「開発ガイドライン（APL）」参照</br>
psqlを利用したSQLコマンドスクリプト |SQLコマンドで読み込むSQLスクリプトファイル内で使用。 |SQL文の組立に関しては本書</br>
PL/pgSQL言語 |本システムではストアドプロシジャーの作成は原則禁止とする。（※） |対象外</br>

※ただし、性能面や業務要件の実現のためにストアドプロシジャーの記述が必須となる場合にはDB管理グループに相談のこと。</br>
特に本規約は、主にjava言語内で使用されることを想定としたSQL規約とする。

### 2.3 対象SQL文
前述のSQLコーディング規約対象範囲である「DML文」の対象としているSQLは、以下の通り。</br>
a．	SELECT 文</br>
b．	INSERT 文</br>
c．	UPDATE 文</br>
d．	DELETE 文</br>

### 2. コーディングスタイル
本章では規約対象のSQLを開発する際のコーディングスタイルに関して記述する。
本コーディングスタイルは、java言語内でコーディングするSQL（それは文字列定数としてハードコーディング、または定数定義される）を想定にスタイルの分かり安さ、開発時の適用のしやすさを重視している。

## 3.1.	SQLコーディングスタイル
### 3.1.1	ソースの桁数は、他言語も含め、1行80文字程度とし、最大120文字を制限とすること
一行の桁数を超える場合は、改行する。

### 3.1.2	SQL文のオブジェクト名は小文字、予約語は大文字で記述すること
SQL文のオブジェクト名は小文字、予約語は基本大文字で記述する。</br>
例○）</br>
```sql
SELECT                                     --SQL構文としての予約語
    user_id, user_name                  --列名/
FROM developer_user                      --オブジェクト名
WHERE user_id = 'taro';
```

### 3.1.3	各項目間は１文字以上の空白を入れること

例○）下記の例の△は空白を意味する。</br>
```sql
SELECT△user_id,△user_name
FROM．．．
```

項目が関数や演算項目の場合も構成項目間に空白を入れることを原則とする。</br>
空白を入れることで分かりづらくなる場合は、括弧を使用し意味的にまとめる。

例○）下記の例の△は空白を意味する。

```sql
SELECT△user_id,△user_name,△(sal△*△1.1△+△100) user_sal
FROM△gkmsg_00
WHERE△user_id△=△'taro'； 
```

### 3.1.4	改行するSQL構文の予約語
以下のSQL構文のキーワードでは必ず改行すること。</br>
※ただし、Java言語内でコーディングするSQLの文字列リテラルの中に改行を含めるという意味ではない。

 - FROM </br>
SELECT時のFROMのみ。DELETE時のFROMでは改行はしないこと。
FROM句内の改行時はインデントを行うこと。</br>
例○）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   emp e
    LEFT OUTER JOIN dept d  on   e.eno = d.dno
```

例○）</br>
```sql
DELETE FROM emp
WHERE ename = ‘sample’
```

 - WHERE、GROUP BY、HAVING</br>
WHERE句、GROUP BY句、HAVING句では改行すること。</br>
WHERE句、GROUP BY句、HAVING句内の改行時はインデントを行うこと。</br>
例○）</br>
```sql
SELECT ename, eno
FROM   emp
WHERE ename = ‘cond1’
    OR ename = ‘cond2’
```

例○）</br>
```sql
SELECT bushocd, AVG(salary) total_salary
FROM   emp
GROUP BY bushocd
HAVING bushocd > 1500
```

- AND（BETWEEN構文のANDは除く）、OR</br>
WHERE句内のAND、及びORでは必ず改行すること。ただしBETWEEN 値１ AND 値２構文のANDは例外とする。</br>
例○）</br>
```sql
WHERE  upd_dt = TO_DATE('20051001', 'YYYYMMDD')
    AND    sal >= 3000
    AND    sal < 4000/1.1;
```

例○）</br>
```sql
WHERE  upd_dt = TO_DATE('20051001', 'YYYYMMDD')
    OR    sal >= 3000
```

- ORDER BY</br>
ORDER BY句では必ず改行すること。</br>
例○）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   emp e
ORDER BY e.ename
```


- LEFT OUTER JOIN </br>
LEFT OUTER JOINでは必ず改行すること。</br>
例○）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   emp e
    LEFT OUTER JOIN dept d  ON   e.eno = d.dno
```

- FULL OUTER JOIN </br>
FULL OUTER JOINでは必ず改行すること。（ただしFULL OUTER JOINは非推奨）</br>

例○）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   emp e
    FULL OUTER JOIN dept d  ON   e.eno = d.dno
```

- 副問合せのSELECT</br>
副問合せの開始SELECTでは必ず改行し、以降の改行時にはインデントを合わせること。
例○）
SELECT e.empno, e.deptno 
FROM emp e
WHERE EXISTS( 
    SELECT d.empno
    FROM dept d 	
    WHERE e.empno = d.empno )

- VALUES、SET
INSERT時のVALUES、UPDATE時のSETでは改行すること。</br>
VALUES、SET内の改行時はインデントを行うこと。</br>
例○）</br>
```sql
INSERT INTO departments
    ( department_id, department_name, manager_id, dn )
VALUES  ( 280, 'Recreation', 
    121,  );
```

例○）</br>
```sql
UPDATE departments 
SET department_name = 'Recreation',  manager_id = ’125’
    dn = ’SAMPLE’
WHERE department_id = 280;
```

- 集合演算子（UNION、UNION ALL、INTERSECT、EXCEPT）
集合演算子は、使用しないことを推奨。使用する場合必ず改行すること。

- CASE式（CASE、WHEN、ELSE、END）
CASE式を使用する際のCASE、WHEN、ELSE、ENDでは改行すること。</br>
また、CASEとEND間（WHEN、ELSE）は継続改行とし、インデントをすること。</br>
例○）</br>
```sql
SELECT empno, sal,
    CASE
        WHEN sal < 1000 THEN ‘1000未満’
        WHEN sal < 2000 THEN ‘1000以上2000未満’
        ELSE ‘2000以上’
    END AS rank
FROM emp
```

### 3.1.5 継続改行はインデントすること
以下の場合に継続改行をする。
※ただし、Java言語内でコーディングするSQLの文字列リテラルの中にインデントを含めるという意味ではない。</br>
•	SELECT句やWHERE句の文字列が長くなりすぎる場合</br>
•	意味的なまとまりで改行することで分かりやすくなる場合

継続改行をする際は、インデントをすること。</br>
例○）</br>
```sql
SELECT user_id, user_name, busho_cd, sal, 
△△△△col3, col4, col5
FROM  emp
WHERE   busho_cd = ‘sales’
    AND     sal >= 3000;
```
※△は半角空白</br>

### 3.1.6	SQL文はインデントを合わせること
SQL文は、インデント（半角空白）をそろえる。
※ただし、Java言語内でコーディングするSQLの文字列リテラルの中にインデントを含めるという意味ではない。</br>
挿入するインデントの空白数を４とする。</br>
例○）</br>
```sql
SELECT ename, sal, sal * 1.1  new_sal,sal + COALESCE(comm, 0)  old_sal,
△△△△	TO_CHAR(sal)         char_sal
FROM  emp;
```
###	インデントをする箇所</br>
- 	継続改行時
- 	FROM句内での改行
- 	WHERE、GROUP BY、HAVING句内での改行
- 	VALUES、SET句内での改行
- 	括弧”（”内での改行

# 4.	共通コーディングルール
本節では、問合せ処理、更新処理に共通したコーディングルールに関して記述する。
## 4.1.	共通コーディングルール
本節では、SQLコーディングを実施する際の推奨する事項に関して記述する。
### 4.1.1	SQL発行回数の低減を心がけること
SQLの発行回数の低減は、RDBMSの処理負荷軽減に繋がるため、極力SQL発行回数が少なくなるようなSQLコーディングを心がけること。

### 4.1.2	業務処理プログラム内ではDDL文を使用しないこと
業務プログラム内では、CREATE TABLE等のDDL文を原則、使用しない。</br>
以下のDDLを業務処理プログラム内での使用する場合、DB管理グループに相談の上使用すること。</br>
※　使用しないと実現不能な場合やパフォーマンスの改善が必要な場合のみ相談可</br>

###	TRANCATEによるテーブルレコードの破棄
ロールバックの必要が無く表内の全レコードを削除する場合に、パフォーマンスの改善が期待できる。ただし以下の考慮をする必要がある。
- 未確定トランザクションへの考慮
- ロック待ちの考慮

### 中間テーブルを作成する用途のCREATE TABLE AS SELECT
一時的な中間テーブルを作成する用途でのCREATE TABLE AS SELECT構文を使用する。ただし以下の考慮をする必要がある。

- DROPの考慮
- DROP TABLE時、未確定トランザクションへの考慮
- DROP TABLE時、ロック待ちの考慮

### パーティション操作
パーティションの追加操作などは、DBA管理の下に、適切なHWリソースの設定値を基に作成する必要があるため、業務アプリケーションでは原則実施しない。

### 4.1.3	CASE式のELSE句は必ず明示的に指定すること
CASE式では条件にマッチしない場合、ELSE句を返却するが、ELSE句を明示的に指定しない場合NULLを返却する。</br>
保守性とバグの誘発を防止するため、ELSE句は明示的に指定すること。</br>
例○）</br>
```sql
UPDATE emp SET</br>
salary = </br>
    CASE  deptno</br>
        WHEN 10 THEN salary * 1.2</br>
        WHEN 20 THEN salary * 0.9</br>
        ELSE salary</br>
    END</br>
```

例×）</br>
UPDATE emp SET</br>
```sql
salary = 
    CASE  deptno
        WHEN 10 THEN salary * 1.2
        WHEN 20 THEN salary * 0.9
    END
```
※このCASE式はdeptnoが10,20以外をNULLに更新する</br>

### 4.2.	禁止事項
本節では、SQLコーディングを実施する際に特に禁止する事項に関して記述する。

### 4.2.1	暗黙の型変換の禁止
PostgreSQLでは、SQLで明示的に指定しなくても数値型と文字型の変換を行う。索引列では関数が使用されたのと同じことになり、索引が使用されない。また、暗黙の型変換は一度代入に失敗した後に行なわれるために、パフォーマンスの低下に繋がるので型に合った指定をすること</br>
日時型のレコードの選択、更新時にはTO_DATE、TO_CHARは必ず利用する。</br>
例×）dept_idが char型で定義されている場合</br>
```sql
SELECT dept_id, dept_name </
FROM dept 
WHERE dept_id = 123;
```

例○）dept_idが char型で定義されている場合</br>
```sql
SELECT dept_id, dept_name 
FROM dept 
WHERE dept_id = ‘123’;
```

例○）Date型の列（update_timestamp）から値を文字列として取得する場合</br>
```sql
SELECT TO_CHAR(update_timestamp,’YYYY/MM/DD HH24:MI:SS’) UPDATE
FROM sampletable
・・・
```

例○）Date型の列（update_timestamp）に日付文字列を更新する場合</br>
```sql
UPDATE sampletable 
SET update_timestamp = TO_DATE(‘2006/09/31 23:44:55’,
    ’YYYY/MM/DD HH24:MI:SS’)
WHERE・・・
```
※	日付書式に関しては、業務において統一する必要がある。</br>

3.2.2	業務日付として現在の日時を返す関数（CURRENT_DATE、CURRENT_TIMESTAMP、など）を使用することは禁止</br>

「タイムゾーンの問題」や「業務日付をシステム日付の相違」という問題に対する混乱を避けるため。</br>
タイムゾーンはシステムのデフォルト指定で変わってくる。　</br>
また、業務日付等は日替処理をいつ行うかによって変わってくる。</br>
基本的に日付処理クラスやタイムゾーンの指定等は共通ライブラリ(アプリ共通で検討中)を使用する方針とする。


# 5.	問合せ処理コーディングルール
本章では、問合せ（及び副問合せ）処理のコーディングルールに関して記述する。
## 5.1.	抽出項目記述（SELECT句内）のコーディングルール

SELECT文では以下のように抽出項目を指定することができる。（以下抽出項目例の一例。）</br>
(１)	表の列名、以下の例では ename と sal</br>
(２)	演算項目：列の値を使用した数値計算。以下の例では sal *1.1</br>
(３)別名（エイリアス）：一つの表現で一つ以上の列名。 以下の例では、 sal+COALESCE(comm, 0) </br>
(４)	関数項目：関数を指定。 以下の例では文字列関数 TO_CHAR(sal)</br>
抽出項目例）</br>
```sql
SELECT ename,  sal,
    sal * 1.1                    new_sal,
    sal + COALESCE(comm, 0)         total_sal,
    TO_CHAR(sal)                char_sal
FROM  emp;
```
本節ではSELECT句内のコーディングルールに関して記述する。</br>

### 5.1.1	列名称をアスタリスク（*）で省略しないこと
以下の理由から必要な列分記述すること。</br>
•	保守性（ソースの可読性、仕様変更の影響を小さくするため）
•	パフォーマンス（全ての列を取り出す*を指定すると*を全列に読替える処理時間が増加する）
例×）</br>
```sql
SELECT *  
FROM  emp
```

### 5.1.2	COUNT関数を使用した全レコード数の問合せは*を指定すること
特定の列を指定しNULLを許容する場合、NULL値はカウントされない。</br>
※　特定の列を指定する場合とパフォーマンスの面でも問題ない</br>
例○）</br>
```sql
SELECT COUNT(*)  
FROM  emp
```

### 5.1.3	関数項目、演算項目には別名（エイリアス）を記述すること
ソースコードの可読性向上のために、関数項目、演算項目には別名を指定すること。</br>
例○）</br>
```sql
SELECT ename,  sal,
    sal * 1.1                    new_sal,
    sal + COALESCE (comm, 0)         total_sal,
    TO_CHAR(sal)                char_sal
FROM  emp;
```

例×）</br>
```sql
SELECT ename,  sal,
        sal * 1.1,
        sal + COALESCE(comm, 0),
        TO_CHAR(sal)
FROM  emp;
```

### 5.1.4	複数テーブルから選択する場合、列名の頭に必ず表別名をつけること
複数テーブルから選択する場合、各テーブルに別名を指定し、明示的に別名を指定し各項目を指定すること。
別名を必ず指定することでSQL解析時間を軽減できる。
例○	）</br>
```sql
FROM emp a, dept b
WHERE a.deptno = b.deptno;
```

例○）</br>
```sql
SELECT e.ename, d.dname 
FROM emp e, dept d
WHERE  e.deptno = d.deptno
     AND      e.sal > 1000
```

例×）</br>
```sql
SELECT ename, dname
FROM emp, dept
WHERE emp.deptno = dept.deptno AND sal > 1000
```

## 5.2.	条件句、結合処理でのコーディングルール</br>
問合せ処理をコーディングする際、論理演算子と結合した AND や OR などのブール表現を使用した複合表現を含む WHERE 句により検索結果を選定することができる。</br>
本節では、問合せ処理の条件句、複数テーブルの結合処理に関するコーディングルールに関して記述する。

## 5.2.1	WHERE句内の条件記述順序は結合条件、抽出条件順とすること
ソースコードの可読性向上のために、WHERE句の条件記述順序は、以下の順に記述する。</br>
①結合条件を先にまとめて記述する。</br>
②抽出条件を後に記述する。</br>

## 5.2.2	WHERE句の論理演算文が複雑になる場合は、（）で演算順序を明確にすること
ソースコードの可読性向上、保守性向上のために、WHERE句の論理演算文が複雑になる場合には、括弧()を使用し、演算順序を明確にすること。

例○	）</br>
```sql
WHERE 
    ( COALESCE(TO_CHAR(b.emp_id), '***') AND (b.sal > 3000) )
    OR 
    ( COALESCE(TO_CHAR(b.emp_id), '---') AND (b.sal < 3000) )
```

例×）</br>
```sql
WHERE </br>
    COALESCE(TO_CHAR(b.emp_id), '***') AND (b.sal > 3000)
    OR
    COALESCE(TO_CHAR(b.emp_id), '---') AND (b.sal < 3000)
```

## 5.2.3	HAVING句はWHERE句に代替可能か検討すること
HAVING句でGROUP BYによるレコードの集計結果を絞り込む場合、WHERE句による検索条件の絞り込みを検討する。</br>
WHERE句によって検索条件を絞り込むことで、集計対象となるレコードを少なくすることができ、集計処理の負荷を軽減することができる。</br>
例○	）</br>
```sql
SELECT busho_id, busho_name, AVG(salary) total_salary
FROM emp
WHERE busho_id > 100
GROUP BY busho_id
```

例×）</br>
```sql
SELECT busho_id, busho_name, AVG(salary) total_salary
FROM emp
GROUP BY busho_id
HAVING busho_id > 100
```

## 5.2.4	レコードの存在チェックではLIMIT 1を使用すること
レコード存在チェックは、LIMI 1を使用すると一レコード見つけた時点でSQL文を終了するためパフォーマンスがよい。</br>
例○）</br>
```sql
SELECT 1 dummy 
FROM emp
WHERE emp_no = 50
LIMIT 1;
```

例×）</br>
```sql
SELECT COUNT(*) 
FROM emp
WHERE emp_no = 50;
```

## 5.2.5	結合はWHERE句に結合条件を指定（=）すること
結合処理における[INNER] JOIN構文はWHERE句に結合条件を指定することで書き換えることができるため、可読性の観点から、結合記述はJOIN構文ではなくWHERE句に項目を指定すること。</br>
また、デカルト積（クロス結合、直積）のCROSS JOIN構文、自然結合におけるNATURAL JOIN構文、USING句を使用した結合記述は使用せず、WHERE句に結合条件を指定すること。</br>
例×）</br>
```sql
SELECT  a.deptno, a.deptname
FROM dept a 
INNER JOIN busho b ON a.deptno=b.deptno;
```

例○）</br>
```sql
SELECT  a.deptno, a.deptname
FROM deptno a, busho b
WHERE a.deptno = b.deptno;
```

## 5.2.6	結合時のDISTINCT句はEXISTS句で代替すること
一対多関係の表を結合する問合せ時に重複レコードの削除として使用するDISTINCT句は以下の理由からEXISTS句で代替することを検討すること。</br>
DISTINCTは、条件に一致するレコードを取り出し、暗黙のソート処理後に重複レコードを排除する。EXISTS句は条件に一致するレコード１件でもあれば以後のレコードは取り出されないため、DISTINCTに比べると負荷が小さくなる。</br>

例×）</br>
```sql
SELECT DISTINCT e.empno, e.deptno 
FROM emp e, dept d
WHERE e.empno = d.empno
```

例○）</br>
```sql
SELECT e.empno, e.deptno 
FROM emp e
WHERE EXISTS( 
    SELECT d.empno
    FROM dept d 
    WHERE e.empno = d.empno )
```


## 5.2.7	外部結合は、左外部結合を使用すること
右外部結合は左外部結合に書き換えることができるため、可読性の観点から左外部結合に統一する。</br>
例×）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   dept d
    RIGHT OUTER JOIN emp e ON d.dno = e.eno;
```

例○）</br>
```sql
SELECT e.ename, e.eno, d.dno
FROM   emp e
    LEFT OUTER JOIN dept d ON d.dno = e.eno;
```


## 5.2.8	副問合せは、極力結合操作に置き換えること
副問合せは可読性を損なうため極力結合操作に置き換えること。</br>
ただし、副問合せを使用しない場合にパフォーマンスが劣化する場合はその限りではない。</br>
また、副問合せを使用しないと実現不能な場合もその限りではない

## 5.2.9	副問合せのネストは禁止
副問合せのネストは可読性を損なうため禁止する。結合操作に置き換えること。</br>
ただし、副問合せのネストを使用しない場合にパフォーマンスが劣化する場合はその限りではない。</br>
また、副問合せのネストを使用しないと実現不能な場合もその限りではない

## 5.2.10	INを使用する副問合せは結合操作に代替可能か検討すること
副問合せを使用した場合、副問合せとSQL本文は別のアクセスパスで解析されることが多いため、副問合せを使用しないSQL文はアクセスパス選択の自由度が増し、処理性能が向上することが多くなる。</br>

例×）</br>
```sql
SELECT e.ename, e.eno
FROM   emp e
WHERE e.dno IN ( 
    SELECT d.dno,
    FROM dept d
    WHERE d.dept_name = ‘A’)
```

例○）</br>
```sql
SELECT e.ename, e.eno
FROM   emp e, dept d
WHERE e.dno = d.dno,
    AND d.dept_name = ‘A’
```

### 5.2.11	VIEWは結合しないこと
VIEWを結合するとPostgreSQLの実行計画が複雑となり、不適切な実行計画が採用される可能性が高くなるためVIEWの結合はしない。</br>
同様に、副問合せにVIEWを使用しないこと。</br>
上記のような要件に対応する場合、新たにVIEWを作成することで対応すること。</br>

### 5.2.12	集合演算子：UNION、UNIONALL、INTERSECT及びEXCEPT は極力使用しない
一般に上記集合演算子は性能劣化の要因となることが多いので、上記集合演算子は極力使用しないようにする。
ただし、業務要件上使用する必要がある場合には、以下の制限に注意する。</br>
①	集合演算子の前の SELECT 構文のリストに式が含まれている場合、ORDER BY句でその式を参照するには、式に列の別名を指定する必要がある。</br>
②	FOR UPDATE句またはFOR SHAREは、これらの集合演算子とともに指定できない。</br>
③	これらの演算子の副問合せには、ORDER BY句を指定できない。

### 5.2.13	相関サブクエリは極力使用しないこと
相関サブクエリを使用した場合、SQLが繰り返し実行されることとなるため性能劣化の要因となることが多い。
一概には言えないが、外側の表の対象件数が100件程度を超える場合は相関サブクエリを内部結合の形に書き直すことを推奨する。</br>

例×）</br>
```sql
SELECT e.ename, e.eno<
FROM   emp e
WHERE EXISTS ( 
    SELECT 1
    FROM dept d
    WHERE e.dno = d.dno)
```

例○）</br>
```sql
SELECT e.ename, e.eno
FROM   emp e
WHERE e.dno IN (
    SELECT e2.dno
    FROM emp e2
    INNER JOIN dept d<
    ON e2.dno = d.dno)
```

なお、更新処理時においても同様の考慮が必要である。


## 5.2.14	テーブルの結合は原則8つまでとすること
結合するテーブル数が8を超える場合はDB管理チームへ連絡すること。</br>
【理由】</br>
パラメータのfrom_collapse_limit は副問い合わせ（FROM 句）で、join_collapse_limit は結合処理（JOIN 句）で、それぞれ指定するテーブル数の上限を設定し、それを超えるテーブル数になった場合は併合を行わない。よって、最適な実行計画とならない可能性がある。</br>
結合する最大のテーブル数を両パラメータに設定しておくことで、併合も含めた全ての実行計画のパターンから最適なものを選択して実行させるようにするため。</br>

## 5.2.15	同一列に対する等号OR 条件はIN 記述に置き換える
同一列に対する等号OR 条件は、IN 記述に置き換えること。</br>
【理由】</br>
・ SQL の可読性の向上のため。</br>
・ 性能向上のため。</br>
実行計画を比較すると、IN 句を使用した場合のほうがコストが低くなるため、性能差が出る可能性がある。</br>

例×）</br>
```sql
SELECT ename
FROM emp
WHERE deptno = 10
OR deptno = 20
OR deptno = 30;
																	QUERY PLAN
--------------------------------------------------------------------------------------- ---------------------------
Seq Scan on emp (cost=0.00..15.43 rows=5 width=32)
Filter: ((deptno = 10::numeric) OR (deptno = 20::numeric) OR (deptno = 30::numeric))
```

例〇）</br>
```sql
SELECT ename
FROM emp
WHERE deptno IN (10, 20, 30);
																	QUERY PLAN
------------------------------------------------------------------------------------------------------------------
Seq Scan on emp (cost=0.00..14.26 rows=5 width=32)
Filter: (deptno = ANY ('{10,20,30}'::numeric[]))
```




## 5.2.16	IN 副問合せはANY ARRAY 副問合せへの変換を検討する

IN 副問い合わせは ANY ARRAY 副問い合わせに変換することを検討する。</br>
【理由】</br>
・ インデックス検索となり、パフォーマンスが向上する可能性があるため。</br>

例×）</br>
```sql
SELECT *
FROM pgbench_accounts
WHERE bid IN (SELECT bid FROM pgbench_branches);
																QUERY PLAN
--------------------------------------------------------------------------------------- ---------------------------
Hash Semi Join (cost=3.12..198214.12 rows=5000000 width=97)
Hash Cond: (pgbench_accounts.bid = pgbench_branches.bid)
-> Seq Scan on pgbench_accounts (cost=0.00..129461.00 rows=5000000 width=97)
-> Hash (cost=2.50..2.50 rows=50 width=4)
-> Seq Scan on pgbench_branches (cost=0.00..2.50 rows=50 width=4)
```

例〇）</br>
```sql
SELECT *
FROM pgbench_accounts
WHERE bid = ANY(ARRAY(SELECT bid FROM pgbench_branches));
																QUERY PLAN
------------------------------------------------------------------------------------------------------------------
Index Scan using pgbench_accounts_bid on pgbench_accounts (cost=2.51..37918.61
rows=914636 width=97)
Index Cond: (bid = ANY ($0))
InitPlan 1 (returns $0)
-> Seq Scan on pgbench_branches (cost=0.00..2.50 rows=50 width=4)
```



## 5.3.	索引を利用する際のコーディングルール
索引が有効に動作するのは、一般的に取得するデータの量が表全体の5%～15%以下の場合である。一概には言えないが、それ以上20%以上のデータを取得する場合や、検索する表の容量が小さい場合は、索引を使用しないで全表スキャンを行った方が高速であることが多い。</br>
本節では索引を利用する際のコーディングルールに関して記述する。</br>

### 5.3.1	WHERE句では、なるべく索引を利用している列を指定すること
WHERE句では、索引の利用を心がけ、なるべく、索引を利用している列（主キー等）を指定する。</br>
主キー以外で頻繁に検索条件となるような列には、索引を作成することを考慮する。</br>
ただし、表の大部分のレコードを操作するような場合は除く。（全表スキャンをする方が、効率がよいため）

### 5.3.2	WHERE条件句でNULL値との比較検索は極力使用しないこと
WHERE条件句でのNULL値との比較検索は極力使用しない。</br>
索引（デフォルトのB-Tree索引）にはNULL値が存在しないため、検索条件でNULLもしくはNOT NULLを指定すると、索引を使用せず、全表スキャンを行ってしまう。</br>

例×）</br>
```sql
SELECT ename 
FROM emp 
WHERE deptno IS NULL;
```

```sql
SELECT ename 
FROM emp 
WHERE deptno IS NOT NULL;
```


このようなNULL値（設定されていない状態）を検索する要件がある場合、以下についてDB管理グループと相談し、検討すること。</br>
	NULL値ではなくNULL値の代わりに別のデータ（-1や半角スペース等）を用意することを検討</br>

### 5.3.3	OR検索の条件指定列は全て索引列を指定すること
OR検索は、指定列全てに索引がないと全表スキャンとなるため、なるべく索引を利用している列を条件に指定すること。</br>

例）×col1のみ索引、○col1、col2に索引</br>
```sql
SELECT col1, col2
FROM sample 
WHERE col1 = 30
    OR col2 > 200 
```

### 5.3.4	選択される件数が少ない場合、NOT EQUAL 句（!=、<>）は極力使用しない

WHERE句内の条件式で列に対してNOT EQUAL 条件（!=、<>）を利用している場合、条件を分割してUNION ALLで連結するよう書き換えることを検討する。</br>
ただし、NOT EQUAL 句に指定した項目に作成されている索引を使用する場合でかつ、選択される件数が少ないときに限る。選択される件数が多い場合は、全表走査の方が速い場合もあるので注意が必要。</br>
PostgreSQL/PPASの場合には、NOT EQUALの条件に合致するものが過半数を下回るのであれば、その条件で部分インデックスを作成することも検討する。</br>
また、NOT EQUAL 句に指定した項目に作成されている索引以外の索引による絞り込みが期待できる場合は、書き換える必要はない。</br>
【理由】</br>
・ NOT EQUAL（!=、<>）を使用した場合、B*Tree 索引は使用されないため。</br>

--comm列に索引があり、job列に索引が無い場合</br>
例）×</br>
```sql
SELECT empno, ename
FROM emp
WHERE comm != 500
```
AND job IN ('SALESMAN', 'CLERK');</br>
※comm列に索引があった場合でもその索引は使われない。</br>
ただし、PostgreSQL/PPAS の場合、以下のように部分インデックスを作成した場合は、上記SQL でも索引が使われる。</br>
```sql
CREATE INDEX emp_comm_index ON
emp(comm)
WHERE comm <> 300;
```

例）○</br>
```sql
SELECT empno, ename
FROM emp
WHERE comm > 500
AND job IN ('SALESMAN', 'CLERK')
UNION ALL
SELECT empno, ename
FROM emp
WHERE comm < 500
AND job IN ('SALESMAN', 'CLERK');
```

### 5.3.5	全表スキャンとなる条件記述NOT INを極力使用しないこと
WHERE句の条件に他の索引項目を指定していない場合に、索引項目に対してNOT IN句を使用すると全表スキャンとなるため極力使用しない。

### 5.3.6	全表スキャンとなる条件記述NOT EXSITSを極力使用しないこと
NOT INをNOT EXISTSで代用した方が、パフォーマンスが改善する。NOT IN句は、内部でソートし、結合されるので負荷が大きい。NOT EXISTS句は条件に一致するレコード１件でもあれば以後のレコードは取り出されないため、NOT IN句に比べると負荷が小さくなる。
ただし、WHERE句の条件に他の索引項目を指定していない場合に、索引項目に対してNOT EXISTSも全表スキャンとなるため避けること。

### 5.3.7	全表スキャンとなる条件記述LIKE ‘%PATTERN’を極力使用しないこと
WHERE句の条件に他の索引項目を指定していない場合に、索引項目に対してLIKE演算子を利用した場合は、前方一致以外を指定すると、全表スキャンとなるため、前方一致以外のLIKEは避けること。

    ＜索引使用＞          WHERE address LIKE 'FUKU%'
    ＜全表スキャン＞      WHERE address LIKE '%OKA'

### 5.3.8	索引項目に対して演算記述をして使用しないこと
索引付きの列が、WHERE句で関数の一部として使用されている場合、オプティマイザは索引を使用しない。
オプティマイザは、計算に使われている索引付きの列を見つけると全表スキャンを行う。
このような場合、索引が設定されている列ではなく、比較対象の数値に対して計算式を記述する。

例○	）索引を利用する</br>
```sql
SELECT ename 
FROM emp
WHERE  upd_dt = TO_DATE( ‘20000803’, ‘YYYYMMDD’ );
```

例×）全表スキャン</br>
```sql
SELECT ename 
FROM emp
WHERE  TO_CHAR( upd_dt, ‘YYYYMMDD’ ) = ‘20000803’; 
```

例○）索引を利用する</br>
```sql
SELECT ename 
FROM emp 
WHERE sal  > 950 / 1.1;
```

例×）全表スキャン</br>
```sql
SELECT ename 
FROM emp 
WHERE sal * 1.1 > 950;
```


### 5.3.9	索引項目に対してDISTINCTを極力使用しないこと
索引項目に対してDISTINCT句を使用すると索引を使用しないため、索引項目に対してDISTINCTを極力使用しないこと。</br>
この様な場合、他の索引キーをWHERE句に別途指定するか、結合時であればEXISTS構文を使用可能か検討すること。</br>

### 5.3.10	索引項目に対してWHERE句のないGRUOP BYを極力使用しないこと
GROUP BY句を索引項目に対して使用すると、WHERE句と合わせて使用する場合以外、索引を使用しないため、索引項目に対してGROUP BYを極力使用しないこと。</br>
この様な場合、WHERE句の条件に別途索引キーが含まれるような条件を指定することを検討する。

### 5.3.11	「複合索引」を使用する場合は、列の順番に注意すること
複数の列を組み合わせた「複合索引」を使用する場合、列の順番が重要で、複合索引の最初の列を指定しないとパフォーマンスが劣るため、極力、複合索引の先頭列を指定すること。</br>

deptnoとjob列の順で複合索引が作成されている場合の例）</br>
例○ 複合索引が使用される。）</br>
```sql
SELECT ename 
FROM emp
WHERE  deptno = 20 AND job = ‘MANAGER’;
```

```sql
SELECT ename 
FROM emp 
WHERE deptno = 20;
```

例×パフォーマンスが劣る。）</br>
```sql
SELECT ename 
FROM emp 
WHERE job = ‘MANAGER’; 
```

### 5.3.12	全表スキャンとなる同じ表にある項目間の大小比較を避けること
WHERE条件句で同一表の項目同士で大小比較を実施すると、索引を使用せず全表スキャンを行ってしまう。</br>

例×）</br>
```sql
SELECT col1,col2
FROM sample
WHERE col1 >[=] col2 (同じ表の場合）
```

```sql
SELECT col1,col2
FROM sample
WHERE col1 <[=] col2(同じ表の場合）
```


## 5.4.	ソートに関するコーディングルール</br>
本節では、ソートのコーディングルールに関して記述する。

### 5.4.1	ORDER BY句の対象列は列Noではなく列名称を指定すること
ORDER BY句では、SELECT句の列番号を指定することができるが、列番号と列名称との対応づけの処理負荷と可読性を損なう点を考慮し、対象列の列名称を指定すること。</br>
例×）ORDER BYの対象を列Noで指定している。</br>
```sql
SELECT col1,col2
FROM table01 
ORDER BY 2;
```

### 5.4.2	非検索列へのORDER BYは実施しないこと
パフォーマンスの観点から、検索対象外の列（SELECTで指定しない列）をORDER BYに指定してソートしない。</br>
例×）検索対象外col2をORDER BYでソートしている。</br>
```sql
SELECT col1 
FROM table01 
ORDER BY col2;
```

### 5.4.3	ORDER BY、DISTINCT、GROUP BYに指定する項目は、極力索引順序とすること
ORDER BY、DISTINCT、GROUP BYに指定する項目の順序を、索引順序に指定することで索引が有効になりソートを回避することができる。</br>
極力、索引順序になるように項目順序を指定すること。</br>

### 5.4.4	暗黙的にソートされるSQL構文に注意すること
前述の「5.4.3ORDER BY、DISTINCT、GROUP BYに指定する項目は、極力索引順序とすること」ルールを適用することができなければ、以下のSQL文は、暗黙的にソート処理が走るため、可能ならば他のSQL文で書き換えること。</br>
DISTINCT	結合時EXISTS構文での代替を検討する</br>
GROUP BY</br>
INTERSECT	INTERSECT構文は非推奨</br>
EXCEPT	EXCEPT構文は非推奨</br>
UNION	UNION構文は非推奨</br>

# 6.	更新処理コーディングルール
本章では、更新処理（データ新規登録、データ更新、データ削除）特有の規約について記載する。

## 6.1.	更新処理共通ルール
本節では更新処理（データ新規登録、データ更新、データ削除）共通のルールについて記載する。

### 6.1.1	SQL実行前にアプリケーション側でSQL実行時エラーが発生しないように考慮すること
更新処理のSQLを実行する際、以下のようなアプリケーションで制御可能なエラーは、SQL実行前にアプリケーションで十分にチェックし発生しないようにすること。</br>
- NOT NULL制約
- チェック制約

アプリケーション側で十分にデータを検査せずにSQLを発行し、SQL実行後発生したエラーを解析し上記エラーをハンドリングするようなことはしない。SQL発行前にアプリケーションで十分にデータを検査することで、無駄なSQLの発行回数を抑え、RDBMSへの処理負荷を軽減することができる。

### 6.1.2	更新SQL実行時、ユニークキー制約、外部参照制約のエラーをハンドリングすること
以下の制約は、更新SQL実行前に十分にチェックしても、更新SQLを発行した際にエラーが発生することを完全には回避できない。</br>
- ユニークキー制約
- 外部参照制約

アプリケーションは更新SQL発行時、上記制約エラーが発生することを想定し、制約エラーが発生した際のハンドリングをすること。

### 6.1.3	ユニークキー制約、外部参照制約エラーが想定される場合、事前チェックを実施すること
更新SQL発行時、以下のエラーが発生することが予期できるケースでは、事前に制約エラーが発生しないかどうかアプリケーションでチェックを実施すること。</br>

- ユニークキー制約の事前チェック（主キーがユーザ入力の場合など）
登録するデータの存在チェック
- 外部参照制約の事前チェック（頻繁に参照テーブルのレコード変更がある場合など）
登録するデータが参照先テーブルに存在するか、被参照データを削除しようとしていないかなど。
ただし、モジュール間の参照関係により、他モジュールへの問い合わせが頻発してしまい、SQLの発行数が多くなってしまうようであれば、外部参照制約の例外をハンドリングしても良い
またデータ更新SQLに対して、実行直前（同一トランザクション内において）に以下のようなチェックは不要。
- テーブルロックによる制約のチェック
テーブルをロックして、制約をチェックする必要はない。
同時操作性への影響が大きいため。</br>

事前チェックを実施しても、更新SQLの実行時に発生する制約エラーを完全に回避することは不可能だが、事前チェックをすることで以下のメリットがある。</br>
- 実行時エラーとなる更新発行回数を抑えることで、トランザクションの開始を遅らせることができる
- RDBMSの処理負荷が軽減できる</br>

※制約エラーの発生が想定されないシーンでは、SQL発行回数を抑えるため事前チェックは不要。
※制約エラーが発生しないように、主キーをシーケンスによる代替キーとすることや、ロック順序見直し等検討もすること。

### 6.1.4	CASE式で、条件句のない更新処理を実施しないこと
パフォーマンス劣化とバグの誘発を防止するため、条件句のない更新処理をCASE式で実施しないこと。
以下の例のように、２件の更新処理を実施する場合に、条件句を明示的に指定せずにCASE式を使用した場合、全レコードに対して更新処理を実施しコストがかかる。</br>
CASE式の例）</br>
-- 以下の２つのUPDATE文を統合する</br>

```sql
UPDATE emp SET</br>
SALARY = salary* 1.2 </br>
WHERE deptno = 10</br>
```

```sql
UPDATE emp SET</br>
SALARY = salary* 0.9</br>
WHERE deptno = 20</br>
```

```SQl
UPDATE emp SET
salary = 
    CASE  deptno
        WHEN 10 THEN salary * 1.2
        WHEN 20 THEN salary * 0.9
        ELSE salary
    END
```
※このCASE式は上記２つのUPDATE＋それ以外をデフォルト値（salaryの値）で更新する全表更新処理のためパフォーマンスは悪い。</br>

<!--2021.10.28 TQ1538　add-->

### 6.1.5	DB共通保持項目に対する更新処理ルール

Aurora PostgreSQLに作成する全テーブルの共通保持項目に設定する値にばらつきが出ないよう、以下のルールに則りデータ設定を行う

■DB共通保持項目に対する設定値
create_datetime　→　[current_timestamp]　(※1)
update_datetime　→　[current_timestamp]
update_container_id　→　コンテナID　(※2)
update_user_id　→　CASGUIのユーザID　(※3)

(※1)
登録(INSERT)時のみ設定。

(※2)
API経由の場合はAPI ID。

(※3)
CASGUI以外は設定しない。

</br>


<!--#2020 add-->
### 6.1.6	DB登録時の編集処理ルール
データ登録時に格納するデータに対しては以下のルールに則り編集を行うこととする。


•	半角スペースの編集</br>
→文字列の前後の半角スペースをトリムした状態で登録する。</br>
例）
```
"△AB△C△"→"AB△C"
```

•	右詰半角0埋めの編集
→DBの型定義がINTEGERの場合はINTEGERとして登録する。</br>
例)
```
"0000123"→123
```
→DBの型定義がVARCHARの場合は項目次第で、金額など半角0の意味が無い場合は埋められている半角0を除外して登録する。</br>
例)
```
"0000123"→"123"
```
→半角0に意味がある場合はそのまま登録する。</br>
例)
```
"0000123"→"0000123"
```

•	空文字("")の編集</br>
→NULLに変換して登録する。</br>
例)
```
""→NULL
```


(補足)
ただし、業務・アプリ観点で半角スペースに意味がある場合、処理の都合上無いと困る場合はこの限りではないものとする。</br>
例)「OPERATION FREQUENCY DAYS（VARCHARA〔7〕）」</br>

そのような特定の項目については、参照アプリ側でどのように扱いたいのかを確認して個別にご判断すること。


## 6.2.	データ新規登録処理（INSERT）のコーディングルール
本節ではデータ新規登録処理のルールについて記載する。

### 6.2.1	挿入列項目は明示的に指定すること
テーブルにデータ新規登録処理をする際、明示的に挿入する列項目を指定することで、列の増減、列順の変更に強いSQL文となる。</br>
a．	挿入する項目は省略せずに必ず記述すること。</br>
b．	定義項目数と挿入項目数とは一致すること。</br>
c．	設定値を省略するときは「，，」とする。</br>
例）</br>
```sql
CREATE TABLE departments (
          department_id     INTEGER(4) NOT NULL,
          department_name  VARCHAR(30) NOT NULL,
          manager_id        INTEGER(6)    NOT NULL,
          location_id       INTEGER(4),
          dn                  VARCHAR(300) );
```

上記CREATE文で定義した departments テーブルの項目名 location_id, dn の項目値のNULL設定を許すとしたとき。</br>

例○）</br>
```sql
INSERT INTO departments
             ( department_id, department_name, manager_id, dn )
VALUES      ( 280, 'Recreation', 121,  );
```
例×）</br>
```sql
INSERT INTO departments
             ( department_id, department_name, manager_id, dn )
VALUES      ( 280, 'Recreation', 121 );
```
上の例の場合、 location_id, dn にはNULLが設定される。</br>

5.2.2	複数行INSERT する場合はVALUES 句に複数の行データを指定する
複数行INSERT する場合、VALUES 句に複数の行データを指定することで、1 つのSQL 文とすること。</br>
【理由】</br>
・ SQL の発行回数の軽減になるため。</br>
例）</br>
```sql
INSERT INTO emp (empno,ename)
VALUES
(100, 'AAA'),
(200, 'BBB'),
(300, 'CCC')
;
```



## 6.3.	データ更新処理（UPDATE）のコーディングルール
本節ではデータ更新処理のルールについて記載する。

### 6.3.1	複数レコードを更新する場合は、SELECT ～FOR UPDATE文で更新前に明示的な行ロックを行うこと
一度に大量のレコードをロックしたり、表全体をロックしたりするような大規模のロックは同時操作性にも問題が生ずるので極力控えること。複数のレコードを更新する必要がある場合は、レコード毎にロックするのではなく、1つのSQLで、同時にロックすること。</br>
明示的に行ロックを実施することで、ロック待ちの防止、デットロックの防止をすることができる。
例）複数レコードを同時にロックする場合は、１つのSQLで実施する</br>
```sql
SELECT col1 
FROM sample 
WHERE col1=1 
    OR col1=15
    FOR UPDATE OF col1;
```

```sql
UPDATE sample 
SET col2=2 
WHERE col1=1;

UPDATE sample 
SET col2=30 
WHERE col1=15;
COMMIT;
```

### 6.3.2	副問合せを使用した更新は避けること
更新処理ではコードミスの回避と保守を考慮し、副問合せを使用した更新は極力避けること。</br>
a．	更新処理では複雑な副問合せは極力避けるようにする。
b．	複数項目に対する副問合せは極力避けるようにする。
※	業務要件上使用する必要がある場合は、以下のように副問合せの記述を階層構造に記述すること。ただし、副問合せをネストすることは禁止とする。</br>
例○）</br>
```sql
UPDATE emp<
SET        department_id  = (
                      SELECT department_id
                      FROM    departments
                      WHERE   location_id ='2100')
             salary, commission_pct = (
                      SELECT 1.1*AVG(salary), 1.5*AVG(commission_pct)
                      FROM    emp B
                      WHERE  A.department_id =B.department_id)
WHERE   department_id  IN (
                     SELECT department_id 
                     FROM    departments
                     WHERE  location_id = 2900
                     OR        location_id = 2700);
```
例×）</br>
```sql
UPDATE emp A
  SET  department_id = (SELECT department_id FROM departments WHERE location_id ='2100'),
  (salary, commission_pct) = (SELECT 1.1*AVG(salary), 1.5*AVG(commission_pct) FROM emp B WHERE A.department_id =B.department_id)
WHERE    department_id  IN (SELECT department_id FROM departments WHERE location_id = 2900 OR location_id = 2700);
```

※上記UPDATE文によって、次の処理が実行される。</br>
- ジュネーブまたはミュンヘン（location_id は2900 または 2700） で働く従業員のみを更新する。</br>
- これらの従業員の department_idをボンベイ(location_id は2100) の対応するdepartment_idに設定する。</br>
- 各従業員の給与を、その部門の平均給与の１０％引き上げる。</br>
- 各従業員の歩合を、その部門の平均歩合の５０％引き上げる。</br>

### 6.3.3	更新対象データが存在しなかった場合の処理は個別仕様に従うこと
更新対象データが存在しなかった場合の考慮を、クラス仕様もしくはDAOクラスのJavadocの記述に従いSQLを発行するアプリケーションで実施すること。</br>
例）</br>
- 業務続行不可として例外をスローする</br>
- 更新対象データが存在しない場合の業務を継続する</br>

### 6.3.4	複数テーブル更新時、別途定められたロック順序に従いロックすること
複数テーブルをまたぐ更新時、複数テーブルを別途定められたロック順序に従いロックすること。

### 6.3.5	レコードの更新はdelete→insertを極力使用しないこと。
レコードを更新する場合は極力update文を使用すること。</br>
delete→insertにて更新処理を実現した場合、deleteしたレコードは当初格納されていた領域とは別の領域にinsertされることになり、データの格納効率が低下して以後のDMLの実行速度に影響を及ぼすことになる。

### 6.3.6	SELECT 文により1 行ずつデータをフェッチし、その後フェッチした行に対して更新処理を行う場合に、CTID の有効利用を検討する
SELECT 文により1 行ずつデータをフェッチし、その後フェッチした行に対して更新処理を行う場合は、SELECT 時にCTID を取得しておき、後続のSQL 文では、そのCTID を条件にデータを取得すること。</br>
【理由】</br>
-  高速に処理できるため</br>
CTID は、テーブル内における行バージョンの物理的な位置を表す。そのため、1 レコードを操作する処理において最も高速なのはCTID を基にしたアクセスである。</br>
ただしCTID は該当行の更新やVACUUM FULL の実行で値が変更されるため、長期の行識別子として保持する事はできないことに注意する。</br>
× 非効率な例</br>
```sql
SELECT ename, sal
FROM emp
WHERE empno = $1
FOR UPDATE NOWAIT;
```

-- ここで該当行が画面に表示され、画面への入力により</br>
データが更新されることを想定</br>

```sql
UPDATE emp
SET sal = $2
WHERE empno = $1;
```
-- この例ではempno 列の索引に再度アクセスする</br>

○ CTID を使った例</br>
```sql
SELECT ename, sal, ctid
FROM emp
WHERE empno = $1
FOR UPDATE NOWAIT;;
```
-- ここで該当行が画面に表示され、画面への入力により
データが更新されることを想定</br>
```sql
UPDATE emp
SET sal = $2
WHERE ctid = '(0, 2)';
```
-- 取得したCTID を基にUPDATE</br>

なお、CTID はOracle Database のROWID と同様、積極的な使用が推奨されるものではないため、CURRENT OF カーソルを利用することを最初に検討し、それが不可能な場合にのみCTID を利用する。</br>
例）</br>
```sql
CREATE OR REPLACE FUNCTION current_of_cur(
IN p_empno_in emp.empno%TYPE,
IN p_sal_in emp.sal%TYPE
) RETURNS VOID
AS $$
DECLARE
emp_cur CURSOR FOR
SELECT ename,
sal
FROM emp
WHERE empno = p_empno_in
FOR UPDATE;
emp_rec RECORD; -- 取り出し用レコードの宣言
BEGIN
OPEN emp_cur;
FETCH emp_cur INTO emp_rec;
```

```sql
UPDATE emp
SET sal = p_sal_in
WHERE CURRENT OF emp_cur; -- カーソルが示す行を更新する
CLOSE emp_cur;
END;
$$ LANGUAGE plpgsql;
```


## 6.4.	データ削除処理(DELETE)のコーディングルール
本節ではデータ削除処理のルールについて記載する。

### 6.4.1	複数レコードを削除する場合は、SELECT ～FOR UPDATE文を使用し削除前に明示的な行ロックを行うこと
一度に大量のレコードをロックしたり、表全体をロックしたりするような大規模のロックは同時操作性にも問題が生ずるので極力控えること。複数のレコードを削除する必要がある場合は、レコード毎にロックするのではなく、1SQLで、同時にロックすること。</br>
明示的に行ロックを実施することで、ロック待ちの防止、デットロックの防止をすることができる。</br>
例）</br>
```sql
SELECT col1 
FROM sample 
WHERE col1=1
    OR col1=15
    FOR UPDATE;
```

```sql
DELETE FROM sample 
WHERE col1=1 
    OR col1=15;
COMMIT;
```

### 6.4.2	副問合せを使用した削除は避けること
削除処理ではコードミスの回避と保守を考慮し、副問合せを使用した削除は極力避けること。</br>
a．	削除処理では複雑な副問合せは極力避けるようにする。</br>
b．	複数項目に対する副問合せは極力避けるようにする。</br>

※	業務要件上使用する必要がある場合は、副問合せの記述を階層構造に記述すること。ただし、副問合せをネストすることは禁止とする。（「5.3.2副問合せを使用した更新は避ける」のコーディング例参照）

### 6.4.3	削除対象データが存在しなかった場合の処理は個別仕様に従うこと
削除対象データが存在しなかった場合の考慮を、個別のクラス仕様もしくはDAOクラスのJavadocの記述に従いSQLを発行するアプリケーションで実施すること。</br>
例）</br>
- 業務続行不可として例外をスローする</br>
- 削除対象データが存在しない場合の業務を継続する</br>


### 6.4.4	論理削除と物理削除については各業務要件に従うこと。
テーブルからデータを削除する際に、論理削除（削除フラグを立てる）を実施するか、物理的に削除するかは各機能の業務要件に従う。

### 6.4.5	複数テーブル削除時、別途定められたロック順序に従いロックすること
複数テーブルをまたぐ削除時、削除するテーブルを別途定められたロック順序に従いロックすること。

