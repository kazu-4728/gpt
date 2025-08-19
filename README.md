# Agent Runbook

Runbook documentation is available in [docs/agent_runbook_v1.md](docs/agent_runbook_v1.md).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## クイックスタート
1. PRで `/slides` とコメントすると、`PRFAQ_1pager.md` から slides.pdf と slides.html が生成されます。
2. `/site` で `content/` 内の Markdown を `docs/site/` に静的HTMLとして公開します。
3. `/thumb` で `content/` 内の Markdown から OG画像と各解像度のサムネイルを作成します。

### トラブルシューティング
| 問題 | 解決策 |
| --- | --- |
| `pandoc: command not found` | `sudo apt-get install -y pandoc` |
| `convert: command not found` | `sudo apt-get install -y imagemagick` |
| PRを作成できない | リポジトリ設定で「Allow GitHub Actions to create and approve pull requests」と「Read and write permissions」を有効にする |

### DIFF-5
レビューを容易にするため、1つのPRでの変更は5行以内を目指してください。
