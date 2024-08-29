# 関数の移動：詳細ガイド

## 概要

「関数の移動」は、関数やメソッドを、より適切なクラスや模块に移動させるリファクタリング技術です。これにより、コードの構造を改善し、関連する機能をより論理的にグループ化することができます。

## 例

### Before

```typescript
class Order {
  private customer: Customer;
  private items: OrderItem[];

  calculateTotalPrice(): number {
    let basePrice = this.items.reduce((sum, item) => sum + item.price, 0);
    return this.applyCustomerDiscount(basePrice);
  }

  private applyCustomerDiscount(basePrice: number): number {
    if (this.customer.isPreferred()) {
      return basePrice * 0.95; // 5% discount for preferred customers
    }
    return basePrice;
  }
}

class Customer {
  private preferred: boolean;

  isPreferred(): boolean {
    return this.preferred;
  }
}
```

### After

```typescript
class Order {
  private customer: Customer;
  private items: OrderItem[];

  calculateTotalPrice(): number {
    let basePrice = this.items.reduce((sum, item) => sum + item.price, 0);
    return this.customer.applyDiscount(basePrice);
  }
}

class Customer {
  private preferred: boolean;

  isPreferred(): boolean {
    return this.preferred;
  }

  applyDiscount(basePrice: number): number {
    if (this.isPreferred()) {
      return basePrice * 0.95; // 5% discount for preferred customers
    }
    return basePrice;
  }
}
```

## 動機

1. **責任の適切な配置**: 関数を、その機能に最も関連のあるクラスや模块に配置することで、コードの論理的構造が改善されます。

2. **結合度の低減**: 他のクラスのデータに過度に依存している関数を移動することで、クラス間の結合度を下げることができます。

3. **凝集度の向上**: 関連する機能を一箇所にまとめることで、クラスや模块の凝集度が高まります。

4. **コードの再利用性の向上**: 適切な場所に配置された関数は、他の場面でも再利用しやすくなります。

5. **保守性の向上**: 関連する機能が論理的にグループ化されることで、コードの理解と保守が容易になります。

## 手順

1. **関数の依存関係の分析**:

   - 移動対象の関数が現在のコンテキストで使用しているプログラム要素（変数、他の関数など）を全て調べます。
   - それらの要素も一緒に移動すべきかどうかを検討します。

2. **ポリモーフィズムの確認**:

   - 移動対象の関数がポリモーフィックなメソッドでないことを確認します。

3. **関数の移動**:

   - 関数を移動先のクラスまたは模块にコピーします。
   - 新しい環境に合わせて、必要な調整（パラメータの追加・削除など）を行います。

   ```typescript
   class Customer {
     // ... 既存のコード ...

     applyDiscount(basePrice: number): number {
       if (this.isPreferred()) {
         return basePrice * 0.95;
       }
       return basePrice;
     }
   }
   ```

4. **静的解析の実行**:

   - コードの静的解析を行い、構文エラーや型エラーがないことを確認します。

5. **参照の更新**:

   - 元のクラス（この例では `Order`）から新しい場所（`Customer`）の関数を参照できるようにします。

   ```typescript
   class Order {
     // ... 既存のコード ...

     calculateTotalPrice(): number {
       let basePrice = this.items.reduce((sum, item) => sum + item.price, 0);
       return this.customer.applyDiscount(basePrice);
     }
   }
   ```

6. **元の関数の処理**:

   - 元の関数を削除するか、必要に応じて委譲関数に変更します。

7. **テスト**:

   - ユニットテストと統合テストを実行し、機能が正しく動作することを確認します。

8. **最適化の検討**:
   - 必要に応じて、元の場所に残った委譲関数に対して「関数のインライン化」の適用を検討します。

## 利点

- **コードの構造改善**: 関連する機能が論理的にグループ化されます。
- **保守性の向上**: コードの理解と修正が容易になります。
- **再利用性の向上**: 適切に配置された関数は他の場面でも使いやすくなります。
- **拡張性の向上**: 将来の機能追加や変更が容易になります。

## 注意点

- **影響範囲の確認**: 関数の移動が他の部分に与える影響を慎重に評価する必要があります。
- **テストの重要性**: 移動後の機能が正しく動作することを確認するため、十分なテストが不可欠です。
- **段階的な実施**: 大規模な移動は、小さな段階に分けて実施することをおすすめします。

## まとめ

「関数の移動」は、コードの構造を改善し、保守性と再利用性を高めるための強力なリファクタリング技術です。適切に実施することで、より論理的で理解しやすいコード構造を実現できます。ただし、慎重な分析とテストが重要です。システムの全体的な設計を考慮しながら、適切なタイミングでこの技術を適用することが、長期的なコードの品質向上につながります。
