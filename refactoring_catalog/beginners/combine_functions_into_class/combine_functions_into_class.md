# 関数群のクラスへの集約

## 概要

関数群のクラスへの集約は、共通のデータを操作する関連する関数群を一つのクラスにまとめるリファクタリング技法です。これにより、コードの構造化、再利用性の向上、そして保守性の改善が図れます。

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
  // ...基本料金の計算ロジック
};

const taxableCharge = (aReading: Reading) => {
  // ...課税対象額の計算ロジック
};

const calculateBaseCharge = (aReading: Reading) => {
  // ...基本料金の計算ロジック
};
```

### After

```typescript
class Reading {
  constructor(
    public customer: string,
    public quantity: number,
    public month: number,
    public year: number
  ) {}

  base() {
    // ...基本料金の計算ロジック
  }

  taxableCharge() {
    // ...課税対象額の計算ロジック
  }

  calculateBaseCharge() {
    // ...基本料金の計算ロジック
  }
}
```

## 動機

1. **データとロジックの結合**: クラスを使用することで、データと関連する操作を一箇所にまとめることができます。
2. **カプセル化の向上**: クラス内部でデータを管理することで、外部からの不適切なアクセスを防ぎます。
3. **コードの再利用性**: 関連する機能をクラスにまとめることで、他の部分でも容易に再利用できます。
4. **保守性の向上**: 関連する機能が一箇所にまとまることで、変更や拡張が容易になります。
5. **コードの意図の明確化**: クラスという構造体を使うことで、関連する機能のグループ化がより明確になります。

## 手順

1. **共有データのカプセル化**:
   関数間で共有しているデータのレコードに「レコードのカプセル化」を施します。

   ```typescript
   class Reading {
     constructor(
       public customer: string,
       public quantity: number,
       public month: number,
       public year: number
     ) {}
   }
   ```

   もし、データがレコード構造としてまとめられていない場合は、「パラメータオブジェクトの導入」を施し、それらをまとめるレコードを作ります。

2. **関数の移動**:
   「関数の移動」を適用して、共通レコードを扱う関数をそれぞれ、新たなクラスへと移します。

   ```typescript
   class Reading {
     // ... 既存のコンストラクタ ...

     base() {
       // ...基本料金の計算ロジック
     }

     taxableCharge() {
       // ...課税対象額の計算ロジック
     }

     calculateBaseCharge() {
       // ...基本料金の計算ロジック
     }
   }
   ```

3. **ロジックの抽出と移動**:
   データを操作するロジックの断片があれば、「関数の抽出」で抽出してクラスに移します。

   ```typescript
   class Reading {
     // ... 既存のメソッド ...

     private calculateTax() {
       // 税金計算のロジック（元々は taxableCharge 内の一部だったとする）
     }
   }
   ```

4. **テスト**:
   各ステップごと、および全ての変更が完了した後にテストを実行し、機能が正しく動作することを確認します。

## 利点

- コードの構造が改善され、関連する機能が一箇所にまとまります。
- データとそれを操作するロジックが密接に結びつき、カプセル化が向上します。
- クラスとして定義することで、他の部分のコードでも容易に再利用できます。
- 将来の拡張や変更が容易になります。

## 注意点

- クラスに移動する関数を選択する際は、その関数がデータにどの程度依存しているかを慎重に評価してください。
- 大規模なリファクタリングの場合、段階的に行い、各段階でテストを実施することが重要です。
- クラスが肥大化しすぎないように注意してください。必要に応じて、さらなる分割を検討します。
- このリファクタリングにより、既存のコードの動作が変わらないようにする必要があります。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、過度な抽象化や複雑化を避けるため、常にコードの簡潔さとのバランスを取ることが重要です。
