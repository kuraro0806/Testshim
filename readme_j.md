# SHIMの遅延値計測、挿入

- [SHIMの遅延値計測、挿入](#shimの遅延値計測挿入)
  - [はじめに](#はじめに)
  - [遅延値計測](#遅延値計測)
  - [遅延値挿入](#遅延値挿入)

## はじめに

本ディレクトリ *openshim* では、ハードウェア抽象化である *SHIM* の遅延値(*latency*)を取り扱うための機能を提供します。

以下の手順で作業を行ってください。


 1. [遅延値計測](#遅延値計測)
 2. [遅延値挿入](#遅延値挿入)

## 遅延値計測

実機で計測プログラムを実行して実行サイクル等を出力します。

[*measure*](./measure) ディレクトリにて提供しています。

## 遅延値挿入

先の工程で得たサイクル数を用いて遅延値を計算します。

[*latency*](./latency)ディレクトリにて提供しています。