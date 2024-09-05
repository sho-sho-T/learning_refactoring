# ステートメントの関数内への移動（修正版）：詳細ガイド

## 概要

「ステートメントの関数内への移動」は、関数の外部にある関連するステートメントを関数内部に移動させるリファクタリング技術です。これにより、コードの重複を減らし、関数の一貫性と再利用性を向上させることができます。

## 例

### Before

```typescript
const renderPerson = (outStream: string[], person: Person): string => {
  const result = [];
  result.push(`<p>name: ${person.name}</p>`);
  result.push(`<p>title: ${person.photo.title}</p>`); // この行を移動します
  result.concat(photoData(person.photo));
  return result.join("\n");
};

const photoData = (aPhoto: Photo): string[] => {
  return [
    `<p>location: ${aPhoto.location}</p>`,
    `<p>date: ${aPhoto.date.toDateString()}</p>`,
  ];
};
```

### After

```typescript
const renderPerson = (outStream: string[], person: Person): string => {
  const result = [];
  result.push(`<p>name: ${person.name}</p>`);
  result.concat(photoData(person.photo));
  return result.join("\n");
};

const photoData = (aPhoto: Photo): string[] => {
  return [
    `<p>title: ${aPhoto.title}</p>`, // この行が移動しました
    `<p>location: ${aPhoto.location}</p>`,
    `<p>date: ${aPhoto.date.toDateString()}</p>`,
  ];
};
```

## 動機

1. **コードの重複削減**: 関連するロジックを一箇所にまとめることで、重複を減らします。
2. **関数の一貫性向上**: 関連する操作を 1 つの関数にまとめることで、関数の責任が明確になります。
3. **保守性の向上**: 将来の変更が必要な場合、一箇所で修正するだけで済むようになります。
4. **再利用性の向上**: より多くのロジックを含む関数は、他の場面でも再利用しやすくなります。
5. **コードの可読性向上**: 関連する処理がまとまることで、コードの意図がより明確になります。

## 手順

1. **ステートメントの移動**:

   - 移動したいステートメントを特定し、それを関数呼び出しの近くに移動します。

   ```typescript
   // Before
   result.push(`<p>name: ${person.name}</p>`);
   result.push(`<p>title: ${person.photo.title}</p>`); // この行を移動します
   result.concat(photoData(person.photo));

   // After
   result.push(`<p>name: ${person.name}</p>`);
   result.concat(photoData(person.photo));
   // `<p>title: ${person.photo.title}</p>` はphotoData関数内に移動します
   ```

2. **関数の修正**:

   - 移動先の関数（この場合は `photoData`）を修正して、新しいステートメントを含めます。

   ```typescript
   const photoData = (aPhoto: Photo): string[] => {
     return [
       `<p>title: ${aPhoto.title}</p>`, // この行が追加されました
       `<p>location: ${aPhoto.location}</p>`,
       `<p>date: ${aPhoto.date.toDateString()}</p>`,
     ];
   };
   ```

3. **呼び出し元の更新**:

   - 必要に応じて、`photoData` 関数の呼び出し元を更新します。この例では、`renderPerson` 関数から重複するステートメントを削除します。

4. **テスト**:
   - 変更後にテストを実行し、機能が正しく動作することを確認します。

## 利点

- **コードの一貫性**: 写真に関連する全ての情報が `photoData` 関数内にまとまりました。
- **保守性の向上**: 写真データの表示形式を変更する場合、`photoData` 関数のみを修正すれば良くなりました。
- **再利用性の向上**: `photoData` 関数がより完全な情報を提供するようになり、他の場面でも使いやすくなりました。
- **コードの可読性**: `renderPerson` 関数がよりシンプルになり、その役割がより明確になりました。

## 注意点

- **関数の責任**: 移動後も関数の責任が明確で単一であることを確認します。
- **パラメータの追加**: 新しいステートメントが追加のデータを必要とする場合、関数のパラメータを適切に調整する必要があります。
- **テストの重要性**: 各ステップでのテストが重要です。機能の変更がないことを確認してください。

## まとめ

「ステートメントの関数内への移動」は、コードの重複を減らし、関数の一貫性と再利用性を向上させるための有効なリファクタリング技術です。この例では、写真のタイトル表示を `renderPerson` 関数から `photoData` 関数に移動することで、写真に関連する全ての情報を一箇所にまとめました。これにより、コードの構造が改善され、将来の保守や拡張が容易になります。
