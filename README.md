# waifu2x-caffe

本ソフトは、画像変換ソフトウェア「[waifu2x](https://github.com/nagadomi/waifu2x)」の変換機能のみを、
[Caffe](http://caffe.berkeleyvision.org/)を用いて書き直したソフトです。


 要求環境
----------

このソフトを動作させるには最低でも以下の環境が必要です。

 * OS : Windows Vista以降 64bit (32bit用exeはありません)
 * メモリ : 空きメモリ1GB以上 (ただし、変換する画像サイズによる)
 * Visual C++ 2013 再頒布可能パッケージがインストールされていること(必須)
    - 上記パッケージは[こちら](https://www.microsoft.com/ja-jp/download/details.aspx?id=40784)
    - `ダウンロード` ボタンを押した後、`vcredist_x64.exe`を選択し、ダウンロード・インストールを行って下さい。
    - 見つからない場合は、「Visual C++ 2013 再頒布可能パッケージ」で検索してみて下さい。

cuDNNで変換する場合はさらに

 * GPU : Compute Capability 3.0 以上のNVIDIA製GPU

自分のGPUのCompute Capabilityが知りたい場合は[こちらのページ](https://developer.nvidia.com/cuda-gpus)で調べて下さい。


 cuDNNについて
--------

cuDNNはNVIDIA製GPUでのみつかえる高速な機械学習向けのライブラリです。
cuDNNを使うと使わない場合に比べて大きな画像を高速に変換することが出来ますが(cuDNNを使わなくてもGPUで変換出来ます)、
ライセンスの関係上動作に必要なファイルを配布することが出来ません。
なので、cuDNNを使いたい人は[こちらのページ](https://developer.nvidia.com/cuDNN)でWindows向けバイナリをダウンロードし、
「cudnn64_65.dll」をwaifu2x-caffeのフォルダに入れて下さい。
(cuDNNをダウンロードするにはNVIDIA Developerへの登録とCUDA Registered Developersへの登録が必要です。
 CUDA Registered Developersはおそらく(簡単な)審査があるっぽいので登録してもすぐにcuDNNをダウンロード出来るわけではありません。)


 使い方
--------

「waifu2x-caffe.exe」はGUIソフトです。ダブルクリックで起動します。

「入力」欄に画像かフォルダをドラッグ&ドロップで放り込むと「出力」欄が自動で設定されます。
出力先を変えたい場合は「出力」欄を変更して下さい。

好みに合わせて変換設定を変更することが出来ます。

「変換モード」
     変換モードを指定します。
      * ノイズ除去 : ノイズ除去を行います
      * 拡大 : 拡大を行います
      * ノイズ除去と拡大 : ノイズ除去と拡大を行います 
      * ノイズ除去(自動判別)と拡大 : 拡大を行います。入力がJPEG画像の場合のみノイズ除去も行います

「JPEGノイズ除去レベル」
    ノイズ除去レベルを指定します。

「拡大率」
    拡大率を指定します

「出力拡張子」
    出力拡張子を指定します

「プロセッサー」
    変換を行うプロセッサーを指定します
      * CPU : CPUのみを使って変換を行います
      * GPU(使えたらcuDNN) : GPUを使って変換を行います(cuDNNが使えるならcuDNNが使われます)

「cuDNNチェック」ボタンを押すとcuDNNが使えるか調べることが出来ます。
が、cuDNNが動く環境でしか動かしていないためうまくチェック出来るかは不明です。

「実行」ボタンを押すと変換が始まります。
途中でキャンセルしたい場合は「キャンセル」ボタンを押します。
ただし、実際に停止するまでタイムラグがあります。


「waifu2x-caffe-cui.exe」はコマンドラインツールです。
`コマンドプロンプト` を立ち上げ、次のようにコマンドを打ち込み、使用して下さい。


以下のコマンドは、使い方を画面に出力します。
```
waifu2x-caffe-cui.exe --help
```

以下のコマンドは、画像変換を実行するコマンドの例です。
```
waifu2x-caffe-cui.exe -i mywaifu.png -m noise_scale -j 8 --scale_ratio 1.6 --noise_level 2
```
以上を実行すると、`mywaifu(noise_scale)(Level2)(x1.600000).png`に変換結果が保存されます。


本ソフトでは、以下のオプションを指定することが出来ます。

   -i <文字列>,  --input_file <文字列>
     (必須)  変換する画像へのパス
     フォルダを指定した場合、そのフォルダ以下の画像ファイルを全て変換してoutput_fileで指定したフォルダへ出力します。

   -o <string>,  --output_file <string>
     変換された画像を保存するファイルへのパス
     (input_fileがフォルダの場合)変換された画像を保存するフォルダへのパス
     (input_fileが画像ファイルの場合)拡張子(最後の.pngなど)は必ず入力するようにして下さい。
     指定しなかった場合は自動でファイル名を決定し、そのファイルに保存します。
     ファイル名の決定ルールは、
     `[元の画像ファイル名]``(モード名)``(ノイズ除去レベル(ノイズ除去モードの場合))``(拡大率(拡大モードの場合))``.png`
     のようになっています。
     保存される場所は、基本的には入力画像と同じディレクトリになります。


   -l <文字列>,  --input_extention_list <文字列>
     input_fileがフォルダの場合の、フォルダ内の変換する画像の拡張子を指定します。
     デフォルト値は`png:jpg:bmp`です。
     また、区切り文字は`:`です。
     例. png:jpg:bmp

   -l <文字列>,  --input_extention_list <文字列>
     input_fileがフォルダの場合の、出力画像の拡張子を指定します。
     デフォルト値は`png`です。

   -m <noise|scale|noise_scale>,  --mode <noise|scale|noise_scale>
     変換モードを指定します。指定しなかった場合は`noise_scale`が選択されます。
      * noise : ノイズ除去を行います (正確には、ノイズ除去用のモデルを用いて画像変換を行います)
      * scale : 拡大を行います (正確には、既存アルゴリズムで拡大した後に、拡大画像補完用のモデルを用いて画像変換を行います)
      * noise_scale : ノイズ除去と拡大を行います (ノイズ除去を行った後に、引き続き拡大処理を行います)
      * auto_scale : 拡大を行います。入力がJPEG画像の場合のみノイズ除去も行います

   -s <小数点付き数値>, --scale_ratio <小数点付き数値>
     何倍に拡大するかを指定します。デフォルト値は`2.0`ですが、2.0倍以外も指定できます。
     2.0以外の数値を指定すると、次のような処理を行います。
      * まず、指定された倍率を必要十分にカバーするように、2倍拡大を繰り返し行います。
      * 2の累乗以外の数値がしてされている場合は、指定倍率になるように拡大した画像を線形フィルタで縮小します。

   -n <1|2>, --noise_level <1|2>
     ノイズ除去レベルを指定します。ノイズ除去用のモデルはレベル1とレベル2のみ用意されているので、
      1 もしくは 2 を指定して下さい。
     デフォルト値は`1`です。

   --model_dir <文字列>
     モデルが格納されているディレクトリへのパスを指定します。デフォルト値は`models`です。
     基本的には指定しなくても大丈夫です。独自のモデルを使用する時などに指定して下さい。

   --,  --ignore_rest
     このオプションが指定された後の全てのオプションを無視します。
     スクリプト・バッチファイル用です。

   --version
     バージョン情報を出力し、終了します。

   -h,  --help
     使い方を表示し、終了します。
     手軽に使い方を確認したい時などにどうぞ。


 おことわり
------------

本ソフトは無保証です。
利用者の判断の下に使用して下さい。
制作者はいかなる義務も負わないものとします。


 謝辞
------
オリジナルのwaifu2x、及びモデルの制作を行い、MITライセンスの下で公開して下さった ultraistさん、
オリジナルのwaifu2xを元にwaifu2x-converterを作成して下さった アミーゴさん(READMEやLICENSE.txtの書き方、OpenCVの使い方等かなり参考にさせていただきました)
に、感謝します。