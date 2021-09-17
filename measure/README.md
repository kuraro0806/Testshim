<script type="text/javascript" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">


 MathJax.Hub.Config({
 tex2jax: {
 inlineMath: [['$', '$'] ],
 displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
 }
 });

</script>

# Measure Execution Cycles 
- [Measure Execution Cycles](#measure-execution-cycles)
  - [Description](#description)
    - [Execution Environment](#execution-environment)
  - [Preparation](#preparation)
    - [Tools](#tools)
    - [Insert macros to get execution cycle](#insert-macros-to-get-execution-cycle)
      - [Run **run.sh**](#run-runsh)
      - [Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*](#make-sure-that-c_src_macro-directory-is-created-in-insertmacroc_src)
      - [*c_src_Macro* 内のプログラムをクロスコンパイルする](#c_src_macro-内のプログラムをクロスコンパイルする)
  - [Run programs on a actual device and get execution cycle](#run-programs-on-a-actual-device-and-get-execution-cycle)
    - [注意点](#注意点)
      - [PMUの起動](#pmuの起動)
      - [動作周波数の固定](#動作周波数の固定)
      - [結果の出力方法](#結果の出力方法)
        - [各カラムの説明](#各カラムの説明)

## Description

This directory provides functions to cross-compile the measurement program for the actual device and acquire the execution cycles.

The procedure is as follows.

+ [Insert macros to get execution cycle](#Insert-macros-to-get-execution-cycle)
+ Cross-compile to run on a actual device
+ [Run programs on a actual device and get execution cycle](#Run-programs-on-a-actual-device-and-get-execution-cycle)

### Execution Environment

We have confirmed that the programs runs on the following platform.

+ Environment
  + *Ubuntu ver.16.04LTS*
<br>

+ Target Hardware
  + *Raspberry Pi3 Model B+*
    + *CPU*：*Cortex-A53*
    + *OS*：*Linux ver4.14.68*

## Preparation

This section describes the flow of inserting a macro into the measurement programs for execution.

### Tools

You will need the following tools:

+ Execution environment of *Python*
  + We have confirmed executing *ver 2.7.12*  
+ Environment to cross-compile for the target hardware
  + We used *aarch64-poky-linux* compiler

The files to be prepared are as follows:

+ The mesurement programs: [*c_src*](../c_src/)
    + Place *c_src* directory in *insertMacro*

### Insert macros to get execution cycle

The procedure to insert is as follows:

  1. [Run **run.sh**](#Run-runsh)
  2. [Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*](#[Make-sure-that-*c_src_Macro*-directory-is-created-in-*insertMacro/c_src*)
  3. [Cross-compile the programs in *c_src_Macro*](#Cross-compile-the-programs-in-*c_src_Macro*)


#### Run **run.sh**

The command to run is as follows:

`sudo ./run.sh c_src/*`

#### Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*

*c_src* 内に *c_src_Macro* ができていて、そのディレクトリの中にマクロが挿入された計測プログラムがあるのを確認してください。

※ **GaussianElimination.c**に*return*文が複数あるせいでマクロ挿入が上手くいきません。97行目から122行目のマクロを削除することで解決します。

#### *c_src_Macro* 内のプログラムをクロスコンパイルする

ターゲットのハードウェアで実行できるようにクロスコンパイルしてください。

## Run programs on a actual device and get execution cycle

コンパイルした実行ファイルをUSBメモリ等でターゲットハードウェアに移し、実行してください。

### 注意点
以下の3点に注意して実行してください。

1. [*PMU* の起動](#PMUの起動)
2. [動作周波数の固定](#動作周波数の固定)
3. [結果の出力方法](#結果の出力方法)

#### PMUの起動

[*PMU_module*](PMU_module) 内の3種類のカーネルモジュール(***.ko*)を以下のコマンドでロードしてください。

`insmod (カーネルモジュール)`

#### 動作周波数の固定

[*PMU_module*](PMU_module) 内の**cpusetup.sh**を以下のコマンドで動かしてください。

`./cpusetup3.sh`

#### 結果の出力方法

実行結果は、以下の例のようにcsvファイルに出力してください。
(*BubbleSort* は**BubbleSort.c**をコンパイルしたファイル)

`./BubbleSort > res_BubbleSort.csv`

csvファイルには以下のような結果が出力されていることを確認してください。

```bash
L1I-Refill,,,,L1D-Refil,,,,L1D-Access,,,,L1I-Access,,,,L2D-Access,,,,L2D-Refill,,,,
Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,
775,7131,7906,775,163,5122,5285,163,74496,3968026,4042522,74496,92733,9955366,10048099,92733,1101,22235,23336,1101,237,2071,2308,237,
1403,6366,6994,628,251,4948,5036,88,147372,3646561,3719437,72876,181942,9158109,9247318,89209,1910,20774,21583,809,601,1719,2083,364,
.
.
.
35050,6353,7044,691,6781,4417,4543,126,7072631,1807635,1881169,73534,8500710,4620092,4710709,90617,50606,19746,20707,961,18628,1888,2255,367,
35816,6667,7433,766,6917,4294,4430,136,7146198,1811331,1884898,73567,8590754,4627120,4717164,90044,51650,20123,21167,1044,18965,1853,2190,337,
-----------------------
TotalTime,22501440,Iteration,100
exec count,225014
L1access,71261
L1missrate,0.000943
L2missrate,0.367183
```
<br>

##### 各カラムの説明

それぞれ *Iteration* 回、*L1I/L1D/L2D* について *Refill* 回数と *Access* 回数を取得している。
*PMU* を用いて上記のイベントの回数を取得している。*Total、Start、End、lap* は以下のように表される。

|カラムの名前|説明|
|---|---|
|Start|計測プログラムを始めるときのイベントカウンタの数|
|End|計測プログラムが終わるときのイベントカウンタの数|
|lap|*lap = End - Start*|
|Total|*Total = lap*の総和|

最後に出力されるパラメータは下記の通りである。

|パラメータ名|パラメータの説明|
|---|---|
|TotalTime|*Iteration*回実行した実行サイクルの和|
|Iteration|マクロで定義したプログラムを実行する回数|
|exec count|実行サイクルの平均回数 (*TotalTime / Iteration*)|
|L1access|L1アクセス回数の平均回数|
|L1missrate|L1キャッシュミス率 (*L1-Rifill / L1-Access*)|
|L2missrate|L2キャッシュミス率 (*L2-Refill / L2-Access*)|
