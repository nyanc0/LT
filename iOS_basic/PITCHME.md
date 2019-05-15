## AndroidエンジニアがSwiftでiOSアプリを作った時に感じたこと
---
アプリ開発始めて4年経って、  
iOSをやっと本格的に触ったので、  
n番煎じですがその感想を共有します。

---
## 今回作成したアプリ

---
## お品書き
1. どんな知識が必要なの？
2. どう作っていったの？
3. どこに注意が必要？
4. やっていてよかったこと

---
## どんな知識が必要なの？

---
## どんな知識が必要なの？
- **Must:** これまで何らかの言語で開発した経験がある
- **Must:** iOSアプリってXcodeで作るんだよね、Swiftで作るんだよねぐらいは知っている
- **Want:** 何となくiOSアプリ開発時に利用する部品の名前を知っている(StoryboardとかViewControllerとか)
- **Want:** MVP,MVC,MVVMなどのアーキテクチャに関して知識がある

プログラミング初心者が、  
何の知識もなく入るにはちょっとハードル高い

---
## ちなみに私の技術レベル
- Java歴： 4年
- Kotlin歴： 1年半(業務経験はなし。個人開発レベル)
- Androidアプリ作成歴： 4年
- iOSの知識： 開発時に何が必要か、Androidと比較してどういう違いや特徴があるか、Apple様のガイドラインは知ってるレベル

---
## どう作っていったの？

---
## どう作っていったの？
こんな順番で作っていきました。

1. アーキテクチャ選定
2. ライブラリの選定
3. モデルの設計
4. 基盤(データソース)周りの作成
5. UI周りの作成

---
## 1. アーキテクチャ選定
![](https://github.com/nyanc0/LT/blob/master/iOS_basic/images/app_architecture.png?raw=true)

今回はAndroidの場合とどこが変わるのか？を知りたかったので、  
Androidのデモアプリと同様にMVVM + CleanArchitectureを選びました。

---
## 2. ライブラリの選定
今回はプロジェクトで利用しているものを使いたい＆  
iOS標準をなるべく使いたいという観点でライブラリを選びました。

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
## 3. モデルの設計
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

まずはアプリで扱う情報を決めました。  
JsonパースにCodableを利用する想定だった＆  
mapper挟むのが面倒だったので、ユビキタス言語が・・・とかまで厳密にしていません。

---
## 4. 基盤(データソース)周りの作成

```
┗ Data
    ┗ DS ： Realmを操作するDao
    ┗ NW ： APIへのアクセスをするManager
    ┗ Repository ： データ操作を行うRepositoryの具象クラス
┗ Domain
    ┗ UseCase ： 各画面のユースケース
    ┗ Repository ： UseCaseから呼びだすデータ操作のInterface
```

次に、DB操作やAPIアクセスする部分の作成をしました。

---
## 4. 基盤(データソース)周りの作成
View → ViewModel → DataのつなぎはRepositoryを挟み、

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
## 4. 基盤(データソース)周りの作成
ViewはUseCaseを呼び出すようにすることで、  
Viewを実装する時にデータ周りの処理を考えなくても良いようにしました。

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
## 5. UI周りの作成
```
┗ Presentation
    ┗ Navigator ： 画面遷移をするsegueの呼び出し
    ┗ ViewController ： 画面のStoryboardとViewController
    ┗ Common ： View層で共通利用するRxの変換関数など
    ┗ View ： CellなどのUI部品のxib, UIクラス
    ┗ ViewModel ： 各画面のViewControllerで利用するViewModel
```

最後にView周りの作成を行いました。  
iOS特有の部分はだいたいこの部分に集約されます。


---
## どこに注意が必要？

- Viewの作成にかなり時間がかかる
    - 実装時間のほとんどがここにかかる。
    - iOS特有の動きを理解するのに時間が必要。
- エラー原因の特定の仕方をちゃんと調べる
    - Xcodeでエラーをどう追えばいいのか？は早い段階で調査しておく
    - デバッグの仕方も必須
- RxSwiftは初心者は手を出さない
    - Rxの設計部分に一番時間が割かれて勿体無い
    - 初心者には多分必要ない(PJが使っていれば別)
- Storyboardと仲良くなるには時間がかかることを覚悟する

---
## やっていてよかったこと

- Data,Domain周りのテストコード作成
    - 動作保証されているのでViewの実装に集中できる
    - 修正の影響が出ているかがチェックしやすい
- ライブラリの利用
    - CardViewっぽい見た目やFloatingButtonは自力でやるのやめよう

---

とりあえず書いてみよう！でも何とかなるので、  
ぜひチャレンジしてみてください。

---
## おまけ：ここが面倒、iOS

- ライブラリをいれないとリソースとかID定義が文字列ベタがき
    - AndroidならID自動的に振ってくれるのに・・・
- Storyboardの制約設定面倒
    - AndroidのConstraintLayoutならwrapも使えるのに・・・
- StoryboardとViewのつなぎが面倒
    - いちいち引っ張って来なきゃだめ？

---

Fin.
