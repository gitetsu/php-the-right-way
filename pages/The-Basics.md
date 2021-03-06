---
layout: page
title: The Basics
---

# 基本

## 比較演算子

比較演算子はPHPの中でも見過ごされがちなところで、そのせいで予期せぬ結果になってしまうことが多い。
そんな問題の原因のひとつが、緩い比較と厳格な比較の違いだ。

{% highlight php %}
<?php
$a = 5;   // 5はinteger型

var_dump($a == 5);       // 値の比較。trueを返す
var_dump($a == '5');     // 値の比較(型は無視)。trueを返す
var_dump($a === 5);      // 型と値の比較(integer vs. integer)。trueを返す
var_dump($a === '5');    // 型と値の比較(integer vs. string)。falseを返す

/**
 * 厳格な比較
 */
if (strpos('testing', 'test')) {    // 'test' は 0 番目の位置にあり、これはboolean型の'false'と見なされる
    // コード...
}

vs.

if (strpos('testing', 'test') !== false) {    // 厳格な比較が行われるので、これは成立する(0 !== false)
    // コード...
}
{% endhighlight %}

* [比較演算子](http://php.net/manual/ja/language.operators.comparison.php)
* [比較の表](http://php.net/manual/ja/types.comparisons.php)

## 条件分岐

### If 文

'if/else'文を関数やクラスの中で使うときにありがちな誤解がある。
そうじゃなかったときにどんな結果になるのかを示すために'else'が必須だと考えることだ。
でも、もしその結果を戻り値に使うのなら'else'は必須じゃない。
だって'return'の時点で関数は終了するんだから、'else'は無意味だ。

{% highlight php %}
<?php
function test($a)
{
    if ($a) {
        return true;
    } else {
        return false;
    }
}

vs.

function test($a)
{
    if ($a) {
        return true;
    }
    return false;    // 別にelseがなくたっていいよね
}
{% endhighlight %}

* [If 文](http://php.net/manual/ja/control-structures.if.php)

### Switch 文

Switch文を使えば、ifとelseifを延々と書き連ねる必要がなくなる。
でも、気をつけないといけないこともある。

- Switch文は値を比較するだけで、型は比較しない(つまり、'=='で比較してるってこと)
- マッチする条件が見つかるまで、すべてのcaseを順に評価する。マッチするものがない場合、もしdefaultが定義されていればそれを使う
- 'break'がなければそのまま次のcaseに進み、breakかreturnに達するまで止まらない
- 関数の中で'return'を使うときは'break'は不要だ。その時点で関数を終了する

{% highlight php %}
<?php
$answer = test(2);    // 'case 2'のコードと'case 3'のコードを両方実行する

function test($a)
{
    switch ($a) {
        case 1:
            // コード...
            break;             // breakでswitch文を抜ける
        case 2:
            // コード...        // breakしてないので'case 3'の評価に進む
        case 3:
            // コード...
            return $result;    // 関数の中で'return'すると、ここで関数を抜ける
        default:
            // コード...
            return $error;
    }
}
{% endhighlight %}

* [Switch 文](http://php.net/manual/ja/control-structures.switch.php)
* [PHP switch](http://phpswitch.com/)

## グローバル名前空間

名前空間を使っていると、自作の関数に邪魔されて組み込みの関数が実行できなくなることがある。
これを修正するには、組み込み関数を使いたいときには関数名の前にバックスラッシュをつけるようにするといい。

{% highlight php %}
<?php
namespace phptherightway;

function fopen()
{
    $file = \fopen();    // 組み込みの関数と同じ名前の関数を定義しているので、
                         // 組み込みのfopen関数を実行するには"\"を追加する
}

function array()
{
    $iterator = new \ArrayIterator();    // ArrayIteratorは組み込みのクラスである。名前にバックスラッシュをつけずに使うと、
                                         // phptherightway名前空間でこの名前を探してしまう。
}
{% endhighlight %}

* [グローバル空間](http://php.net/manual/ja/language.namespaces.global.php)
* [グローバルに関するルール](http://php.net/manual/ja/userlandnaming.rules.php)

## 文字列

### 連結

- 一行がある一定の長さ (目安は120文字) を超える場合は複数行に分けて連結する
- 可読性を考えると、代入演算子でつなげるよりも連結演算子を使ったほうがよい
- 同じ変数のスコープ内ではあるが、連結演算子で次の行に移るときはインデントする


{% highlight php %}
<?php
$a  = 'Multi-line example';    // 連結代入演算子 (.=)
$a .= "\n";
$a .= 'of what not to do';

vs.

$a = 'Multi-line example'      // 連結演算子 (.)
    . "\n"                     // 改行してインデント
    . 'of what to do';
{% endhighlight %}

* [文字列演算子](http://php.net/manual/ja/language.operators.string.php)

### 文字列型

文字列型は、PHPの中ではあまりばらつきがないものだ。
このセクションでは、文字列型のちょっとした違いやその利点そして利用法を紹介しよう。

#### シングルクォート

シングルクォートは文字列を表す一番シンプルな方法で、たいていの場合は一番高速だ。
高速になる理由は、PHPが文字列をパースしない(つまり、変数の展開をしない)ことだ。
シングルクォートを使うのに適しているのは、こんな場面だ。

- パースする必要がない文字列
- 変数の内容をプレーンなテキストで表す

{% highlight php %}
<?php
echo 'This is my string, look at how pretty it is.';    // 単純な文字列で、パースする必要がない

/**
 * 出力は、
 *
 * This is my string, look at how pretty it is.
 */
{% endhighlight %}

* [シングルクォート](http://www.php.net/manual/ja/language.types.string.php#language.types.string.syntax.single)

#### ダブルクォート

ダブルクォートは、文字列界におけるスイスアーミーナイフだ。
でも、文字列をパースするぶんだけ速度が落ちる。ダブルクォートを使うのに適しているのは、こんな場面だ。

- エスケープした文字列
- プレーンなテキストの中に変数が埋め込まれた文字列
- 複数行をひとまとめにして可読性を上げたい場合

{% highlight php %}
<?php
echo 'phptherightway is ' . $adjective . '.'     // シングルクォートを使った例。複数の要素を連結し、
    . "\n"                                       // 変数の埋め込みやエスケープを使っている
    . 'I love learning' . $code . '!';

vs.

echo "phptherightway is $adjective.\n I love learning $code!"  // ダブルクォートを使えば、別々の要素に分けずに
                                                               // ひとまとめにできる
{% endhighlight %}

ダブルクォートで囲んだ文字列に変数を含むとき、変数とそれ以外の文字がつながってしまうこともよくある。
そうなってしまえばPHPが変数をパースできなくなる。どこまでが変数でどこからが普通の文字列かがわからなくなるからだ。
この問題を解決するには、変数の部分を波括弧で囲めばよい。

{% highlight php %}
<?php
$juice = 'plum';
echo "I drank some juice made of $juices";    // $juiceがパースできない

vs.

$juice = 'plum';
echo "I drank some juice made of {$juice}s";    // これで、変数は$juiceだとわかる

/**
 * 配列などの場合も波括弧で囲む
 */

$juice = array('apple', 'orange', 'plum');
echo "I drank some juice made of {$juice[1]}s";   // これで、$juice[1]がパースできる
{% endhighlight %}

* [ダブルクォート](http://www.php.net/manual/ja/language.types.string.php#language.types.string.syntax.double)

#### Nowdoc 構文

NowdocはPHP 5.3で導入された構文で、内部的にはシングルクォートと同じような動きをする。
複数行にまたがる文字列を、連結演算子なしで表すのに適している。

{% highlight php %}
<?php
$str = <<<'EOD'             // 最初は <<<
Example of string
spanning multiple lines
using nowdoc syntax.
$a does not parse.
EOD;                        // 終了文字列はそれ単体でひとつの行に書く。また行頭に書かないといけない

/**
 * 出力は、
 *
 * Example of string
 * spanning multiple lines
 * using nowdoc syntax.
 * $a does not parse.
 */
{% endhighlight %}

* [Nowdoc](http://www.php.net/manual/ja/language.types.string.php#language.types.string.syntax.nowdoc)

#### ヒアドキュメント構文

ヒアドキュメントは、内部的にはダブルクォートと同じような動きをする。
複数行にまたがる文字列を、連結演算子なしで表すのに適している。

{% highlight php %}
<?php
$a = 'Variables';

$str = <<<EOD               // 最初は <<<
Example of string
spanning multiple lines
using heredoc syntax.
$a are parsed.
EOD;                        // 終了文字列はそれ単体でひとつの行に書く。また行頭に書かないといけない

/**
 * 出力は、
 *
 * Example of string
 * spanning multiple lines
 * using heredoc syntax.
 * Variables are parsed.
 */
{% endhighlight %}

* [ヒアドキュメント](http://www.php.net/manual/ja/language.types.string.php#language.types.string.syntax.heredoc)

## 三項演算子

三項演算子を使えばコードを短くできるが、必要以上に使いすぎていることが多い。
三項演算子をネストさせることもできるけれど、それぞれ別の行にしたほうが読みやすくなる。

{% highlight php %}
<?php
$a = 5;
echo ($a == 5) ? 'yay' : 'nay';

vs.

// 三項演算子をネストしてみた
$b = 10;
echo ($a) ? ($a == 5) ? 'yay' : 'nay' : ($b == 10) ? 'excessive' : ':(';    // やりすぎ。もはや読めない :-(
{% endhighlight %}

三項演算子で値を 'return' するには、書きかたに気をつけないといけない。

{% highlight php %}
<?php
$a = 5;
echo ($a == 5) ? return true : return false;    // この書きかただとエラーになる

vs.

$a = 5;
return ($a == 5) ? 'yay' : 'nope';    // この書きかたなら 'yay' を返す
{% endhighlight %}

* [三項演算子](http://php.net/manual/ja/language.operators.comparison.php)

## 変数の宣言

コードをより「きれい」にするために、変数を宣言してよりわかりやすい名前をつけたくなることがよくある。
実際のところこれは、スクリプト内でメモリを二倍消費していることになる。
次の例で考えてみよう。仮にこの文字列が1MBぶんのデータを含んでいたとすると、
変数をコピーすれば実行時のメモリ消費量が2MBになってしまう。

{% highlight php %}
<?php
$about = 'A very long string of text';    // メモリを2MB消費する
echo $about;

vs.

echo 'A very long string of text';        // メモリの消費は1MBだけ
{% endhighlight %}

* [パフォーマンス改善のヒント](https://developers.google.com/speed/articles/optimizing-php)
