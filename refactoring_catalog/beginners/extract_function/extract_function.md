# 関数の抽出

## 概要

関数の抽出は、コード内の特定の部分を新しい関数として分離するリファクタリング技法です。この技法は、コードの可読性、再利用性、そして保守性を向上させるために頻繁に使用されます。

## 例

### Before

```typescript
const printOwing = (invoice: Invoice) => {
  printBanner();
  let outstanding = calculateOutstanding();

  // 明細の印字 (print details)
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
};
```

### After

```typescript
const printOwing = (invoice: Invoice) => {
  printBanner();
  let outstanding = calculateOutstanding();
  printDetails(outstanding);

  function printDetails(outstanding: number) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
};
```

## 動機

1. **コードの可読性向上**: 「何」をしているかを示す名前の関数として抽出することで、コードの意図が明確になります。
2. **再利用性の向上**: 抽出された関数は他の場所でも使用できるようになります。
3. **保守性の向上**: 小さな関数は理解しやすく、変更も容易です。
4. **重複の削減**: 類似したコードを一箇所にまとめることができます。
5. **テストの容易さ**: 小さな関数は個別にテストしやすくなります。

## 手順

1. **新しい関数の作成**:
   新たな関数を作り、その意図に沿って命名します（どうするかではなく、何をするかによって名づける）。

   ```typescript
   function printDetails(outstanding: number) {
     // ここに抽出するコードが入ります
   }
   ```

2. **コードのコピー**:
   抽出したいコードを、元の関数から新たな関数にコピーします。

   ```typescript
   function printDetails(outstanding: number) {
     console.log(`name: ${invoice.customer}`);
     console.log(`amount: ${outstanding}`);
   }
   ```

3. **スコープの調整**:
   抽出したコードを調べて、元の関数ではスコープ内だったが抽出後の関数ではスコープ外になった変数を特定し、それらをパラメータとして渡します。

   ```typescript
   function printDetails(invoice: Invoice, outstanding: number) {
     console.log(`name: ${invoice.customer}`);
     console.log(`amount: ${outstanding}`);
   }
   ```

4. **コンパイル**:
   全ての変数を処理したらコンパイルします。これにより、正しく処理できていなかった変数を見つけることができます。

5. **関数呼び出しの置き換え**:
   元の関数に残った抽出前のコードを、抽出された関数への呼び出しに置き換えます。

   ```typescript
   const printOwing = (invoice: Invoice) => {
     printBanner();
     let outstanding = calculateOutstanding();
     printDetails(invoice, outstanding);
   };
   ```

6. **テスト**:
   変更後、テストを実行して機能が正しく動作することを確認します。

7. **類似コードの検討**:
   残りのコードを見て、抽出したコードと同じまたは類似したコードを探し、「関数呼び出しによるインラインコードの置き換え」を適用し、新しい関数を呼ぶ形にできないかを検討します。

## 利点

- コードの意図がより明確になります。
- 重複コードを減らすことができます。
- 関数を小さく保つことで、理解とメンテナンスが容易になります。
- 再利用可能なコードの単位を作ることができます。

## 注意点

- 過度に小さな関数を作らないよう注意してください。関数名が処理内容よりも長くなる場合は、抽出を再考する必要があるかもしれません。
- 抽出した関数の名前は、その目的を明確に表現するものを選んでください。
- 多くの変数を引数として渡す必要がある場合は、クラスへの抽出を検討してください。
- パフォーマンスへの影響を考慮してください。頻繁に呼び出される部分で過度に関数を分割すると、パフォーマンスに影響を与える可能性があります。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルの抽出を選択することが重要です。
