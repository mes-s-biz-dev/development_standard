コーディングルールが厳格に定められていないプロジェクトにおいては名前の衝突や意図しないソースコードの改変が発生するため、ネーミングルールを統一できるよう資料化する。

## 前提
- 記法については呼ばれ方がいくつかあるが区別のしやすさから以下の表の通り呼称することとする。

|呼称|表記例|
|---|---|
|スネークケース|snake_case|
|キャメルケース|camelCase|
|パスカルケース|PascalCase|
## 基本方針
- 変数名,関数名,クラス名等は原則30文字以内とする。
- 略称表記を極力使用しない。(Velocity -> Vel や Parameter -> Paraなど)
- 予約語との衝突を避けるため、接頭辞としての_(アンダースコアは使用しない。)
- C言語による実装と明示的に区別可能な個所は区別する。
- その他ルールが規定されていないものについては基本的にGoogle社のC++ Style Guideに従うものとする。

https://google.github.io/styleguide/cppguide.html

https://qiita.com/ktsujino/items/d65637660208d59b11a0

## プロジェクトフォルダの構成

プロジェクト直下には以下のようなフォルダを作成し、管理することとする。

|フォルダ名|役割|
|--|--|
|include|各クラスの定義部(hppファイル)を配置|
|src|各クラスの実装部(cppファイル)を配置|
|test|テストコード(cppファイル)を配置|
|build|バイナリデータ(exe,dll)などを配置<br>バージョン管理の対象外フォルダとする|

```
${PROJECT_ROOT} --- 
                 |- .gitignore
                 |- CMakeLists.txt
                 |- include
                 |   |--- ship
                 |         |--- ship.hpp
                 |         |--- component
                 |               |--- hull.hpp
                 |               |--- rudder.hpp
                 |               |--- propeller.hpp
                 |               |--- ...
                 |
                 |- src
                 |   |--- ship
                 |         |--- ship.hpp
                 |         |--- component
                 |               |--- hull.hpp
                 |               |--- rudder.hpp
                 |               |--- propeller.hpp
                 |               |--- ...
                 |
                 |- test
                 |   |--- ship
                 |         |--- test_ship.cpp
                 |         |--- component
                 |               |--- test_hull.cpp
                 |               |--- test_rudder.cpp
                 |               |--- test_propeller.cpp
                 |               |--- ...
                 |
                 |- build
                     |- ...
```

## ファイル名称
- スネークケース
- ヘッダーファイル、実装部のファイルはそれぞれCPPのコードであることを明示するため(hpp,cpp)と記述する。
- テストコードは明示的にテストであることを表す。

ex. bow_thruster.hpp, test_bow_thruster.hpp, ...

## 変数名
- 基本的にはすべて小文字のスネークケース
- 論文中などから引用する変数名を使用する場合についてはこの限りではない。

ex. gear_ratio

### bool変数
- flag~,または~Flagは使用しない。
- is~, has~など、trueの時にどうなっているかわかる名称にする。

ex. is_executable, has_next, ...

## 関数
キャメルケース

### 引数voidを省略する。
- C的記法と区別するため。

## 構造体名, クラス名, 名前空間
パスカルケース

## マクロ, 静的変数
全て大文字のスネークケース

## インクルードガード

ファイルパスを踏襲する形で記載する。
(下記例はinclude/ship/component/propeller.hpp)

``` cpp
#ifndef _SHIP_COMPONENT_PROPELLER_HPP_
#define _SHIP_COMPONENT_PROPELLER_HPP_

...

#endif // _SHIP_COMPONENT_PROPELLER_HPP_
```

## 名前空間
- パスカルケース
- ルールは検討中...

## クラス
- メンバーのみ字下げを行い、アクセス指定子は字下げしない。
- アクセス指定子はprivate->protected->publicの順に、アクセス指定子の中ではメンバ変数->メンバ関数の順で記述する。
- メンバ変数は他のローカル変数と区別するため後置のアンダースコアを付与する。(前置ではC++の予約語と重複の恐れがある。)

``` cpp
class Propeller
{
private:
    double diameter_;
    double gear_ratio_;
    double limit_plus_;
    double limit_minus_;
    ...
    bool is_calculatable();
    Force* calculate_force();
protected:
    ...
public:
    ...
}
```

ヘッダーでインクルードするものは下記由来を基本とする。
C由来のヘッダーはC++用に別途ラップしたものを使用する。

https://cppmap.github.io/standardization/header/
    
    
## コメントの付け方
実装部(cppファイル内)ではなく宣言部(hppファイル内)でコメントは記述する。
クラスメンバについてのコメントの記載方法はメンバー変数についてはDoxygen形式で記載する。
(Doxygenはソースファイルへのコメントからクラス図等のドキュメントをリバース生成するツールである。)

https://qiita.com/developer-kikikaikai/items/3984606dbdbf2bbbe74e

``` cpp
class Propeller
{
private:
    double diameter_;           /**< 直径 */
    double gear_ratio_;         /**< ギア比 */
    double limit_plus_;         /**< +リミット */
    double limit_minus_;        /**< -リミット */
    ...
protected:
    ...
public:
    ...
}
```
