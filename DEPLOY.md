# PRECO Recruit Light — 公開手順（GitHub Pages ＋ お名前.com 独自ドメイン）

この採用サイトは `index.html` と `assets/` だけで動く**完全な静的サイト**です。
サーバー処理は不要で、GitHub Pages（無料・HTTPS対応）に置いて、お名前.comで取得した
独自ドメインを向けるだけで公開できます。

想定所要時間：**30〜60分**（DNSの反映待ちを除く）。

---

## 全体の流れ

```
① お名前.com でドメイン取得
        ↓
② GitHub にリポジトリを作成
        ↓
③ このリポジトリを push
        ↓
④ GitHub Pages を有効化
        ↓
⑤ GitHub に独自ドメインを登録（CNAMEファイル自動作成）
        ↓
⑥ お名前.com の DNS を設定（A / CNAME レコード）
        ↓
⑦ HTTPS を有効化（Enforce HTTPS）
```

---

## ① お名前.com でドメインを取得

1. https://www.onamae.com/ にアクセスし、希望のドメイン名（例：`preco-recruit.jp`）を検索。
2. カートに入れて購入。支払い後、会員メニューにログインできる状態にしておく。
3. **取得したドメイン名をメモしておく**（以降 `example.com` と表記します。実際のドメインに読み替えてください）。

> ヒント：`.jp` / `.com` どちらでも可。会社の正式ドメインなら `.co.jp` も検討を（`.co.jp` は登記情報が必要）。

---

## ② GitHub にリポジトリを作成

1. https://github.com/new を開く。
2. **Repository name**：例 `preco-recruit`（任意）
3. **Public** を選択（GitHub Pages無料枠はPublicが確実）。
4. 「Add a README」などは**チェックしない**（このリポジトリを push するため空で作成）。
5. **Create repository**。

作成後に表示される `https://github.com/<あなたのユーザー名>/preco-recruit.git` を控える。

---

## ③ このリポジトリを push

このプロジェクトのフォルダで、ターミナルから以下を実行します
（`<あなたのユーザー名>` と `preco-recruit` は実際の値に置き換え）。

```bash
git remote add origin https://github.com/<あなたのユーザー名>/preco-recruit.git
git branch -M main
git push -u origin main
```

> すでにこのサイトはコミット済みです。上記でそのまま push できます。

---

## ④ GitHub Pages を有効化

1. GitHubのリポジトリ → **Settings** → 左メニュー **Pages**。
2. **Build and deployment**
   - **Source**：`Deploy from a branch`
   - **Branch**：`main` / フォルダは `/ (root)` を選択 → **Save**。
3. 1〜2分後、ページ上部に `https://<あなたのユーザー名>.github.io/preco-recruit/` の公開URLが表示されます。
   まずはこのURLでサイトが正しく表示されるか確認してください（独自ドメイン設定前の動作確認）。

---

## ⑤ GitHub に独自ドメインを登録

1. 同じ **Settings → Pages** の **Custom domain** 欄に、取得したドメインを入力。
   - サブドメイン無しで公開：`example.com`（推奨。apexドメイン）
   - `www` で公開：`www.example.com`
2. **Save** を押すと、リポジトリのルートに `CNAME` というファイルが**自動作成**されます
   （＝この手順書のリポジトリに手動で追加する必要はありません）。
3. 「DNS check in progress」と表示されます。次の⑥でDNSを設定すると解決されます。

---

## ⑥ お名前.com の DNS を設定

お名前.com にログイン → **ドメイン → DNS → DNSレコード設定** を開き、
対象ドメインに対して以下を登録します。

> 🚨 **メールを止めないための鉄則**
> DNSレコード一覧の中で **`MX` と書かれた行はメール用**です。
> **`MX` の行は絶対に削除・変更しないでください。**
> ここで行うのは「`A`（Webサイト用）を追加する」だけ。`MX` に触れなければ
> メールは今まで通り届きます。
> （もし一覧に `MX` の行が見当たらない場合は、DNSが別の場所で管理されている
> 可能性があります。作業を止めて確認してください。）

> ⚠️ **トップ（apex）で公開する前の確認**
> ブラウザで `https://preco-inc.co.jp` を開いて、**すでに会社サイトが表示される**場合、
> 下記のAレコード設定でそれが採用サイトに**置き換わります**。既存サイトを残したい場合は、
> apexではなく `recruit.` などのサブドメイン（後述パターンC）で公開してください。

### パターンA：`example.com`（サブドメイン無し／apex）で公開する場合 ★推奨

**A レコードを4つ**登録します（GitHub PagesのIP）。

| ホスト名 | TYPE | VALUE |
|---|---|---|
| （空欄 or `@`） | A | `185.199.108.153` |
| （空欄 or `@`） | A | `185.199.109.153` |
| （空欄 or `@`） | A | `185.199.110.153` |
| （空欄 or `@`） | A | `185.199.111.153` |

あわせて `www` も使えるようにする場合は、CNAMEを1つ追加：

| ホスト名 | TYPE | VALUE |
|---|---|---|
| `www` | CNAME | `<あなたのユーザー名>.github.io` |

（任意）IPv6対応もするなら **AAAA レコードを4つ**追加：
`2606:50c0:8000::153` / `2606:50c0:8001::153` / `2606:50c0:8002::153` / `2606:50c0:8003::153`

### パターンB：`www.example.com` で公開する場合

| ホスト名 | TYPE | VALUE |
|---|---|---|
| `www` | CNAME | `<あなたのユーザー名>.github.io` |

### パターンC：`recruit.example.com`（サブドメイン）で公開する場合 ★メール・既存サイトに一切触れず最も安全

`recruit` の CNAME を1つ追加するだけ。apexの `A` も `MX` も触りません。

| ホスト名 | TYPE | VALUE |
|---|---|---|
| `recruit` | CNAME | `<あなたのユーザー名>.github.io` |

（この場合、GitHubの Custom domain には `recruit.example.com` と入力します）

> 注意：
> - **`MX`（メール）レコードは削除しない**こと（上の鉄則を参照）。
> - お名前.comの「URL転送」設定が同じホスト名に入っていると競合します。
>   **Web用のA/CNAMEと競合するURL転送だけ**を外してください（MXは残す）。
> - DNSの反映には数分〜最大72時間かかります（多くは1時間以内）。

---

## ⑦ HTTPS を有効化

1. DNSが反映されると、**Settings → Pages** の Custom domain のチェックが緑（成功）になります。
2. **Enforce HTTPS** のチェックボックスが有効化できるようになるので、**ON** にします。
   （証明書はGitHubが自動発行。反映に十数分かかることがあります）
3. `https://example.com` でサイトが表示されれば公開完了です 🎉

---

## トラブルシュート

- **404 が出る** → Pagesの Source/Branch が `main /(root)` になっているか、
  `index.html` がリポジトリのルートにあるか確認。
- **独自ドメインで表示されない** → お名前.com側のA/CNAMEが正しいか、
  古い「URL転送」設定が残っていないか確認。`nslookup example.com` で
  `185.199.10x.153` が返るかチェック。
- **Enforce HTTPS が押せない** → DNSがまだ伝播していない可能性。時間を置いて再確認。
- **画像の一部が出ない** → 「THE WORK」「PEOPLE」の一部画像はUnsplashのURL参照です。
  完全に自前配信したい場合は画像を `assets/` に保存し、`index.html` の該当 `src` を
  ローカルパスへ変更してください（ご希望あれば対応します）。

---

## 補足：更新の反映

サイトを直したら、以下だけで本番へ反映されます（数十秒〜数分で公開）。

```bash
git add -A
git commit -m "update site"
git push
```
