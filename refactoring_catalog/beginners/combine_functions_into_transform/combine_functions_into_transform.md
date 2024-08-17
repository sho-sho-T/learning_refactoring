# 関数群の変換への集約

## 概要

関数群の変換への集約は、複数の関連する計算や変換を行う関数群を、単一の変換関数にまとめるリファクタリング技法です。この技法は、データの一貫性を保ち、計算の重複を避け、コードの保守性を向上させるのに役立ちます。

## 例

### Before

```typescript
interface Reading {
  customer: string;
  quantity: number;
  month: number;
  year: number;
}

const base = (aReading: Reading) => {
  // ... 基本料金の計算ロジック
  return baseAmount;
};

const taxableCharge = (aReading: Reading) => {
  // ... 課税対象額の計算ロジック
  return taxableAmount;
};

// クライアントコード
const rawReading = { customer: "Ivan", quantity: 10, month: 5, year: 2017 };
const baseCharge = base(rawReading);
const taxCharge = taxableCharge(rawReading);
```

### After

```typescript
interface EnrichedReading extends Reading {
  baseCharge?: number;
  taxableCharge?: number;
}

const enrichReading = (argReading: Reading): EnrichedReading => {
  // 入力オブジェクトのディープコピーを作成
  const aReading: EnrichedReading = JSON.parse(JSON.stringify(argReading));

  // baseCharge を計算し、新しいプロパティとして追加
  aReading.baseCharge = base(aReading);

  // taxableCharge を計算し、新しいプロパティとして追加
  aReading.taxableCharge = taxableCharge(aReading);

  // 拡張されたオブジェクトを返す
  return aReading;
};

// クライアントコード
const rawReading = { customer: "Ivan", quantity: 10, month: 5, year: 2017 };
const enrichedReading = enrichReading(rawReading);
console.log(enrichedReading.baseCharge);
console.log(enrichedReading.taxableCharge);
```

## 動機

1. **計算の一貫性**: 派生値の計算を一箇所にまとめることで、一貫性が保たれます。
2. **重複の排除**: 同じ計算が複数の場所で行われることを防ぎます。
3. **データの整合性**: 関連するデータと計算結果を同じオブジェクトにまとめることで、データの整合性が向上します。
4. **パフォーマンスの最適化**: 計算結果をキャッシュすることで、不要な再計算を避けられます。
5. **コードの可読性**: 関連する計算が一箇所にまとまることで、コードの理解が容易になります。

## 手順

1. **変換関数の作成**:
   変換されるレコードを入力とし、同じ値を返す変換関数を作ります。通常はレコードのディープコピーを作成します。

   ```typescript
   const enrichReading = (argReading: Reading): EnrichedReading => {
     return JSON.parse(JSON.stringify(argReading));
   };
   ```

2. **ロジックの移動と新フィールドの追加**:
   選択したロジックを変換関数に移動し、レコードに新たなフィールドを設けます。クライアント側のコードを変更して、新しいフィールドを使用するようにします。

   ```typescript
   const enrichReading = (argReading: Reading): EnrichedReading => {
     const aReading: EnrichedReading = JSON.parse(JSON.stringify(argReading));
     aReading.baseCharge = base(aReading);
     return aReading;
   };

   // クライアントコード
   const enrichedReading = enrichReading(rawReading);
   console.log(enrichedReading.baseCharge);
   ```

3. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

4. **他の関数の統合**:
   他の関連した関数についても、同様の手順を繰り返します。

   ```typescript
   const enrichReading = (argReading: Reading): EnrichedReading => {
     const aReading: EnrichedReading = JSON.parse(JSON.stringify(argReading));
     aReading.baseCharge = base(aReading);
     aReading.taxableCharge = taxableCharge(aReading);
     return aReading;
   };
   ```

## 利点

- 計算ロジックが一箇所にまとまり、保守が容易になります。
- データと関連する計算結果が同じオブジェクトに保持されるため、整合性が向上します。
- 不要な再計算を避けることができ、パフォーマンスが向上する可能性があります。
- コードの意図がより明確になり、可読性が向上します。

## 注意点

- 変換関数が大きくなりすぎないよう注意してください。必要に応じて、さらなる分割を検討します。
- 入力オブジェクトを変更しないよう、必ずディープコピーを作成してください。
- この変換が頻繁に行われる場合、パフォーマンスへの影響を考慮する必要があります。
- 元の関数をそのまま残すか、完全に置き換えるかを慎重に検討してください。既存のコードへの影響を最小限に抑えるため、段階的な移行が必要な場合があります。

この技法を適切に適用することで、コードの品質、保守性、そして可読性を大幅に向上させることができます。ただし、過度な抽象化や複雑化を避けるため、常にコードの簡潔さとのバランスを取ることが重要です。
