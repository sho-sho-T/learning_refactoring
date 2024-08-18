# 問い合わせによる一時変数の置き換え

## 概要

問い合わせによる一時変数の置き換えは、一時変数をメソッド（問い合わせ）に置き換えるリファクタリング技法です。この技法により、コードの再利用性が向上し、関数の抽出が容易になります。

## 例

### Before

```typescript
class Order {
  private _quantity: number;
  private _itemPrice: number;

  constructor(quantity: number, itemPrice: number) {
    this._quantity = quantity;
    this._itemPrice = itemPrice;
  }

  calculateTotal() {
    const basePrice = this._quantity * this._itemPrice;
    if (basePrice > 1000) {
      return basePrice * 0.95;
    } else {
      return basePrice * 0.98;
    }
  }
}
```

### After

```typescript
class Order {
  private _quantity: number;
  private _itemPrice: number;

  constructor(quantity: number, itemPrice: number) {
    this._quantity = quantity;
    this._itemPrice = itemPrice;
  }

  get basePrice() {
    return this._quantity * this._itemPrice;
  }

  calculateTotal() {
    if (this.basePrice > 1000) {
      return this.basePrice * 0.95;
    } else {
      return this.basePrice * 0.98;
    }
  }
}
```

## 動機

1. **コードの再利用性**: メソッドとして抽出することで、他の場所でも同じロジックを簡単に使用できます。
2. **関数の抽出の容易さ**: 変数をメソッドに置き換えることで、大きな関数の分解が容易になります。
3. **依存性と副作用の特定**: メソッドとして抽出することで、依存関係や副作用がより明確になります。
4. **重複の回避**: 似通った関数での計算ロジックの重複を避けることができます。
5. **カプセル化の向上**: クラス内でこの技法を使用すると、内部状態へのアクセスをより制御しやすくなります。

## 手順

1. **値の確定性の確認**:
   一時変数の値が使用前に確定していること、および計算が常に同じ結果を返すことを確認します。

2. **変数の読み取り専用化**:
   可能であれば、一時変数を読み取り専用（const）にします。

   ```typescript
   const basePrice = this._quantity * this._itemPrice;
   ```

3. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

4. **関数の抽出**:
   一時変数への代入を関数として抽出します。

   ```typescript
   get basePrice() {
       return this._quantity * this._itemPrice;
   }
   ```

   注意: 抽出された関数に副作用がないことを確認します。副作用がある場合は、「問い合わせと更新の分離」を適用します。

5. **テスト**:
   抽出後、再度テストを実行して機能が正しく動作することを確認します。

6. **変数のインライン化**:
   「変数のインライン化」を適用して、一時変数を完全に取り除きます。

   ```typescript
   calculateTotal() {
       if (this.basePrice > 1000) {
           return this.basePrice * 0.95;
       } else {
           return this.basePrice * 0.98;
       }
   }
   ```

## 応用例

複数の一時変数を問い合わせに置き換える例：

```typescript
class Product {
  constructor(private basePrice: number, private discountRate: number) {}

  get price() {
    return this.discountedPrice;
  }

  private get discountedPrice() {
    return this.basePrice * (1 - this.discountRate);
  }

  get discountAmount() {
    return this.basePrice - this.discountedPrice;
  }
}
```

## 利点

- コードの再利用性が向上します。
- 大きな関数の分割が容易になります。
- 依存関係や副作用がより明確になります。
- コードの重複を減らすことができます。
- オブジェクト指向プログラミングの原則に沿ったコードになります。

## 注意点

- 過度な適用は避け、適切なバランスを保つことが重要です。
- パフォーマンスに敏感な部分では、メソッド呼び出しのオーバーヘッドを考慮する必要があります。
- 複雑な計算や副作用を持つ処理の場合、この技法の適用は慎重に検討する必要があります。
- クラス外で広く使用される変数の場合、この技法の適用が適切かどうか慎重に判断してください。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルの抽出を選択することが重要です。
