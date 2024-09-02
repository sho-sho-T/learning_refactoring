# フィールドの移動

## 概要

「フィールドの移動」は、あるクラスのフィールドを、より適切な別のクラスに移動させるリファクタリング技術です。これにより、データ構造を改善し、クラス間の責任をより適切に配分することができます。

## 例

### Before

```typescript
class Customer {
  private _plan: Plan;
  private _discountRate: number;

  constructor(plan: Plan, discountRate: number) {
    this._plan = plan;
    this._discountRate = discountRate;
  }

  get plan(): Plan {
    return this._plan;
  }

  get discountRate(): number {
    return this._discountRate;
  }
}

class Plan {
  // Plan related fields and methods
}
```

### After

```typescript
class Customer {
  private _plan: Plan;

  constructor(plan: Plan) {
    this._plan = plan;
  }

  get plan(): Plan {
    return this._plan;
  }

  get discountRate(): number {
    return this.plan.discountRate;
  }
}

class Plan {
  private _discountRate: number;

  constructor(discountRate: number) {
    this._discountRate = discountRate;
  }

  get discountRate(): number {
    return this._discountRate;
  }
}
```

## 動機

1. **データの一貫性**: フィールドをより関連性の高いクラスに移動することで、データの一貫性が向上します。

2. **カプセル化の強化**: データとそれを操作するロジックを同じ場所に配置することで、カプセル化が強化されます。

3. **クラスの責任の明確化**: フィールドの適切な配置により、各クラスの責任がより明確になります。

4. **コードの重複削減**: 共通のデータを一箇所にまとめることで、コードの重複を減らすことができます。

5. **保守性の向上**: データ構造の改善により、長期的な保守性が向上します。

## 手順

1. **フィールドのカプセル化**:

   - 移動元のクラスでフィールドをカプセル化します（まだされていない場合）。

   ```typescript
   class Customer {
     private _discountRate: number;

     get discountRate(): number {
       return this._discountRate;
     }

     set discountRate(value: number) {
       this._discountRate = value;
     }
   }
   ```

2. **テスト**:

   - この時点でテストを実行し、変更が悪影響を与えていないことを確認します。

3. **移動先クラスへのフィールド追加**:

   - 移動先のクラス（この例では `Plan`）に新しいフィールドとアクセサを追加します。

   ```typescript
   class Plan {
     private _discountRate: number;

     get discountRate(): number {
       return this._discountRate;
     }

     set discountRate(value: number) {
       this._discountRate = value;
     }
   }
   ```

4. **静的解析の実行**:

   - コードの静的解析を行い、構文エラーや型エラーがないことを確認します。

5. **参照の更新**:

   - 移動元のクラス（`Customer`）から新しい場所（`Plan`）のフィールドを参照できるようにします。

   ```typescript
   class Customer {
     get discountRate(): number {
       return this.plan.discountRate;
     }

     set discountRate(value: number) {
       this.plan.discountRate = value;
     }
   }
   ```

6. **テスト**:

   - この時点で再度テストを実行し、変更が正しく機能していることを確認します。

7. **移動元フィールドの削除**:

   - 元のクラス（`Customer`）から古いフィールドを削除します。

   ```typescript
   class Customer {
     // _discountRate フィールドを削除
   }
   ```

8. **最終テスト**:
   - すべての変更が完了した後、総合的なテストを実行し、システム全体が正しく動作することを確認します。

## 利点

- **データ構造の改善**: より論理的なデータ配置により、全体的なデータ構造が改善されます。
- **カプセル化の強化**: データとその操作が同じ場所に配置されることで、カプセル化が強化されます。
- **コードの可読性向上**: 適切に配置されたデータは、コードの理解を容易にします。
- **保守性の向上**: 論理的なデータ構造により、将来の変更や拡張が容易になります。

## 注意点

- **依存関係の変更**: フィールドの移動により、クラス間の依存関係が変更される可能性があります。
- **パフォーマンスへの影響**: データアクセスのパターンが変わることで、パフォーマンスに影響を与える可能性があります。
- **広範囲な影響**: フィールドを参照している全ての箇所を更新する必要があるため、影響範囲が広くなる可能性があります。

## まとめ

「フィールドの移動」は、データ構造を最適化し、クラスの責任をより明確にするための重要なリファクタリング技術です。適切に実施することで、コードの品質、保守性、そして拡張性が向上します。ただし、慎重な分析とテストが重要です。システム全体のデザインを考慮しながら、適切なタイミングでこの技術を適用することが、長期的なコードの健全性につながります。
