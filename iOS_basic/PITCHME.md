## @css[h2](Androidエンジニアが<br>SwiftでiOSアプリを作った)
---
アプリ開発始めて4年経って、  
iOSを(やっと)本格的に触ったので、  
n番煎じですがiOSを始めるポイントとか  
感想とかを共有します。

---
## @css[h2](今回作成したアプリ)

---
## @css[h2](お品書き)
1. どんな知識が必要なの？
2. どう作っていったの？
3. 作れるようになるためのポイント
4. まとめ

---
## @css[h2](1. どんな知識が必要なの？)

---
## @css[h2](1. どんな知識が必要なの？)
- **Must:** ググり方
- **Want:** iOSアプリってXcodeで作るんだよね、Swiftで作るんだよねぐらいは知っている
- **Want:** これまで何らかの言語で開発した経験がある
- **Want:** 何となくiOSアプリ開発時に利用する部品の名前を知っている(StoryboardとかViewControllerとか)
- **Want:** MVP,MVC,MVVMなどのアーキテクチャに関して知識がある

---
## @css[h2](1. どんな知識が必要なの？)

@css[h2](ググり方)

---
## @css[h2](1. どんな知識が必要なの？)

プログラミング経験があるかどうか、より、  
情報を自分で拾ってこられるか？  
の方が大事

---
## @css[h2](ちなみに私の技術レベル)
- Java歴： 4年
- Kotlin歴： 1年半(業務経験はなし。個人開発レベル)
- Androidアプリ作成歴： 4年
- iOSの知識： 開発時に何が必要か、Androidと比較してどういう違いや特徴があるか、Apple様のガイドラインは知ってるレベル

とはいえ、  
開発経験があるに越したことはないです。

---
## @css[h2](2. どう作っていったの？)

---
## @css[h2](2. どう作っていったの？)
こんな順番で作っていきました。

1. アーキテクチャ選定
2. ライブラリの選定
3. モデルの設計
4. 基盤(データソース)周りの作成
5. UI周りの作成

