# 変数の抽出

## 例

```typeScript
return order.quantity * order.itemPrice -
    Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
    Math.min(order.quantity * order.itemPrice * 0.1, 100);
```

**⇩**

```typeScript
const basePrice = order.quantity * order.itemPrice;
const quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
const shipping = Math.min(basePrice * 0.1, 100);

return basePrice - quantityDiscount + shipping;
```

## 動機

式は複雑で読みにくくなることがあるので、ローカル変数を活用して分解することで扱いやすくできる。

デバッグにも便利（デバッガやプリント文を仕掛けて内容を把握しやすい）

「変数の抽出」を検討するのは**コード内の式に名前をつけたいとき**

## 手順

### ① 抽出しようとする式に副作用がないことを確認

### ② 変更不可な変数を定義する。名付けたい式の値をその変数に設定する

### ③ 元の式を新しい変数で置き換える

### ④ テストする
