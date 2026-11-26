# Cashu limit diff

[Download the complete MIT-licensed source code, examples and tests](./Cashu-Limit-Diff.zip). Extract the ZIP before running the examples below.

A mint upgrade can still advertise “bolt11 / sat” after disabling melting or raising its minimum mint amount. Give this tool two public Cashu `GET /v1/info` responses to see the exact payment amounts that changed, separately for minting and melting. Wallet integrators can use the JSON CLI in deployment checks. Mint operators can use the offline browser page to review changes before deployment.

## Try the browser version

Open `index.html` from the extracted directory. Select **Load example**, or paste your snapshots and select **Compare limits**. The page and its JavaScript are local and use no network requests or external packages. The bundled example is a simulated deployment.

| Example operation | Amounts lost | Amounts gained |
| --- | --- | --- |
| Mint via bolt11, sat | 10–99 | 10,001–15,000 |
| Melt via bolt11, sat | 1–1,000 | None |

## Use it in CI

Node 18 or later works without npm installation.

```sh
node cli.cjs examples/before.json examples/after.json
node cli.cjs --fail-on-loss examples/before.json examples/after.json
node --test test.cjs
```

The first command outputs JSON. The second exits with **1** because the example loses amount coverage. Exit codes are **0** for a completed comparison, **2** for invalid inputs or CLI usage, and (in fail-on-loss mode) **3** if an operation cannot be compared. Unknown coverage takes precedence over loss; the JSON still reports known losses. Endpoints in the result are decimal strings. A null upper endpoint means no upper bound was advertised.

## What the comparison covers

Each operation has its own method and unit pairs. Positive whole amounts use the smallest unit defined by that unit. Boundaries are inclusive. Missing or null min/max values impose no advertised bound on that side. A disabled operation advertises no amounts, even if its old method rows are retained. If an entire NUT-04 or NUT-05 section is missing, the result reports unknown for that operation. A malformed section produces an error. Exact unions of repeated/overlapping ranges and disjoint gaps are supported; if rows differ in method-specific options, the reported coverage is the union **across options**.

Method-specific invoice rules, fees, mint liquidity, denominations, client authorization, and whether a quote will succeed sit outside the calculation. Returned ranges describe changes in the contract a mint advertises. Snapshot integer fields are accepted through JavaScript’s safe integer maximum; outside that range the input is rejected. The algorithm uses BigInt for interval endpoints and never enumerates individual amounts in production.

The automated suite checks missing/malformed settings, global disables, null bounds, huge endpoints, independent method/unit pairs and 4,096 pairs of small schedules against brute-force enumeration.

## Protocol references

[NUT-04 mint settings](https://github.com/cashubtc/nuts/blob/main/04.md#settings) · [NUT-05 melt settings](https://github.com/cashubtc/nuts/blob/main/05.md#settings) · [NUT-06 public mint information](https://github.com/cashubtc/nuts/blob/main/06.md).

Built November 26, 2026 with AI assistance. MIT licensed.
