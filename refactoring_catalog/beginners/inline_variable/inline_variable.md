# 変数のインライン化

## 概要

変数のインライン化は、変数の使用箇所をその変数の値で直接置き換えるリファクタリング技法です。この技法は、不必要な変数を削除し、コードをより直接的で理解しやすいものにするために使用されます。

## 例

### Before

```typescript
let basePrice = anOrder.basePrice;
return basePrice > 1000;
```

### After

```typescript
return anOrder.basePrice > 1000;
```

## 動機

1. **コードの簡素化**: 変数名が式以上の情報を提供していない場合、インライン化によってコードがより直接的になります。
2. **不要な抽象化の除去**: 時として変数は不必要な抽象化レベルを導入することがあり、それを取り除くことでコードが明確になります。
3. **リファクタリングの準備**: 他のリファクタリング技法を適用する前の準備段階として使用されることがあります。
4. **パフォーマンスの微小な改善**: 不要な変数割り当てを減らすことで、わずかながらパフォーマンスが向上する可能性があります。

## 手順

1. **副作用のチェック**:
   代入の右辺に副作用がないことを確認します。副作用がある場合、インライン化は適切でない可能性があります。

   ```typescript
   // 副作用がある例（インライン化すべきでない）
   let basePrice = calculateBasePrice(); // calculateBasePrice() が副作用を持つ可能性
   ```

2. **変更不可性の確認**:
   その変数が変更不可（const）と宣言されていなければ、変更不可にしてテストします。これにより、変数が再代入されていないことを確認できます。

   ```typescript
   const basePrice = anOrder.basePrice;
   // テストを実行して問題がないことを確認
   ```

3. **最初の参照の置き換え**:
   その変数への最初の参照を探し、代入の右辺と置き換えます。

   ```typescript
   // Before
   const basePrice = anOrder.basePrice;
   return basePrice > 1000;

   // After
   return anOrder.basePrice > 1000;
   ```

4. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

5. **残りの参照の置き換え**:
   変数を参照している箇所の置き換えを繰り返し、全ての参照箇所を更新します。

   ```typescript
   // 複数箇所で使用されている場合
   // Before
   const basePrice = anOrder.basePrice;
   if (basePrice > 1000) {
     console.log("High value order");
   }
   return basePrice * 0.1;

   // After
   if (anOrder.basePrice > 1000) {
     console.log("High value order");
   }
   return anOrder.basePrice * 0.1;
   ```

6. **変数宣言の削除**:
   変数の宣言と代入を取り除きます。

7. **最終テスト**:
   全ての変更が完了したら、再度テストを実行して機能が正しく動作することを確認します。

## 応用例

複数の変数をインライン化して、コードをより直接的にする例：

### Before

```typescript
const isSpecialDeal = (order: Order) => {
  const basePrice = order.basePrice;
  const quantity = order.quantity;
  const threshold = 100;
  return basePrice * quantity > threshold;
};
```

### After

```typescript
const isSpecialDeal = (order: Order) => {
  return order.basePrice * order.quantity > 100;
};
```

## 利点

- コードがより直接的で理解しやすくなります。
- 不要な抽象化レベルが削除され、コードの意図が明確になります。
- 変数名と実際の値の不一致によるバグのリスクが減少します。

## 注意点

- インライン化によってコードが複雑になる場合は、この技法の適用を再考してください。
- 同じ式が複数回使用される場合、インライン化によって冗長性が増す可能性があります。その場合、変数の使用を維持することを検討してください。
- パフォーマンスに敏感な部分では、複雑な式のインライン化が計算の重複を引き起こす可能性があることに注意してください。

この技法を適切に適用することで、コードの直接性と効率性を向上させることができます。ただし、各状況に応じて慎重に判断し、コードの可読性と保守性のバランスを保つことが重要です。
