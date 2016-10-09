<div id="sect_title_img_2_10"></div>

<div id="sect_title_text"></div>

# Blender の UI を制御する③

<div id="preface"></div>

###### B



## 作成するアドオンの仕様

* 以下のようなタブを *3Dビュー* エリアの *ツール・シェルフ* に追加する

![アドオンの仕様](https://dl.dropboxusercontent.com/s/ial27tu1ousllmx/specification.png "アドオンの仕様")


## アドオンを作成する

[1-5節](../chapter_01/05_Install_own_Add-on.md) を参考にして以下のソースコードを テキスト・エディタに入力し、ファイル名を ```sample_2-10.py``` として保存してください。

[import](../../sample/src/chapter_02/sample_2-10.py)


## アドオンを使用する

### アドオンを有効化する

[1-5節](../chapter_01/05_Install_own_Add-on.md) を参考に作成したアドオンを有効化すると、コンソールに以下の文字列が出力されます。

```sh
サンプル2-10: アドオン「サンプル2-10」が有効化されました。
```

また、 *3Dビュー* エリアのメニュー *オブジェクト* に *項目 1* と *項目2* が追加されます。

### アドオンの機能を使用する

*3Dビュー* エリアの *ツール・シェルフ* にタブ *カスタムメニュー3* をクリックすると、カスタムメニューのメニューが表示されます。



#### 利用可能なアイコンをすべて表示ボタン

<div id="process_title"></div>

##### Work

<div id="process"></div>

|<div id="box">1</div>|タブ *カスタムメニュー* の *利用可能なアイコンをすべて表示* ボタンをクリックします。|![利用可能なアイコンをすべて表示ボタン1](https://dl.dropboxusercontent.com/s/ru4ckm8y65wyzsz/icon_list_1.png "利用可能なアイコンをすべて表示ボタン1")|
|---|---|---|

<div id="process_sep"></div>

---

<div id="process"></div>

|<div id="box">2</div>|*ツール・シェルフ* のオプションに、アドオンから利用することのできるアイコン一覧と、それぞれのアイコンを表示するためのアイコンのキーコードが表示されます。|![利用可能なアイコンをすべて表示ボタン2](https://dl.dropboxusercontent.com/s/8tddbytmq5j8ghr/icon_list_2.png "利用可能なアイコンをすべて表示ボタン2")|
|---|---|---|

<div id="process_sep"></div>

---

<div id="process"></div>

|<div id="box">3</div>|*一行に表示するアイコン数* から一行に表示するアイコンの数を変更することができます。|![利用可能なアイコンをすべて表示ボタン3](https://dl.dropboxusercontent.com/s/fzv6foiqzln3dyg/icon_list_3.png "利用可能なアイコンをすべて表示ボタン3")|
|---|---|---|


<div id="process_start_end"></div>

---


### アドオンを無効化する

[1-5節](../chapter_01/05_Install_own_Add-on.md) を参考に有効化したアドオンを無効化すると、コンソールに以下の文字列が出力されます。

```sh
サンプル2-10: アドオン「サンプル2-10」が無効化されました。
```


## ソースコードの解説


#### ポップアップメッセージを表示する

アドオンからBlender内で、ポップアップメッセージを表示することもできます。

以下は、ポップアップメッセージを表示するオペレータクラスです。

```python
class ShowPopupMessage(bpy.types.Operator):
    bl_idname = "object.show_popup_message"
    bl_label = "ポップアップメッセージ"
    bl_description = "ポップアップメッセージ"
    bl_options = {'REGISTER', 'UNDO'}

    # execute() メソッドがないと、やり直し未対応の文字が出力される
    def execute(self, context):
        return {'FINISHED'}

    def invoke(self, context, event):
        wm = context.window_manager
        # ポップアップメッセージ表示
        return wm.invoke_popup(self, width=200, height=100)

    # ポップアップメッセージに表示する内容
    def draw(self, context):
        layout = self.layout
        layout.label("メッセージ")
```

ポップアップメッセージの表示はボタンを押したときに呼ばれる ```invoke()``` メソッドの ```wm.invoke_popup()``` 関数で行っています。

```invoke()``` メソッドは、処理が実行された時に呼ばれるメソッドです。これまで使ってきた ```execute()``` メソッドも処理が実行された時に呼ばれますが、 ```execute()``` メソッドの前に ```invoke()``` メソッドが呼ばれる点が異なります。このため、 ```execute()``` メソッドの実行前に行いたい処理がある場合は、 ```invoke()``` メソッドを使用します。

```wm.invoke_popup()``` 関数の引数を以下に示します。

|引数|型|意味|
|---|---|
|第1引数|オペレータクラスのインスタンス||
|```width```|整数|ポップアップメッセージの横幅|
|```height```|整数|ポップアップメッセージの縦幅(UI に応じて自動的に調整されるため効果なし)|

```wm.invoke_popup()``` 関数により表示されるポップアップの UI は、 ```draw()``` メソッドで定義します。
本節のサンプルでは、 ```メッセージ``` と書かれたラベルを表示しています。

```wm.invoke_popup()``` 関数の戻り値は ```{'RUNNING_MODAL'}``` ですが、ここでは説明を省略します。
ポップアップメッセージを表示する時には ```wm.invoke_popup()``` 関数を ```invoke()``` メソッドの戻り値に指定すればよいということだけを覚えておきましょう。

ポップアップメッセージを表示するためのボタンの配置は、以下の処理で行います。

```python
# ポップアップメッセージを表示する
layout.label(text="ポップアップメッセージを表示する:")
layout.operator(ShowPopupMessage.bl_idname)
```


#### ダイアログメニューを表示する

ポップアップメッセージの応用として、プロパティをポップアップから入力することのできるダイアログメニューを表示することもできます。

以下は、ダイアログメニューを表示するオペレータクラスです。

```python
class ShowDialogMenu(bpy.types.Operator):
    bl_idname = "object.show_dialog_menu"
    bl_label = "ダイアログメニュー"
    bl_description = "ダイアログメニュー"
    bl_options = {'REGISTER', 'UNDO'}

    prop_int = IntProperty(
        name="ダイアログプロパティ 1",
        description="ダイアログプロパティ 1",
        default=100,
        min=0,
        max=255)
    prop_float = FloatProperty(
        name="ダイアログプロパティ 2",
        description="ダイアログプロパティ 2",
        default=0.75,
        min=0.0,
        max=1.0)
    prop_enum = EnumProperty(
        name="ダイアログプロパティ 3",
        description="ダイアログプロパティ 3",
        items=[
            ('ITEM_1', "項目 1", "項目 1"),
            ('ITEM_2', "項目 2", "項目 2"),
            ('ITEM_3', "項目 3", "項目 3")],
        default='ITEM_1')
    prop_floatv = FloatVectorProperty(
        name="ダイアログプロパティ 4",
        description="ダイアログプロパティ 4",
        subtype='COLOR_GAMMA',
        default=(1.0, 1.0, 1.0),
        min=0.0,
        max=1.0)

    def execute(self, context):
        self.report({'INFO'}, "1: %d, 2: %f, 3: %s, 4: (%f, %f, %f)"
            % (self.prop_int, self.prop_float, self.prop_enum, self.prop_floatv[0], self.prop_floatv[1], self.prop_floatv[2]))

        return {'FINISHED'}

    def invoke(self, context, event):
        scene = context.scene

        self.prop_int = scene.cm_prop_int
        self.prop_float = scene.cm_prop_float
        self.prop_enum = scene.cm_prop_enum
        self.prop_floatv = scene.cm_prop_floatv

        # ダイアログメニュー呼び出し
        return context.window_manager.invoke_props_dialog(self)
```

```ShowDialogMenu``` クラスには4つのプロパティクラスの変数が宣言されていて、ダイアログメニューではこれらのプロパティを表示します。
ダイアログメニューの表示は ```context.window_manager.invoke_props_dialog()``` 関数で行います。
引数には、ダイアログメニューに表示するプロパティクラスの変数を持つオペレータクラスのインスタンスを渡します。

```context.window_manager.invoke_props_dialog()``` 関数の引数を以下に示します。

|引数|型|意味|
|---|---|
|第1引数|オペレータクラスのインスタンス|OK ボタンを押したときに、引数に指定したインスタンスの ```execute()``` メソッドが実行される。<br>また、ダイアログメニューのプロパティは本引数に指定したインスタンスに定義したプロパティクラスの変数が表示される|
|```width```|整数|ポップアップメッセージの横幅|
|```height```|整数|ポップアップメッセージの縦幅(UI に応じて自動的に調整されるため効果なし)|

```context.window_manager.invoke_props_dialog()``` 関数の戻り値はポップアップメッセージと同様、```{'RUNNING_MODAL'}``` ですが、ここでは説明を省略します。
ダイアログメニューを表示する時には ```wm.invoke_popup()``` 関数を ```invoke()``` メソッドの戻り値に指定すればよいということだけを覚えておきましょう。

ダイアログメニューに表示された OK ボタンを押すと、 ```execute()``` メソッドが実行されます。
```execute()``` メソッドでは、ダイアログメニューのプロパティに指定した値をコンソール・ウィンドウに出力します。
ダイアログメニューで指定したプロパティの値でアドオンの処理を実行したいときに活用しましょう。

ダイアログメニューを表示するためのボタンの配置は、以下の処理で行います。

```python
# ダイアログメニューを表示する
layout.label(text="ダイアログメニューを表示する:")
layout.operator(ShowDialogMenu.bl_idname)
```


#### ファイルブラウザを表示する

ファイルを開いたり保存したりする時などの Blender 標準の機能を使用した場合でも、ファイルを選択するためのファイルブラウザを表示する処理が存在します。
```context.window_manager.fileselect_add()``` を用いることで、アドオンからファイルブラウザを表示することができます。

本節のサンプルでは、以下のようにしてファイルブラウザを表示しています。

```python
class ShowFileBrowser(bpy.types.Operator):
    bl_idname = "object.show_file_browser"
    bl_label = "ファイルブラウザ"
    bl_description = "ファイルブラウザ"
    bl_options = {'REGISTER', 'UNDO'}

    filepath = StringProperty(subtype="FILE_PATH")
    filename = StringProperty()
    directory = StringProperty(subtype="FILE_PATH")

    def execute(self, context):
        self.report({'INFO'}, "FilePath: %s, FileName: %s, Directory: %s" % (self.filepath, self.filename, self.directory))
        return {'FINISHED'}

    def invoke(self, context, event):
        wm = context.window_manager
        # ファイルブラウザ表示
        wm.fileselect_add(self)

        return {'RUNNING_MODAL'}
```

ファイルブラウザを表示させるためには、 ```invoke()``` メソッド内で ```wm.fileselect_add()``` 関数を呼ぶ必要があります。
引数には、ファイルブラウザ内でファイルを確定した時に実行される、 ```execute()``` メソッドが定義されたオペレータクラスのインスタンスを指定します。
```invoke()``` メソッドの戻り値は、 ```{'RUNNING_MODAL'}``` にする必要があります。

またファイルブラウザで確定したファイルの情報を保存するために、メンバ変数 ```filepath``` ・ ```filename``` ・ ```directory``` を宣言しています。
ファイルブラウザからファイルの情報を受け取るためには、これらの変数名でなくてはならないことに注意が必要です。
なお、 ```filepath``` や ```directory``` は、 プロパティクラス ```StringProperty``` の引数 ```subtype``` にファイルパスを格納するプロパティであることを示す ```FILE_PATH``` を指定する必要があります。

ファイルブラウザでファイルを確定すると ```execute()``` メソッドが呼ばれ、確定したファイルパス・ファイル名・ファイルが置かれたディレクトリをコンソール・ウィンドウに表示します。

ファイルブラウザを表示するボタンを表示する処理は、以下の通りです。

```python
# ファイルブラウザを表示する
layout.label(text="ファイルブラウザを表示する:")
layout.operator(ShowFileBrowser.bl_idname)
```


#### 実行確認のポップアップを表示する

Blender の機能の中には、実行する前に本当にその処理を実行するか確認するためのポップアップを表示するものがあります。
例えば、 *情報* エリアのメニュー *ファイル* > *スタートアップファイルを保存* が実行確認のポップアップを表示する例です。

実行確認のポップアップは、 ```context.window_manager.invoke_confirm()``` 関数により表示することができます。

本節のサンプルでは、以下のようにして実行確認のポップアップを表示しています。

```python
class ShowConfirmPopup(bpy.types.Operator):
    bl_idname = "object.show_confirm_popup"
    bl_label = "確認ポップアップ"
    bl_description = "確認ポップアップ"
    bl_options = {'REGISTER', 'UNDO'}

    def execute(self, context):
        self.report({'INFO'}, "確認ポップアップボタンをクリックしました")
        return {'FINISHED'}

    def invoke(self, context, event):
        wm = context.window_manager
        # 確認メッセージ表示
        return wm.invoke_confirm(self, event)
```

実行確認のポップアップは、 ```invoke()``` メソッド内から ```wm.invoke_confirm()``` 関数を呼び出して表示しています。

```vm.invoke_confirm()``` 関数の引数を以下に示します。

|引数|意味|
|---|---|
|第1引数|実行確認ポップアップで実行を決定したときに呼び出される ```execute()``` メソッドが定義されたオペレータクラスのインスタンス|
|第2引数|```invoke()``` メソッドの引数 ```event``` を指定|

クリック時に実行確認のポップアップを表示するボタンを表示する処理は、以下の通りです。

```python
# 確認ポップアップを表示する
layout.label(text="確認ポップアップを表示する:")
layout.operator(ShowConfirmPopup.bl_idname)
```


#### プロパティ付きポップアップを表示する

プロパティを変更することができる、プロパティ付きポップアップを作ることもできます。
実行結果を見るとダイアログメニューと同じ動作のように見えますが、ダイアログメニューは ```OK``` ボタンを押すまで処理が実行されないのに対し、プロパティ付きポップアップではプロパティを変更するたびに処理が実行されます。

本節のサンプルでは、以下のようにしてプロパティ付きポップアップを表示しています。

```python
class ShowPropertyPopup(bpy.types.Operator):
    bl_idname = "object.show_property_popup"
    bl_label = "プロパティ付きポップアップ"
    bl_description = "プロパティ付きポップアップ"
    bl_options = {'REGISTER', 'UNDO'}

    prop_int = IntProperty(
        name="プロパティ 1",
        description="プロパティ 1",
        default=100,
        min=0,
        max=255)
    prop_float = FloatProperty(
        name="プロパティ 2",
        description="プロパティ 2",
        default=0.75,
        min=0.0,
        max=1.0)
    prop_enum = EnumProperty(
        name="プロパティ 3",
        description="プロパティ 3",
        items=[
            ('ITEM_1', "項目 1", "項目 1"),
            ('ITEM_2', "項目 2", "項目 2"),
            ('ITEM_3', "項目 3", "項目 3")],
        default='ITEM_1')
    prop_floatv = FloatVectorProperty(
        name="プロパティ 4",
        description="プロパティ 4",
        subtype='COLOR_GAMMA',
        default=(1.0, 1.0, 1.0),
        min=0.0,
        max=1.0)

    def execute(self, context):
        self.report({'INFO'}, "1: %d, 2: %f, 3: %s, 4: (%f, %f, %f)"
            % (self.prop_int, self.prop_float, self.prop_enum, self.prop_floatv[0], self.prop_floatv[1], self.prop_floatv[2]))
        return {'FINISHED'}

    def invoke(self, context, event):
        wm = context.window_manager
        # プロパティ付きポップアップ表示
        return wm.invoke_props_popup(self, event)
```

プロパティ付きポップアップは、```invoke()``` メソッド内から ```wm.invoke_props_popup()``` 関数を実行することで表示することができます。
プロパティを変更すると ```execute()``` メソッドが実行され、 現在のプロパティの値が *コンソール・ウィンドウ* に表示されます。

```wm.invoke_props_popup()``` 関数には、以下のような引数を指定します。

|引数|意味|
|---|---|
|第1引数|プロパティを変えた時に呼び出される ```execute()``` メソッドが定義されたオペレータクラスのインスタンス|
|第2引数|```invoke()``` メソッドの引数 ```event``` を指定|

なお、プロパティ付きポップアップで表示されたプロパティは、 *ツール・シェルフ* の *オプション* にも表示されているため、ポップアップが閉じてしまった場合でも他の操作を行わなわない限り変更することができます。

クリック時にプロパティ付きポップアップを表示するボタンを表示する処理は、以下に示します。

```python
# プロパティ付きポップアップを表示する
layout.label(text="プロパティ付きポップアップを表示する:")
layout.operator(ShowPropertyPopup.bl_idname)
```


#### 検索ウィンドウ付きポップアップを表示する

あらかじめ登録した項目について検索することができる、検索ウィンドウ付きのポップアップを表示することができます。
実際この UI がどのように役立つのかよくわかっていませんが、 Blender の API として用意されていましたので紹介します。

本節のサンプルでは、検索ウィンドウ付きポップアップを以下のようにして表示しています。

```python
class ShowSearchPopup(bpy.types.Operator):
    bl_idname = "object.show_search_popup"
    bl_label = "検索ウィンドウ付きポップアップ"
    bl_description = "検索ウィンドウ付きポップアップ"
    bl_options = {'REGISTER', 'UNDO'}
    bl_property = "item"

    item = EnumProperty(
        name="配置位置",
        description="複製したオブジェクトの配置位置",
        items=[
            ('ITEM_1', '項目1', '項目1'),
            ('ITEM_2', '項目2', '項目2'),
            ('ITEM_3', '項目3', '項目3')
        ],
        default='ITEM_1'
    )

    def execute(self, context):
        self.report({'INFO'}, "%s を選択しました" % self.item)
        return {'FINISHED'}

    def invoke(self, context, event):
        wm = context.window_manager
        # 検索ウィンドウ付きポップアップ表示
        wm.invoke_search_popup(self)

        # {'FINISHED'} を返す必要がある
        return {'FINISHED'}
```

検索ウィンドウ付きのポップアップを表示するためには、 ```invoke()``` メソッド内で ```wm.invoke_search_popup()``` 関数を使います。
引数には、項目確定時に呼び出される ```execute()``` メソッドが定義されたクラスのインスタンスを指定します。
なお ```invoke()``` メソッドは ```{'FINISHED'}``` を返す必要があります。

項目を確定すると ```execute()``` メソッドが呼び出され、選択した項目の識別子が *コンソール・ウィンドウ* に表示されます。

検索ウィンドウで検索できる項目は、アドオン開発者が追加する必要があります。
検索ウィンドウへ追加する項目リストを持つ変数は ```EnumProperty``` プロパティクラスの型である必要があり、 メンバ変数 ```bl_property``` にその変数名を記載する必要があります。
本節のサンプルでは、メンバ変数 ```item``` が項目リストであるため、 ```bl_property="item"``` としています。

クリック時に検索ウィンドウ付きポップアップを表示するボタンを表示する処理は、以下に示します。

```python
# 検索ポップアップを表示する
layout.label(text="検索ポップアップを表示する:")
layout.operator(ShowSearchPopup.bl_idname)
```

## まとめ


|UI|API|
|---|---|
|ポップアップメッセージ|```context.window_manager.invoke_popup()```|
|ダイアログメニュー|```context.window_manager.invoke_props_dialog()```|
|ファイルブラウザ|```context.window_manager.fileselect_add()```|
|確認ポップアップ|```context.window_manager.invoke_confirm()```|
|プロパティ付きポップアップ|```context.window_manager.invoke_props_popup()```|
|検索ウィンドウ付きポップアップ|```context.window_manager.invoke_search_popup()```|
|メニューへの項目追加(末尾)|```append()```|
|メニューへの項目追加(先頭)|```prepend()```|



|UI|API|
|---|---|
|メニューへの項目追加(末尾)|```append()```|
|メニューへの項目追加(先頭)|```prepend()```|

本節では Blender の UI を構築する方法を説明しましたが、わかりやすい UI を構築するためのポイントについては説明していません。
わかりやすい UI を構築するのはアドオンの開発と異なり、はっきりとした答えがないため非常に難しいです。
他の Blender の アドオンの UI を参考にするだけでなく、他の人が作成した Web ページやアプリの画面などにもアンテナを常に張り巡らせ、自分で良いと思ったデザインを真似して吸収していくのが、わかりやすい UI を構築する最も早い道であると思います。

本節で本書の前編は終了です。
ここまで読まれた方であれば、アドオンを作るだけでなく Python で書かれている Blender の UI も自由に変更することができるようになるでしょう。
本書の後半では、より高度なアドオンを作りたい人向けの説明を行います。


<div id="point"></div>

### ポイント

<div id="point_item"></div>

* オペレータクラスに定義する ```invoke()``` メソッドは、オペレータクラスが実行された時に呼ばれるメソッドで、 ```execute()``` メソッドより前に呼ばれる
* 本節のサンプルは、アドオンから利用可能なアイコンの一覧を確認できる
* UI の構築方法を知ることとわかりやすい UI の構築することは別物である。わかりやすい UI を構築するために他人が作成した UI を参考にしよう