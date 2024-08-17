# パラメータオブジェクトの導入

## 概要

パラメータオブジェクトの導入は、複数の関連するパラメータをひとつのオブジェクトにまとめるリファクタリング技法です。この技法は、コードの構造化、可読性の向上、そして将来的な拡張性を高めるために使用されます。

## 例

### Before

```typescript
const amountInvoiced = (startDate: string, endDate: string) => {
  // ...
};

const amountReceived = (startDate: string, endDate: string) => {
  // ...
};

const amountOverdue = (startDate: string, endDate: string) => {
  // ...
};
```

### After

```typescript
class DateRange {
  constructor(public startDate: string, public endDate: string) {}
}

const amountInvoiced = (dateRange: DateRange) => {
  // ...
};

const amountReceived = (dateRange: DateRange) => {
  // ...
};

const amountOverdue = (dateRange: DateRange) => {
  // ...
};
```

## 動機

1. **データ構造の明確化**: 関連するデータ項目をグループ化することで、それらの関係を明示できます。
2. **パラメータ数の削減**: 関数のパラメータ数を減らすことで、関数の呼び出しがシンプルになります。
3. **一貫性の向上**: 同じ構造体を使用する全ての関数で、データアクセスの一貫性が保たれます。
4. **将来の拡張性**: 新しいデータ項目が必要になった場合、既存の関数シグネチャを変更せずに追加できます。
5. **ドメインモデルの強化**: データをオブジェクトとしてモデル化することで、ドメイン特有の振る舞いを追加しやすくなります。

## 手順

1. **構造体の作成**:
   適切な構造体（多くの場合クラス）がまだ存在しない場合、新たに作成します。

   ```typescript
   class DateRange {
     constructor(public startDate: string, public endDate: string) {}
   }
   ```

2. **テスト**:
   新しい構造体が正しく動作することを確認するためのテストを作成し、実行します。

3. **関数宣言の変更**:
   「関数宣言の変更」を適用し、新たな構造体用のパラメータを追加します。

   ```typescript
   const amountInvoiced = (
     startDate: string,
     endDate: string,
     dateRange: DateRange
   ) => {
     // ...
   };
   ```

4. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

5. **呼び出し側の修正**:
   新たな構造体の正しいインスタンスを渡すように各呼び出し側を修正します。1 つの呼び出しを修正するごとにテストします。

   ```typescript
   const result = amountInvoiced(
     "2023-01-01",
     "2023-12-31",
     new DateRange("2023-01-01", "2023-12-31")
   );
   ```

6. **パラメータの置き換え**:
   元のパラメータを使用している箇所を、新たな構造体の要素を使うように一つひとつ置き換えます。全ての置き換えが完了したら、元のパラメータを削除し、テストします。

   ```typescript
   const amountInvoiced = (dateRange: DateRange) => {
     const startDate = dateRange.startDate;
     const endDate = dateRange.endDate;
     // ... 以降の処理
   };
   ```

## 応用例

日付範囲に加えて、追加の機能を持つパラメータオブジェクトの例：

```typescript
class DateRange {
  constructor(public startDate: string, public endDate: string) {}

  duration(): number {
    // 日付の差分を計算
    return /* 計算ロジック */;
  }

  includes(date: string): boolean {
    // 指定された日付が範囲内かどうかを判定
    return /* 判定ロジック */;
  }
}

const amountInvoiced = (dateRange: DateRange) => {
  console.log(`Period: ${dateRange.duration()} days`);
  // ... 残りの処理
};
```

## 利点

- コードの構造が改善され、関連するデータがグループ化されます。
- 関数シグネチャがシンプルになり、可読性が向上します。
- 新しいデータ項目の追加が容易になります。
- ドメイン特有のロジックをパラメータオブジェクトに集約できます。

## 注意点

- 過度に小さなグループ化は避け、意味のある単位でオブジェクト化することが重要です。
- パラメータオブジェクトの導入により、既存のコードベースに大きな変更が必要になる可能性があります。段階的な適用を検討してください。
- パラメータオブジェクトが肥大化しすぎないよう注意してください。必要に応じて、さらなる分割を検討します。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルのオブジェクト化を選択することが重要です。
