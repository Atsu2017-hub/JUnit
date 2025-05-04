# JUnit

## assertメソッド群
- assertEquals(expected, actual) 値の比較, double比較は第3引数に誤差範囲を追加
- assertThrows(例外.class, () -> {例外期待コード}) 例外期待コード中で指定した例外が発生すればOK
- assertThat(actual, is(expedted)) Matcherオブジェクトに基づいた比較
  - is()では値が渡されるとMatcherを作成し、Matcherが渡されるとそのまま返す。
  - assertThatは中でmatcher.matches(actual)を呼びだす(matcharは第二引数)。
  - matcherの正体はIsEqualsオブジェクト。IsEqualsオブジェクトはexpectedのフィールドを保持している。またmatchesメソッド(戻り値boolean)が定義されていて実装必須。このメソッドで比較している。
  - デフォルトのIsEqualsのmatchesメソッドではequalsによる比較。
  - プリミティブ型はオートボクシング	int → Integerされ、equalsによって内容比較。
  - MatcherのサブクラスがIsEqualやIsNot(Matcherクラスを保持し、matchメソッドでは正誤を反転)
  - ~~~Java
    public static <T> Matcher<T> not(T value) {
      return not(equalTo(value)); //equalTo(value)はisEqualオブジェクトを返す。
    }
    public static <T> Matcher<T> not(Matcher<T> matcher) {
      return new IsNot<>(matcher);
    }
    public static <T> Matcher<T> is(T value) {
      return equalTo(value);
    }
    public static <T> Matcher<T> is(Matcher<T> matcher) {
      return matcher;
    }
    ~~~
  - 例：assertThat(actual, is(not(expected)))
    1. not(expected)でIsNotオブジェクト(IsEqualオブジェクト(expectedがフィールド)を保持)が生成。
    2. assertThat内部でIsNotオブジェクトのmatches(actual)メソッドが呼び出される。
    3. このmatchesメソッドでは`!IsEquals.matches(actual)`を返す。
    4. IsEquals.matchesではexpected.equal(actual)を検証
    5. このようにして委譲していく。
  - describeToではDescriptionに文字列を入れていく。appendValueは""付きで表示され、appendTextはなし。
## その他
- Throwableには、ErrorとExceptionがある。
  - Errorは回復不可能(スタックオーバーフロー、メモリ不足等)、try-catchは推奨されない。
  - ExceptionクラスはRuntimeExceptionクラス(配列の未定義indexの参照など)とその他のクラス(IOExceptionなど)に分類される。その他のクラスは検査例外でRuntimeExceptionクラスは非検査例外(try-catchいらない)。
  - `==`：参照の比較（アドレスの同一性）
  - `.equals()`：内容の比較（オブジェクトのフィールドの等価性）
  - .classpathにはclaspathentryが存在。プロジェクトをIDE上に再現、展開する。
  - |kindの値|意味|例|
    |:-------|:---|:-|
    |src|ソースフォルダ|path="src" は src をクラスパスに含める|
    |lib|	JARファイルなどのライブラリ|path="lib/some-lib.jar"|
    |con|	Eclipseのコンテナ（JREとか)|path="org.eclipse.jdt.launching.JRE_CONTAINER"|
    |output|コンパイル後のクラス出力先|path="bin" など|
  - enumについて
    - ~~~Java
      public class OuterClass {
        public enum Color {
          RED, GREEN, BLUE
        }
      }
      ~~~
      このときOuterClass.Color.REDで指定。import static OuterClass.Color.REDでREDで指定できる。
      メリットはカプセル化、enumの複数定義可能。
  - Javaの三項if文
    - 条件式 ? 真の場合の値 : 偽の場合の値
    - 式なので値を返す。代入文やreturnに使える。
  - staticについて
    - static class
      - 内部クラスで使用
      - 外部クラスのstaticなメンバのみのアクセス可能。(クラスの所有物なので。非static変数はクラスの領域には確保されず、インスタンス生成時にメモリ確保)
  - テストメソッドごとにそのテストクラスがインスタンス化される。
  - Methodクラスについて
    - Methodクラスの変数mは、メソッドの定義情報(メタ情報)を表すオブジェクトへの参照(どのクラスのどんなメソッドか)。
    - メタ情報：プログラムの要素(クラス、メソッド、フィールドなど)に関する情報そのもの。.classファイルに存在。
      - ~~~Java
        Method m = MyClass.class.getMethod("sayHello");
        m.invoke(myInstance);
        ~~~
      - MyClass.classで.classファイルの読み込み。Classオブジェクトとして保持。このクラスオブジェクトはMyClassの構造(フィールド、メソッド)などを保持。
      - getMethodでClassオブジェクトからsayHelloのメソッドオブジェクトを返す。
      - Methodオブジェクトのinvokeメソッドで、JVMに「このインスタンスのこのメソッドを実行して」と頼む。
    - ~~~Java
        for (Method testMethod : testMethods) {
          MyTest testInstance = new MyTest();     // ← インスタンス生成
          testInstance.setUp();                   // @Before 呼び出し
          testMethod.invoke(testInstance);        // テスト実行
        }
        ~~~
    - 匿名クラスについて
      - アクセス可能なもの
        - 外部クラスのインスタンス変数
        - 外部クラスのメソッド
        - 外部メソッドの実質的(変更されない)finalな変数(ローカル変数)
      - 匿名クラスが外部クラスのインスタンスに暗黙的にリンクされているため。また外部メソッドの変数がコピーされている。
      - 外部メソッドが終了すると、そのローカル変数は消える。この時、匿名クラスでその変数を参照できなくなる。
    - プラグイン：開発環境(Eclipseなど)を拡張。Eclipseを便利にするツール。
      - EclipseやIDE自体が使う
    - ライブラリ：アプリケーションに機能を追加。自分のアプリで使う部品(コード集)
      - Javaアプリケーションのコードで使う
    - JUnitの場合：
      - JUnitプラグイン（Eclipseの一部）
      - → テストクラスの自動生成、右クリックでテスト実行などの機能をEclipseに追加。→ Eclipseの「UI機能」「ランチャー」を提供。

    - JUnitライブラリ（jar）
    - → 実際に @Test を使ってテストコードを書くときに必要なクラス。→ プロジェクトのビルドパスに追加されるべきもの。

