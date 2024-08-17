# フェーズの分離

## 概要

フェーズの分離は、異なる処理を行う一つのコードブロックを、順次的な複数のフェーズに分割するリファクタリング技法です。この技法により、コードの責任が明確に分離され、可読性と保守性が向上します。

## 例

### Before

```typescript
const orderData = orderString.split(/\s+/);
const productPrice = priceList[orderData[0].split("-")[1]];
const orderPrice = parseInt(orderData[1]) * productPrice;
```

### After

```typescript
const parseOrder = (aString: string) => {
  const values = aString.split(/\s+/);
  return {
    productID: values[0].split("-")[1],
    quantity: parseInt(values[1]),
  };
};

const price = (
  order: { productID: string; quantity: number },
  priceList: { [key: string]: number }
) => {
  return order.quantity * priceList[order.productID];
};

const orderRecord = parseOrder(orderString);
const orderPrice = price(orderRecord, priceList);
```

## 動機

1. **責任の分離**: 異なる処理を明確に分けることで、各部分の役割が明確になります。
2. **コードの再利用性**: 分離されたフェーズは、他の場所で再利用しやすくなります。
3. **テストの容易さ**: 各フェーズを個別にテストできるようになります。
4. **保守性の向上**: 一つの変更が他の部分に影響を与えにくくなります。
5. **理解のしやすさ**: 各フェーズの処理が明確になり、コード全体の理解が容易になります。

## 手順

1. **後半フェーズの抽出**:
   後半となるフェーズのコードを、関数として抽出します。

   ```typescript
   const price = (
     orderData: string[],
     priceList: { [key: string]: number }
   ) => {
     const productPrice = priceList[orderData[0].split("-")[1]];
     return parseInt(orderData[1]) * productPrice;
   };
   ```

2. **テスト**:
   抽出した関数が正しく動作することを確認するためのテストを実行します。

3. **中間データ構造の導入**:
   抽出した関数に追加される引数として、中間データ構造を導入します。

   ```typescript
   interface Order {
     productID: string;
     quantity: number;
   }

   const price = (order: Order, priceList: { [key: string]: number }) => {
     return order.quantity * priceList[order.productID];
   };
   ```

4. **テスト**:
   変更後、再度テストを実行して機能が正しく動作することを確認します。

5. **パラメータの移動**:
   抽出した後半のフェーズの各パラメータを確認し、前半のフェーズでも使われているものを中間データ構造へと移します。一つ移すごとにテストします。

6. **前半フェーズの抽出**:
   前半のフェーズのコードに「関数の抽出」を施し、中間データ構造を返すようにします。

   ```typescript
   const parseOrder = (aString: string): Order => {
     const values = aString.split(/\s+/);
     return {
       productID: values[0].split("-")[1],
       quantity: parseInt(values[1]),
     };
   };
   ```

## 応用例

複数のフェーズに分離する例：

```typescript
const processOrder = (
  orderString: string,
  priceList: { [key: string]: number },
  taxRate: number
) => {
  // フェーズ1: 注文の解析
  const order = parseOrder(orderString);

  // フェーズ2: 価格の計算
  const basePrice = calculatePrice(order, priceList);

  // フェーズ3: 税金の適用
  const finalPrice = applyTax(basePrice, taxRate);

  return finalPrice;
};
```

## 利点

- コードの構造が改善され、各部分の責任が明確になります。
- 個々のフェーズを独立してテストできるようになります。
- コードの再利用性が向上します。
- 将来の変更や拡張が容易になります。

## 注意点

- フェーズの分離により、一時的にコードの行数が増える可能性があります。
- 過度な分離は避け、意味のある単位でフェーズを分けることが重要です。
- 分離したフェーズ間でのデータの受け渡しに注意を払う必要があります。
- パフォーマンスに敏感な部分では、フェーズの分離によるオーバーヘッドを考慮する必要があります。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルの分離を選択することが重要です。
