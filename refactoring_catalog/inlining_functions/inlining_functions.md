# 関数のインライン化

## 例

```typeScript
const getRating = (driver: Driver) => {
    return moreThanFiveLateDeliveries(driver) ? 2 : 1;
};

const moreThanFiveLateDeliveries = (driver: Driver) => {
    return driver.numberOfLateDeliveries > 5;
};
```

**⇩**

```typeScript
const getRating = (driver: Driver) => {
    return (driver.numberOfLateDeliveries > 5) ? 2 : 1;
}
```

## 動機

意図がわかるように命名された短い関数を使うことで、コードを明快に読みやすいものにしてくれるが、時として、関数の本体がその名前と同じくらいに分かりやすいこともある。その場合はその関数を取り除いてしまおう。

## 手順

### ① 関数がポリモーフィックなメソッドでないことを確認する

### ② この関数の呼び出し元を全て見つける

### ③ 関数の各呼び出し元を関数の中身で置き換える

### ④ 一つ置き換えるごとにテストする

### ⑤ 関数の定義を取り除く
