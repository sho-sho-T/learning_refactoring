# オブジェクトによるプリミティブの置き換え

## 概要

このリファクタリング手法は、単純なデータ型（プリミティブ）をより複雑なオブジェクトに置き換えることで、コードの柔軟性と再利用性を向上させます。

## 例

### Before

```typescript
orders.filter((o) => "high" === o.priority || "rush" === o.priority);
```

### After

```typescript
orders.filter((o) => o.priority.higherThan(new Priority("normal")));
```

## 動機

1. **初期段階の単純化**: 開発の初期段階では、データを数値や文字列などの単純な形で表現しがちです。
2. **複雑性の増加**: 開発が進むにつれて、これらの単純なデータ項目が複雑になっていきます。
3. **コードの重複**: 単純なデータ型を使用したロジックは、コードベース内で急速に重複する傾向があります。
4. **拡張性の向上**: クラスを使用することで、必要に応じて固有の振る舞いを簡単に追加できます。
5. **ドメインロジックの集約**: オブジェクトを使用することで、特定のドメイン概念に関連するロジックを一箇所にまとめることができます。これにより、コードの一貫性が向上し、バグの可能性が減少します。
6. **型安全性の向上**: プリミティブ型を使用する代わりに専用のクラスを使用することで、型チェックがより厳密になり、誤用を防ぐことができます。
7. **コードの意図の明確化**: 専用のクラスを使用することで、そのデータが何を表しているのか、どのように使用されるべきかがコード上で明確になります。

> **重要**: 単純な表示以上のことが必要だと分かった時点で、小さなデータでも新たなクラスにすることを検討しましょう。

## 手順

1. **変数のカプセル化**:
   まだ行っていない場合、変数をカプセル化します。

   ```typescript
   class Order {
     constructor(private _priority: string) {}

     get priority(): string {
       return this._priority;
     }
     set priority(value: string) {
       this._priority = value;
     }
   }
   ```

2. **値クラスの作成**:
   データ値のための単純な値クラスを作成します。コンストラクタで既存の値を受け取り、値を返す getter を用意します。

   ```typescript
   class Priority {
     constructor(private value: string) {
       if (!["low", "normal", "high", "rush"].includes(value)) {
         throw new Error("Invalid priority value");
       }
     }

     toString(): string {
       return this.value;
     }
   }
   ```

3. **静的チェック**:
   コードの静的チェックを実行します。TypeScript を使用している場合、コンパイラがエラーを検出します。

4. **setter の変更**:
   値クラスのインスタンスを作成するように setter を変更します。

   ```typescript
   class Order {
     private _priority: Priority;

     constructor(priority: string) {
       this._priority = new Priority(priority);
     }

     get priority(): Priority {
       return this._priority;
     }
     set priority(value: Priority) {
       this._priority = value;
     }
   }
   ```

5. **getter の変更**:
   値クラスの getter の呼び出し結果を返すように getter を変更します。（この例では既に変更済み）

6. **テスト**:
   変更後のコードをテストします。単体テストを作成し、新しい実装が正しく動作することを確認します。

7. **関数名の変更**:
   必要に応じて、元のアクセサの名前をより適切なものに変更します。この例では変更は不要です。

8. **オブジェクトの役割の明確化**:
   新たなオブジェクトが値オブジェクトか参照オブジェクトかを検討します。この例では、`Priority`は値オブジェクトとして扱うのが適切です。

   ```typescript
   class Priority {
     // ... 既存のコード ...

     equals(other: Priority): boolean {
       return this.value === other.value;
     }

     higherThan(other: Priority): boolean {
       const order = ["low", "normal", "high", "rush"];
       return order.indexOf(this.value) > order.indexOf(other.value);
     }
   }
   ```

最終的に、リファクタリングされたコードは以下のようになります：

```typescript
const highPriorityOrders = orders.filter((o) =>
  o.priority.higherThan(new Priority("normal"))
);
```

## 利点

- コードの重複が減少します。
- データに関連するロジックを一箇所にまとめることができます。
- コードの可読性と保守性が向上します。
- 将来的な拡張が容易になります。
- ドメイン固有の概念をコード上で明確に表現できます。
- 型安全性が向上し、誤用を防ぐことができます。

## 注意点

- 過度な抽象化を避けるため、実際に必要となる場合にのみこのリファクタリングを適用してください。
- 新しいクラスの責務を明確に定義し、単一責任の原則に従うようにしましょう。
- パフォーマンスへの影響を考慮してください。多数の小さなオブジェクトを作成することで、メモリ使用量が増加する可能性があります。
