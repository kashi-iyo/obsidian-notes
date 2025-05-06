# Obsidian Vault セットアップ手順

> この記事 ([Shotovim2024](https://note.com/shotovim/n/na1d91f10c1d0)) と同じ構成図を、macOS 上の `obsidian-notes` Vault で再現するためのステップバイステップガイドです。

---

## 0. 事前準備

- `obsidian-notes` という空フォルダをあらかじめ作成しておく
  - 例: `/Users/<ユーザー名>/obsidian-notes` （このフォルダが Vault ルートになります）
- 以下のオンラインサービスのアカウントを取得し、ログインできる状態にしておく
  - **GitHub / AWS / Kindle / Cursor**
- 本ガイドで使用する主要ツール・プラグインの名前を把握しておく（インストールは後のステップで実施）
  - Obsidian / obsidian-git / Anki / obsidian-to-anki / s3-image-uploader

---

## 1. Obsidian 本体の導入（公式サイト版）

1. ブラウザで <https://obsidian.md/> を開き、画面右上の **Download** をクリック。
2. **macOS** を選択して `.dmg` ファイルをダウンロード。
3. ダウンロードした `Obsidian-*.dmg` を開き、アプリケーションフォルダへドラッグしてインストール。
4. 初回起動時に Gatekeeper の警告が出たら「開く」を選択。
5. Obsidian を起動し、`Open folder as vault` を選択して `obsidian-notes` ディレクトリを指定。
6. 必要に応じて Settings › Appearance › **Interface language** → `日本語` に変更。

> ※ Homebrew を使う場合は `brew install --cask obsidian` でも可ですが、公式サイト経由が最新版を取得しやすいです。

---

## 2. GitHub 連携 (obsidian-git)

### 2-1. リポジトリ準備

```bash
cd ~/obsidian-notes
git init
git remote add origin git@github.com:<YourAccount>/obsidian-notes.git
```

### 2-2. GitHub リポジトリ作成

1. ブラウザで GitHub にログインし、右上 **＋** → **New repository** をクリック。
2. **Repository name** に `obsidian-notes` と入力し、**Public / Private** を選択（推奨: Private）。
3. 「Initialize this repository with…」は **何もチェックしない**（空リポジトリとして作成）。
4. **Create repository** を押すと、リポジトリ URL `git@github.com:<YourAccount>/obsidian-notes.git` が表示されるのでコピー。

### 2-3. obsidian-git プラグイン設定（詳細）

1. Obsidian 右上の **Settings（歯車アイコン）** を開き、左メニューの **Community plugins** をクリック。
2. 画面上部の **Safe mode** を **OFF** に切り替え → **Browse** を押す。
3. 検索バーに `obsidian git` と入力し、表示された **Obsidian Git** を **Install → Enable**。
4. Settings 一覧の最下部に新しく **Obsidian Git** が追加されるのでクリックし、以下を設定。

   | 設定項目 | 推奨値 | 説明 |
   |----------|--------|------|
   | Auto pull on vault open | ON | Vault 起動時にリモートを取得 |
   | Auto pull interval (min) | 20 | 定期的に pull |
   | Auto push after pull | ON | 取得後に自動 push して衝突を減らす |
   | Auto push interval (min) | 20 | 定期的に push |
   | Default commit message | `${numFiles} files updated` | 自動コミットメッセージ |
   | Show status bar | ON | 右下に Git アイコンを表示 |

5. 設定を閉じると、Obsidian 右下に **⬆︎⬇︎ Git** アイコンが出現。クリックすると **Pull / Commit / Push** などのメニューが開きます。
6. 初回のみ下記 3 ステップを実行してリモート (`obsidian-notes`) と同期を取ります。
   1. Command Palette (`⌘P`) → **Git: Pull**
   2. **Git: Commit all changes**
   3. **Git: Push**
   これでローカル Vault が GitHub `obsidian-notes` リポジトリにアップロードされ、以降は自動 sync が走ります。
7. もし認証を求められたら、事前に設定した **SSH キー** か **HTTPS トークン** を利用してください（SSH 推奨）。

> ✅ これでデスクトップ側は自動 pull/push、モバイル側（Working Copy）は手動 Git という構成で衝突を最小化できます。

---

## 3. 画像を S3 に置く (s3-image-uploader)

### 3-1. AWS 側

1. S3 バケット例 `obsidian-media-<name>` を東京リージョンで作成
2. IAM でアクセスキーを発行し、次のポリシーを付与

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject","s3:GetObject","s3:DeleteObject","s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::obsidian-media-<name>",
        "arn:aws:s3:::obsidian-media-<name>/*"
      ]
    }
  ]
}
```

### 3-2. Obsidian 側

1. Community plugins › **s3-image-uploader** を導入
2. Settings › S3 Image Uploader
   - `Access Key / Secret`: IAM キー
   - `Bucket`: `obsidian-media-<name>`
   - `Region`: `ap-northeast-1`
   - `Upload Path`: `attachments/`
   - `Replace Format`: `![${name}](${url})`
3. 画像を貼り付け → `⌘P` › Upload image to S3 で URL に置換

---

## 4. Kindle ハイライト取り込み

1. Community plugins › **Kindle Highlights** をインストール
2. Settings
   - ドメイン: `amazon.co.jp`
   - 保存先: `Zettelkasten/LiteratureNote/Kindle/`
3. `⌘P` › Import Kindle Highlights

---

## 5. Anki 連携 (obsidian-to-anki)

### 5-1. Anki / AnkiConnect

#### 5-1. Anki Desktop のインストール（公式手順／macOS）

1. ブラウザで <https://apps.ankiweb.net> を開き、トップの **Download** ボタンから macOS 版 `Anki-*.dmg` をダウンロード。
2. ダウンロード完了後、`Anki-*.dmg` を開いて **Anki.app** を **Applications** フォルダへドラッグ。
3. Launchpad もしくは Finder › Applications から **Anki** を起動。
   - Gatekeeper の警告が出た場合は「開く」を選択。
4. 初回起動時にプロフィール名（例 `default`）を入力してスタート。

> 代替手段: Homebrew ユーザは `brew install --cask anki` でもインストールできます（[Anki公式マニュアル](https://docs.ankiweb.net/platform/mac/installing.html#homebrew) 参照）。

##### Homebrew でインストールした場合の初期セットアップ

```bash
brew install --cask anki      # インストール
open -a Anki                  # 起動（初回 Gatekeeper で「開く」を選択）
```

1. 起動後、プロフィール名 (`default` 等) を入力してスタート。
2. メニュー **Anki › Preferences…** で `Language` を `Japanese` に変更 → 再起動。
3. 右上の同期ボタンで AnkiWeb アカウントを紐付け（任意）。
4. その後は上記 **AnkiConnect アドオン導入** 手順へ進む。

> Homebrew 版 Anki のアップデート: `brew upgrade --cask anki`
> アンインストール: `brew uninstall --cask anki`

#### 5-2. AnkiConnect アドオン導入

1. Anki メニュー **Tools › Add-ons › Get Add-ons…** を開く。
2. コード `2055492159` を入力 → **OK** → ダウンロード後に Anki を再起動。

#### 5-2-1. AnkiConnect 接続設定 (config.json)

Anki のユーザフォルダに `AnkiConnect.json`（存在しなければ新規作成）を置き、以下の内容を設定します。

```json
{
    "apiKey": null,
    "apiLogPath": null,
    "webBindAddress": "127.0.0.1",
    "webBindPort": 8765,
    "webCorsOrigin": "http://localhost",
    "webCorsOriginList": [
        "http://localhost",
        "app://obsidian.md"
    ]
}
```

> macOS のパス例: `~/Library/Application Support/Anki2/addons21/AnkiConnect/AnkiConnect.json`
> 既定値と同じ項目も明示的に書いておくことで、アップデートによる挙動変更を防ぎます。

設定後に Anki を再起動すると Obsidian 側からの CORS エラーが解消されます。

### 5-2. Obsidian 側

1. Community plugins › **obsidian-to-anki** をインストール
2. Settings
   - `AnkiConnect port`: `8765`
   - `Default deck`: 例 `Obsidian`

### 5-3. カードテンプレ例

```markdown
---
deck: 韓国語::基本
tags: [韓国語]
---

