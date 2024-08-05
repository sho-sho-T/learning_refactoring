# 変数のカプセル化

## 例

```typeScript

let defaultOwner = { firstName: "Martin", lastName: "Fowler" };

```

**⇩**

```typeScript

let defaultOwnerData = { firstName: "Martin", lastName: "Fowler" };

export const defaultOwner = () => {
    return defaultOwnerData;
};

export const setDefaultOwner = (arg) => {
    defaultOwnerData = arg;
};

```

## 動機

広い範囲で利用されるデータを移動したい時は、まずそれをカプセル化して、変数へのアクセスを関数経由にするのが良い。

カプセル化は、データの変更や参照を監視できる利点を得られ、データ更新の際の検証や後処理を追加するのも簡単。

**データのスコープが大きくなるほどカプセル化はより重要になる**

## 手順

### ① 変数を参照・更新するためのカプセル化用関数を作る

### ② 静的チェックを実行する

### ③ 変数への参照を、１つひとつ適切なカプセル化関数の呼び出しに置き換える。置き換えるごとにテストする

### ④ 変数の可視性を制限する

- 変数への直接アクセスを防げないことがよくある。その場合、変数名を変更してテストすることで、置き換えが漏れた参照を見つけることができる

### ⑤ テストする

### ⑥ 変数の値がレコードの場合 「レコードのカプセル化」を検討する。
