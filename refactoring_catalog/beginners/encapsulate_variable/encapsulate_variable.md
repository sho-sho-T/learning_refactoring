# 変数のカプセル化

## 概要

変数のカプセル化は、直接アクセス可能な変数をゲッターとセッター関数で包み込むリファクタリング技法です。これにより、データへのアクセスを制御し、将来の変更に対する柔軟性を向上させます。

## 例

### Before

```typescript
let defaultOwner = { firstName: "Martin", lastName: "Fowler" };

// クライアントコード
console.log(defaultOwner.firstName);
defaultOwner = { firstName: "Kent", lastName: "Beck" };
```

### After

```typescript
let defaultOwnerData = { firstName: "Martin", lastName: "Fowler" };

export const getDefaultOwner = () => {
  return Object.assign({}, defaultOwnerData);
};

export const setDefaultOwner = (arg: {
  firstName: string;
  lastName: string;
}) => {
  defaultOwnerData = arg;
};

// クライアントコード
console.log(getDefaultOwner().firstName);
setDefaultOwner({ firstName: "Kent", lastName: "Beck" });
```

## 動機

1. **アクセス制御**: データへのアクセスを監視し、制御することができます。
2. **変更の容易さ**: 内部実装を変更しても、外部インターフェースを維持できます。
3. **検証と後処理**: データの更新時に検証やその他の処理を追加できます。
4. **一貫性の維持**: データの取得と設定に一貫したインターフェースを提供します。
5. **拡張性**: 将来的な要件変更に対して、より柔軟に対応できます。

## 手順

1. **カプセル化関数の作成**:
   変数を参照・更新するためのカプセル化用関数（ゲッターとセッター）を作成します。

   ```typescript
   let defaultOwnerData = { firstName: "Martin", lastName: "Fowler" };

   export const getDefaultOwner = () => {
     return Object.assign({}, defaultOwnerData);
   };

   export const setDefaultOwner = (arg: {
     firstName: string;
     lastName: string;
   }) => {
     defaultOwnerData = arg;
   };
   ```

2. **静的チェック**:
   コードの静的チェックを実行し、エラーがないことを確認します。

3. **参照の置き換え**:
   変数への直接参照を、適切なカプセル化関数の呼び出しに置き換えます。置き換えるごとにテストします。

   ```typescript
   // Before
   console.log(defaultOwner.firstName);
   defaultOwner = { firstName: "Kent", lastName: "Beck" };

   // After
   console.log(getDefaultOwner().firstName);
   setDefaultOwner({ firstName: "Kent", lastName: "Beck" });
   ```

4. **変数の可視性制限**:
   可能であれば、元の変数の可視性を制限します。直接アクセスを完全に防げない場合は、変数名を変更してテストすることで、置き換えが漏れた参照を見つけることができます。

   ```typescript
   let _defaultOwnerData = { firstName: "Martin", lastName: "Fowler" };
   ```

5. **テスト**:
   全ての変更が完了したら、テストを実行して機能が正しく動作することを確認します。

6. **レコードのカプセル化の検討**:
   変数の値がレコードの場合、「レコードのカプセル化」の適用を検討します。

   ```typescript
   class DefaultOwner {
     constructor(public firstName: string, public lastName: string) {}
   }

   let defaultOwnerData = new DefaultOwner("Martin", "Fowler");

   export const getDefaultOwner = () => {
     return new DefaultOwner(
       defaultOwnerData.firstName,
       defaultOwnerData.lastName
     );
   };

   export const setDefaultOwner = (arg: DefaultOwner) => {
     defaultOwnerData = arg;
   };
   ```

## 利点

- データアクセスの一元管理が可能になります。
- 将来的な実装の変更が容易になります。
- データの整合性を保つためのロジックを追加できます。
- デバッグやログ記録が容易になります。

## 注意点

- 過度なカプセル化は、コードの複雑性を増す可能性があります。適切なバランスを取ることが重要です。
- パフォーマンスへの影響を考慮する必要があります。特に頻繁にアクセスされる変数の場合は注意が必要です。
- イミュータブルなデータ構造を使用することで、さらなる安全性を確保できる場合があります。
- 言語やフレームワークによっては、プロパティやデコレータなど、より洗練された方法でカプセル化を実現できる場合があります。

この技法を適切に適用することで、コードの保守性、拡張性、そして堅牢性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルのカプセル化を選択することが重要です。
