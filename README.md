# Tree Money — public track record

**Simulated** portfolio (paper trading, $100,000 virtual capital)
managed by a multi-agent AI committee. Real market prices, real benchmark.
Goal: beat SPY buy & hold.

This is a **performance-only** record: holdings and decisions are private,
NAV and benchmark are public — the same way funds report.

| | |
|---|---|
| First snapshot | 2026-08-17T18:46:28Z |
| Latest snapshot | 2026-08-20T20:30:11Z |
| Total snapshots | 9 |
| Equity | $98,389.67 |
| Total return | -1.61% |
| Vs SPY buy & hold | -0.25% |

## Integrity

`ledger.jsonl` is **append-only and hash-chained**: every entry embeds the
SHA256 of the previous one, so any retroactive edit breaks the chain. Git
history is never rewritten (no force-push); GitHub push timestamps provide
independent proof of when each value was recorded.

Each entry also carries a **`commitment`** — the SHA256 of the full private
snapshot (holdings included) taken at that moment. Holdings are not
published, but any snapshot can be *revealed* later and verified against
the commitment published at the time: performance is private in detail,
tamper-proof in substance (commit-reveal).

Verify the chain:

```python
import json, hashlib
prev = "genesis"
for line in open("ledger.jsonl", encoding="utf-8"):
    e = json.loads(line)
    h = e.pop("hash")
    assert e["prev_hash"] == prev, "chain broken"
    assert h == hashlib.sha256((e["prev_hash"] + json.dumps(e, sort_keys=True, ensure_ascii=False)).encode()).hexdigest(), "invalid hash"
    prev = h
print("chain intact")
```
