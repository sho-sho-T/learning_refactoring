# 関数のインライン化

## 概要

関数のインライン化は、関数呼び出しをその関数の内容で直接置き換えるリファクタリング技法です。この技法は、不必要な間接参照を削除し、コードをより直接的で理解しやすいものにするために使用されます。

## 例

### Before

```typescript
const getRating = (driver: Driver) => {
  return moreThanFiveLateDeliveries(driver) ? 2 : 1;
};

const moreThanFiveLateDeliveries = (driver: Driver) => {
  return driver.numberOfLateDeliveries > 5;
};
```

### After

```typescript
const getRating = (driver: Driver) => {
  return driver.numberOfLateDeliveries > 5 ? 2 : 1;
};
```

## 動機

1. **コードの簡素化**: 関数の本体が名前と同じくらい明確な場合、インライン化によってコードがより直接的になります。
2. **過度の間接参照の除去**: 不必要な関数呼び出しを削除することで、コードの実行効率が向上する可能性があります。
3. **リファクタリングの準備**: 複数の関数を一度インライン化し、その後より適切な形で再抽出することで、より良い設計に到達できることがあります。
4. **重複の削減**: インライン化によって似た関数の存在が明らかになり、重複を削減する機会が得られます。

## 手順

1. **ポリモーフィズムのチェック**:
   関数がポリモーフィックなメソッドでないことを確認します。クラスのメソッドで、サブクラスでオーバーライドされている場合はインライン化できません。

   ```typescript
   // インライン化できない例
   class Parent {
     virtual method() { /* ... */ }
   }
   class Child extends Parent {
     override method() { /* ... */ }
   }
   ```

2. **呼び出し元の特定**:
   この関数の呼び出し元を全て見つけます。

   ```typescript
   // 呼び出し元の例
   const rating1 = getRating(driver1);
   const rating2 = getRating(driver2);
   ```

3. **関数の中身での置き換え**:
   関数の各呼び出し元を関数の中身で置き換えます。

   ```typescript
   // Before
   const rating1 = getRating(driver1);

   // After
   const rating1 = driver1.numberOfLateDeliveries > 5 ? 2 : 1;
   ```

4. **テスト**:
   一つ置き換えるごとにテストを実行し、機能が正しく動作することを確認します。

5. **関数定義の削除**:
   全ての呼び出し元の置き換えが完了し、テストが通ったら、元の関数の定義を削除します。

   ```typescript
   // この関数定義を削除
   const moreThanFiveLateDeliveries = (driver: Driver) => {
     return driver.numberOfLateDeliveries > 5;
   };
   ```

## 応用例

複数の関数をインライン化して、より適切な形で再構成する例：

### Before

```typescript
const calculateTotal = (order: Order) => {
  return (
    calculateSubtotal(order) + calculateTax(order) + calculateShipping(order)
  );
};

const calculateSubtotal = (order: Order) => {
  return order.items.reduce(
    (total, item) => total + item.price * item.quantity,
    0
  );
};

const calculateTax = (order: Order) => {
  return calculateSubtotal(order) * 0.1;
};

const calculateShipping = (order: Order) => {
  return order.items.length > 0 ? 5.99 : 0;
};
```

### After

```typescript
const calculateTotal = (order: Order) => {
  const subtotal = order.items.reduce(
    (total, item) => total + item.price * item.quantity,
    0
  );
  const tax = subtotal * 0.1;
  const shipping = order.items.length > 0 ? 5.99 : 0;
  return subtotal + tax + shipping;
};
```

## 利点

- コードがより直接的で理解しやすくなります。
- 不必要な関数呼び出しのオーバーヘッドが削減されます。
- コードの重複が明確になり、削減の機会が得られます。

## 注意点

- インライン化によってコードが複雑になる場合は、この技法の適用を再考してください。
- 広く再利用されている関数や、将来的に変更される可能性が高い関数のインライン化は慎重に行ってください。
- インライン化後も、コードの意図が明確であることを確認してください。必要に応じて、コメントや変数名の改善を検討します。

この技法を適切に適用することで、コードの直接性と効率性を向上させることができます。ただし、各状況に応じて慎重に判断し、コードの可読性と保守性のバランスを保つことが重要です。
