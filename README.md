# JUnit

## assertメソッド群
- assertEquals(expected, actual) 値の比較, double比較は第3引数に誤差範囲を追加
- assertThrows(例外.class, () -> {例外期待コード}) 例外期待コード中で指定した例外が発生すればOK

## その他
- JUnit5からはメソッドはpublic付けなくてもよい
- testと打ち`ctrl + Space`でテストメソッド補完