## START
### {{韓国語}}
{{日本語}}
## END
```

保存 → `⌘P` › Send to Anki

---

## 6. Cursor との連携

1. Cursor で `obsidian-notes` フォルダを開く
2. <kbd>⌘⌥C</kbd> で **Compose Agent** を呼び出す
3. 例: `compose: tag notes from past 7 days with my tag rules`

---

## 7. モバイル同期 (iOS)

1. iOS に **Obsidian** & **Working Copy** をインストール
2. Working Copy で GitHub リポジトリを clone → Export → Obsidian
3. 編集後は Working Copy で Commit / Push、Pull 後は Export

---

## 8. フォルダ構成 (最終形)

```
obsidian-notes/
├── Zettelkasten/
│   ├── FleetingNote/
│   ├── LiteratureNote/
│   ├── PermanentNote/
│   └── IndexNote/
│
├── Daily/
│
├── MEMO(Thino)/
│
└── Template/
```

---

## 9. 動作チェックリスト

- [ ] Git push/pull が通る (obsidian-git)
- [ ] 画像が S3 にアップロード → URL 置換
- [ ] Kindle ハイライト md が生成
- [ ] Anki カードが送信される
- [ ] Cursor Agent でタグ付け / ノート生成が動く
- [ ] iOS ↔︎ Mac で Git 衝突なく同期できる
