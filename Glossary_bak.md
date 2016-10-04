<div id="sect_title_img_0_0"></div>

<div id="sect_title_text"></div>

# 用語集

<div id="preface"></div>

###### 　


### プロパティパネル

エリア上でNキーを押した時に、エリアの右側に表示されるパネルです。

### ツール・シェルフ

エリア上でTキーを押した時に、エリアの左側に表示されるパネルです。

### テキストエディター

Blenderに複数あるエリアのうちの1つで、テキストを編集する機能を持ちます。Blenderの機能の中で、アドオン開発者がよく利用する機能のうちの1つです。

入力補完やシンタックスハイライトなどの機能を持ち、Blender向けのPythonスクリプトを作成することに特化されています。Pythonスクリプトをテキストエディターから実行することもできるため、簡単なPythonスクリプトを実行する時に重宝します。

また、テキストエディターではアドオンのテンプレート(サンプル)を参照することもできるため、アドオン開発の勉強に役立ちます。

### コンソール

Windowsのコマンド・プロンプト、MacやLinuxのターミナルのことを指します。

コンソールからBlenderを起動することで、デバッグ情報をコンソールに出力することができます。デバッグ情報はアドオン開発時に大変参考になるため、アドオン開発者はコンソールからBlenderを起動することを心がけましょう。

### コンソール・ウィンドウ

情報エリアのメニューの上にある、メッセージが表示される場所を指します。

初期状態でBlenderを起動した時は非表示状態となっており、表示するためには情報エリアのメニューを下に移動させる必要があります。

コンソール・ウィンドウには、過去にBlenderで行った操作やログ、警告、エラー情報が表示されます。特にエラー情報はアドオン開発時に大変役立つ情報であるため、アドオン開発時は常にコンソール・ウィンドウを表示しておくと良いと思います。

### サポートレベル

アドオンの提供方法のことで、全てのアドオンは提供方法に応じてRelease/Contrib/Externalの3つのいずれかのサポートレベルに決まります。

ReleaseはBlender本体とともに提供されるアドオン、ContribはテストビルドされたBlender本体と共に提供されるアドオン、Externalは各個人が提供するアドオンです。このことからサポートレベルは、アドオンの品質やメンテナンスなどの将来性を示していると考えても良いです。

Contrib以上のサポートレベルにするためには、Blender開発者のレビューに合格する必要があります。

### パネルクラス

プロパティパネルやツール・シェルフにUIを構築するためのクラスです。

bpy.types.Panelクラスを継承して作成します。draw()メソッドにUIを構築するための処理を記述します。

### プロパティ

メニューなどから処理を実行した時に、ツール・シェルフの下側に表示されるUIです。

値を変更することで、直前に行った処理に対してパラメータを変更して再実行することができます。

### プロパティ用クラス

bpy.propsモジュールに含まれるクラス全体を指します。

ツール・シェルフの下側に表示されるプロパティやパネルのUIの構築、クラス間のパラメータ受け渡しなどに使用します。整数型や浮動小数点型など、基本的な型がプロパティ用クラスとして用意されています。

### エリア

Blenderは3DビューやUVエディターなど、機能ごとに複数の領域に分割することができます。この領域のことをエリアと言います。

エリアは複数のリージョンから構成されます。

### リージョン

ツール・シェルフやプロパティパネルなど、エリア内のUIのまとまり単位をリージョンと言います。

### オペレータクラス

アドオンの機能の処理を記述するクラスです。

bpy.types.Operationクラスを継承して作成します。execute()メソッドに機能の処理を記述します。

### モーダルクラス

モーダルモード時に実行される、modal()メソッドが定義されたクラスです。

bpy.types.Operationクラスを継承して作成します。modal()メソッドはキー入力やマウス移動などのイベント発生時に呼ばれるため、インタラクティブ性のあるアドオンを作成するときに利用します。

<div id="space_m"></div>


### メニュークラス

メニューを構築するためのクラスです。

bpy.types.Menuクラスを継承して作成します。draw()メソッドにメニュー構築のための処理を記述します。

### モーダルモード

キー入力やマウス移動などのイベントを受け取り続けるモードです。

モーダルモード中は、context.window_manager.modal_handler_add()に指定されたモーダルクラスのmodal()メソッドが、イベント発生の度に呼ばれ続けます。

モーダルモードを終了させるためには、modal()メソッドが {'FINISHED'} または {'CANCELLED'} を返す必要があります。