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

## その他
- Throwableには、ErrorとExceptionがある。
  - Errorは回復不可能(スタックオーバーフロー、メモリ不足等)、try-catchは推奨されない。
  - ExceptionクラスはRuntimeExceptionクラス(配列の未定義indexの参照など)とその他のクラス(IOExceptionなど)に分類される。その他のクラスは検査例外でRuntimeExceptionクラスは非検査例外(try-catchいらない)。
  - `==`：参照の比較（アドレスの同一性）
  - `.equals()`：内容の比較（オブジェクトのフィールドの等価性）

## JUnit4からJUnit5への変更
- メソッドはpublic付けなくてもよい(private以外)
- assertThatはJUnit5には組み込まれていない(外部ライブラリ↓を導入する必要あり)
  - import static org.hamcrest.MatcherAssert.*;
  - import static org.hamcrest.Matchers.*;
- @Test(expected or timeout = ...)からAssertThrows, @Timeoutに変更
- Before, BeforeClassからBeforeEach, BeforeAllに変更。Afterも同様。 
