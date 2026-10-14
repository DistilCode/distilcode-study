# DistilCode++: 66% lower cost, 80% fewer tokens than other harnesses 

[Donwload here](https://www.distilcode.dev/how-tokens-are-saved)

Continue reading for more details and proof.

## DistilCode on AgencyBench: token and cost comparison

This bundle compares **DistilCode**, our context-managing coding harness, with **opencode** on the [AgencyBench v2](https://github.com/GAIR-NLP/AgencyBench) Code and Backend scenarios. Every configuration uses the same model and the same task prompts, and the full (redacted) transcript of every run is included.

## Configurations

| Name | What it is |
|---|---|
| opencode | Stock opencode, default settings |
| DistilCode | Our base harness, with multiple built-in optimisation techniques. For a glimpse, visit [here](https://www.distilcode.dev/how-tokens-are-saved) |
| DistilCode+ | DistilCode plus ponytail (leaner tool output) |
| DistilCode++ | DistilCode+ plus caveman (terser responses) |

## Setup

- **Model:** DeepSeek V4.1 Flash (`opencode-go/deepseek-v4.1-flash`) for every configuration.
- **Tasks:** AgencyBench v2 Code scenarios 1–8 and Backend scenarios 1–3. Multi-step scenarios give their subtasks one at a time in a single session.
- **Limits:** 900 seconds per subtask. A run that hits the limit stops there and is marked as timed out.
- **Isolation:** each run started from a fresh copy of the scenario files with its own settings and data directories. Codebase memory was off.

### Pricing

Costs are calculated with typical per-token billing, at DeepSeek V4.1 Flash's input and output list prices:

- **Output tokens** (including reasoning): $0.60 per million
- **Input tokens** (not served from cache): $0.15 per million
- **Cache reads:** input tokens discounted by 90%

## Cost

Totals across all 11 scenarios:

| Configuration | Input | Output | Cache read | Total tokens | Input cost | Output cost | Cache-read cost | **Total cost** | vs opencode |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| opencode | 1.64M | 1.12M | 110.03M | 112.8M | $0.247 | $0.671 | $1.650 | **$2.568** | — |
| DistilCode | 1.77M | 0.90M | 28.46M | 31.1M | $0.266 | $0.537 | $0.427 | **$1.230** | -52% |
| DistilCode+ | 1.16M | 0.79M | 22.74M | 24.7M | $0.175 | $0.473 | $0.341 | **$0.988** | -62% |
| DistilCode++ | 1.20M | 0.65M | 20.59M | 22.4M | $0.180 | $0.389 | $0.309 | **$0.877** | -66% |

Per scenario (total cost of the run):

| Scenario | opencode | DistilCode | DistilCode+ | DistilCode++ |
|---|---:|---:|---:|---:|
| Code 1 | $0.226† | $0.128 | $0.133 | $0.068 |
| Code 2 | $0.050 | $0.035 | $0.047 | $0.062 |
| Code 3 | $0.093 | $0.041 | $0.047 | $0.064 |
| Code 4 | $0.504 | $0.233 | $0.115 | $0.100 |
| Code 5 | $0.434 | $0.141 | $0.165 | $0.135 |
| Code 6 | $0.077 | $0.057† | $0.017 | $0.078† |
| Code 7 | $0.022 | $0.013 | $0.055† | $0.053† |
| Code 8 | $0.245 | $0.123 | $0.033 | $0.033 |
| Backend 1 | $0.503† | $0.168 | $0.193 | $0.126 |
| Backend 2 | $0.159 | $0.098 | $0.099 | $0.061 |
| Backend 3 | $0.256 | $0.193 | $0.083 | $0.096 |
| **Total** | **$2.568** | **$1.230** | **$0.988** | **$0.877** |

† The run reached the 900-second limit before finishing.

## Accuracy

Scores are the AgencyBench evaluator's 0–10 score, averaged over a scenario's subtasks (Code 4: partial credit). A run that timed out scores 0.

| Scenario | opencode | DistilCode | DistilCode+ | DistilCode++ |
|---|---:|---:|---:|---:|
| Code 1 | 0.0† | 8.0 | 10.0 | 8.0 |
| Code 2 * | 0.0 | 0.0 | 0.0 | 0.0 |
| Code 3 * | 0.0 | 0.0 | 0.0 | 0.0 |
| Code 4 | 3.1 | 3.3 | 3.3 | 3.3 |
| Code 5 ¶ | 0.0 | 0.0 | 0.0 | 0.0 |
| Code 6 | 0.0 | 0.0† | 10.0 | 0.0† |
| Code 7 | 10.0 | 10.0 | 0.0† | 0.0† |
| Code 8 | 2.5 | 3.5 | 4.0 | 1.4 |
| Backend 1 | 0.0† | 8.4 | 6.4 | 6.6 |
| Backend 2 | 6.8 | 7.2 | 7.6 | 8.0 |
| Backend 3 | 9.6 | 6.9 | 9.1 | 7.1 |
| **Average (9 scored scenarios)** | **3.6** | **5.3** | **5.6** | **3.8** |

\* **Code 2 and Code 3 score 0 for every configuration and are left out of the average.** Both tasks ask the agent to modify an existing AutoGen repository. The task didn't specify which AutoGen version to work on, so each agent pulled a different one (at least six different commits across runs). With no common base to grade against, the evaluator found no repository it could score. They are excluded to keep the results clean; their token and cost figures are still real and are included above.

¶ **Code 5 scores 0 for every configuration.** Its evaluator grades `outputs/test.json`, the research agent's predictions on the test set. No run produced that file, so the official score is 0 everywhere; it is included in the average.

† Timed out (scored 0).

## What is in this bundle

| Path | Contents |
|---|---|
| `transcripts/<scenario>/<configuration>.md` | Readable transcript: prompts, assistant messages, every tool call with its input and output, and token usage per turn |
| `transcripts/<scenario>/<configuration>.json` | The same transcript as structured data |
| `results.csv` | One row per run: status, score, token counts, cost |
| `raw-root-hash.txt`, `raw-manifest-hashes.txt` | SHA-256 commitment to the unredacted raw runs (see below) |

### Redactions

Transcripts are complete except for the following, which are replaced by bracketed markers:

- The exact wording DistilCode injects to manage context. DistilCode's internal context-management tool calls (input and output) are removed rather than marked; the tokens they used remain in the per-turn usage.
- Model reasoning text, for every configuration including opencode. Reasoning token counts are kept in the per-turn usage.
- Secrets, environment-variable listings, benchmark-run metadata files, and files outside the workspace.
- Local usernames, email addresses and machine paths. Workspace paths appear as `<workspace>`.

### Verifying

`raw-root-hash.txt` holds the SHA-256 of the file listing of the unredacted raw runs (`486ba9becd2e3429de6975af01a5c01149f6bffdfce193bd1c14e23b9edd1ffa`). The raw data can be shown to a reviewer, who can check it against this hash. Anyone can also rerun the benchmark with the released DistilCode build.
