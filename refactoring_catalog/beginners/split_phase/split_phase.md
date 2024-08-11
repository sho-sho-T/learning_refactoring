# フェーズの分離

## 例

```typeScript

const orderData = orderString.split(/\s+/);
const productPrice = priceList[orderData[0].split("-")[1]];
const orderPrice = parseInt(orderData[1]) * productPrice;

```

**⇩**

```typeScript

const parseOrder = (aString) => {
    const values = aString.split(/\s+/);
    return ({
        productID: value[0].split("-")[1],
        quantity: parseInt(values[1]),
    });
};

const price = (order, priceList) => {
    return order.quantity * priceList[order.productID];
};

const orderRecord = parseOrder(order);
const orderPrice = price(orderRecord, priceList);

```

## 動機

一つのコードが異なる二つの処理を行っている場合、別々のモジュールに分割する方法がないか探す。

綺麗な分離を行うために、振る舞いを順次的な二段階のフェーズに分けるのが良い。

## 手順

### ① 後半となるフェーズのコードを、関数として抽出する

### ② テストする

### ③ 抽出した関数に追加される引数として、中間データ構造を導入する。

### ④ テストする

### ⑤ 抽出した後半のフェーズの各パラメータを確認する。それらが前半のフェーズでも使われているなら、中間データ構造へと移す。一つ移すごとにテストする。

### ⑥ 前半のフェーズのコードに「関数の抽出」を施し、中間データ構造を返すようにする。
