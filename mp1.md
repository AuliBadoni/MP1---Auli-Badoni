# Mini Project 1 — Billboard Hot 100 (2023–2026)

**C3 — Data cleaning and file handling** · **C5 — Data analysis with pandas** · **C6 — Data visualization** · **C7 — Critical evaluation and professional judgment**

This file is the project-level competency document the assignment's Section 5 Competency Claim block points to. It is written for three audiences in the same pass: the grader (rubric checklists + strong claims per competency), a non-technical reader (a plain-English block and run instructions), and future-me (process notes I want to remember next time). The analysis itself lives in [`week 6/week6_mp1_starter.ipynb`](week%206/week6_mp1_starter.ipynb); the chart-by-chart justification lives in [`week6.md`](week6.md). This file ties them together.

---

## Project at a glance

I analyzed weekly Billboard Hot 100 chart data from **2023-01-07 to 2026-05-02** — **17,400 rows across 174 chart weeks**, covering **2,181 unique songs** and **506 unique primary artists** — scraped via the `billboard.py` package from Billboard.com. I asked three questions of this data:

1. Which songs and artists had the strongest chart presence between 2023 and 2026 (total weeks, Top 10 weeks, and weeks at #1)?
2. How do songs typically move through the Hot 100 — slow climb, debut high, hold steady, or spike and drop?
3. Has chart behavior changed across 2023–2026 — new entries, average longevity, rank volatility?

I shipped four charts (three answering the research questions, one in Section 4 reframing the Q1 finding) and wrote an interpretation cell under each chart explaining what the result means, what surprised me, and what I cannot conclude from the data.

---

## Process story — matplotlib → plotly, with AI assistance

I did not write this notebook in one clean pass. I started in a **separate file** using the **professor's template** with **matplotlib** for the first round of visualizations. After feedback that the charts needed to be more legible, more interactive, and able to show extra metadata like artist names and album covers, I **rebuilt the visualizations in plotly** inside [`week 6/week6_mp1_starter.ipynb`](week%206/week6_mp1_starter.ipynb). The matplotlib pass taught me what data I needed — the plotly pass let me show it.

For the code I used **Claude** as a coding partner, especially for the **plotly figure scaffolding** (subplots layouts, hover text, annotation offsets) and for sketching the shape of the **trajectory classifier** in Q2. For in-editor tweaks, refactors, and markdown drafting I used **Cursor**. The analytical decisions — which question to ask, where to set a threshold, what to call a limitation, and when *not* to over-claim a trend — were mine. The AI sped up scaffolding; I owned the judgment.

---

## What I built

The four charts shipped from this project:

- [`chart1_songs_top10.png`](chart1_songs_top10.png) — horizontal bar, "Billboard Hot 100: Top 10 Most Charted Songs (2023–2026)" — answers Q1.
- [`chart2_song_trajectories.png`](chart2_song_trajectories.png) — multi-line, "Three Ways a Song Can Win (2023–2026)" — answers Q2.
- [`chart3_chart_behavior.png`](chart3_chart_behavior.png) — 3-panel stacked line with rolling-average overlays, "Has Chart Behavior Changed? (2023–2026)" — answers Q3.
- Section 4 scatter — "Long Top-10 runs don't guarantee weeks at #1 (2023–2026)" — same Q1 finding viewed as the relationship between two metrics, with four annotated outliers (Lose Control, A Bar Song (Tipsy), Espresso, Birds Of A Feather). Lives in the notebook; can be exported to `chart4_top10_vs_number1.png` by uncommenting the `fig.write_image(...)` line in that cell.

---

## For someone who does not read code

You can read this section without touching Python.

- **The dataset.** Three and a half years of weekly Billboard Hot 100 charts. Each row is one song's position on one chart week. There are 17,400 of those rows.
- **Chart 1** ranks the ten songs that spent the most weeks in the Top 10 of the chart during this period, and overlays a second bar showing how many weeks each song spent at #1. The shape of the chart tells you which songs stayed popular for a long time and which ones actually reached the very top — and that these are two different things.
- **Chart 2** picks three songs that each tell a different "shape of fame" story: one peaks immediately and stays high (Beautiful Things by Benson Boone), one builds slowly over months before exploding (Pink Pony Club by Chappell Roan), and one peaks early then carries a very long tail (Lose Control by Teddy Swims). The chart lets you literally see climb-vs-debut.
- **Chart 3** asks "is the chart itself behaving differently in 2026 than it was in 2023?" — has new music turning over faster, are songs lasting shorter, is the chart bouncier? Three stacked panels, one per question. The honest answer is *no clean trend* — it is spiky and seasonal, not steadily shifting.
- **The Section 4 scatter** is the same Chart 1 story told a different way: one dot per song, with weeks in the Top 10 on the horizontal axis and weeks at #1 on the vertical axis. Songs at the very right of the chart but near the bottom are the "popular for ages but never #1" story.

---

## How to run it

1. Open the `hcde530` folder in Cursor or VS Code.
2. Select the kernel that matches your project `.venv` (not a stray system Python). This avoids the "matplotlib is installed but the notebook can't see it" trap I hit in Week 5.
3. Open [`week 6/week6_mp1_starter.ipynb`](week%206/week6_mp1_starter.ipynb).
4. **Run All** from top to bottom. The notebook is ordered:
   - **Section 1 — Overview** (markdown)
   - **Setup cells** (`pip install` guard, imports)
   - **Section 2 — Data Profile** (loads [`week 6/data/billboard_hot100_2023_2026.csv`](week%206/data/billboard_hot100_2023_2026.csv), fixes dtypes, derives `primary_artist`, `featured_artists`, `song_key`)
   - **Section 3 — Analysis** (Q1 / Q2 / Q3 — one code cell per question with the chart, followed by an interpretation markdown cell)
   - **Section 4 — Visualization** (scatter that reframes Q1) + chart rationale
   - **Section 5 — Conclusions** (summary of findings + project wrap-up)
5. Optional: to regenerate the PNGs at the repo root, uncomment the `fig.write_image(...)` lines at the bottom of each chart cell.
6. Dependencies (`pandas`, `plotly`, `kaleido`, `requests`, `Pillow`, `nbformat`) are pinned in [`requirements.txt`](requirements.txt). The setup cell will install anything missing.

**What "success" looks like:** four rendered charts (or four PNGs at the repo root if you exported them), no red error tracebacks, and `df.shape` reading `(17400, 19)` after the cleaning section.

---

## Competency 3 — Data cleaning and file handling

### Evidence in this repo

| Rubric item | Where to look |
| --- | --- |
| **Loads a non-trivial dataset** | `pd.read_csv('week 6/data/billboard_hot100_2023_2026.csv')` in Section 2 of the notebook |
| **Dtype fixes** | `pd.to_datetime(df['chart_date'])`, `pd.to_numeric(..., errors='coerce')` over `rank`, `last_week`, `peak_rank`, `weeks_on_board`, `rank_change` |
| **Reshapes / derives columns** | `primary_artist`, `featured_artists`, `song_key = song + ' — ' + primary_artist` |
| **Handles missing values honestly** | `df.isnull().sum()` plus a breakdown showing the 2,788 null `last_week` rows are exactly the 2,044 `new` + 744 `re-entry` rows — *expected behavior, not missing data* |
| **Documents trade-offs** | Cleaning notes call out the artist-string trade-off so the limitation travels with the data |

### How I demonstrated C3

The most interesting cleaning problem was **artist-string inconsistency**. The same kind of credit appears as `&`, `Featuring`, `X`, `x`, and `,` depending on the song — "Drake & 21 Savage" vs. "Drake Featuring 21 Savage" vs. "Metro Boomin, The Weeknd & 21 Savage" — so the same collaboration looked like different rows to a `groupby`. I wrote a small regex split (`re.split(r' & | Featuring | X | x |, ', artist)`) that takes the first credit as `primary_artist` and the rest as a `featured_artists` list, and I built `song_key = song + ' — ' + primary_artist` to dedupe across small variations. This is a deliberate trade-off, not a clean fix: a true duet like "Lady Gaga & Bruno Mars" reduces to "Lady Gaga", which **under-credits the collaborator**. For chart-behavior analysis I prioritized consistent grouping over perfectly representing every collaboration, and I named the trade-off in the cleaning notes so a future reader knows what the number does and does not mean.

I also did the unglamorous-but-required parts: I converted `chart_date` from string to `datetime64`, coerced the numeric columns so `groupby` aggregations would not silently break, and traced **every** null. The 2,788 nulls in `last_week` and `rank_change` are not missing data — they are exactly the 2,044 `new` entries plus 744 `re-entries`, which by definition have no previous week. I kept those rows; dropping them would have removed every debut on the chart.

### Hardest moment in C3 — album covers

The piece that broke me for the longest was **incorporating album covers into the charts**. The dataset includes an `image_url` column from the Billboard CDN, but it is **null for 696 rows** — disproportionately for the top-ranked songs because of how `billboard.py` scrapes. I built a small image-lookup dictionary keyed by `song_key`, fetched and cached cover art for the highlighted songs, and handled the missing-image case so the chart did not crash on a `None`. The trajectory classifier (covered under C7) was hard for analytical reasons; the album-covers piece was hard for *plumbing* reasons — null handling, request error handling, and keeping the layout clean when an image failed to load.

### Strong claim

*"I loaded a 17,400-row weekly chart dataset, fixed dtypes, derived `primary_artist` / `featured_artists` / `song_key` from inconsistent collaboration strings via regex split, traced every null back to a real movement type, and wrote the artist-string trade-off into the cleaning notes so the limitation travels with the data."*

---

## Competency 5 — Data analysis with pandas

### Evidence in this repo

| Rubric item | Where to look |
| --- | --- |
| **Loads a dataset** | `pd.read_csv('week 6/data/billboard_hot100_2023_2026.csv')` |
| `head()` / `info()` / `describe()` | Section 2 |
| **`groupby` + `agg`** | `df.groupby(['song', 'primary_artist']).agg(top10_weeks=('is_top_10', 'sum'), number1_weeks=('is_number_one', 'sum'), peak_rank=('rank', 'min'))` for Chart 1 |
| **Derived column from arithmetic** | `chart_score = 101 - rank` so higher = better and weekly scores can be summed/averaged |
| **`cumcount` for sequencing** | `df.groupby('song_key').cumcount() + 1` to build `week_number` (the song's nth week on chart, sorted by `chart_date`) |
| **`idxmax` for peak detection** | inside `_trajectory_for` to find each song's peak week |
| **Time-series rolling stat** | monthly aggregation + `rolling(...)` mean overlay for Chart 3 panels |
| **Interpretation, not just output** | Markdown interpretation cell under each chart in Section 3 |

### How I demonstrated C5

The analysis is pandas-first. For Chart 1 the `groupby(...).agg(...)` produces a tidy frame of `top10_weeks`, `number1_weeks`, and `peak_rank` per song; that aggregation is where **A Bar Song (Tipsy)'s 19 weeks at #1** fell out as a number. For Chart 2 I derived `chart_score = 101 - rank` and `week_number` via `cumcount`, then defined `_trajectory_for` to compute `peak_position = peak_week / total_weeks` using `idxmax()` on `chart_score`; that step is where **Lose Control was labeled Spike & Drop** despite a 112-week run. For Chart 3 I aggregated by `(year, month)` for new entries, average `weeks_on_board`, and average `abs(rank_change)`, then layered a `rolling(...)` mean so the trend reads through the noise. Every number I cite in the interpretation cells traces back to a specific pandas call — that traceability is the point.

### Strong claim

*"I built three research-question answers from `groupby`/`agg`/`cumcount`/`idxmax`/`rolling` pipelines on a 17,400-row Hot 100 dataset, wired specific numbers (Lose Control's 80 Top-10 weeks, A Bar Song's 19 weeks at #1) back to the exact pandas operations that produced them, and interpreted each result in markdown."*

---

## Competency 6 — Data visualization

### Evidence in this repo

| Rubric item | Where to look |
| --- | --- |
| **Chart type matches the question** | Horizontal bar for ranked categorical, multi-line for trajectory-over-time, 3-panel line for three "is this changing?" questions, scatter for a two-metric relationship |
| **Claim-style titles** | "Top 10 Most Charted Songs", "Three Ways a Song Can Win", "Has Chart Behavior Changed?", "Long Top-10 runs don't guarantee weeks at #1" |
| **Labeled axes** | All four charts |
| **Notebook + markdown** | Each chart sits in [`week 6/week6_mp1_starter.ipynb`](week%206/week6_mp1_starter.ipynb) next to an interpretation cell; the Section 4 scatter has a chart-rationale cell |
| **Published artifacts** | [`chart1_songs_top10.png`](chart1_songs_top10.png), [`chart2_song_trajectories.png`](chart2_song_trajectories.png), [`chart3_chart_behavior.png`](chart3_chart_behavior.png) committed at the repo root |

### How I demonstrated C6

Chart type was matched deliberately to data shape — chart-by-chart justifications are in [`week6.md`](week6.md). The short version: **horizontal bar** for Chart 1 because song + artist labels are long and ranked, and because I needed to overlay two related metrics on the same row to make the longevity-vs-dominance gap visible. **Multi-line** for Chart 2 because trajectory is fundamentally rank-over-time and only a line makes the climb-peak-drop shape literally readable. **3-panel stacked line** for Chart 3 because three "is this changing?" questions deserve a shared x-axis but compete for a single y. **Scatter** for the Section 4 chart because the finding is the *relationship* between two metrics — points sitting on `y = 0` with large `x` are the literal shape of "popular ≠ #1", which a bar chart could not show.

The matplotlib → plotly migration is part of this competency: I picked the chart *type* in matplotlib and then re-implemented in plotly to get hoverable detail, claim-style titles, color-coded categories, and annotated outliers. The lesson is that *picking the right chart* is a different skill from *making the chart readable*, and the second pass is where most of the design happened.

### Strong claim

*"I matched four chart types to four data shapes — horizontal bar, multi-line, 3-panel line, and scatter — wrote claim-style titles ('Long Top-10 runs don't guarantee weeks at #1' rather than 'Top-10 weeks vs #1 weeks'), labeled the axes, and migrated from a matplotlib first pass to plotly so the charts could carry hover detail and annotated outliers without losing readability."*

---

## Competency 7 — Critical evaluation and professional judgment

This is the competency I want to be evaluated hardest on.

### Q2 trajectory thresholds with the professor's nudge

My first attempt was to **classify trajectories across the full 17,400-row dataset**. The professor flagged that implementing a clean classifier at full scale would be hard — too much variation in chart life (5 weeks to 100+), too many edge cases for short-lived songs. I took the nudge: I **narrowed scope to the top 20 most-charted songs** by total `chart_score`, classified within that set, and defined three categories with explicit thresholds on `peak_position = peak_week / total_weeks`:

- `peak_position <= 0.25` → **Debut High**
- `peak_position >= 0.60` → **Slow Climber**
- otherwise → **Spike & Drop**

Then I made the harder call. **Lose Control (Teddy Swims)** felt *culturally* like a slow burn — it took months to get heavy radio rotation. But its peak rank fell in the first half of its 112-week run, so the rule labeled it **Spike & Drop**. I *kept the label*. I did not fudge the threshold to make a single song read the way I felt about it. Instead I wrote that exact disagreement into the Q2 interpretation cell as an **honest limitation of fixed thresholds when total chart life varies by an order of magnitude**, and I cite Lose Control by name as the case where the math and the culture diverge. That is the kind of moment C7 is asking about.

### Primary artist vs. featuring collaborations

The second judgment call was the artist-string cleaning trade-off (described in C3). I split on `&`/`Featuring`/`X`/`,` and grouped by first-billed artist, which **under-credits true duets** like "Lady Gaga & Bruno Mars" (collapses to Lady Gaga). For chart-behavior analysis I valued consistent grouping over perfect representation of every collaboration — but I refused to make the trade-off invisible. I wrote it into the cleaning notes so anyone running the notebook later knows what `primary_artist` does and does not mean. The right call here is not to find a clever workaround; it is to be honest about what the simplification costs.

### Not over-claiming Chart 3

Chart 3 is where C7 shows up by *not* shipping a finding. I expected to see the "TikTok era, everything turns over faster" pattern — more new entries, shorter chart life, higher volatility. **It is not there.** All three panels are spiky and seasonal rather than steadily shifting in one direction. I could have cropped the y-axes, drawn a trend line, or framed a single quarter as "the inflection point" — and I did not. The interpretation cell explicitly says *no clean structural shift*, and the Section 5 summary names that as the **surprise**. A practitioner reading Chart 3 should walk away thinking the chart is shaped by bursts and events more than by one macro trend, which is a useful, non-overclaimed finding on its own.

### Strong claim

*"When my first trajectory classifier did not scale, I narrowed scope on the professor's nudge and shipped a working version on the top 20 songs; when the rule labeled Lose Control as Spike & Drop instead of the Slow Climber it culturally feels like, I kept the label and named the disagreement as a limitation; when Chart 3 refused to show a structural trend, I did not invent one. Every limitation in this project is documented next to the result that produced it."*

---

## Observations

- **Aggregation changes meaning.** "Most weeks in the Top 10" and "Most weeks at #1" are different rankings (Lose Control vs. A Bar Song (Tipsy)). A single "popularity" number would have hidden the gap.
- **Re-entries materially shape longevity stats.** 744 of 17,400 weekly rows are re-entries — large enough that holiday returners like All I Want For Christmas Is You distort cumulative `weeks_on_board` numbers if you do not treat them deliberately.
- **Chart 3 is seasonal, not structural.** Spikes in new entries (Jul 2023: 94 new songs) and volatility (Oct 2023: ~13.7 avg absolute rank change) are events, not trends. Rolling averages help you see that without smoothing the truth away.
- **Threshold rules vs. cultural narratives can disagree.** Fixed `peak_position` thresholds break down for very long chart runs — Lose Control is the case study, and naming that is part of the analysis.
- **Matplotlib taught me the data; plotly taught me the design.** The first pass was about what to show. The second pass was about what to let a reader hover, see in a tooltip, and read off the chart in two seconds.
- **AI sped up scaffolding; I owned the judgment.** Claude helped me get plotly subplots, hover-text formatting, and the trajectory-classifier skeleton up faster; Cursor helped with refactors and markdown drafting. Every threshold, every label, every limitation was verified by me reading the data — not accepted because the tool was confident.
- **Kernel matters more than the chart code.** Carrying the Week 5 lesson: when in doubt, check that the notebook is using the kernel that has your packages before you rewrite anything.

---

## So what? / Next time

If I restarted MP1 tomorrow, the iteration I want to run is **TikTok / Reels popularity vs. Billboard rise/fall**. The Hot 100 is a *response* signal — it measures what already broke through. A short-form virality signal (TikTok sound count, Reels usage, or a TikTok-charting index) is the *leading* signal. Joining the two on `song_key` (or song + primary_artist) and asking *does viral pre-chart traction predict trajectory category?* would let me say something Chart 2 cannot say on its own — whether a song with heavy TikTok use before debut is more likely to be Debut High than Slow Climber, or whether viral fade predicts Spike & Drop.

Two smaller next steps that fall out of the limitations I already named: an **artist-level rollup** (total weeks + total #1 weeks + distinct charting songs per `primary_artist`) so Morgan Wallen with many songs can be compared fairly against Taylor Swift with fewer long-running songs; and an **in-window-only longevity recomputation** that ignores Billboard's cumulative `weeks_on_board` and counts only weeks actually observed between 2023-01-07 and 2026-05-02, which would clean up Chart 3's middle panel.

The bigger lesson I want to carry forward: **ship the limitation next to the result**. Every interpretation cell in this notebook names what the number means *and* what it cannot mean, and that pattern is the discipline I want to keep using for the next project.
