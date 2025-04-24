# JUnit

## assertメソッド群
- assertEquals(expected, actual) 値の比較, double比較は第3引数に誤差範囲を追加
- assertThrows(例外.class, () -> {例外期待コード}) 例外期待コード中で指定した例外が発生すればOK
- assertThat(actual, is(expedted)) Matcherオブジェクトに基づいた比較

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
