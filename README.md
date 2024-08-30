コーディングルールが厳格に定められていないプロジェクトにおいては名前の衝突や意図しないソースコードの改変が発生するため、ネーミングルールを統一できるよう資料化する。

## 前提
- 記法については呼ばれ方がいくつかあるが区別のしやすさから以下の表の通り呼称することとする。

|呼称|表記例|
|---|---|
|大文字スネークケース|LARGE_SNAKE_CASE|
|小文字スネークケース|small_snake_case|
|キャメルケース|camelCase|
|パスカルケース|PascalCase|

## 基本方針
- ファイルエンコードはUTF-8を使用する。
- インデント(字下げ)はスペース4文字とし、タブ文字は使用しない。
- プロジェクト構成の変更に対応できるようにするためヘッダーのインクルードはinclude/thirdparyフォルダからの相対パスで記載する。
- 変数名,関数名,クラス名等は原則30文字以内とする。
- 略称表記を極力使用しない。略称を用いる場合、下記サイトから省略した表現で検索できることを可能な限り確認する。
  https://www.acronymfinder.com/PARA.html
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
|thirdpary|外部ライブラリを配置|
|lib/bin|バイナリデータ(exe,dll)などを配置<br>(バージョン管理の対象外フォルダとする)|
|build|ビルド時の一時ファイル出力先フォルダ<br>(バージョン管理の対象外フォルダとする)|

ex. 

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
                 |         |--- ship.cpp
                 |         |--- component
                 |               |--- hull.cpp
                 |               |--- rudder.cpp
                 |               |--- propeller.cpp
                 |               |--- ...
                 |
                 |- test
                 |   |--- ship
                 |         |--- test_calculate_total_force.cpp
                 |         |--- component
                 |               |--- hull
                 |               |     |--- test_calculate_force.cpp
                 |               |--- rudder
                 |               |     |--- test_calculate_force.cpp
                 |               |--- propeller
                 |                     |--- test_calculate_force.cpp
                 |
                 |- build
                     |- ...
```

## ファイル名称
- 小文字スネークケース
- ヘッダーファイル、実装部のファイルはそれぞれCPPのコードであることを明示するため(hpp,cpp)と記述する。
- テストコードは明示的にテストであることを表す。

ex. bow_thruster.hpp, test_bow_thruster.hpp, ...

## 変数名
- 小文字スネークケース

ex. gear_ratio, ship_name

ただし、以下のような特別な理由がある場合は例外を許容する。
- パラメータファイル中のキー名と表記を統一したい
- 引用した論文やその他ドキュメントと表記を統一したい
- 変数をJSONやXML文字列へパースする際にできるだけ短い変数名にしたい

### bool変数
- flag_~,または~_lagは使用しない。
- is~, has~など、trueの時にどうなっているかわかる名称にする。

ex. is_executable, has_next, ...

## 関数
小文字スネークケース

C的記法と区別するため引数voidを省略することを推奨する。

### 構造体
- パスカルケース
- 内部にさらに構造体定義がなされている場合は字下げする
- 定義と変数宣言を同時に行わない

C++に構造体とクラスはデフォルトのアクセス指定子の違いのみである。
(structはデフォルトがpublic, classはprivate)
定義対象をstructを使用するかclassにするかは任意であるが
POD型(以下の条件に合致するもの)についてはstructを使用することとする。
- データ構造のみを保持する。
- メンバのアクセシビリティが全て同じであるもの(特に全てpublicのもの)
- メンバ関数を持たないもの

ex. 
``` cpp
struct Geometory
{
    struct Angle
    {
        double degree;
        ...
    };

    Angle latitude;
    Angle longitude;
};
```

## クラス内メンバ
- メンバーのみ字下げを行い、アクセス指定子は字下げしない。
- アクセス指定子はpublic->protected->privateの順に、アクセス指定子の中ではメンバ変数->メンバ関数の順で記述する。
- プライベートメンバ変数は他のローカル変数と区別するため後置のアンダースコアを付与する。(前置ではC++の予約語と重複の恐れがある。)

``` cpp
class Propeller
{
public:
    bool is_calculatable();
    Force* calculate_force();
    ...
protected:
    ...
private:
    double diameter_;
    double gear_ratio_;
    double limit_plus_;
    double limit_minus_;
    ...
}
```

## マクロ, 定数
- 大文字スネークケース

コンパイル時の型チェックができないためマクロを定数値として使用してはならない。
定数値を使用したい場合は以下のようにすること。

``` cpp
// NG
#define CONST_VALUE 10

// OK
static constexpr int CONST_VALUE = 10;
```

ヘッダーで関数型マクロを定義する場合は **変数名を文字列する** 用途以外に使用してはならない。
また、使用するスコープがあらかじめ定まっている場合は使用後にundefすることとする

インクルードガードはinclude配下からの相対パスを踏襲する形で記載する。(下記例はinclude/ship/component/propeller.hpp)
また、endifディレクティブで対象のマクロをコメントとして付与するようにする

ex.
``` cpp
#ifndef SHIP_COMPONENT_PROPELLER_HPP
#define SHIP_COMPONENT_PROPELLER_HPP

...

#endif // SHIP_COMPONENT_PROPELLER_HPP
```

現在、多くのコンパイラは#pragma onceをサポートしているが移植性も考慮し、使用を控える。

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
