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
                 |   |--- Ship
                 |   |     |--- Ship.hpp
                 |   |     |--- Component
                 |   |           |--- Hull.hpp
                 |   |           |--- Rudder.hpp
                 |   |           |--- Propeller.hpp
                 |   |           |--- ...
                 |   |
                 |   |--- Repository
                 |         |--- ...
                 |
                 |- src
                 |   |--- Ship
                 |   |     |--- Ship.hpp
                 |   |     |--- Component
                 |   |           |--- Hull.hpp
                 |   |           |--- Rudder.hpp
                 |   |           |--- Propeller.hpp
                 |   |           |--- ...
                 |   |
                 |   |--- Repository
                 |         |--- ...
                 |
                 |- test
                 |   |--- Ship
                 |   |     |--- TestShip.hpp
                 |   |     |--- Component
                 |   |           |--- TestHull.hpp
                 |   |           |--- TestRudder.hpp
                 |   |           |--- TestPropeller.hpp
                 |   |           |--- ...
                 |   |
                 |   |--- Repository
                 |         |--- ...
                 |- build
                     |- ...
```

## ファイル名称
- パスカルケース
- テストコードは明示的にテストであることを表す

ex. BowThruster.hpp, TestBowThruster.hpp, ...

## 変数名
- 基本的にはキャメルケース
- 論文中などから引用する変数名を使用する場合についてはこの限りではない。

ex. gearRatio, 

### bool変数
- flag~,または~Flagは使用しない。
- is~, has~など、trueの時にどうなっているかわかる名称にする。

ex. isExecutable, hasNext, ...

## 関数
- キャメルケース
- 

### 引数voidを省略する。
- C的記法と区別するため。

## インクルードガード

ファイルパスを踏襲する形で記載する。
(下記例はinclude/Ship/Component/Propeller.hpp)

``` cpp
#ifndef SHIP_COMPONENT_PROPELLER_HPP
#define SHIP_COMPONENT_PROPELLER_HPP

...

#endif
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
    double gearRatio_;
    double limitPlus_;
    double limitMinus_;
    ...
    bool isCalculatable();
    Force* calculateForce();
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
    double diameter_;          /**< 直径 */
    double gearRatio_;         /**< ギア比 */
    double limitPlus_;         /**< +リミット */
    double limitMinus_;        /**< -リミット */
    ...
protected:
    ...
public:
    ...
}
```
