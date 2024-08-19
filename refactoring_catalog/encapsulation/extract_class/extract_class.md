# クラスの抽出

## 概要

クラスの抽出は、一つのクラスが多すぎる責務を持っている場合に、その一部を新しいクラスに分離するリファクタリング技法です。この技法により、単一責任の原則を守り、コードの保守性と再利用性を向上させることができます。

## 例

### Before

```typescript
class Person {
  private _officeAreaCode: string;
  private _officeNumber: string;

  constructor(officeAreaCode: string, officeNumber: string) {
    this._officeAreaCode = officeAreaCode;
    this._officeNumber = officeNumber;
  }

  get officeAreaCode() {
    return this._officeAreaCode;
  }

  get officeNumber() {
    return this._officeNumber;
  }
}
```

### After

```typescript
class Person {
  private _telephoneNumber: TelephoneNumber;

  constructor(areaCode: string, number: string) {
    this._telephoneNumber = new TelephoneNumber(areaCode, number);
  }

  get officeAreaCode() {
    return this._telephoneNumber.areaCode;
  }

  get officeNumber() {
    return this._telephoneNumber.number;
  }
}

class TelephoneNumber {
  private _areaCode: string;
  private _number: string;

  constructor(areaCode: string, number: string) {
    this._areaCode = areaCode;
    this._number = number;
  }

  get areaCode() {
    return this._areaCode;
  }

  get number() {
    return this._number;
  }
}
```

## 動機

1. **単一責任の原則**: クラスは一つの明確な責務のみを持つべきです。
2. **複雑性の管理**: クラスが大きくなりすぎると、理解と保守が困難になります。
3. **再利用性の向上**: 小さな、焦点を絞ったクラスは他の場所で再利用しやすくなります。
4. **テストの容易さ**: 小さなクラスは個別にテストしやすくなります。
5. **コードの組織化**: 関連するデータとメソッドを論理的にグループ化できます。

## 手順

1. **責務の特定**:
   クラスの責務をどのように切り出すかを決めます。関連するデータとメソッドのグループを特定します。

2. **新クラスの作成**:
   切り出した責務を記述するための新たな子クラスを作ります。

   ```typescript
   class TelephoneNumber {
     // この時点では空
   }
   ```

3. **インスタンス生成とリンク**:
   元の親クラスのインスタンスを生成するときに、新たな子クラスのインスタンスも作り、親インスタンスから子インスタンスへのリンクを加えます。

   ```typescript
   class Person {
     private _telephoneNumber: TelephoneNumber;

     constructor(areaCode: string, number: string) {
       this._telephoneNumber = new TelephoneNumber();
     }
   }
   ```

4. **フィールドの移動**:
   移動したい各フィールドに「フィールドの移動」を施します。移動のたびにテストします。

   ```typescript
   class TelephoneNumber {
     private _areaCode: string;
     private _number: string;

     constructor(areaCode: string, number: string) {
       this._areaCode = areaCode;
       this._number = number;
     }
   }
   ```

5. **メソッドの移動**:
   メソッドを子クラスに移動するために「関数の移動」を施します。低レベルのメソッド（呼ぶ側ではなく呼ばれる側）から着手します。移動のたびにテストします。

   ```typescript
   class TelephoneNumber {
     // ... 前のコード ...

     get areaCode() {
       return this._areaCode;
     }

     get number() {
       return this._number;
     }
   }
   ```

6. **インターフェースの見直し**:
   両クラスのインターフェースを見直します。不要なメソッドを取り除き、新たな状況にふさわしい名前に変更します。

7. **新クラスの公開の検討**:
   新たな子クラスのインスタンスを公開するかどうかを決めます。公開する場合は「参照から値への変更」を新たなクラスに施すことを検討します。

## 応用例

複数の責務を持つクラスを分割する例：

```typescript
class Order {
  // 注文の基本情報
  private _orderNumber: string;
  private _customer: Customer;

  // 配送情報
  private _shippingAddress: Address;
  private _shippingMethod: ShippingMethod;

  // 支払い情報
  private _paymentMethod: PaymentMethod;
  private _paymentStatus: PaymentStatus;

  // これらの情報を管理するメソッド...
}
```

このクラスを以下のように分割できます：

```typescript
class Order {
  private _orderNumber: string;
  private _customer: Customer;
  private _shipping: Shipping;
  private _payment: Payment;

  // 基本的な注文管理のメソッド...
}

class Shipping {
  private _address: Address;
  private _method: ShippingMethod;

  // 配送関連のメソッド...
}

class Payment {
  private _method: PaymentMethod;
  private _status: PaymentStatus;

  // 支払い関連のメソッド...
}
```

## 利点

- クラスの責務が明確になり、コードの理解が容易になります。
- 各クラスが小さくなることで、保守性が向上します。
- 分割されたクラスは他の場所で再利用しやすくなります。
- テストがより簡単になります。

## 注意点

- 過度な分割は避け、意味のある単位でクラスを抽出することが重要です。
- 抽出後のクラス間の関係性を慎重に設計する必要があります。
- パフォーマンスへの影響を考慮する必要があります（特に多数の小さなオブジェクトを作成する場合）。
- 既存のコードベースに大きな変更を加える可能性があるため、段階的なリファクタリングを検討してください。

この技法を適切に適用することで、コードの品質、可読性、そして保守性を大幅に向上させることができます。ただし、各状況に応じて適切なレベルの抽出を選択することが重要です。