## JUnit4からJUnit5への変更
- メソッドはpublic付けなくてもよい(private以外)
- assertThatはJUnit5には組み込まれていない(外部ライブラリ↓を導入する必要あり)
  - import static org.hamcrest.MatcherAssert.*;
  - import static org.hamcrest.Matchers.*;
- @Test(expected or timeout = ...)からAssertThrows, @Timeoutに変更
- Before, BeforeClassからBeforeEach, BeforeAllに変更。Afterも同様。
- Enclosedから@Nestedへ
  - ~~~Java
    class SampleTest {
      @Nested
      class テストパターン1 {
       
      }

      @Nested
      class テストパターン2 {
        
      }
    }
    ~~~
- パラメータ化テスト
  - Theoriesから以下に変更
  - ~~~Java
    @ParameterizedTest
    @ValueSource(strings = { "foo", "bar", "baz" })
    void 文字列テスト(String keyword) throws Exception {
      // do something
    }
    ~~~
  - JUnit5 では: 繰り返し対象がプリミティブ型の場合は, @ParameterizedTest + @ValueSource. 繰り返し対象がオブジェクトの場合は, @ParameterizedTest + @MethodSource(組み合わせ可能)
  - @Rulesが廃止、代わりに@ExtendWith(VerifierExtension.class)。

## Maven
- JUnit Platform：JUnit5の実行基盤。テストの実行、結果のレポートなどのインフラを提供
- Mavenではjunit-jupiter-api(テストを書く際必要)とjunit-jupiter-engine(Platform上でテストの実行に必要)の2つが必須。
- maven-surefire-pluginはmvn testを実行したとき、テストクラスを自動で検出、実行する。junit-jupiter-engineにテストクラスを渡し実行。テストを検出 → 実行 → 結果を判断・出力。様々な設定でテストの挙動を制御(includeなど)
  - Maven が mvn test を実行→ Surefire Plugin が起動→ JUnit Platform の Launcher を呼び出す→ Platform が利用可能なテストエンジン（例: junit-jupiter-engine）を検出→ 各エンジンが、対応するテスト（例: JUnit5 のクラス）を実行→ 結果を Platform 経由で受け取り、Surefire がレポートを生成
  - JUnit 5 を使うには、バージョン 3.0.0-M5 以上 が必要なためpomに記入
  - engine検索時に複数のエンジンがあれば、それぞれのテストメソッドにおいて、そのバージョンに応じてエンジンが使用される。
