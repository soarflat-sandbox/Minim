# BeatDetectクラスを使ってみる
BeatDetectクラスを利用すると、オーディオストリームのビートを分析（検出?）できる。

## ビートとは
>音響において、ビートは周波数がわずかに異なる2つの音の間の干渉パターンであり、その割合は2つの周波数の差である音量の周期的な変化として知覚される。

[Beat (acoustics)](https://en.wikipedia.org/wiki/Beat_(acoustics))

>人間の音を捉える仕組みは、連続する音の周期的な拍を検出することによって、音楽のリズムを決定する。耳で傍受される信号には一定のエネルギーが含まれ、このエネルギーは脳が解釈する電気信号に変換される。そのため、音が伝わるエネルギーが大きければ大きいほど、音は大きいと思われるのは明らかである。しかし、現在の音のエネルギーが過去の音のエネルギー履歴よりもはるかに優れている、つまり脳が音のエネルギーの粗暴な変化を検出した場合にのみ、音がビートとして聞こえる。したがって、耳が時々大きなエネルギーピークを伴う単調な音を傍受すると、ビートを検出するが、連続した大きな音を鳴らすと、ビートは認識されない。したがって、ビートは音エネルギーの大きな変化のこと。

[BeatDetect](http://code.compartmental.net/minim/beatdetect_class_beatdetect.html)

ちょっと良くわかっていないが、音の大きな変化のことであり、BeatDetectを利用すればビート（音の大きな変化）を分析（取得）できると思っていればとりあえずは良さそう。

## オーディオストリームとは
オーディオのストリーム（stream）のこと。

### ストリーム（stream）とは
データを「流れるもの」として捉え、流れ込んでくるデータを入力、流れ出て行くデータを出力として扱う抽象データ型のこと。

# BeatDetectクラスを利用するには
BeatDetectクラスを利用するためには、以下のように`draw()`関数の中で、最初に`detect()`を呼び出して解析したいAudioBufferを渡す必要がある。

次に`isKick()`、`isHat()`関数などを使用して、そのフレームでどのビートが発生したか調べることができる。

```processing
// 利用方法のイメージなので、このコードは動かない
void draw() {
    beat.detect(player.mix);

    if (beat.isKick()) kickSize = 32;
    if (beat.isSnare()) snareSize = 32;
    if (beat.isHat()) hatSize = 32;
}
```

## `SOUND_ENERGY`モードと`FREQ_ENERGY`モード
BeatDetectクラスには`SOUND_ENERGY`と`FREQ_ENERGY`の2つのモードがある。

インスタンスを生成する際に引数を渡すか渡さないかでモードが変わる。

```processing
// SOUND_ENERGYモードになる
beat = new BeatDetect();

// FREQ_ENERGYモードになる
beat = new BeatDetect(bufferSize, sampleRate);
```

### `SOUND_ENERGY`
`SOUND_ENERGY`モードでは、`level()`によって返されるバッファのレベルが各フレームのインスタントエネルギー（描画などに利用する値）として使用される。ビートは、この値のスパイク（とがった波形を持つ、瞬間的に上昇・下降する音信号）で、前の1秒の音に比例する。

`SOUND_ENERGY`モードでは、`isOnset()`を利用して、アルゴリズムを照会する。

### `FREQ_ENERGY`
`FREQ_ENERGY`モードでは同じプロセスが使用されるが、バッファのレベルを取得する代わりに、FFTを使用してスペクトルを取得し、`logAverages（）`を使用して平均帯域に分割し、これらの帯域のそれぞれを個別に追跡する。その結果、キックドラムやスネアドラムのように、周波数スペクトルの異なる部分で独立して発生する音をトラッキングすることが可能になる。ようは周波数毎のデータを取得できる。

`FREQ_ENERGY`モードでは、`isOnset(int i)`、`isKick()`、`isSnare()`、 `isRange()`を利用して、特定の周波数帯域または周波数帯域の範囲を照会する。