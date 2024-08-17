# 変数の抽出

## 概要

変数の抽出は、複雑な式や計算をより小さく、理解しやすい部分に分解するリファクタリング技法です。この技法を適用することで、コードの可読性が向上し、デバッグや保守が容易になります。

## 例

### Before

```typescript
function calculatePrice(order: Order): number {
  return (
    order.quantity * order.itemPrice -
    Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
    Math.min(order.quantity * order.itemPrice * 0.1, 100)
  );
}
```

### After

```typescript
function calculatePrice(order: Order): number {
  const basePrice = order.quantity * order.itemPrice;
  const quantityDiscount =
    Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
  const shipping = Math.min(basePrice * 0.1, 100);

  return basePrice - quantityDiscount + shipping;
}
```

## 動機

1. **可読性の向上**: 複雑な式を意味のある名前を持つ変数に分解することで、コードの意図がより明確になります。
2. **デバッグの容易さ**: 中間結果を変数に格納することで、デバッガやログ出力を使って値を確認しやすくなります。
3. **重複の削減**: 同じ式が複数回使用される場合、変数として抽出することで重複を減らせます。
4. **ロジックの明確化**: 各ステップに名前を付けることで、計算プロセスが明確になります。
5. **テストの容易さ**: 複雑な計算の中間段階を変数として抽出することで、個別にテストしやすくなります。

## 手順

1. **副作用の確認**:
   抽出しようとする式に副作用がないことを確認します。副作用がある場合、変数の抽出は適切でない可能性があります。

2. **変数の定義**:
   変更不可な変数（const）を定義し、名付けたい式の値をその変数に設定します。

   ```typescript
   const basePrice = order.quantity * order.itemPrice;
   ```

3. **式の置き換え**:
   元の式を新しい変数で置き換えます。

   ```typescript
   const basePrice = order.quantity * order.itemPrice;
   const quantityDiscount =
     Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
   const shipping = Math.min(basePrice * 0.1, 100);

   return basePrice - quantityDiscount + shipping;
   ```

4. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

5. **他の箇所の検討**:
   同じ式や類似した式が他の箇所にも存在する場合、それらも同様に変数として抽出することを検討します。

## 応用例

複雑な条件式の場合も同様に変数の抽出を適用できます：

### Before

```typescript
if (date.before(SUMMER_START) || date.after(SUMMER_END)) {
  charge = quantity * winterRate + winterServiceCharge;
} else {
  charge = quantity * summerRate;
}
```

### After

```typescript
const isSummer = date.after(SUMMER_START) && date.before(SUMMER_END);
const winterCharge = quantity * winterRate + winterServiceCharge;
const summerCharge = quantity * summerRate;

if (isSummer) {
  charge = summerCharge;
} else {
  charge = winterCharge;
}
```

## 利点

- コードの意図がより明確になります。
- デバッグが容易になります。
- 複雑な計算や条件を分解することで、理解とメンテナンスが容易になります。
- 中間結果に名前を付けることで、ドメイン概念をコードに反映しやすくなります。

## 注意点

- 過度に多くの変数を導入すると、かえってコードが読みにくくなる可能性があります。適度なバランスを保つことが重要です。
- 変数名は慎重に選択し、その変数が表す概念を正確に反映するようにしてください。
- パフォーマンスに敏感な部分では、不必要な変数の導入がわずかなオーバーヘッドを生む可能性があることに注意してください。
- 抽出した変数が他の場所でも使用される可能性がある場合は、関数の抽出を検討してください。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルの抽出を選択することが重要です。
