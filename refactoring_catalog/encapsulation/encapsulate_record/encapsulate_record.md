# レコードのカプセル化

## 概要
レコードのカプセル化は、単純なデータ構造（レコード）をクラスでラップすることで、データの操作と管理をより柔軟かつ安全にするリファクタリング技法です。

## 例

### Before
```typescript
let organization = {name: "Acme Gooseberries", country: "GB"};
```

### After
```typescript
class Organization {
    private _name: string;
    private _country: string;

    constructor(data: {name: string, country: string}) {
        this._name = data.name;
        this._country = data.country;
    }

    get name(): string {
        return this._name;
    }

    set name(value: string) {
        this._name = value;
    }

    get country(): string {
        return this._country;
    }

    set country(value: string) {
        this._country = value;
    }
}

let organization = new Organization({name: "Acme Gooseberries", country: "GB"});
```

## 動機

1. **データの一貫性**: オブジェクトを使用することで、データの一貫性を保証しやすくなります。
2. **振る舞いの追加**: データに関連する振る舞いを同じ場所に定義できます。
3. **カプセル化**: 内部データ構造を隠蔽し、外部からのアクセスを制御できます。
4. **型安全性**: 特に静的型付け言語では、型チェックが強化され、誤用を防ぐことができます。
5. **拡張性**: 将来的な要件変更に対して、より柔軟に対応できます。

## 手順

1. **変数のカプセル化**:
   レコードを保持する変数をカプセル化します。

   ```typescript
   let organization = {name: "Acme Gooseberries", country: "GB"};

   function getOrganization() {
       return organization;
   }
   function setOrganization(arg) {
       organization = arg;
   }
   ```

2. **クラスの作成**:
   レコードをラップする簡単なクラスを作成し、元のレコードデータを返すアクセサを定義します。

   ```typescript
   class Organization {
       private _data;

       constructor(data) {
           this._data = data;
       }

       get rawData() {
           return {...this._data};
       }
   }

   let organization = new Organization({name: "Acme Gooseberries", country: "GB"});

   function getOrganization() {
       return organization;
   }
   function setOrganization(arg) {
       organization = new Organization(arg);
   }
   ```

3. **テスト**:
   この時点で単体テストを実行し、変更が正しく機能していることを確認します。

4. **個別フィールドのアクセサ作成**:
   レコードの各フィールドに対するゲッターとセッターを追加します。

   ```typescript
   class Organization {
       private _data;

       constructor(data) {
           this._data = data;
       }

       get name() { return this._data.name; }
       set name(value) { this._data.name = value; }

       get country() { return this._data.country; }
       set country(value) { this._data.country = value; }

       get rawData() {
           return {...this._data};
       }
   }
   ```

5. **使用箇所の更新**:
   コードベース全体で、直接レコードアクセスをオブジェクトのメソッド呼び出しに置き換えます。

   ```typescript
   // Before
   let name = getOrganization().name;
   getOrganization().name = "NewName";

   // After
   let name = getOrganization().name;
   getOrganization().name = "NewName";
   ```

6. **生データアクセサの削除**:
   全ての使用箇所を更新したら、`rawData`アクセサを削除します。

   ```typescript
   class Organization {
       private _name: string;
       private _country: string;

       constructor(data: {name: string, country: string}) {
           this._name = data.name;
           this._country = data.country;
       }

       get name() { return this._name; }
       set name(value) { this._name = value; }

       get country() { return this._country; }
       set country(value) { this._country = value; }
   }
   ```

7. **最終テスト**:
   全ての変更が完了したら、再度テストを実行して機能が正しく動作することを確認します。

8. **再帰的適用**:
   レコード内にネストされた構造がある場合、それらに対しても同様のプロセスを適用することを検討します。

## 利点

- データの整合性が向上します。
- コードの意図がより明確になります。
- 将来的な拡張が容易になります。
- タイプミスなどによる誤用を防ぎやすくなります。
- 関連する振る舞いをデータと一緒にカプセル化できます。

## 注意点

- 単純なデータ構造に対して過度に複雑なオブジェクトを作成しないように注意してください。
- パフォーマンスへの影響を考慮してください。
- 既存のコードベースが広範囲にわたってレコードを直接参照している場合、移行には時間がかかる可能性があります。

このリファクタリング技法を適切に適用することで、コードの品質と保守性を大幅に向上させることができます。