# Tích Tắc 12.12 · Mission Control

**A live-day control tool for a USD 1M, 15-hour TikTok Shop livestream.**
Once an hour, enter the numbers from the LIVE dashboard. Mission Control tells you whether the hour passed, which GMV driver is weakest, and what the pre-agreed next move is and who owns it.

**[Open the live demo →](https://aitoolforriskcontrol.vercel.app/)**

Built for the **TikTok Shop Southeast Asia Regional Case Challenge (Vietnam)** by team *The Three of Cups*. It supports the operations plan in our "Trust in Every Click" solution: 63 days of staged risk control, ending in the live day. *Tích tắc* is Vietnamese for "tick-tock".

<!-- Add a screenshot of the Live day tab as docs/live-day.png, then uncomment the next line. -->
<!-- ![Live day view](docs/live-day.png) -->

> **About the data.** All case figures (creator metrics, GMV, demand indices) are fictional and were supplied by the organiser for illustration. The tool's built-in demo data is made up and is not a forecast. This is an independent project, not affiliated with or endorsed by TikTok or ByteDance.

## The problem

The case asked for one emerging creator to reach USD 1M GMV in a single 12.12 livestream day through strategy and operations, not subsidy.

Our answer picked **Creator B**, the only creator whose peak GMV per hour (GPH) of USD 67.8K covers USD 1M inside a day: 14.8 hours, rounded up to a 15-hour show. GMV was then broken into three drivers:

```text
GMV per hour = views per hour × orders per 1,000 views × AOV ÷ 1,000
```

At the targets (5.1M views, 1.27 orders per 1,000 views, USD 10.30 AOV) that comes to about USD 66.7K per hour. The risk is holding it: Creator B's peak session lasted 8.5 hours, not 15. Mission Control catches a fading hour early and turns the response into a pre-agreed rule instead of an improvised one.

## How it works on the day

Four steps, once an hour:

1. **Enter.** At :00, TTS Ops (TikTok Shop's operations team) copies cumulative GMV, orders, views and, if shown, cancelled GMV from the LIVE dashboard. Type them in, or import an `.xlsx` or `.csv`.
2. **Test.** The hour is tested against one fixed pass mark: USD 1M ÷ 15 h = **USD 66,667**. It passes or misses; there are no amber or red bands.
3. **Flag.** *On pace*, then **Watch** after one miss and **Act now** after two in a row. **Fix data** if the numbers don't add up, and **Target met** once ordered GMV reaches USD 1M.
4. **Act.** The tool shows the pre-agreed next move and its owner, taken from the solution deck. After two misses that is an early shift reset: fresh hero deal, ad top-up, co-host swap. Nothing is generated at run time.

## What's inside

### Live day (12 Dec, 08:00–23:00)

- **Hour-by-hour grid.** 15 cells in three 5-hour shifts (resets at 08:00, 13:00 and 18:00), with the live shift outlined.
- **Driver diagnosis.** Splits the last completed hour into views, conversion and order value, compares each with its target and names the weakest.
- **Cancellations.** Cancelled GMV, cancel share and net GMV beside ordered GMV, benchmarked against the team's own 11.11 rehearsal.
- **Pace view.** A KPI strip and a chart of cumulative GMV against the straight-line plan. There is no forecast line.
- **Logs and exports.** Hourly log, decision log (time, owner, action, status at the time), a one-click "copy hourly update", and Excel and JSON export.

### 63-day runway

Five phases: **Commit** (10–23 Oct), **Build** (24 Oct–6 Nov), **Prove** (7–13 Nov), **Lock** (14 Nov–11 Dec) and **Live day** (12 Dec). Each shows its critical risk, a warning signal with the input that tests it, preventive controls to tick off, and an owner.

A **go / no-go gate** on 14 Nov reads the 11.11 rehearsal: GPH of at least USD 68,000 keeps Creator B, otherwise switch to the standby, Creator E. The 11.11 cancelled GMV entered here becomes the live-day cancellation benchmark.

### Numbers & rules, and How to use

**Numbers & rules** lists every number the tool uses with a source tag: `CASE` (case data), `DERIVED` (arithmetic, formula shown), `EXTERNAL` (a named source) or `DECK RULE` (a management rule from the solution deck). **How to use** is an in-app guide for the ops owner, from set-up to the 13 Dec review.

## Design choices

- **No invented thresholds.** The only warning rule is the deck's: two misses in a row. The pass mark stays fixed all day; the catch-up rate is shown for information and never moves the bar.
- **Unknown is not zero.** A skipped hour shows as unknown, and a blank cancelled-GMV field means "not tracked", not 0.
- **Validate before deciding.** Counters can't go down, cancelled GMV can't exceed ordered GMV, entries must fall on the hour inside show hours, and orders and views must be above zero. If a check fails, the banner switches to Fix data and the next-move panel is blocked until it is fixed.

## Run it

**Online:** [aitoolforriskcontrol.vercel.app](https://aitoolforriskcontrol.vercel.app/)

**Locally:** it is one static HTML file, so there is nothing to install or build.

```bash
git clone https://github.com/racheltran184/tich-tac-mission-control.git
cd tich-tac-mission-control
open index.html   # macOS; on Windows: start index.html
```

The font and the SheetJS library load from public CDNs. Tracking works offline; Excel and CSV import and export need a connection.

### Import format

One row per hour from 09:00 to 23:00, with cumulative values. `cumulative_cancelled_gmv` is optional. The in-app **Download template** button gives a blank file.

```csv
time,cumulative_gmv,cumulative_orders,cumulative_views,cumulative_cancelled_gmv
09:00,71000,6827,5300000,3900
10:00,145000,13875,10700000,8700
11:00,214000,20574,15850000,13200
```

These rows are illustrative: the first three hours of the built-in demo.

## Under the hood

| Area | Details |
|---|---|
| Front end | HTML5, CSS3 (custom properties for light and dark themes, grid layout) and vanilla JavaScript (ES6). No framework, no build step. |
| Layout | Responsive down to phone width. Follows the system light or dark setting, with a toggle. |
| Charts | Inline SVG, drawn by hand. No chart library. |
| Import / export | [SheetJS](https://sheetjs.com/) (`xlsx` 0.18.5) for `.xlsx` and `.csv` in, Excel out. JSON export is built in. |
| State | Held in memory in the open tab. No backend, database or login. |
| Hosting | Vercel (static). |

Everything lives in `index.html`. The places to edit:

| To change | Look for |
|---|---|
| Target, show hours, shift resets, driver targets | `DEF` |
| Runway phases, dates, warning signals, controls | `PH` |
| Driver actions and owners | `DRV` |
| The core calculation | `compute()` |
| The Numbers & rules ledger | `renderLedger()` |

Runway dates are written for the 2026 campaign.

## Limitations and next steps

- **State lives in memory.** Hours, logs and runway inputs are held in the open tab, so a refresh clears them. Use Export Excel or JSON during the day. A natural next step is autosave to `localStorage`.
- **Manual entry.** Ops copies the numbers from the LIVE dashboard; there is no API integration. The tool checks pace and does not replace the dashboard.
- **One operator.** One person enters data and everyone else reads the same screen. There are no accounts or shared state.
- **Late cancellations.** Orders can still be cancelled after the show, so the 23:00 cancelled figure is re-entered on 13 Dec for the final net GMV.
- **Fictional data.** Thresholds follow the case and the deck, not real campaign results.

## Project context

| Case question | Our answer |
|---|---|
| Who should TikTok Shop prioritise? | **Creator B**: GPH of USD 67.8K, twice the next best, and 14.8 hours to USD 1M. Health & Beauty and Fashion make up 98% of its sales. |
| What drives performance? | The GMV drill-down (views × orders per 1,000 views × AOV), read through the 4Cs: customer needs, cost, convenience, communication. |
| What actions? | Hero skincare, makeup and apparel bundles (about 60% Health & Beauty, 40% Fashion) from official stores and authorised sellers, plus a six-step live funnel from teasers and seeding ads to free shipping with a spend threshold. |
| What timeline and risk controls? | 63 days in five phases, each with a warning signal and a preventive control. This tool tracks them. |

## Team

**The Three of Cups**

| Name | Role |
|---|---|
| Nguyen Khuat Son Tra | Project lead, Strategy |
| Nguyen Do Minh Huu | Project lead, Campaign |
| Tran Ngoc Bao Han | Project lead, Ops |

## Contact

[LinkedIn](https://www.linkedin.com/in/racheltran184/) · [GitHub](https://github.com/racheltran184) · racheltran184@gmail.com
