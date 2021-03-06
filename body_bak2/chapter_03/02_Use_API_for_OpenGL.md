<div id="sect_title_img_3_2"></div>

<div id="sect_title_text"></div>

# OpenGL向けのAPIを利用する

<div id="preface"></div>

###### これまではボタンやメニューなどのBlenderの決まったフレームワークの中で、UIを構築する方法を紹介しました。しかし、アドオンの機能によっては独自のUIを構築する必要がある場合があると思います。その時に独自のUIを構築する方法として、例えばOpenGLを利用する方法が考えられます。本節ではBlenderが提供しているOpenGL向けのAPIを利用し、3Dビューエリア上に図形を表示するサンプルを紹介します。

## OpenGLとは？

2D/3D向けのグラフィックAPIはOpenGL (Open Graphic Library)とDirectXの2つが主流です。3DCGに何かしら関わっている方はご存知かもしれませんが、OpenGLとDirectXの違いがわからない方向けにOpenGLとDirectXの違いをまとめました。

|　|OpenGL|DirectX|
|---|---|---|
|開発元|Microsoft|シリコングラフィクス|
|主な用途|ゲームの描画|3DCGソフト、CADソフト|
|レンダリング精度|OpenGLに比べて低精度|精度が求められるCADソフトで利用されるため高精度|
|レンダリング速度|高い応答性が求められるゲームで利用されるため高速|DirectXに比べて低速|
|動作環境|Windows, XBox|Windows, Mac, Linux, iOS, Android, Webアプリなど|

上記の比較からもわかるように、3DCGソフトかつ複数のOSでの動作をサポートするBlenderがグラフィックスAPIとしてOpenGLを利用するのは理にかなっています。

BlenderはPythonからOpenGLへアクセスするためのAPIも用意しているため、ユーザはPythonスクリプトからOpenGLの機能の一部を扱うことができます。


## 作成するアドオンの仕様

* 3Dビューエリアに図形を表示
* 表示する図形は、3Dビューエリアのプロパティパネルから選択

## アドオンを作成する

[1-4節](../chapter_01/04_Install_own_Add-on.md) を参考にして以下のソースコードをテキスト・エディタに入力し、ファイル名 ```sample_8.py``` として保存してください。

[import](../../sample/src/chapter_03/sample_8.py)

## アドオンを使用する

### アドオンを有効化する

[1-4節](../chapter_01/04_Install_own_Add-on.md) を参考に作成したアドオンを有効化すると、コンソールに以下の文字列が出力されます。

```sh
サンプル8: アドオン「サンプル8」が有効化されました。
```

<div id="sidebyside"></div>

