---?color=#183454

@css[h1](Androiderに贈る<br>レビュアーの心の健康のために<br>守って欲しい1ポイント)  

---?color=#183454

## @css[h2](自己紹介)
- 福岡侑里奈
- Github:nyanc0
- TIS株式会社(4年目)
- Androidエンジニア

---?color=#183454

## @css[h2](ターゲット)
- これからAndroidアプリを作りたい人 |
- 最近Androidを触り始めた人 |
- なんとなくアプリを作っている人 |

---?color=#183454

@css[h1](レビュアーの心の健康のために)  
@css[h1](「意識」して欲しいこと)

---?color=#183454

新人のコードや<br>レガシーなアプリを見てまずはじめに思うこと

---?color=#183454

読みたくねぇ。。。。

---?color=#183454

## @css[h2](読みたくない理由)

- 無駄なインスタンス
- メソッド・クラス名の不統一
- 無意味なreturn
- 長すぎるメソッド処理

@snap[south-east]
and more....
@snapend

---?color=#183454

## @css[h2](非同期処理を<br>考慮できていない)

---?color=#183454

## @css[h2](これが重なると<br>大きな負債になる)

---?color=#183454

@css[h1](Androidアプリは腐敗しやすく<br>リファクタしづらい)

- アーキテクチャの不在 |
- 複雑なライフサイクル |

---?color=#183454

@css[h1](何でも書けるが故のGod Class)

```java
class MainActivity : AppCompatActivity(), Task.TaskCallback<Response>() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setSupportActionBar(binding.toolbar)
        initFragment()
        // ...
    }

    fun loadTagList(page: Int) {
      // API取得
      task.execute(page, this)
    }

    public override fun onSuccess(result: Response) {
      // データ変換処理
      val tagModel = TagModel()
      tagModel.setName(result.name)

      // View反映
      adapter.addItem(tagModel)
    }
```

@[4-5]
@[9-12]
@[15-17]
@[19-20]

---?color=#183454

@css[h1](Viewは勝手に死んで行く)  
処理が終わるまでOSは待っていてくれない

---?color=#183454

@css[section img]<img src="https://github.com/nyanc0/LT/blob/master/dx/assets/images/smp_l.png?raw=true">

---?color=#183454

<img src="https://github.com/nyanc0/LT/blob/master/dx/assets/images/smp_s.png?raw=true">

---?color=#183454

<img src="https://github.com/nyanc0/LT/blob/master/dx/assets/images/smp_s_death.png?raw=true">

---?color=#183454

@css[h1](Viewは勝手に死んで行く)  

```java
public override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    // レイアウトのセット
    setContentView(R.layout.activity_main)
    // 何かしらの情報をAPIから取得してくる
    repository =
            TagRepository.refreshTags(nextPage, 20, "count")
}

override fun onSuccess(result: TagModel) {
    // このタイミングでViewが生きている保証はない
    renderViews(result)
}
```

@[3-4]
@[5-7]
@[10-13]

---?color=#183454

@snap[north]
MVP,MVVM,AAC
@snapend

@snap[east]
RxJava
@snapend

@snap[midpoint]
@css[h2](解決手法はいろいろある)
@snapend

@snap[south]
Kotlin Coroutines
@snapend

@snap[west]
テストコード
@snapend

---?color=#183454

### @css[h2](業務だけで<br>アーキテクチャやツールの習熟度は<br>上げにくい)

---?color=#183454

## @css[h2](必要性を感じていなければ<br>なかなか身につかない)

---?color=#183454

## @css[h2](一旦、アーキテクチャや方式は<br>忘れよう)

---?color=#183454

## @css[h2](考えるのは、)

---?color=#183454

@css[h1](Viewと非同期処理の分離を<br>「意識する」こと)

---?color=#183454

@css[h1](Viewと非同期処理の分離を<br>「意識する」こと)

新人やレガシーコードの多くがこの問題を抱えている<br>解決できずとも意識するだけで<br>コードの書き方が変わる

---?color=#183454

## @css[h2](具体的には？)

---?color=#183454
### @css[h2](1-1. API/DB処理が返ってきたら<br>まずはView状態をチェックする)
こんなチェックの仕方が発生しないことがベストだが<br>最初はこんなレベルでOK

```java
public override fun onSuccess(result: Result) {
  if(!isActive) return
}
```

---?color=#183454
### @css[h2](1-2. ViewからAPI/DB処理を直接呼ばない)
RepositoryはPresenterやViewModelからよび、

```java
class ViewModel @Inject constructor(
        private val repository: TagRepository,
        private val appSchedulerProvider: SchedulerProvider
){
    fun loadTagList(nextPage: Int): Flowable<Result<List<TagModel>>> {
        return repository.refreshTags(nextPage, 20, "count")
                .map {
                    Result.success(it)
                }.onErrorReturn { e -> Result.failure(e.message ?: "unknown", e) }
                .observeOn(appSchedulerProvider.ui())
                .startWith(Result.inProgress())
    }
}

```

@[5-12]

---?color=#183454
### @css[h2](1-2. ViewからAPI/DB処理を直接呼ばない)
ViewはPresenter,ViewModelを監視するのみ<br>必要ないならdispose

```java
override fun onActivityCreated(savedInstanceState: Bundle?) {
        // Tagデータの取得
        tagsViewModel.loadTagList(1)
                .subscribe {
                    when (it) {
                        is Result.Success -> {
                            renderViews(it.data)
                        }
                    }
                }
                .addTo(compositeDisposable)
    }
```

@[3-11]

---?color=#183454

### @css[h2](2. 生存期間を考える)
アプリ起動中ずっと必要 -> Application  
画面単位で利用するだけ -> Activity,Fragment

```java
  TagView viewModel = new TagViewModel(this); // Activity
  TagView viewModel = new TagViewModel(this); // Fragment
  TagView viewModel = new TagViewModel(getApplicationContext());
```

@css[h2](必要なContextは必要な期間だけ<br>)
@css[h2](「this」が何を指すのかを意識する)

---?color=#183454

### @css[h2](3. ViewにModelの情報を漏らさない)
ViewはModelをそのまま表現するだけ<br>
Modelに対する処理はViewにくるまでで終わらせる

```java
public override fun onSuccess(list: List<TagModel>) {
    // ResponseがModelに変換された状態なので、Adapterにセットするだけ
    if (tagsAdapter != null) {
      tagsAdapter.reset(list)
    } else { // 省略
    }
}
```

@css[h2](データがどうなったのか？をViewの管理から外せる)

---?color=#183454

### @css[h2](4. 言語機能を利用する)
非同期処理を行うための機能に従う

```kt
fun <T> async(context: CoroutineContext = CommonPool, start: CoroutineStart = CoroutineStart.DEFAULT, block: suspend CoroutineScope.() -> T)
        = kotlinx.coroutines.experimental.async(context, start, block)

fun ui(start: CoroutineStart = CoroutineStart.DEFAULT, block: suspend CoroutineScope.() -> Unit)
        = launch(UI, start, block)
```

@css[h2](簡単にかけるし、安全)

---?color=#183454

## @css[h2](大事なのは、)

---?color=#183454

## @css[h2](Viewと非同期処理の分離を<br>「意識する」こと)

---?color=#183454

## @css[h2](「意識」すると<br>勉強意欲が高まります。)  
どうやったら分離できるの？が気になる

---?color=#183454

## @css[h2](「意識」すると<br>コードが変わります。)  
Android独特の動きを考慮できるようになる

---?color=#183454

コードが変わればレビュアーの心が軽くなります。<br>ぜひ助けると思って「意識」してみてください。

---?color=#183454

Fin.
