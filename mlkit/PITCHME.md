# 今さらML Kitを触ったのでついでにKotlin Croutineも入れてみた

---

作成したプロジェクトは[こちら](https://github.com/nyanc0/MLKitSample)

---

## MLKitって何？

- Firebaseが提供する機械学習を組み込むためのSDK
- iOS/Androidどちらでも使える

---

## on-deviceとcloud

- on-device
    - オフラインでも実行できる
    - 軽い
 - cloud
    - 高機能
    - オンライン必須
    - 従量課金(1000アクセス/月まで無料)

---

## 提供する機能

||on-device|cloud|
|---|---|---|
|Image labeling 画像のラベリング|○|○|
|Text Recognition テキスト認証|○|-|
|Face Detection 顔認証|○|-|
|Barcode scanning バーコードスキャン|○|-|
|Landmark Detection ランドマーク認証|-|○|

---

## 使い方


---

## 使ってみた感触

- 認証自体のコードは簡単に書けて手軽
- i/Aで同じ機構が利用できるのは便利
- 複雑な認証をしたい場合にはデータが物足りないこともあるかも

---

## Kotlin Croutineって何？

- Kotlin 1.3からexperimentalが外れた、coroutine機能
- 「中断可能な計算インスタンス」を提供し、非同期処理を行いやすい
- AndroidではRxの代替として利用されることが多いが、Rxとは全く違うもの

---

詳しくは[こちら](https://qiita.com/k-kagurazaka@github/items/8595ca60a5c8d31bbe37)がとても勉強になります。

---

## 今回の使い所

今回は下記の場所でCoroutineを利用しています。

- Firebase解析APIの非同期処理
- Bitmap変換(リサイズ周り)

---

## 今回のポイント

FirebaseVisionのDetection処理はコールバック形式になっているため、  
suspendCoroutineを使いCoroutine関数に変換をすることで中断できる関数に変換をしています。

---

### Repositoryの実装

```kt
@WorkerThread
    suspend fun startFirebase(bitmap: Bitmap, detector: Detector): Result<MutableList<Graphic>> {

        val image = FirebaseVisionImage.fromBitmap(bitmap)
        val firebaseVision = FirebaseVision.getInstance()

        // ここでCoroutineに変換
        return suspendCoroutine { cont ->
            when (detector) {
                Detector.TEXT_DETECTION -> {
                    firebaseVision.onDeviceTextRecognizer
                        .processImage(image)
                        .addOnSuccessListener { texts ->
                          // 省略
                          // ここで値を返す
                            cont.resume(Result.success(result))
                        }.addOnFailureListener { exception ->
                          // ここで値を返す
                          // 今回はResultオブジェクトを自作しているが、
                          // エラーの場合はresumeWithExceptionでもOK
                            cont.resume(Result.failure(exception.message, exception))
                        }
                }
                Detector.CLOUD_TEXT_DETECTION -> {
                    val options = FirebaseVisionCloudTextRecognizerOptions.Builder()
                        .setModelType(FirebaseVisionCloudDetectorOptions
```

---

### 呼び出し側の実装

```kt
fun detect(detector: Detector) {
    bitmap.value?.let {
        // Scope
        launch {
            // awaitで処理を中断
            val result = withContext(Dispatchers.Default) {
                FirebaseRepository.startFirebase(it, detector)
            }
            // 取得した結果で処理
            when (result) {
                is Result.Success -> {
                    mutableGraphics.postValue(result.data)
                }
                is Result.Failure -> {
                    mutableGraphics.postValue(null)
                }
            }
        }
    }
}
```

---

## 使ってみた感触

- 非同期処理が描きやすい
- Rxに比べて学習コストが低い(個人的感想)
- Androidで非同期のために使うなら選択肢としてかなりあり

---

Kotlin Cotoutineについては、  
どこかでまた詳しくかければいいなと思います。

---

Fin
