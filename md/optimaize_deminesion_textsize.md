## AndroidでCanvas#drawTextを使って文字表示する時のサイズ設定

onDrawなどで、Canvas#drawTextを使って文字を表示させる時に<br>
Paint#setTextSizeでテキストサイズを指定しても端末間の解像度の違いは<br>
考慮してくれない
<br>
そこで[TextView#setTextSize](http://tools.oesf.biz/android-5.1.1_r1.0/xref/frameworks/base/core/java/android/widget/TextView.java)を参考にして実装した時のメモ


#### TextView#setTextSize
***


```java
/**
 * Set the default text size to a given unit and value.  See {@link
 * TypedValue} for the possible dimension units.
 *
 * @param unit The desired dimension unit.
 * @param size The desired size in the given units.
 *
 * @attr ref android.R.styleable#TextView_textSize
 */
public void setTextSize(int unit, float size) {
    Context c = getContext();
    Resources r;

    if (c == null)
        r = Resources.getSystem();
    else
        r = c.getResources();

    setRawTextSize(TypedValue.applyDimension(
            unit, size, r.getDisplayMetrics()));
}
```
これを見るとTypedValue#applyDimensionで<br>
解像度を考慮したサイズに変換している事が分かる


#### TypedValue#applyDimension
***

```java
/**
  * Converts an unpacked complex data value holding a dimension to its final floating
  * point value. The two parameters <var>unit</var> and <var>value</var>
  * are as in {@link #TYPE_DIMENSION}.
  *
  * @param unit The unit to convert from.
  * @param value The value to apply the unit to.
  * @param metrics Current display metrics to use in the conversion --
  *                supplies display density and scaling information.
  *
  * @return The complex floating point value multiplied by the appropriate
  * metrics depending on its unit.
  */
 public static float applyDimension(int unit, float value,
                                    DisplayMetrics metrics)
 {
     switch (unit) {
     case COMPLEX_UNIT_PX:
         return value;
     case COMPLEX_UNIT_DIP:
         return value * metrics.density;
     case COMPLEX_UNIT_SP:
         return value * metrics.scaledDensity;
     case COMPLEX_UNIT_PT:
         return value * metrics.xdpi * (1.0f/72);
     case COMPLEX_UNIT_IN:
         return value * metrics.xdpi;
     case COMPLEX_UNIT_MM:
         return value * metrics.xdpi * (1.0f/25.4f);
     }
    return 0;
 }
 ```
テキストサイズなので引数の`unit`には`COMPLEX_UNIT_SP`を指定<br>
以上を考慮してサイズ変換メソッドを作成

```java
/**
* 端末に最適化したテキストサイズを取得
*
* @param context コンテキスト
* @param size 元サイズ
* @return 最適化されたテキストサイズ
*/
public static float toDimensionTextSize(Context context, float size) {

    Resources r;
    if (context == null) {
        r = Resources.getSystem();

    } else {
         r = context.getResources();
    }
    return TypedValue.applyDimension(
        TypedValue.COMPLEX_UNIT_SP, size, r.getDisplayMetrics());
}
```

Paint#setTextSize時にこのメソッドを呼ぶ

```java
  paint.setTextSize(toDimensionTextSize(getContext(), 10.0f));
```
