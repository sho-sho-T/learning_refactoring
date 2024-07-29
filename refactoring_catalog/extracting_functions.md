# 関数の抽出

## 例

```typeScript
const printOwing = (invoice: Invoice) => {
    printBanner();
    let outstanding = calculateOutstanding();

    // 明細の印字 (print details)
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
}
```

**⇩**

```typeScript
const printOwing = (invoice: Invoice) => {
    printBanner();
    let outstanding = calculateOutstanding();
    printDetails(outstanding)

    const printDetails = (outstanding: number) => {
        console.log(`name: ${invoice.customer}`);
        console.log(`amount: ${outstanding}`);
    };
}
```

## 動機

**頻繁に行われるリファクタリング**

### 「何」をしているかを示す名前の関数として抽出すべき。

関数名を読み返すだけで関数の目的がすぐ伝わる。

小さい関数は名前が良くないと意味がない。

## 手順

### 新たな関数を作り、その意図に沿って命名する（どうするかではなく、何をするかによって名づける）

### 抽出したいコードを、元の関数から新たな関数にコピーする

### 抽出したいコードを調べて、元の関数ではスコープ内だったが抽出後の関数ではスコープ外になった関数を特定する。それらをパラメータとして渡す

### 全ての変数を処理したらコンパイルする

### 元の関数に残った抽出前のコードを、抽出された関数への呼び出しに置き換える

### テストする

### 残りのコードを見て、抽出したコードと同じまたは類似したコードを探し、「関数呼び出しによるインラインコードの置き換え」を適用し、新しい関数を呼ぶ形にできないかを検討する
