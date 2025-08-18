# agent_runbook_v1.md（運用手引き・図解付き）
## 最短手順（3行）
1. Issueに `目的:` を1行 → 自動で Draft PR が生成  
2. PRで `/slides` → Actions の Artifacts に **slides.pdf / slides.html**  
3. `content/hello.md` を追加 → PRで `/site` → **docs/site/** に静的HTML

## 使い方（ChatOps）
- `/slides`：PR内の `PRFAQ_1pager.md`（なければ `next_actions_v1.md`）を Marp でスライド化
- `/site [dir]`：Markdownから簡易サイトを生成（既定は `content/`）
- `/thumb <md|png>`：OG画像（1200×630）と各解像度サムネを生成
- `/go`：ドラフト解除／`/kill`：PRクローズ

## トラブル時チェック
- Settings → Actions → General → **Workflow permissions: Read & write**  
  ＆ **Allow Actions to create and approve pull requests** が ON  
- コメントの先頭が `/slides` など **スラッシュ**で始まっているか  
- 実行者が `OWNER`/`MEMBER` か（外部コラボだと動かない設定）

## PCFミニ
目的: Issueの「目的: ～」1行から草案PR（雛形4点）を毎回自動生成し、判断負荷を軽減する  
成果物: docs/agent_runbook_v1.md（本書）  
DoD: 新規Issue→Draft PR自動生成／雛形4ファイル揃い／本書を誰でも参照可能  
Test: 「目的: 起動テスト」でIssue作成→Draft PRが自動で出現する  
参照: latest.md, latest.json, chat_history_full.md, chat_history_full.jsonl（内容不明箇所は [UNKNOWN]）  
禁止: main直編集、無根拠の推測

---

## 0. 要約（ここだけ読めばOK）
- **できること**：Issueに `目的: ～` を1行書くと、**専用ブランチ＋Draft PR＋雛形4ファイル**を自動作成。  
- **使い方**：Issueを新規作成 → 本文先頭に `目的:` を1行 → 数十秒でDraft PRが生まれる。  
- **まだ未導入**：PRFAQの“本文自動生成”、`/slides` での**スライド自動作成**など（設計は下、実装は任意）。

---

## 1. いま自動化できていること（現状）
- ✅ **Issue → Draft PR化**（全自動）  
  1) 専用ブランチ作成  
  2) 雛形4ファイル生成：`TASTE.md` / `PRFAQ_1pager.md` / `DECISION_MEMO.md` / `next_actions_v1.md`  
  3) **Draft PR**起票（JX-15: 下書きPR。マージ不可の準備段階）
- ✅ **受理コメント（PoC）**：新規Issueに自動返信（起動確認）  
- ✅ **手動起動**：Actions→`agent`→Run で `purpose` 入力でも実行可能  
- ⛔ **まだ自動ではない**：PRFAQの本文自動下書き、`/go` `/kill` コメント指令、**スライド自動生成**

---

## 2. 図解（フロー＆シーケンス）

### 2.1 フロー（Issue → Draft PR）
```mermaid
flowchart LR
  A[Issue 作成\n本文先頭に「目的: ～」] --> B[GitHub Actions: agent]
  B --> C[専用ブランチ作成]
  C --> D[雛形4ファイル生成\nTASTE / PRFAQ / DECISION / NEXT]
  D --> E[Draft PR 起票]
  E --> F{編集長の判断}
  F -->|Go| G[次工程へ（実装/検証 追加ワークフロー）]
  F -->|保留/却下| H[PRを残す/閉じる（記録資産化）]

2.2 権限とガード（簡略シーケンス）

sequenceDiagram
  participant U as あなた
  participant I as Issue
  participant A as Actions(agent)
  participant PR as Draft PR
  U->>I: 目的: ～（1行）
  I->>A: issues.opened イベント
  A->>A: 目的抽出・ブランチ名スラッグ化
  A->>PR: 雛形4点コミット→Draft PR作成
  Note over A,PR: Workflow permissions = Read&Write + PR作成許可（設定）


⸻

3. 使い方（誰でもできる最短手順）
	1.	Issues → New issue
	2.	本文の先頭に次の1行だけを書く：

目的: （やらせたいことを1行）


	3.	Pull requests を開く → Draft PR が自動で出る
	4.	PR内の PRFAQ_1pager.md を3～6行だけ埋める（JX-15: PRFAQ＝プレスリリース＋FAQの短縮版）
	5.	Go / 保留 / 却下を決める（コメント、ドラフト解除、クローズなど）

ポイント：main は無破壊（JX-15: 無破壊＝本流に直接変更を入れない）。
すべてPR上で検討→OK時にマージ。

⸻

4. DoD（合格条件）と Test（検収）
	•	DoD
	•	新規Issue（目的: あり）から Draft PRが自動生成
	•	PRに 雛形4ファイル が揃っている
	•	Test
	1.	新規Issueに「目的: 起動テスト」を投稿
	2.	数十秒以内にPRが出る
	3.	next_actions_v1.md に目的文が差し込まれている

⸻

5. トラブル対処（最短表）

症状	よくある原因	対処
何も起きない	.github/workflows ではない／古いIssue	正しいパスへYAML／新規Issueで試す
PR作成で失敗	PR権限がRead-only	Settings→Actions→Read & write＋Allow Actions to create/approve PRs をON
「目的:」を拾わない	全角/半角の混在、先頭でない	行頭に 目的: または 目的： を書く
ブランチ名エラー	日本語や記号を含む	自動スラッグ化済（英数と-のみ）／そのままでOK

（JX-15）
	•	Workflow permissions：ワークフロー用トークンの権限設定。
	•	Artifact：ワークフローが出力した成果物をダウンロード保管する機能。

⸻

6. 将来の拡張（設計のみ・必要時に実装）
	•	/slides（スライド自動生成）
	•	トリガ：PRコメント /slides
	•	入力：PRFAQ_1pager.md
	•	出力：slides.pptx（python-pptx） or slides.pdf（Marp）→ Artifacts に添付、希望なら docs/ にコミット
	•	/go / /kill（指令コメント）
	•	/go：ドラフト解除＋ラベル付与
	•	/kill：PR即クローズ＋却下理由1行を記録
	•	PRFAQ自動下書き
	•	APIキーがある場合のみ、LLMで骨子を自動生成→あなたはTaste採点だけ
	•	READ健全性チェック
	•	リポ基本情報を取得し smoke_read.json を生成（稼働監視）

⸻

7. 役割と合意（ミニ運用規約）
	•	あなた：編集長（Tasteで○×判断）
	•	エージェント：手足（形にして並べる）
	•	ルール：WIP=1（同時進行は常に1本）／本流はPR経由／却下も資産（DECISION_MEMO.md に1行残す）

⸻

8. 用語（JX-15：短注釈）
	•	GitHub Actions：GitHub上の自動実行基盤。イベントをトリガにジョブを走らせる。
	•	Draft PR：下書きのPull Request。マージ不可で検討の場。
	•	Taste（審美基準）：T1〜T6の判断物差し（極端な単純さ/1分完了/迷いゼロ/言い訳禁止/待ち時間ゼロ/物語性）。
	•	スラッグ化：文字列を英数・ハイフンに変換してブランチ名等に使える形にする処理。
	•	PoC：概念実証（最小限で動くことを示す）。

⸻

9. 変更履歴
	•	2025-08-16（JST）: 初版作成（Issue→Draft PR 自動化、運用手引き公開）

---

## 保存手順（UIでそのまま）
1) リポジトリ → **Add file** → **Create new file**  
2) ファイル名: `docs/agent_runbook_v1.md`  
3) 上の**全文を貼り付け** → **Commit**（または Draft PR に追加）

必要なら、この**Runbook**をもとに `/slides` などの**追加ワークフローの全文**も即出します。

---

## ChatOps quick start
1. PRコメント `/slides` → PRFAQ_1pager.md から slides.pdf / slides.html を生成（Artifacts）
2. PRコメント `/site` → content/*.md を静的HTML化して docs/site/ にコミット
3. PRコメント `/thumb content/hello.md` → OG画像と各サイズサムネを生成

### Troubleshooting
|症状|対処|
|---|---|
|Artifactが出ない|コメントが最新か確認し `/slides` を再送|
|docs/site/が更新されない|権限を Read & write に設定し `/site` を再送|
|画像が生成されない|ImageMagick エラーをログで確認し `/thumb` を再送|

### DIFF-5
- ワークフローは1ファイル1責務
- 外部APIに頼らずローカル変換
- 権限不足時は設定を明示
- 生成物はPRブランチへコミット
- ChatOpsはOWNER/MEMBERのみ実行可能
