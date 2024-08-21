# クラスのインライン化：詳細ガイド

## 概要

クラスのインライン化は、不要になったクラスを削除し、その機能を他のクラスに統合するリファクタリング技術です。この手法は、クラスの責務が少なくなった時や、クラス構造を再編成する際に有効です。

## 例

### Before

```typescript
class Person {
  constructor(private _telephoneNumber: TelephoneNumber) {}

  get officeAreaCode() {
    return this._telephoneNumber.areaCode;
  }

  get officeNumber() {
    return this._telephoneNumber.number;
  }
}

class TelephoneNumber {
  constructor(private _areaCode: string, private _number: string) {}

  get areaCode() {
    return this._areaCode;
  }

  get number() {
    return this._number;
  }
}
```

### After

```typescript
class Person {
  constructor(private _officeAreaCode: string, private _officeNumber: string) {}

  get officeAreaCode() {
    return this._officeAreaCode;
  }

  get officeNumber() {
    return this._officeNumber;
  }
}
```

## 動機

1. **クラスの役割の終了**: クラスがその目的を果たし、もはや存在価値がなくなった場合。
2. **責務の移動**: 他のリファクタリング（例：「メソッドの移動」）の結果、クラスの責務が著しく減少した場合。
3. **クラス構造の再編成**: 二つのクラスの特性を再配置する際、一度インライン化してから再分離する方が簡単な場合がある。
4. **過度の抽象化の解消**: システムが必要以上に複雑になっている場合、シンプル化のためにクラスをインライン化することがある。
5. **パフォーマンスの最適化**: 小さすぎるクラスを統合することで、メモリ使用量やメソッド呼び出しのオーバーヘッドを削減できる場合がある。

## 手順

1. **委譲メソッドの作成**:

   - インライン先のクラス（例：`Person`）に、元のクラス（例：`TelephoneNumber`）の全ての public メソッドに対応する委譲メソッドを作成します。
   - これらのメソッドは、元のクラスのメソッドを単に呼び出すだけのものです。

   ```typescript
   class Person {
     // ... 既存のコード ...

     get areaCode() {
       return this._telephoneNumber.areaCode;
     }

     get number() {
       return this._telephoneNumber.number;
     }
   }
   ```

2. **参照の更新**:

   - プロジェクト全体で、元のクラス（`TelephoneNumber`）のメソッドへの参照を、新しい委譲メソッドへの参照に変更します。
   - 変更ごとにテストを実行し、機能が正しく動作することを確認します。

3. **メソッドとデータの移動**:

   - 元のクラスから、全てのメソッドとデータをインライン先のクラスに移動します。
   - 一つずつ移動し、その都度テストを実行します。
   - 元のクラスが空になるまでこのプロセスを繰り返します。

   ```typescript
   class Person {
     private _officeAreaCode: string;
     private _officeNumber: string;

     constructor(areaCode: string, number: string) {
       this._officeAreaCode = areaCode;
       this._officeNumber = number;
     }

     get officeAreaCode() {
       return this._officeAreaCode;
     }

     get officeNumber() {
       return this._officeNumber;
     }
   }
   ```

4. **元のクラスの削除**:
   - 全ての機能が移動されたことを確認後、元のクラス（`TelephoneNumber`）を削除します。
   - 最終的なテストを実行し、システム全体が正しく動作することを確認します。

## 利点

- コードの簡素化: 不必要な抽象化を削除し、システムをシンプルにします。
- 保守性の向上: 関連する機能を一つのクラスにまとめることで、コードの理解と保守が容易になります。
- パフォーマンスの最適化: 小さなクラスを統合することで、メモリ使用量とメソッド呼び出しのオーバーヘッドを削減できます。

## 注意点

- 慎重に進める: 各ステップでテストを実行し、機能の破壊がないことを確認します。
- 単一責任の原則: インライン化の結果、クラスが複数の責任を持つようになる場合は、別のリファクタリング技術（例：クラスの抽出）の適用を検討します。
- バージョン管理: 大きな変更を行う前に、コードのスナップショットを保存するか、バージョン管理システムを使用します。

## まとめ

クラスのインライン化は、不要な複雑さを取り除き、コードベースをシンプルに保つための強力なツールです。しかし、慎重に適用し、各ステップでテストを行うことが重要です。この技術を適切に使用することで、より理解しやすく、保守しやすいコードを作成できます。
