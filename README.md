# ChatOps エージェント最小構成（ゼロから再構築）
- PR の会話タブでコメント `/slides`, `/site`, `/thumb <path>` を打つと、PDF/HTML/PNG などの成果物を自動生成します。
- 1ファイル1役、共通処理は composite action に集約、キャッシュで時短。

## 使い方（最短）
1) PRを開く（DraftでもOK）  
2) コメント `/slides` → Actions の Artifacts に slides.pdf/html  
3) コメント `/site` → ブランチに docs/site/ がコミット（Pagesは任意で有効化）  
4) コメント `/thumb content/hello.md` → public/og/*.png, public/thumb/*.webp がコミット  

## トラブル時チェック
- Settings → Actions → General → **Workflow permissions: Read and write**  
- **Allow GitHub Actions to create and approve pull requests** を ON  
- コメントは **先頭が `/`**、実行者が **OWNER/MEMBER**

## 用語メモ（JX-15）
- **Artifacts**: Actions 実行で出来た成果物の置き場  
- **Composite Action**: `.github/actions/...` に置く共通手順の部品  
- **キャッシュ**: 依存の再ダウンロードを避けて時短する仕組み
