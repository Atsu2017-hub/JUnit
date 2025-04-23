# JUnit

## assertメソッド群
- assertEquals(expected, actual) 値の比較, double比較は第3引数に誤差範囲を追加
- assertThrows(例外.class, () -> {例外期待コード}) 例外期待コード中で指定した例外が発生すればOK

## その他
- Throwableには、ErrorとExceptionがある。
  - Errorは回復不可能(スタックオーバーフロー、メモリ不足等)、try-catchは推奨されない。
  - ExceptionクラスはRuntimeExceptionクラス(配列の未定義indexの参照など)とその他のクラス(IOExceptionなど)に分類される。その他のクラスは検査例外でRuntimeExceptionクラスは非検査例外(try-catchいらない)。

## JUnit4からJUnit5への変更
- メソッドはpublic付けなくてもよい(private以外)
- assertThatは廃止
- @Test(expected or timeout = ...)から@expected, @Timeoutに変更
- Before, BeforeClassからBeforeEach, BeforeAllに変更。Afterも同様。 
