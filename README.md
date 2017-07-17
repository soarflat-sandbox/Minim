# Minim
Processingのオーディオライブラリ

# オーディオに関する基礎知識
ドキュメントを理解するために必要な、オーディオに関する基礎知識を記載していく。

## バッファ（buffer）
バッファ（buffer）とはデータを一時的に貯めておくデータ領域のこと。

主に先読みしたデータを貯める。

### 何故、先読みしたデータを貯めるのか

データ送信が途切れた際でも、連続するデータの流れが途切れない（動画や音楽の再生が途中で止まらない）ようにするため。

### バッファの利用例

バッファは様々なサービスやシーンで利用されている。例としてYoutubeを挙げてみる。

<img src="README_resources/introduction/buffer.jpg" width="853">

再生位置の右側の、薄いグレーの線がバッファである。

バッファ内の再生位置に移動しても、すぐに動画が再生される。これは上記の通り先読みしたデータが貯められているからである。

逆にバッファ外の再生位置に移動したり、バッファがない状態で再生をしていると動画が途中で止まったり、読み込みに時間がかかることがある。こういった状況を極力防ぐためにバッファが利用されている。

## バッファサイズ
バッファ（データを一時的に貯めておくデータ領域）のサイズのこと。