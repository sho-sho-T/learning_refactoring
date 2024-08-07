# パラメータオブジェクトの導入

## 例

```typeScript

const amountInvoiced = (startDate: string, endDate: string) => {
    ...
};

const amountReceived = (startDate: string, endDate: string) => {
    ...
};

const amountOverdue = (startDate: string, endDate: string) => {
    ...
};

```

**⇩**

```typeScript

const amountInvoiced = (aDateRange: DataRange) => {
    ...
};

const amountReceived = (aDateRange: DataRange) => {
    ...
};

const amountOverdue = (aDateRange: DataRange) => {
    ...
};

```

## 動機

データを構造体にまとめることには意味があり、データ項目間の関係を明示することができる。

新たな構造体を使えば、関数のパラメータ数は少なくて済み、その構造体を使う全ての関数が、構造体の要素を取得するのに同じ名前を使うことで、一貫性の向上にも役立つ。

## 手順

### ① ふさわしい構造体がまだ存在しないなら作成する

- あとで振る舞いをまとめやすいのでクラスにすることが多い。

### ② テストする

### ③ 「関数宣言の変更」を施し、新たな構造体用のパラメータを追加する

### ④ テストする

### ⑤ 新たな構造体の正しいインスタンスを渡すように各呼び出し側を修正する。１つの呼び出しを修正するごとにテストする

### ⑥ 元のパラメータを使用している箇所を、新たな構造体の要素を使うように一つひとつ置き換える。元のパラメータを削除し、テストする
