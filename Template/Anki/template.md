---
TARGET DECK: サンプル::Ankiテンプレ
model: 基本
tags: #Anki #Template #Guide
---

# Anki カード記法テンプレート

以下では `data.json › fields_dict` に登録されている **6 種類** のノートタイプ（モデル）それぞれについて、`START`〜`END` ブロックでの書式例を示します。

---

## 1. 基本

```markdown
START
基本
質問文（例: SOLID の "S" は？）
回答文（例: Single Responsibility Principle）
END
```

---

## 2. 基本 (文字入力解答)

```markdown
START
基本 (文字入力解答)
質問文（キーボード入力で解答）
正解テキスト
END
```

---

## 3. 基本 (裏表反転カード付き)

```markdown
START
基本 (裏表反転カード付き)
表面テキスト（例: SOLID の "O"）
裏面テキスト（例: Open-Closed Principle）
END
```

*このモデルは自動で「裏→表」を反転した追加カードも生成されます。*

---

## 4. 基本 (裏表反転カード追加可能)

```markdown
START
基本 (裏表反転カード追加可能)
表面テキスト
裏面テキスト
yes            # ← ここに任意の文字を入れると反転カードが生成される
END
```

---

## 5. 画像穴埋め問題

対応フィールド: [穴埋め箇所, 画像, ヘッダー, 裏面追記, コメント]

```markdown
START
画像穴埋め問題
The {{c1::heart}} pumps blood.
(空行可)
![heart](attachments/heart.png)
### 循環器系
酸素を含む血液を全身へ送り出す。
関連語: cardiac
END
```

---

## 6. 穴埋め問題 (Cloze)

```markdown
START
穴埋め問題
SOLID は {{c1::5}} つの設計原則の頭文字である。
裏面追記（任意）
END
```

---

> **補足:** フロントマターの `model: 基本` は、このファイル内でモデルが明記されていないカードがあった場合に既定値として使われます。ブロック 2 行目に別モデルを記述すれば、そのカードだけ上書きされます。
