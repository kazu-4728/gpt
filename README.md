# Agent Runbook

Runbook documentation is available in [docs/agent_runbook_v1.md](docs/agent_runbook_v1.md).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Quickstart
1. Comment `/slides` on a pull request to render `PRFAQ_1pager.md` into slides.
2. Comment `/site` to publish Markdown in `content/` to `docs/site/`.
3. Comment `/thumb` to create social thumbnails from `content/`.

### Troubleshooting
| problem | fix |
| --- | --- |
| `pandoc: command not found` | `sudo apt-get install -y pandoc` |
| `convert: command not found` | `sudo apt-get install -y imagemagick` |
| can't create pull request | enable "Allow GitHub Actions to create and approve pull requests" with read/write workflow permissions |

### DIFF-5
Keep pull requests small—aim for under five changed lines—to ease review.