---
## @css[h2](2-1. アーキテクチャ選定)
![](https://github.com/nyanc0/LT/blob/master/iOS_basic/images/app_architecture.png?raw=true)

---
## @css[h2](2-1. アーキテクチャ選定)

今回はAndroidの場合とどこが変わるのか？を知りたかったので、  
Androidのデモアプリと同様にMVVM + CleanArchitectureを選びました。

---
## @css[h2](2-2. ライブラリの選定)
今回はプロジェクトで利用しているものを使いたい＆  
iOS標準をなるべく使いたいという観点でライブラリを選びました。

---
## @css[h2](2-2. ライブラリの選定)

|ライブラリ名|用途|
|---|---|
|SwiftLint|Lintチェック|
|RxSwift|各層のつなぎ,Event通知,ViewへのBinding|
|RxCocoa|各層のつなぎ,Event通知,ViewへのBinding|
|RealmSwift|データ保存|
|SDWebImage|画像ロード|
|RxDataSources|Collection,TableViewで利用|
|MaterialComponents|FloatingButton, CardViewの利用|
|RxBlocking|ObservableをBlockingObservableに変換して結果が返ってくるまでロックし、テストできるようにする|
|RxTest|指定した時刻にObservableにイベントを発行することができるスケジューラ|
|Mockingjay|APIのMock|

---
## @css[h2](2-3. モデルの設計)
```swift
struct Recipe: Codable {
    let genreCd: String
    let genreName: String
    let recipeId: String
    let recipeName: String
    let introduction: String
    let mainUrl: String
    let recommendedFlg: String
    let cookingIngredients: [CookingIngredients]
    let cookingMethod: [CookingMethod]
    // 省略
}
```

---
## @css[h2](2-3. モデルの設計)
まずはアプリで扱う情報を決めました。  
JsonパースにCodableを利用する想定だった＆  
mapper挟むのが面倒だったので、ユビキタス言語が・・・とかまで厳密にしていません。

---
## @css[h2](2-4. 基盤周りの作成)

```
┗ Data
    ┗ DS ： Realmを操作するDao
    ┗ NW ： APIへのアクセスをするManager
    ┗ Repository ： データ操作を行うRepositoryの具象クラス
┗ Domain
    ┗ UseCase ： 各画面のユースケース
    ┗ Repository ： UseCaseから呼びだすデータ操作のInterface
```

---
## @css[h2](2-4. 基盤周りの作成)
View → ViewModel → DataのつなぎはRepositoryを挟み、

---
## @css[h2](2-4. 基盤周りの作成)

```swift
// DomainのRepository
protocol RecipeListRepository {
    func getRecipeList() -> Single<[Recipe]>
    func getRecipeList(recipeIds: [String]) -> Single<[Recipe]>
    func getRecipeList(reccomendFlg: String) -> Single<[Recipe]>
}

// DataのRepository
class RecipeListRepositoryImpl: RecipeListRepository {

    /// 全件取得
    /// - returns: Single<[Recipe]>
    func getRecipeList() -> Single<[Recipe]> {
        return WebAPIManager.observe(RecipeListRequest(queries: []))
    }

    // 省略
```

---
## @css[h2](2-4. 基盤周りの作成)
ViewはUseCaseを呼び出すようにすることで、  
Viewを実装する時にデータ周りの処理を考えなくても良いようにしました。

---
## @css[h2](2-4. 基盤周りの作成)

```swift
class RecipeListUseCase {

    private let recipeListRepository: RecipeListRepository

    init(recipeListRepository: RecipeListRepository) {
        self.recipeListRepository = recipeListRepository
    }

    func loadRecipeList() -> Single<[Recipe]> {
        return recipeListRepository.getRecipeList()
    }

    func loadReccomendRecipe() -> Single<[Recipe]> {
        return recipeListRepository.getRecipeList(reccomendFlg: "1")
    }
}
```

---
## @css[h2](2-5. UI周りの作成)
```
┗ Presentation
    ┗ Navigator ： 画面遷移をするsegueの呼び出し
    ┗ ViewController ： 画面のStoryboardとViewController
    ┗ Common ： View層で共通利用するRxの変換関数など
    ┗ View ： CellなどのUI部品のxib, UIクラス
    ┗ ViewModel ： 各画面のViewControllerで利用するViewModel
```

---
## @css[h2](2-5. UI周りの作成)

一番時間がかかり、  
一番iOSらしさが出るのがここ

---
## @css[h2](2-5. UI周りの作成)

まずは[Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)を読みましょう。

---
## @css[h2](3. 作れるようになるためのポイント)

ポイントを5つ  
(多い)

---
## @css[h2](3. 作れるようになるためのポイント)

### 1. エラーに向き合う
詰まった時こそ、  
勉強のチャンスだと捉えて取り組む。  
エラー特定の仕方をまず身につける。

---
## @css[h2](3. 作れるようになるためのポイント)

### 2. 理解してコピペする

コピペ自体は悪くない(と思う)。  
コピペする処理を理解し、  
どこが良いのか悪いのかを考えてコピペする。

---
## @css[h2](3. 作れるようになるためのポイント)

### 3. 要素を詰め込みすぎない

最初からあれも、これもをすると理解するものが多すぎて基礎がおろそかになる。  
最低限必要なところからスタートする。  
(Rx導入してかなり大変でした)

---
## @css[h2](3. 作れるようになるためのポイント)
### 4. できればテストを書く

テストが書ける =  
テストできるコードがかけている＆
処理を理解できている

データ層のテストを書けば、  
Viewの実装に安心して入れる。

---
## @css[h2](3. 作れるようになるためのポイント)
### 5. ライブラリをうまく利用する

一から全て作る必要はなし。  
楽になる方法を考えよう。
(できれば標準機能は使ってみた上で)

---

とりあえず書いてみよう！でも何とかなるので、  
ぜひチャレンジしてみてください。

---
## @css[h2](おまけ：ここが面倒、iOS)

- ライブラリをいれないとリソースとかID定義が文字列ベタがき
    - AndroidならID自動振り・・・
- Storyboardの制約設定面倒
    - Androidならwrapも使えるのに・・・
- StoryboardとViewのつなぎが面倒
    - いちいち引っ張って来なきゃだめ？

---

Fin.
