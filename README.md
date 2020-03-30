# 新入生実験用の倒立振子シミュレータ

Ascento や Handle っぽい脚の先端が車輪になっているタイプの倒立振子のシミュレータ．
Electron を使っているので Windows/Mac/Linux に対応．
制御器との接続は HTTP なので大抵の言語で制御器を書くことができる．

## 単にシミュレータを使いたいとき

dist/ 配下のバイナリを実行する．
その後，制御器を実行する．

## シミュレータを開発したいとき

### 必要なもの
* node.js
* git

### ソースコードと必要なパッケージの取得

``` sh
git clone https://github.com/maruta/invpendrace.git
cd invpendrace
npm install
```

### 実行

```sh
npm run start
```

デバッグ用の Developer Tool がドックインされていて画面が狭いので
別ウィンドウに出した方が良い．

### ファイル構成

#### [stage.js](stage.js)
ステージを改造したい場合はこちら

#### [renderer.js](renderer.js)
画面表示（デバッグ情報以外）を改造したい場合はこちら

#### [game.js](game.js)
その他だいたい全部

## 制御器の作り方

[doc/sample_controller](doc/sample_controller) の下に制御器の例があるので詳しくはそちらを参照．

以下はメモ


### MATLAB のサンプル制御器を実行する際の注意事項

* webwrite を使ってローカルマシン内で HTTP POST を投げるのに（少なくともR2019bにおいては）最短30msかかり，実用的な速度で動作しないので Python の requests ライブラリを使う．
* したがって Python をインストールする必要がある．  
    * [公式サイト](https://www.python.org/)から最新の安定板を取得してインストールするが，その際32bit版を間違えてインストールしないように注意する．
    * 特にWindowsの場合，ダウンロードサイトのトップに表示されているのは32bit版へのリンクであるので注意．Windows x86-64 executable installer みたいなやつが良い．
* Python インストール後，ターミナルで下記のコマンドを実行して requests パッケージを取得しておく．
    ```sh
    pip install requests
    ```
* Macの場合，システムの Python2.7 系列がMATLABのデフォになっていることがあり，MATLABの pyenv('Version', ...) コマンドで Python のバージョンを指定する必要がある．

### シミュレータと制御器のインターフェース

* シミュレータと制御器が独立しており，HTTPプロトコルで通信する．
  ポートは8933を使う．
* あまり意味は無いと思われるが，別のPCからシミュレータを操作することもできる．
* 例えば，シミュレータを起動した状態でWebブラウザから  
  http://localhost:8933/api/screenshot  
  にアクセスするとスクリーンショットがブラウザに表示される．  
  IPアドレスがわかれば他のマシンのシミュレータのスクリーンショットも撮れる

### 同期実行

* シミュレータは制御器と同期して動作する．つまり，制御入力が与えられるまで時間が停止し，制御入力が与えられると直ちに1ステップの物理シミュレーションを行う．それが終わるまで応答（出力）を返さない．
* ただし，出力は制御入力を印加する前の時点の情報で生成されたものである．
* 1ステップは 1/60 s であり，シミュレータ内の重力加速度は 9.8 ㎨ である．シミュレータはMKS単位系を使っている．
* 制御器が 1/60 s ごとに制御入力を供給するとシミュレータ内での時間の進行が実時間と一致する．
* 制御器が 60 Hz に調速しない場合，シミュレータはPCの計算速度に律速される．この場合，視覚的なリアリティは失われるが得られる結果は変わらないので 実験時間を短縮できる．
* 制御器が 1/60 s 以内に計算を完了できない場合，シミュレータ内時間の経過はリアルタイムよりも遅くなるが得られる結果は変わらない．つまり，PCの性能によらず同じ実験結果が得られる．
* 内部的には複数台のロボットを扱えるが，今は簡単のために制御入力の印加とステップ実行を不可分にしているため，２つのロボットに同時に制御入力を与える術が存在しない．

## ロボットのモデル

[doc/model.pptx](doc/model.pptx) を参照のこと．

## ライセンス

[LICENSE](LICENSE) を参照のこと．
