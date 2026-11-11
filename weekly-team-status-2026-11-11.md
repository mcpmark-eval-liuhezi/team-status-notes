# Weekly Team Status Note — 2026-11-11

> Assembled from live lookups (GitHub API for repos/workflows, Alchemy price feeds for USD quotes, the official CPython `json` stdlib docs at `python/cpython` `Doc/library/json.rst`, and the #所有-mcp-atlas Slack channel). Published unlisted (secret) so it can be dropped into chat without公开 visibility.

---

## CI health

Checked all 4 public repositories on the @HeziLiu GitHub account:

| Repository | Actions workflows |
| --- | --- |
| HeziLiu/Little-rookie | **none found** — no workflows configured |
| HeziLiu/Big-Buzzard | **none found** — no workflows configured |
| HeziLiu/CodingInterviewChinese2 | **none found** — no workflows configured |
| HeziLiu/awesome-chatgpt-prompts-zh | **none found** — no workflows configured |

**Summary: none found.** A direct query of the Actions workflows API on each repo returns zero workflows, so there are no workflow runs to report and nothing to re-run. CI is not wired up on any of these repos yet — a good candidate for a first automation pass.

## Treasury (Ethereum mainnet, live at ~19:38–19:39 UTC, 2026-11-11)

| Asset | Contract | USD Price (live) | Note |
| --- | --- | --- | --- |
| USDC | `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48` | **$0.9998** | at peg (updated 2026-11-11T19:38:38Z) |
| DAI | `0x6B175474E89094C44Da98b954EedeAC495271d0F` | **$1.0000** ($0.99990337) | at peg (updated 2026-11-11T19:38:27Z) |
| ETH (context) | — | **$2,079.95** | updated 2026-11-11T19:39:54Z |

Both stablecoins are holding snugly at peg (~$1.00), consistent with normal pinning behavior. ETH context price is ~$2,080.

## Docs note (for our digest script and GitHub API JSON parsing)

Per the **official Python standard library documentation** (`python/cpython`, `Doc/library/json.rst`), the recommended way to read and parse JSON in current Python is:

- Use **`json.loads(s)`** to decode a JSON document from a **string** (or `bytes`/`bytearray`), and **`json.load(fp)`** to decode from a **file-like object** that supports `.read()`. For encoding, use the counterparts `json.dumps(obj)` (returns a `str`) and `json.dump(obj, fp)` (writes to a file). Our digest script should therefore call `json.loads()` on the API response text and `json.load()` on any saved response file.
- **Catch `json.JSONDecodeError`** (a subclass of `ValueError`) — the docs state it is raised "when the data being deserialized is not a valid JSON document," so wrap our `loads()`/`load()` calls in `try/except json.JSONDecodeError` to handle malformed GitHub API responses cleanly.
- **All optional parameters are keyword-only** (changed in Python 3.6): `json.loads(s, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)`. So when customizing, always pass options by keyword (e.g. `parse_float=decimal.Decimal`), never positionally.
- **Size untrusted input carefully.** The docs carry an explicit warning: *"Be cautious when parsing JSON data from untrusted sources. A malicious JSON string may cause the decoder to consume considerable CPU and memory resources. Limiting the size of data to be parsed is recommended."* The module also implements defenses such as the 3.11+ integer-string length limit (helpful against DoS) and 3.15's `array_hook`. For our case: cap response sizes, and consider `json.load()` with a file handle plus CI-side size checks rather than blowing up memory on a huge payload.
- For quick validation, the docs describe the **command-line interface**: `python -m json` validates and pretty-prints JSON (`echo '{"json":"obj"}' | python -m json`), and `python -m json <file.json>` validates a file in place. Useful for ad-hoc debugging of a saved API response.

Source: official `json` module documentation, CPython repo (`python/cpython`) at `Doc/library/json.rst` — https://docs.python.org/3/library/json.html

## Team chatter (#所有-mcp-atlas)

**Channel:** `#所有-mcp-atlas` is where engineering coordination actually happens (21-member general/announcements channel; `#all-dumle-servers` is announcements-only with join events, `#new-channel` has only purpose text and join events, and the `#gaming-suggestions` / `#tv-show-suggestions` / `#movie-suggestions` channels are pure social noise and were skipped). linyue ma is the active coordinator and the best contact.

**What's useful from recent posts (credited to linyue ma):**

1. **Weekly cadence & MCP status:** linyue ma runs the weekly signal digests and an MCP project status briefing. The latest MCP spec is **2026-07-28**, and per linyue ma's verification the project has been under **AGENTIC AI Foundation (AAIF)** governance since December 2025 (donated alongside OpenAI/Block as a Linux Foundation directed fund). linyue ma is the right person to start any MCP-related conversation.
2. **Cleanup item flagged:** linyue ma posted a `FIX THIS` note flagging that an earlier message leaked actual URL paths and needs to be deleted/replaced. If we reference any prior Slack write-ups, quote from clean sources only.
3. **Verified current Python version:** per linyue ma's check against python.org, the current stable release is **Python 3.15.1** (released 21 Oct 2026; 3.15.0 followed 9 Oct 2026; 3.16.0a1 is still alpha). This lines up with the docs note above (keyword-only args since 3.6, `JSONDecodeError` since 3.5, `python -m json` CLI since 3.5).

**Threads:** none found to open — linyue ma's own thread check confirms none of the messages in any of the 9 channels have threaded replies (no `thread_ts` anywhere), so there was nothing hidden in threads to fold in. The channel is quiet; the last substantive discussion there dates to Dec 2025, and activity since has been linyue ma's own digest/briefing posts plus join events. linyue ma is the best internal point of contact.

---

*Prepared 2026-11-11 ~19:40 UTC. All figures are live lookups at note time; re-pull prices before quoting them in standup.*
