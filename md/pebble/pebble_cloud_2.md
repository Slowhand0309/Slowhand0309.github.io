## オリジナルのPebbleアプリを作成 2 - シンプルな時計表示 -

今回も[CloudPebble](https://cloudpebble.net/)を使ってシンプルな時計を表示するアプリを作成してみます。

##### 最小プログラム
****

まずは一番最小となるアプリを作成してみます。<br>
新規にプロジェクトを作成します。プロジェクト名は`SAMPLE2`にしてます。

img1

* PROJECT TYPE : `Pebble C SDK`
* SDK VERSION: `SDK3`
* TEMPLATE: `Empty project`

※今回はTEMPLATEを`Empty project`にしています。<br>
作成後新規にソースファイルを追加します。

img2


ファイル名は`main.c`にしてます。<br>
作成した`main.c`を以下に編集します。

```c
#include <pebble.h>

static Window *s_window;

int main(void) {
  s_window = window_create();
  window_stack_push(s_window, true);

  app_event_loop();

  window_destroy(s_window);
}
```
処理内容としては、

1. `Window`ポインタを`window_create()`で取得

2. 表示させる為に`window_stack_push`でstackにpush

   この時の第二引数はアニメーションの有無です。<br>
    ※PebbleOSは`Window Stack API`にて画面遷移など行います。

3. `app_event_loop`でExitするまでメインループを実行

4. 最後`window_destroy`でWindowを破棄して終了

となります。<br>
早速エミュレータで確認してみます。<br>
`COMPILATION` > `EMULATOR` > `RUN BUILD` > `APLITE`で実行できます。

実行した結果がこちら↓

img3

当然ながら何も表示されてませんが、一応最小のプログラムができました。

##### 時計表示
****

最小限のプログラムを改良して、シンプルな時計を表示させるアプリを作成してみます。

```c
#include <pebble.h>

static Window *s_window;
static TextLayer *s_time_layer;
static char s_buffer[] = "00:00:00";

// Windowが読み込まれた時に呼ばれる
static void window_load(Window *window) {
  // ルートのレイヤーのサイズを取得
  Layer *window_layer = window_get_root_layer(s_window);
  GRect bounds = layer_get_bounds(window_layer);

  // 時計を表示するレイヤーの作成
  s_time_layer = text_layer_create(
      GRect(0, 0, bounds.size.w, bounds.size.h));

  // レイヤーの設定
  text_layer_set_background_color(s_time_layer, GColorPastelYellow);
  text_layer_set_text_color(s_time_layer, GColorBlack);
  text_layer_set_font(s_time_layer, fonts_get_system_font(FONT_KEY_ROBOTO_CONDENSED_21));
  text_layer_set_text_alignment(s_time_layer, GTextAlignmentCenter);

  // レイヤーの追加
  layer_add_child(window_layer, text_layer_get_layer(s_time_layer));
}

// Windowが解放された時に呼ばれる
static void window_unload(Window *window) {
  text_layer_destroy(s_time_layer);
}

// 時間更新
static void update_time() {
  // 現在時刻の取得
  time_t temp = time(NULL);
  struct tm *tick_time = localtime(&temp);

  // フォーマット指定で文字列に出力
  strftime(s_buffer, sizeof(s_buffer), "%T", tick_time);

  // ディスプレイ出力
  text_layer_set_text(s_time_layer, s_buffer);
}

// 秒毎に呼ばれるハンドラ
static void tick_handler(struct tm *tick_time, TimeUnits units_changed) {
  update_time();
}

// メイン
int main(void) {
  // ウィンドウの作成
  s_window = window_create();
  window_set_window_handlers(s_window, (WindowHandlers) {
    .load = window_load,
    .unload = window_unload
  });
  // イベント登録
  tick_timer_service_subscribe(SECOND_UNIT, tick_handler);
  // Window Stackに追加
  window_stack_push(s_window, true);
  // 一度現在時刻で表示
  update_time();

  // メインループ
  app_event_loop();
  // 終了処理
  window_destroy(s_window);
}
```

やってる事としては、、

1. `window_set_window_handlers`でWindowがload、unloadした時のハンドライベントを設定します

2. Windowがloadしたタイミングで時計を表示するレイヤー(`s_time_layer`)を作成します

   ※指定できるフォントは[こちら](https://gist.github.com/sarfata/aec9c06ba9d66d159ed4)を参考にしました

3. `tick_timer_service_subscribe`で1秒毎に`tick_handler`関数を呼び出すように登録します

4. `update_time`で現在時刻を`s_time_layer`に書き出します

です。ただ今回`tick_timer_service_subscribe`で1秒毎(`SECOND_UNIT`)に<br>
ハンドラを呼び出してますが、実機だと電池がその分消費されるので注意です。

最後にエミュレータで動作させた結果がこちらです↓

img4