|アドオンを有効化すると、右図のように3Dビューエリアのプロパティパネルに開始ボタンが表示されます。|![図の表示 手順1](https://dl.dropboxusercontent.com/s/uf0xneikowb5ozz/use_addon_1.png "図の表示 手順1")|
|---|---|

<div id="space_s"></div>


### アドオンの機能を使用する

以下の手順に従って、作成したアドオンの機能を使ってみます。

<div id="process_title"></div>

##### Work

<div id="process"></div>

|<div id="box">1</div>|開始ボタンをクリックすると、3Dビューエリア上に三角形が表示されます。<br>またプロパティパネルには、表示する図形と図形の頂点の座標を変更するためのUIが表示されます。|![図の表示 手順2](https://dl.dropboxusercontent.com/s/056sg7b9x96mdjf/use_addon_2.png "図の表示 手順2")|
|---|---|---|

<div id="process_sep"></div>

---

<div id="process"></div>

|<div id="box">2</div>|三角形の頂点座標を変更します。<br>すると、3Dビューエリア 上に表示されている三角形が頂点の座標の変更に合わせて変形します。|![図の表示 手順3](https://dl.dropboxusercontent.com/s/vlua7b5aiptcc4m/use_addon_3.png "図の表示 手順3")|
|---|---|---|

<div id="process_sep"></div>

---

<div id="process"></div>

|<div id="box">3</div>|表示する図形を三角形から四角形へ変更すると、プロパティパネルで4つの頂点を編集できるようになり、表示図形の変更と同時に3Dビューエリア上に表示されている図形も変更されます。|![図の表示 手順4](https://dl.dropboxusercontent.com/s/1wr0l6uddp64emk/use_addon_4.png "図の表示 手順4")|
|---|---|---|

<div id="process_start_end"></div>

---


### アドオンを無効化する

[1.4節](../chapter_01/04_Install_own_Add-on.md)を参考に有効化したアドオンを無効化すると、コンソールに以下の文字列が出力されます。

```sh
サンプル8: アドオン「サンプル8」が無効化されました。
```

## ソースコードの解説

### OpenGLへアクセスするためのAPIを利用する

本節のサンプルでは、図形を描画するためにBlenderが提供するOpenGLへアクセスするためのAPIを利用します。

OpenGLへアクセスするAPIをアドオンから利用するためには、 ```bgl``` モジュールをインポートする必要があります。

```python
import bgl
```

### アドオンで利用するプロパティを定義する

本節のサンプルでは、クラス間で以下のようなデータを共有します。

|変数|意味|
|---|---|
|```rf_running```|実行中の場合は ```True```|
|```rf_figure```|表示する図形（三角形、四角形）|
|```rf_vert_1```|頂点1の座標（2次元）|
|```rf_vert_2```|頂点2の座標（2次元）|
|```rf_vert_3```|頂点3の座標（2次元）|
|```rf_vert_4```|頂点4の座標（2次元）、四角形表示時のみに利用可能|

以下のようにして、上記プロパティを定義します。

```python
sc = bpy.types.Scene
sc.rf_running = BoolProperty(
    name = "実行中",
    description = "実行中か？",
    default = False
)
sc.rf_figure = EnumProperty(
    name = "図形",
    description = "表示する図形",
    items = [
        ('TRIANGLE', "三角形", "三角形を表示します"),
        ('RECTANGLE', "四角形", "四角形を表示します")]
)
sc.rf_vert_1 = FloatVectorProperty(
    name = "頂点1",
    description = "図形の頂点",
    size = 2,
    default = (50.0, 50.0)
)
sc.rf_vert_2 = FloatVectorProperty(
    name = "頂点2",
    description = "図形の頂点",
    size = 2,
    default = (50.0, 100.0)
)
sc.rf_vert_3 = FloatVectorProperty(
    name = "頂点3",
    description = "図形の頂点",
    size = 2,
    default = (100.0, 100.0)
)
sc.rf_vert_4 = FloatVectorProperty(
    name = "頂点4",
    description = "図形の頂点",
    size = 2,
    default = (100.0, 50.0)
)
```

定義したプロパティは、アドオン無効化時に削除します。

```python
sc = bpy.types.Scene
del sc.rf_running
del sc.rf_figure
del sc.rf_vert_1
del sc.rf_vert_2
del sc.rf_vert_3
del sc.rf_vert_4
```

### 図形を描画する関数を登録する

3Dビューエリア上で図形を描画する関数を登録するための静的メソッド ```RenderFigure.handle_add()``` を作成します。

```RenderFigure.handle_add()``` は静的メソッドとして作成する必要があるため、デコレータ ```@staticmethod``` をメソッド定義の前につけます。

```python
# 画像描画関数を登録
@staticmethod
def handle_add(self, context):
    if RenderFigure.__handle is None:
        RenderFigure.__handle = bpy.types.SpaceView3D.draw_handler_add(
            RenderFigure.render,
            (self, context), 'WINDOW', 'POST_PIXEL')
```

描画関数の登録は ```bpy.types.SpaceView3D.draw_handler_add()``` 関数で行います。ここで ```SpaceView3D``` は3Dビューを指していますが、描画するエリアによって名前が変わります。

引数には、3Dビューエリア上で描画する処理を記述した関数など、以下に示す値を指定します。

|引数|意味|
|---|---|
|第1引数|描画関数（描画関数は静的メソッド、または通常の関数）|
|第2引数|描画関数に渡す引数リスト|
|第3引数|描画する *リージョン*|
|第4引数|描画モード（深度バッファの扱いを指定、基本は ```POST_PIXEL```）|

本節のサンプルでは、第1引数に ```RenderFigure.render``` 、第3引数に ```WINDOW``` を指定しています。第2引数には、自身のクラスインスタンスと実行時コンテキストを渡しています。

```bpy.types.SpaceView3D.draw_handler_add()``` 関数は戻り値としてハンドルを返します。ハンドルはメンバ変数 ```__handle``` に保存し、描画関数の登録解除時に利用します。

### 図形を描画する関数を作成する

図形を描画する静的メソッド ```RenderFigure.render``` を作成します。

```python
@staticmethod
def render(self, context):
    sc = context.scene

    # OpenGLの設定
    bgl.glEnable(bgl.GL_BLEND)

    # 図形を表示
    if sc.rf_figure == 'TRIANGLE':
        bgl.glBegin(bgl.GL_TRIANGLES)
        bgl.glColor4f(1.0, 1.0, 1.0, 0.7)
        bgl.glVertex2f(sc.rf_vert_1[0], sc.rf_vert_1[1])
        bgl.glVertex2f(sc.rf_vert_2[0], sc.rf_vert_2[1])
        bgl.glVertex2f(sc.rf_vert_3[0], sc.rf_vert_3[1])
        bgl.glEnd()
    elif sc.rf_figure == 'RECTANGLE':
        bgl.glBegin(bgl.GL_QUADS)
        bgl.glColor4f(1.0, 1.0, 1.0, 0.7)
        bgl.glVertex2f(sc.rf_vert_1[0], sc.rf_vert_1[1])
        bgl.glVertex2f(sc.rf_vert_2[0], sc.rf_vert_2[1])
        bgl.glVertex2f(sc.rf_vert_3[0], sc.rf_vert_3[1])
        bgl.glVertex2f(sc.rf_vert_4[0], sc.rf_vert_4[1])
        bgl.glEnd()
```

静的メソッド ```render()``` 内の処理について説明します。

```render()``` 関数内での図形の描画処理は、基本的にOpenGLを用いた描画手順に従っています。

最初に ```bgl.glEnable(bgl.GL_BLEND)``` により、半透明処理を有効化します。この処理がないと図形描画時に透過が無効になり、期待した結果になりません。

続いて表示する図形の判定を行った後、 ```bgl.glBegin()``` 関数により図形描画を開始します。```bgl.glBegin()``` の引数には描画モードを指定します。 ```bgl.GL_TRIANGLES``` を指定することで三角形の描画を、 ```bgl.GL_QUADS``` を指定することで四角形の描画を開始します。

次に、 ```bgl.glColor4f()``` 関数により図形の色を指定しています。引数は順に、赤(R)、緑(G)、青(B)、アルファ値(A)となります。今回はやや半透明の白色の設定にしました。

最後に ```bgl.glVertex2f()``` 関数を呼んで図形の頂点の座標を設定した後に、 ```bgl.glEnd()``` 関数により描画を完了します。```bgl.glVertex2f()``` 関数の引数には、X座標、Y座標の順で浮動小数点値で座標を指定します。三角形の場合は3つの頂点を指定するため3回 ```bgl.glVertex2f()``` 関数を呼び、四角形の場合は4つの頂点を指定するため4回 ```bgl.glVertex2f()``` 関数を呼びます。


### 図形を描画する関数を登録解除する

登録した図形を描画する関数は、アドオン無効化時に登録を解除する必要があります。

```python
@staticmethod
def handle_remove(self, context):
    if RenderFigure.__handle is not None:
        bpy.types.SpaceView3D.draw_handler_remove(
            RenderFigure.__handle, 'WINDOW')
        RenderFigure.__handle = None
```

描画関数の登録解除は、 ```bpy.types.SpaceView3D.draw_handler_remove()``` 関数で行います。

描画関数の登録時に使用した ```bpy.types.SpaceView3D.draw_handler_add()``` 関数と同様、 ```SpaceView3D``` は描画関数を登録解除するエリアにより名前が異なります。

```bpy.types.SpaceView3D.draw_handler_add()``` に指定可能な関数の引数は、以下の通りです。

|引数|意味|
|---|---|
|第1引数|ハンドル（```draw_handler_add()``` 関数の戻り値）|
|第2引数|描画する *リージョン*|

### UIを構築する

最後に、本アドオンのUIを構築します。

```python
class OBJECT_PT_RF(bpy.types.Panel):
    bl_label = "図形を表示"
    bl_space_type = "VIEW_3D"
    bl_region_type = "UI"

    def draw(self, context):
        sc = context.scene
        layout = self.layout
        if context.area:
            context.area.tag_redraw()
        if sc.rf_running is True:
            layout.operator(RenderingButton.bl_idname, text="Stop", icon="PAUSE")
            layout.prop(sc, "rf_figure", "図形")
            layout.prop(sc, "rf_vert_1", "頂点1")
            layout.prop(sc, "rf_vert_2", "頂点2")
            layout.prop(sc, "rf_vert_3", "頂点3")
            if sc.rf_figure == 'RECTANGLE':
                layout.prop(sc, "rf_vert_4", "頂点4")
        elif sc.rf_running is False:
            layout.operator(RenderingButton.bl_idname, text="Start", icon="PLAY")
```

[3.1節](01_Handle_Mouse_Click_Event.md) と同様、 ```bpy.types.Panel``` を継承したパネルクラスの中でUIを構築します。

最初に描画中か否かの判定を行った後、描画中であればStopボタンを、描画中でなければStartボタンを配置します。

続いて、描画中であれば描画する図形や頂点の座標を指定できるようにするため、 ```layout.prop()``` 関数を用いてこれらのUIパーツを配置します。 ```layout.prop()``` 関数の詳細については、 [2-8節](../chapter_02/08_Control_Bnder_UI.md) を参照してください。

四角形を描画する場合にはユーザが4つの頂点を指定できる必要があるため、描画する図形が四角形に選択されている場合は、4つ目の頂点を指定するUIパーツを配置します。

最後に、描画開始/終了を行うオペレータクラスを作成します。

```python
class RenderingButton(bpy.types.Operator):
    bl_idname = "view3d.rendering_button"
    bl_label = "図形表示/非表示切り替えボタン"
    bl_description = "図形の表示/非表示を切り替えるボタン"
    bl_options = {'REGISTER', 'UNDO'}

    def invoke(self, context, event):
        sc = context.scene
        if sc.rf_running is True:
            RenderFigure.handle_remove(self, context)
            sc.rf_running = False
        elif sc.rf_running is False:
            RenderFigure.handle_add(self, context)
            sc.rf_running = True

        return {'FINISHED'}
```

描画中にボタンが押された（ ```sc.rf_running``` が ```True``` ）時には、静的メソッド ```RenderFigure.handle_remove()``` 関数を実行して描画関数を登録解除し、描画を中断します。描画中でない場合にボタンが押された（ ```sc.rf_running``` が ```False``` ）時には、静的メソッド ```RenderFigure.handle_add()``` 関数を実行して描画関数を登録し、描画を開始します。

## まとめ

PythonからOpenGLへアクセスするためのAPIである ```bgl``` モジュールを用いて、3Dビューエリアで図形を描画する方法を紹介しました。

本節で紹介した ```bgl``` モジュールと [3.1節](01_Handle_Mouse_Click_Event.md) で紹介したマウスからのイベントを扱う方法を組み合わせることで、Blender専用のUIとは全く異なる独自のUIを構築することができます。

OpenGLを利用するためのAPIが用意されているとはいっても、OpenGLの全ての機能に対してAPIが用意されているわけではありません。このため、 ```bgl``` モジュールを利用する際には、 [4.1節](../chapter_04/01_Research_official_Blender_API_for_Add-on.md) を参考にして、Blenderが提供するAPIを確認する必要があります。

<div id="point"></div>

### ポイント

<div id="point_item"></div>

* OpenGLへアクセスするためのAPIを利用するためには、 ```bgl``` モジュールをインポートする必要がある
* ```bgl``` モジュールを用いて、アドオン内でOpenGLを用いて描画するためには、 ```bpy.types.SpaceXXX.draw_handler_add()``` （XXX：描画するエリア）関数を用いて、描画用の静的メソッドまたは関数を登録する必要がある
* 登録した描画用の静的メソッドまたは関数は、アドオン無効化時に ```bpy.types.SpaceXXX.draw_handler_remove()``` 関数を用いて、登録を解除する必要がある
* ```bgl``` モジュールは、オリジナルのOpenGLの使い方と似たような方法でOpenGLへアクセスするための手段を提供する
* ```context.scene``` に登録したプロパティは、 パネルクラスの ```draw()``` メソッドで ```self.layout.prop()``` 関数を用いることによりUIパーツとして登録できる
* ```bgl``` モジュールは、OpenGLの関数をすべてサポートしているわけではない。事前に使いたいAPIが用意されているかの確認が必要である

<div id="space_page"></div>
