# DevGuideline(APL) 別紙2.Javaコーディング規約ドラフト版

# 目次
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->
- [目次](#目次)
    - [1 基本原則](#1-基本原則)
      - [1.1 PLUS独自変更点](#11-PLUS独自変更点)
        - [1.1.1 日付の設定基準は以下のとおりとすること](#111-日付の設定基準は以下のとおりとすること)
        - [1.1.2 スレッドセーフとなる実装をすること](#112-スレッドセーフとなる実装をすること)
        - [1.1.3 ソースコードへのコピーライト表記について](#113-ソースコードへのコピーライト表記について)
    - [2 コーディングスタイル](#2-コーディングスタイル)
    - [3 コードフォーマッタによる自動整形](#3-コードフォーマッタによる自動整形)
      - [3.1 Eclipseの場合](#31-eclipseの場合)
      - [3.2 VSCodeの場合](#32-vscodeの場合)
    - [4 Checkstyleによる静的テスト](#4-checkstyleによる静的テスト)
    - [5 その他](#5-その他)
      - [5.1 記述子の優先関係がわかりにくい場合、括弧を使用すること](#51-記述子の優先関係がわかりにくい場合括弧を使用すること)
      - [5.2 １文で複数の変数への代入は記述しないこと](#52-１文で複数の変数への代入は記述しないこと)
      - [5.3 実行文中に直接定数値を記述せず、定数として宣言すること](#53-実行文中に直接定数値を記述せず定数として宣言すること)
      - [5.4 ローカル変数は多様目的用途として使い回しをしないこと](#54-ローカル変数は多様目的用途として使い回しをしないこと)
      - [5.5 複数のStringを１つに連結する場合、StringBufferクラスまたはStringBuilderクラスを使用すること](#55-複数のstringを１つに連結する場合stringbufferクラスまたはstringbuilderクラスを使用すること)
      - [5.6 宣言は極力インタフェースを使用すること](#56-宣言は極力インタフェースを使用すること)
      - [5.7 小数表現にはBigDecimalクラスを使用すること](#57-小数表現にはBigDecimalクラスを使用すること)
      - [5.8 ネストを深くしないこと](#58-ネストを深くしないこと)
    - [7 Javadoc記述規約](#7-javadoc記述規約)
      - [7.1 概要](#71-概要)
        - [7.1.1 Javadoc記述対象](#711-javadoc記述対象)
        - [7.1.2 記述粒度](#712-記述粒度)
        - [7.1.3 変更履歴](#713-変更履歴)
      - [7.2 記述要領](#72-記述要領)
      - [7.3 クラスJavadoc](#73-クラスjavadoc)
        - [7.3.1 ガイドラインに定義されたクラス](#731-ガイドラインに定義されたクラス)
        - [7.3.2 ガイドラインに定義のないクラス](#732-ガイドラインに定義のないクラス)
      - [7.4 メソッドJavadoc](#74-メソッドjavadoc)
        - [7.4.1 抽象メソッドと、具象メソッドのJavadoc](#741-抽象メソッドと具象メソッドのjavadoc)
      - [7.5 定数Javadoc](#75-定数javadoc)
      - [7.6 テストメソッドJavadoc](#76-テストメソッドjavadoc)
      - [7.7 Javadocの共通事項](#77-javadocの共通事項)
        - [7.7.1 非推奨化](#771-非推奨化)
        - [7.7.2 参照情報](#772-参照情報)
<!-- /code_chunk_output -->


### 1 基本原則  

PLUS開発では、ネーミング・コーディングはGoogle Java Styleに基づいて行う。  

- 本家サイト：[https://google.github.io/styleguide/javaguide.html](https://google.github.io/styleguide/javaguide.html)
- 日本語版(参考)：[https://kazurof.github.io/GoogleJavaStyle-ja/](https://kazurof.github.io/GoogleJavaStyle-ja/)  

主な特徴は次の通り([参考](https://qiita.com/kazurof/items/f99c90cb9eb091884ac4))。

- インデントは半角スペース2個  
- 1行あたり100文字  

Google Java Styleの利点は次の通り。  

- Javaにおけるデファクトスタンダードである
- 分量が少ない
- Java8以降のパラダイムにも対応している  
- IDE(コードフォーマッタ)、Checkstyle用のフォーマットが配布されている  

開発ツールでは自動検出できないが、ソースコード品質を高めるためのコーディングルールとして以下のドキュメントも参照すること「4.プログラミング全般に関するルール」

>参考　[コーディングルール](https://docs.google.com/document/d/1dXTbH8ZAK5g13rdofvCU6NLVfdaADT-c/edit?usp=sharing&ouid=117180675960603202586&rtpof=true&sd=true)

#### 1.1 PLUS独自変更点  


<!--#5435 mod -->
#### 1.1.1 日付の設定基準は以下のとおりとすること

・アプリ内で新規に日時を生成する場合にはJSTを基準とすること。</br>
・アプリ間IF（COMMON等）については、IF項目名の接頭辞に付与されている（JST/UTC）値を基準に設定すること。</br>
（IF利用者がIF呼び出し時にどの基準の日時を設定すべきかが判断できるようにする。）</br>
・接頭辞が省略されている場合は、ローカルTime(現地日時)として扱うこと。


<!--2021.09.30.mod　 ⑤開発ガイドラインに不足している要素の取込(ableから) -->

<!--#201 mod-->
<!--#5435 mod end-->
#### 1.1.2 スレッドセーフとなる実装をすること

Springの特徴ともいえるDIについてはスレッドセーフを意識して実装する必要がある。
DIを使用すべき箇所は以下のとおりとする。

- 共通処理を差し込みたい(AOP) </BR>
Beanを修正せずにメソッドに共通的な処理を差し込みたい場合。
- Beanをsingleton or prototypeに保ちたい</BR>
メモリ効率化、マルチスレッド環境でもBeanのプロパティでデータを参照したい等。
- Beanを差し替えられるようにしたい</BR>
製造時に共通部品など処理が未実装なときに、Stubを仮で使ってあとで処理を差し替えられるようにしたい場合。

上記に基づき、スレッドごとに中身が変わるオブジェクトについては原則としてBeanで管理しないこと。
また、Beanに限らず、複数スレッドでクラス変数や共有メモリを更新・参照する場合には注意すること。
リクエストに応じてDBから取得したデータをフィールドに持つオブジェクトなどはnew()でオブジェクトを作る。

実装例は以下の通り

```
@Repository
public class Order Dao
    public List<OrderDetail> getDetails(
      String orderNo){
      ・・・
      OrderDetail detail = new OrderDetail();
      ・・・
    return list;
    }
}
```



<!--#201 mod end-->
#### 1.1.3 ソースコードへのコピーライト表記について
コーディング時にコピーライトを付与する際には以下の記載とすること。<BR>
「© 20xx All Nippon Airways Co., Ltd. All rights reserved.」<BR>
「xx」の部分については生成時の年度にあわせて適時修正すること。<BR>

コピーライトを付与する成果物は以下の通り<BR>
・JAVA<BR>
　記載箇所：ソースファイルの先頭行<BR>
　注記　自動生成後に修正した物を対象とする。<BR>
　例は以下の通り<BR>
```
/** © 20xx All Nippon Airways Co., Ltd. All rights reserved. **/
```
<BR>・XML<BR>
　記載箇所：ソースファイルの先頭行←固定文言部分の下<BR>
　注記　自動生成後に修正した物を対象とする。<BR>
　例は以下の通り<BR>
```
<!-- © 20xx All Nippon Airways Co., Ltd. All rights reserved. -->
```
<BR>・yaml<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
#© 20xx All Nippon Airways Co., Ltd. All rights reserved.
```
<BR>・tsx,ts<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
/* © 20xx All Nippon Airways Co., Ltd. All rights reserved.  */
```
<BR>・scss,css<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
/* © 20xx All Nippon Airways Co., Ltd. All rights reserved.  */
```
<BR>・python<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
#© 20xx All Nippon Airways Co., Ltd. All rights reserved.
```
<BR>・sql<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
/* © 20xx All Nippon Airways Co., Ltd. All rights reserved.  */
```
<BR>・propertie<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
##© 20xx All Nippon Airways Co., Ltd. All rights reserved.
```
<BR>・Dockerfile<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
##© 20xx All Nippon Airways Co., Ltd. All rights reserved.
```
<BR>・sh<BR>
　記載箇所：ソースファイルの先頭行<BR>
　例は以下の通り<BR>
```
#© 20xx All Nippon Airways Co., Ltd. All rights reserved.
```





###  2 コーディングスタイル  

Google Java Styleに従うこととし、コードフォーマッタによる自動整形と、Checkstyleによる静的チェックにより適用する。  

ローカルコミットする際に、IDEのコードフォーマッタを適用すること。  


<!--2021.09.30.add　 ⑤開発ガイドラインに不足している要素の取込(ableから) 
#### 1.1.2 周知手順(全体管理者向け)
を移動。
-->
## 2.1 周知手順(全体管理者向け) 
もしGoogle Java Styleを変更する場合は次の作業を行うこと。  

- 変更点の例示  
- フォーマッタファイルの変更と配布  
- Checkstyleルールファイルへの反映  
<!--2021.09.30.addend　 ⑤開発ガイドラインに不足している要素の取込(ableから) -->

## 3 コードフォーマッタによる自動整形  

### 3.1 Eclipseの場合  

- 手動適用：Ctrl + Shift + F
- セーブ時自動適用：「Window」→「Preferences」→「Java」→「Editor」→「Save Actions」→Format source codeを有効にする

### 3.2 VSCodeの場合

- 手動適用：Ctrl + Alt + F
- セーブ時自動適用：「File」→「preferences」→「settings」でformatOnで検索し、Format On Saveを有効にする

## 4 Checkstyleによる静的テスト

Gradleのtest(単体で実行する場合はcheckstyleMain)を実行することで、Checkstyleによるチェックが実行される。  
ファイルは「プロジェクトルート/build/reports/checkstyle」配下に出力されるので、それをもとに修正する。

コードフォーマッタとCheckstyleに整合性があってない場合、Checkstyleを優先して対応する。</br>
例）import文の並び

<!--2021.09.30.add　 ⑤開発ガイドラインに不足している要素の取込(ableから) -->

##  5 その他
### 5.1 記述子の優先関係がわかりにくい場合、括弧を使用すること
記述子の優先関係がわかりにくい場合には、括弧を有効に使用して記述する。

```java
例×）
//優先関係が分かりづらい
if  (a  ==  b  &&  c  ==  d)
例○）
//優先関係が分かり易い
if ((a  ==  b)  &&  (c  ==  d))
```

 ### 5.2 １文で複数の変数への代入は記述しないこと
１文で複数の変数への代入は、ソースコードの可読性を損なうため、記述しないこと。
以下のようなコードは読みやすさをそこなうため、記述しないこと。
```java
例×）
//複数の変数への代入を1文で記述している
d  =  (a  =  b  +  c)  +  r;

例○）
//変数への代入ごとに1文で記述している
a  =  b  +  c;
d  =  a  +  r;
```

 ### 5.3 実行文中に直接定数値を記述せず、定数として宣言すること
数値などの定数値はソースコード中に直接書き込まずに、定数として宣言すること。  
ただしfor文のカウンタの初期値として記述する-1、0、1は例外とし、ループ変数等の初期値や増分値等、定数値の役割が明白な場合は除く。<BR>
enum定義や、パラメータやIPアドレスなどは外部定義にすることも検討する。（「アプリケーション開発ガイドライン 4.2.1.4.1.1 定数定義」も参照すること）<BR>
クラス内で共通使用する定義値は「static」をつけること。

```java
例○）
//定数を定義している。for文の初期定数値（0）は役割が明白なため記述OK
final int LOOP_MAX_LENGTH = 5 ;
for (int i = 0; i < LOOP_MAX_LENGTH; i++) {
    ....
}

例×）
//定数値（40）の意味が不明
if (array.length > 40) {
    ....
}

例×）
//定数値（35,15）の意味が不明
for (int i = 35; i > 15; i--) {
    ....
}
```

 ### 5.4 ローカル変数は多様目的用途として使い回しをしないこと
ある目的のために１度使用した変数を、再度別の目的のために使いまわして使用しない事。  
変数を使い回す場合、事前にワーク用の役割とする変数として命名し、コメントすること。

```java
例×）
public String testClass() {
    //文字列変数 str として、ここでは初期設定文字列として使用している。
    String str = "TEST CASE 1";
    //ここでは、その変数 str を整数文字列として使用している。
    str = Integer.toString(BIG_VALUE);

例○）
public String testClass() {
    /* 文字列の一時変換用として使用
    変数を使いまわす場合は、事前にワーク変数として使うことをコメントしておく。
    */
    String str = null;
    str = Integer.toString(BIG_VALUE);
    str = Integer.toString(SMALL_VALUE);
```

 ### 5.5 複数のStringを１つに連結する場合、StringBufferクラスまたはStringBuilderクラスを使用すること
複数のStringを１つに連結する際には、StringBufferクラスまたはStringBuilderクラスを使用すること。  
StringBuffer、StringBuilderをインスタンス化する際、文字列の長さが予め判明している場合にはその長さで初期化し、そうでない場合には必要な長さを推測し、その長さで初期化すること。
また、１行に記述する文字数の範囲内ならば、同一行内にappend()メソッドを複数回呼び出しても良い。

```java
//Stringの StringAとStringBとStringCを連結する
例○）
final int MAX_LENGTH = 60;
StringBuffer myStringBuffer = new StringBuffer(MAX_LENGTH);
myStringBuffer.append(stringA).append(stringB).append(stringB);

例×）
String myString = stringA + stringB + stringC;
```
文字列定数を定義する際の文字列連結の場合、（String文字列の連結がコンパイル時に行われるならば）、Stringを連結する。

<!--2021.09.30.addend　 ⑤開発ガイドラインに不足している要素の取込(ableから) -->

### 5.6 宣言は極力インタフェースを使用すること

オブジェクトを参照する際は、そのオブジェクトの実装クラスを用いて宣言できるが、適切なインタフェースがある場合、極力インタフェースを指定すること。
インタフェースを使用することで、実装クラスの切り替えを容易にするなど、コードの柔軟性、保守性が向上する。

```java
例○）
//実体クラスのLinkedListではなく、インタフェースのListで宣言する。
List rubberDuckList = new LinkedList();
method(rubberDuckList);

private void method(List inputList)

//上の宣言の実体クラス部分を変更する場合には、
//以下のようにインスタンス生成箇所の変更のみでOK。
List rubberDuckList = new ArrayList(20);

例×）
//実体クラスのLinkedListで宣言する。
LinkedList rubberDuckList = new LinkedList();
method(rubberDuckList);

private void method(LinkedList inputList)

//上の宣言の実体クラス部分を変更する場合には、
//以下のように全ての参照先を変更する可能性が出てくる。
ArrayList rubberDuckList = new ArrayList(20);
private void method(ArrayList inputList)
```

### 5.7 小数表現にはBigDecimalクラスを使用すること

計算時の誤差を考慮し、サービス内部での小数表現にはDouble(double)、Float(float)ではなくBigDecimalを使用すること。
浮動小数点演算は誤差が発生する場合があり、BigDecimalはインスタンス生成時に指定された桁数での精度が保証されている。

### 5.8 ネストを深くしないこと

ネストの深さが４を超える場合、下記の対策を実施する。ネストの深さが４を超えていなくても、ソースの可読性に問題がある場合は対策を実施すること。  
また、ネストの深さを４以下にすることが困難である場合はコメントを追加する等の対策で、ソースの可読性を上げること。  
ネストの深さの数え方は、メソッド内のコードブロックが１、その中のブロックを２と数える。  
例えば、下記の場合は５になる。  

```java
例）
  public boolean hogeHoge(HonyaDto[] paramA) {
    if (paramA != null) {
      // ネスト１
      for (int i=0; i++; i<paramA.length) {
        // ネスト２
        if (paramA[i].isEnabled()) {
          // ネスト３
          try {
            // ネスト４
            // doSomething...
            if (ret == false) {
              // ネスト５
              // doSomething...
            }
          }
        }
      }
    }
  }
```

- if文の統合

  単純に、AND（&&）や OR（||）で条件を結合できるif文が存在する場合は、if文を１つに纏めることを検討する。  
  纏めてしまうと条件が複雑になる場合は、条件判断を別メソッドに分割することも検討すること。  

```java
例×）
    if (paramA != null) {
      if (paramA[i].isEnabled()) {
          // doSomething...
      }
    }

例○）
    if (paramA != null && paramA[i].isEnabled()) {
      // doSomething...
    }
```

- アーリーリターン(early return)の適用

  引数チェックなど、処理の前段階で`return`出来る場合は、早めに`return`することを検討する。  

```java
例×）
  public boolean hogeHoge(HonyaDto[] paramA) {
    boolean result = true;

    if (paramA != null) {
      for (int i=0; i++; i<paramA.length) {
        if (paramA[i].isEnabled()) {
          // doSomething...
        }
      }
    } else {
      log.warn("paramA is null.");
      result = false;
    }

    return result;
  }

例○）
  public boolean hogeHoge(HonyaDto[] paramA) {
    if (paramA == null) {
      log.warn("paramA is null.");
      return false;
    }

    for (int i=0; i++; i<paramA.length) {
      if (paramA[i].isEnabled()) {
        // doSomething...
      }
    }

    return true;
  }
```

- 処理をメソッドに分割

  ネスト内の処理を別のメソッドに切り出すことを検討する。  
  切り出す処理は、再利用が可能（使用する／しないに拘わらず）な処理の塊を意識し、ネストの深さを解消することだけが目的のメソッドは作成しないこと。  

```java
例×）
  public boolean hogeHoge(HonyaDto[] paramA) {
    boolean result = true;

    if (paramA != null) {
      for (int i=0; i++; i<paramA.length) {
        if (paramA[i].isEnabled()) {
          // doSomething...
        }
      }
    } else {
      log.warn("paramA is null.");
      result = false;
    }

    return result;
  }

例○）
  public boolean hogeHoge(HonyaDto[] paramA) {
    boolean result = true;

    if (paramA != null) {
      for (int i=0; i++; i<paramA.length) {
        doHoge(paramA[i]);  // 全HonyaDtoに対してHoge処理を実行する
      }
    } else {
      log.warn("paramA is null.");
      result = false;
    }

    return result;
  }

  public void doHoge(HonyaDto paramA) {
    if (paramA[i].isEnabled()) {
      // doSomething...
    }
  }
```



## 7 Javadoc記述規約  

### 7.1 概要

#### 7.1.1 Javadoc記述対象

アクセス修飾子がpublicまたはprotectedのクラス、メソッド/コンストラクタ、定数については、Javadocの記述を必須とする。  
それら以外についてはJavadocは必須ではないが、個別に記述することを禁止するものではない。  
なお、もしパッケージJavadocを作成する場合は、package-info.java形式とし、package.html形式は使用しないこと。

#### 7.1.2 記述粒度  

開発者は、API（クラス、メソッド、定数）の利用者向けの外部仕様を、Javadocとして記述する。  
内部処理を記載する場合も、重要な副作用、API利用時の制限、API利用時に考慮すべき事項など、API利用者に影響を与える事項といった観点から記載する。  

- API利用者に影響を与える内部処理の記述例  

  - リトライに失敗した場合、リクエストデータはDead Letter Queue(キュー名:xxx)に格納される。  
  - このメソッドは内部でオープンしたコネクションを閉じないので、try-with-resourcesを利用する、finallyでcloseメソッドを呼ぶ、のいずれかの方法で必ずクローズすること。  

プログラム仕様書レベルの記述は、API仕様の理解コスト、コードとの重複保守コストの増加につながるため避けること。  
ただし、外部発注等で内部仕様の記述が必要となる場合は、別に段落を用意したうえで記述してもよい。こうしたコメントはdevelopブランチにマージする際に削除すること。

#### 7.1.3 変更履歴  

変更履歴はバージョン管理システムを使って追跡することとし、変更履歴コメントなどは記載しない。  

###  7.2 記述要領

Google Java Styleの7章に従うことを原則とする。  
1行目は概要とし、2行目以降を段落とする場合は、段落単位でpタグで囲むこと。  

###  7.3 クラスJavadoc

authorタグに会社名（ASY固定）、（チーム名　英語表記）、作成者名（英語表記のフルネーム(名 性)）を記述する。

```java
/**
 *  …
 *  @author "ASY - ruby - Taro Sorano "
 */
 public class SendMailController …
```

####  7.3.1 ガイドラインに定義されたクラス  

開発ガイドラインで決まっているController, Serviceなどのクラスについては、クラス名がその役割を示しているため、最低限の記述でよい。  

```java
/**
 *  メール送信機能のController。
 *
 *　@author xxx
 */
 public class SendMailController …
```

#### 7.3.2 ガイドラインに定義のないクラス

独自に定義したクラスについては、1行で収まる範囲で概要を説明する。  
詳細は適宜記述する。

```java
/**
 *  暗号化処理、復号処理を行うメソッドを提供するクラス。
 *
 *　@author xxx
 */
 public class EncryptionUtil ...
```

```java
/**
 *  送信メールを表すクラス。
 *
 *　@author xxx
 */
 public class Mail ...
```

### 7.4 メソッドJavadoc  

Google Java Styleの7.2にあるとおり、1行目に、何を行うメソッドかを簡潔に記述する。  
2行目以降は詳細を記述する。

```java
/**
 *  コミュニケーションプラットフォームに対して、メール送信指示を行う。
 *  <p>
 *  送信に失敗した場合、メッセージがDead Letter Queue(キュー名：xxx)に格納される。
 *  </p>
 *  @param mail 送信メール情報
 *  @return メール送信結果(成功時：{@see SendMailService.OK}　失敗時：{@see SendMailService.NG})
 *  @throws MailFormatException XXX0001 メール宛先情報が正しくない
 *  @throws MailFormatException XXX0002 プレースホルダが正しく解決されない
 */
public int sendMail(Mail mail) throws MailFormatException
```

タグについては次の通り。ないものは省略してよい。
|タグ名|記載内容|
|---|---|
|@param|引数の名称と説明。各引数について必須|
|@return|戻り値の説明。戻り値がある場合は必須|
|@throws|例外情報。例外原因や、例外コードがあればコードを記載する。<br>呼び出し元で明示的にハンドリングすべき業務例外のみ記述すること。<br>システムエラー(ExceptionHandlerによる例外処理で処理される例外)は記述しない。|

#### 7.4.1 抽象メソッドと、具象メソッドのJavadoc

抽象メソッドと具象メソッドがある場合、API使用者は抽象メソッド（インタフェース側に定義されたメソッド）のみを使用するので、Javadocは抽象メソッド側に記述する。  
実装メソッドのJavadocは{@inheritDoc}を使う。

```java
// インタフェース側
/**
 * xxx処理を行います.
 * @return 処理結果
 */
public String doSomething();

// 実装クラス側
/**
 * {inheritDoc}
 */
@Override
public String doSomething(){ ... }
```

###  7.5 定数Javadoc  

定数の意味を表す説明を記述する。

```java
/** メール送信成功 */
public static int MAIL_SEND_OK = 1;

/**
 *メール送信失敗。
 * <p>
 * 任意の説明…
 * </p>
 */
public static int MAIL_SEND_NG = -1;
```

### 7.6 テストメソッドJavadoc

検証項目の詳細、パラメータの特性など、メソッド名では表現できない事項は、テストメソッドのJavadocに記述すること。  
「コードを見ても何故必要か判断が難しいもの」はコメントで補足する。  

### 7.7 Javadocの共通事項  

#### 7.7.1 非推奨化

クラス、メソッド、定数を非推奨とする場合は、@deprecatedタグを利用する。  
非推奨の理由と、その代替となるAPIなどの情報を記述すること。

```java
/**
 * XXXの設定を保持するクラス。
 *
 * @deprecated XXXの仕様変更により、本クラスは非推奨となりました。example.newapiパッケージのクラスを利用してください。
 */
 @deprecated
 public class Xxx
```

#### 7.7.2 参照情報

関連するクラスやメソッド、内外で参照すべきURLがある場合は@seeを適宜利用する。

``` java
/**
 * RFC5321に準拠したメールアドレスを表すクラス。
 *
 * @see Mail
 * @see https://tools.ietf.org/html/rfc5321 
 */
 public class MailAddress {
```
<!--2021.09.30.add　 ⑤開発ガイドラインに不足している要素の取込(ableから) -->
