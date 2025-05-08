# Anki ノート作成ガイド

このフォルダでは、Anki 用フラッシュカードを効率よく作成・管理するための規則をまとめています。Obsidian 側でノートを作成 → Export to Anki プラグインで送信、というワークフローを想定しています。

---

## 1. ディレクトリ構成

```
Anki/
├── README.md          # 本ファイル
├── Templates/         # カード用テンプレート例
└── Samples/           # サンプルノート
```

---

## 2. Anki 用ノートの基本構造

Export to Anki プラグインのデフォルト構文は **行頭キーワード** です。

```markdown
---
# front-matter 例
# deck / tags などはここに書ける
---

TARGET DECK: <DeckName>

START            # ← 必須（行頭・半角大文字）
Basic            # ← 2 行目 = ノートタイプ名（Anki 側と一致させる）
Front side (質問)
Back side (回答)
END              # ← 必須
```

- `TARGET DECK:` 行を省略した場合は data.json の `Defaults › Deck`（既定では **Default**）に送られます。
- Front/Back などの行数・改行は自由。フィールド区切りは複数行でも OK。
- Cloze 削除は `{{c1::テキスト}}` を書くだけで自動認識されます（`CurlyCloze` 設定が `true` の場合）。

### よく使うフィールド付きテンプレ

```markdown
---
TARGET DECK: プログラミング::OOP::SOLID
model: 基本 (裏表反転カード付き)
fields:
  表面: SOLID 原則の "D" は？
  裏面: Dependency Inversion Principle（依存関係逆転の原則）
---
```

`model:` と `fields:` を指定すると、複数の NoteType に対応できます。

---

## 3. 書き方の Tips

| シーン             | 記法例                                     | 補足                                     |
|--------------------|---------------------------------------------|------------------------------------------|
| **単語カード**      | `## START` <br>`### apple`<br>`りんご`      | Front=英語, Back=日本語                 |
| **リバース**        | `reverse: true` フラグを frontmatter に追加 | Basic (and reversed) 相当               |
| **画像付き**        | `![heart](attachments/heart.png)`           | 画像は S3 アップロード後の URL でも可    |
| **数式**            | `$E=mc^2$`                                  | Anki 側で MathJax 有効                  |
| **複数タグ**        | `tags: [bio, cell, exam2025]`               |                                        |

---

## 4. 参考リンク

- [Anki Manual – Key Concepts](https://docs.ankiweb.net/getting-started.html#key-concepts)
- [Obsidian_to_Anki GitHub](https://github.com/Pseudonium/Obsidian_to_Anki)

## 4. フィールド（NoteType）をカスタマイズする

AnkiConnect 経由で Obsidian → Anki に送信する際、**Markdown → Anki フィールド** の対応は
`.obsidian/plugins/obsidian-to-anki-plugin/data.json › fields_dict`
で制御されています。

### 4-1. 既存 NoteType のフィールド名を変更したい

1. Anki で
   - `ツール › ノートタイプ › フィールド` を開き、フィールドの追加・改名を行う。
2. Obsidian 側で `data.json` を開き、該当モデルの配列順をフィールド順に合わせて書き換える。
3. Obsidian をリロード（設定 › Reload plugins または再起動）。

例: `基本` モデルに `Extra` を追加した場合

```jsonc
"fields_dict": {
  "基本": ["表面", "裏面", "Extra"],
  // ...
}
```

### 4-2. 新しい NoteType を作る

1. Anki で `ノートタイプ › 管理 › 追加` から新規作成。
2. フィールドとカードレイアウトを決めて保存。
3. `data.json` の `fields_dict` に同名キーでフィールド配列を追加。
4. Markdown 側で

   ```markdown
   START
   新規モデル名          # ← 2 行目
   Field1 テキスト…
   Field2 テキスト…
   END
   ```

> **注意:** `fields_dict` を直接編集した後は Obsidian 再起動が確実です。

---

## 5. 参考リンク

- [Anki Manual – Key Concepts](https://docs.ankiweb.net/getting-started.html#key-concepts)
- [Obsidian_to_Anki GitHub](https://github.com/Pseudonium/Obsidian_to_Anki)

### model とは？

`model` は **Anki のノートタイプ名** を指します。

• front-matter で宣言すると、このファイルでモデルを明記していないカードの **デフォルト値** になります。
• Export to Anki プラグインは front-matter の `model` と `TARGET DECK` がセットで記載されているファイルを **Anki 用ファイル** としてスキャンします。
• ブロック内 2 行目に別モデルを指定すると front-matter の値を **上書き** できます。
• `fields_dict` に同名キーが無い、またはフィールド数が一致しないカードはエラーになります。

これにより、「ファイル単位の既定モデル + カード単位の上書き」という柔軟な運用が可能です。
