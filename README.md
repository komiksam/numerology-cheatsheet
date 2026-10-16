# Numerology — Digital Reduction Cheatsheet

A single self-contained HTML file that computes the same name or number under
nineteen different numerological conventions, side by side, updating as you
type.

The point is not to settle which system is right. It is to show you, in one
place, *what each system actually does* — including where they contradict each
other, and including which of them have an interpretive tradition and which are
just arithmetic.

```
numerology-cheatsheet.html     ~160 KB, no dependencies, no network access
```

---

## Contents

- [Quick start](#quick-start)
- [Using it in Obsidian](#using-it-in-obsidian)
- [The interface](#the-interface)
- [Views](#views)
- [Grouping](#grouping)
- [URL parameters](#url-parameters)
- [Settings](#settings)
- [The nineteen cards](#the-nineteen-cards)
- [How to read a card](#how-to-read-a-card)
- [Interpretation: what is claimed and what is not](#interpretation-what-is-claimed-and-what-is-not)
- [Provenance and disputed claims](#provenance-and-disputed-claims)
- [Customising](#customising)
- [Verification](#verification)
- [Known limitations](#known-limitations)
- [Version history](#version-history)

---

## Quick start

Open `numerology-cheatsheet.html` in any modern browser. No server, no build
step, no install. Double-click it.

The page opens with a line of Milton already loaded, because it is the
standard test case: **34 letters, 666 by the `A = 10 … Z = 35` order.**

Type anything into the box. Letters and digits are both accepted, accents are
stripped, and everything recomputes on every keystroke.

---

## Using it in Obsidian

The entire sheet is one `<script>` block that generates its own markup and
styles. Copy everything from `<script>` to the matching `</script>` and paste it
into a note.

What makes this work:

- **The background is transparent** and every colour derives from
  `currentColor`, so it inherits whatever Obsidian theme renders it.
- **No external references at all** — no stylesheet link, no image, no font, no
  network call. One block, no assets.
- **Multiple embeds coexist.** The stylesheet is shared and de-duplicated by
  content; a re-render replaces only its own instance.

Two things are deliberately switched off inside Obsidian:

| Feature | Why |
|---|---|
| URL state and the **copy link** button | Rewriting the address bar inside Obsidian fights the app for its own URL bar. The code checks `location.protocol` and disables it for anything that is not `http:`/`https:`. |

Everything else — all five views, all four grouping modes, the tag row, the
column picker, every card, every diagram — works identically.

---

## The interface

Four rows of controls, then the cards.

| Row | Control | What it does |
|---|---|---|
| 1 | **view** | Which cards exist at all |
| 2 | **group by** | How the tag row is organised, plus the **columns** picker |
| 3 | **tags** / **cards** | `all`, `none`, then one pill per group with a count badge |
| 4 | quick picks | Preset buttons, **clear**, **copy link** |
| — | textarea | The name, date or number |
| — | stats | letters, digits, vowels, consonants, cards shown, and the current agreement |

Each card has a **flip** button for a long explanation and a **copy** button
for its result.

---

## Views

A view decides which cards exist — as distinct from grouping, which only
reorders and filters within the cards that already exist.

| View | Cards | For |
|---|---:|---|
| **general** | 22 | Everything. The default. |
| **name** | 17 | Systems that calculate a personal name |
| **birthday** | 4 | Systems that take numerals: dates, years, phone numbers |
| **layout** | 10 | The grids, pyramids and trees |
| **qabbalah** | 3 | Gematria, alphanumerical, sephirotic |
| **reference** | 5 | Lookup tables: symbology, life path, Chaldean, sephirotic, Lo Shu |

**`name` is narrower than it looks, on purpose.** It drops the two numeral-only
cards, and it also drops **Morse** and the **Modular** card, because neither is
a received tradition for names. Both remain available in `general`.

**`birthday` is only three cards because the sheet has no life-path cards.**
Digit reduction *is* a life-path calculation, so `simple`, `reverse` and
`math` between them cover it. If you want dedicated day/month/year and personal
year cards, they do not exist yet.

Each card declares its own membership with a `views: [...]` array — see
[Customising](#customising).

---

## Grouping

The tag row reorganises the pills four ways. Each mode remembers its own
on/off state, so switching back and forth is stable.

| Mode | Groups by | Notes |
|---|---|---|
| `tradition` | pythagorean, chaldean, qabbalah, chinese, modern, arithmetic | |
| `numeral` | digits, 1–9, 1–8, 1–400, 11–32, other | Which number range each card's table spans |
| `by result` | the figure each card currently returns | **Dynamic.** Keys are rebuilt from the live results, masters first then ascending |
| `no grouping` | each card on its own | One pill per card |

`by result` is the interesting one: type a name and the pills become the set of
numbers the cards actually produced, with counts. It shows you instantly which
cards agree.

### Columns

`auto` plus 1–5. In **auto** the sheet packs with flex rather than grid, so
wrapped lines are always filled edge to edge — a wide card asks for a larger
share instead of demanding a whole row and stranding the rest. Fixed column
counts use a grid where the four wide cards span the full row.

---

## URL parameters

The view round-trips through the query string. Every change is written back with
`history.replaceState`, which **never reloads the page and never grows the
history stack** — the back button still behaves.

| Parameter | Values | Example |
|---|---|---|
| `input` | any text | `?input=Andrea` |
| `view` | `general` `name` `birthday` `layout` `qabbalah` | `?view=name` |
| `groupby` | `tradition` `numeral` `result` `none` | `?groupby=tradition` |
| `cols` | `0` (auto) or `1`–`5` | `?cols=2` |
| `sel` | comma-separated keys that stay **on** | `?sel=chaldean,qabbalah` |

`sel` is only written when something is hidden, so ordinary links stay short.

A complete example:

```
numerology-cheatsheet.html?input=Alexandra+Diaz&view=name&groupby=tradition&cols=2&sel=arithmetic,pythagorean,chaldean,qabbalah
```

That link reopens: the text `Alexandra Diaz`, the `name` view, grouped by
tradition, two columns, with exactly those four traditions shown.

Use **copy link** to put the current view on your clipboard.

Unknown or invalid values are ignored rather than rejected, so a hand-edited or
truncated link still opens.

---

## Settings

Everything you are likely to want to change is at the top of the script, in the
`0. SETTINGS` block.

| Setting | Default | Meaning |
|---|---|---|
| `VIEW_MODE` | `"general"` | Which view opens. One of the ids in `VIEW_CHOICES`. |
| `VIEW_CHOICES` | five entries | The view buttons. Each has `id`, `label` and `title` (the tooltip). **Add an entry here and set `views:` on the cards you want in it.** |
| `GROUP_MODE` | `"none"` | Which grouping opens: `tradition`, `numeral`, `result`, `none`. |
| `COLUMNS` | `3` | `0` means as many as fit (auto). |
| `COLUMN_CHOICES` | `[0,1,2,3,4,5]` | The column buttons. |
| `DEFAULT_TAGS` | see below | Which groups start **on**, per grouping mode. `"*"` means all. |
| `GROUP_LABELS` | — | Friendly names for the grouping keys. |
| `SHORT` | — | Short pill labels used in `no grouping` mode. |
| `INITIAL_INPUT` | `"Of man's first disobedience and the fruit"` | What the box contains on load. |
| `PRESETS` | `["sam", "sam díaz"]` | The quick-pick buttons. |

`DEFAULT_TAGS` looks like this:

```js
const DEFAULT_TAGS = {
  none:      ["*"],
  tradition: ["pythagorean", "chaldean", "qabbalah", "chinese", "modern", "arithmetic"],
  numeral:   ["digits", "1-9", "1-8", "10-35", "1-400", "11-32", "other"],
  result:    ["*"]            // "*" means every result group is on
};
```

Remove a key to hide that group at load. In `none` the keys are card ids
(`simple`, `pyth`, `qabbalah`, …). The `result` mode is not seeded: its groups
are derived from the live numbers, and anything unseen starts visible.

---

## The nineteen cards

| # | Card | `id` | extra views | tradition | numerals |
|---|---|---|---|---|---|
| 1 | Soul urge - heart's desire | `soulurge` | `name` | pythagorean | `1-9` |
| 2 | Personality - outer impression | `personality` | `name` | pythagorean | `1-9` |
| 3 | Life path (master-preserving) | `lifepath` | `birthday` | arithmetic | `digits` |
| 4 | Simple digital root | `simple` | `birthday` | arithmetic | `digits` |
| 5 | Reverse addition | `reverse` | `birthday` | arithmetic | `digits` |
| 6 | Pythagorean | `pyth` | `name` | pythagorean | `1-9` |
| 7 | Chaldean Master Numbers | `chaldean` | `name` | chaldean | `1-8` |
| 8 | Gematria & 13-square | `qabbalah` | `name` `layout` `qabbalah` | qabbalah | `1-400` |
| 9 | Anglossic Qabbalah | `anglossic` | `name` `qabbalah` | qabbalah | `1-400` |
| 10 | Triangle of Life — folding pyramid | `tolife` | `name` `layout` | pythagorean | `1-8` |
| 11 | Pythagorean chart — 45 cells | `pythchart` | `name` `layout` | pythagorean | `1-9` |
| 12 | Chaldean pyramid 3-3-3-3-3-3 | `chpyr` | `name` `layout` | chaldean | `1-8` |
| 13 | Chaldean pyramid — reversed | `chrev` | `name` `layout` | chaldean | `1-8` |
| 14 | Affirmation triangle (3-3-3) | `affirm` | `name` `layout` | modern | `1-9` |
| 15 | Pythagorean 3-column grid | `pyth3` | `name` `layout` | pythagorean | `1-9` |
| 16 | Ternary reduction tree (P3) | `p3` | `name` `layout` | pythagorean | `1-9` |
| 17 | Sephirotic tree — 22 paths | `seph` | `name` `layout` `qabbalah` | qabbalah | `11-32` |
| 18 | Lo Shu magic square | `loshu` | `name` `layout` | chinese | `1-9` |
| 19 | Morse code reduction | `morse` |  | modern | `other` |
| 20 | Telephone keypad (T9) | `tel` | `name` | modern | `other` |
| 21 | Modular & mathematical view | `math` | `birthday` | arithmetic | `other` |
| 22 | Word length & ordinals | `len` | `name` | arithmetic | `other` |

---

## How to read a card

Each card has two levels of text.

**Front.** The big figure, a metadata list, a diagram where relevant, then a
short note explaining what the card is doing, then a **meaning** strip: a few
keywords summarising how the result is (or is not) interpreted.

**Back** — press **flip**. A longer explanation with a worked example, then a
**Reading** section at the foot giving the interpretation in prose.

---

## Interpretation: what is claimed and what is not

The sheet follows one rule, and it is not "be generous":

> A system is read through its own interpretive tradition, or not at all.

Where a tradition exists, that is what you get. Where none exists, the card says
so plainly instead of borrowing a neighbour's meanings. **Nine of the twenty-two cards carry no reading at all**, and that is the honest answer for them.

| Card | Reading status |
|---|---|
| Simple digital root | The core 1–9 meanings, spelled out in full |
| Pythagorean | The source of those core meanings. Vowels = soul, consonants = personality |
| Chaldean | Same core meanings, plus 11 / 22 / 33 as masters |
| Word length | Structural: short names concentrated, long names diffuse |
| Affirmation triangle | Letter count only; no historical text prescribes the grid |
| Telephone keypad | No tradition. The matching claim is native; character reading is borrowed |
| **Lo Shu** | **Genuinely different** — read through feng shui, not the Western 1–9 |
| **Sephirotic** | **A real tradition** — the nine sephiroth named, Malkuth excluded as a path root |
| Gematria, Anglossic | Ciphers for totalling, not systems of meaning. Reduced figures fall into borrowed meanings, and the card says so |
| Morse, Modular | None. Not a received tradition; a residue cannot be read |
| Triangle of Life, 45-cell, both pyramids, 3-column, ternary | Layouts. Each says the number to interpret comes from the card it duplicates |
| Reverse addition | None, deliberately — it exists to prove order does not matter |

### The core meanings

The Western 1–9 set, used by every card that reduces a name to a single digit:

| | | | |
|---|---|---|---|
| 1 leadership, independence | 2 partnership, diplomacy | 3 expression, creativity | 4 diligence, foundation |
| 5 freedom, change | 6 care, family, responsibility | 7 insight, solitude | 8 power, mastery |
| 9 compassion, completion | **11** intuition, illumination | **22** the master builder | **33** the master teacher |

The masters are the near-completed forms of their own roots — 2, 4 and 6 —
carried before being reduced. The classical view stops at 33; some modern systems
add 44 and 55.

---

## Provenance and disputed claims

These were checked against sources rather than assumed, and the results are
recorded here because several are counter-intuitive.

**Confirmed.**

- The alphanumerical cipher was published by **S. L. MacGregor Mathers in
  *Kabbalah Unveiled*, 1887**; it was taught in the **Hermetic Order of the
  Golden Dawn**, and **Crowley** used it extensively for the A∴A∴ and the
  **O.T.O.**
- The numerological **3×3 grid is not attested in any Pythagorean text**. The
  attribution is a later esoteric one and should be read as one.
- The Pythagorean letter values match published charts exactly: 1 = A, J, S …
  9 = I, R.
- **No Babylonian text uses the Chaldean table** — the Latin alphabet postdates
  Chaldea. Cheiro popularised it in English in the early 1900s.
- Gematria as a *system* is post-Biblical, though the Hebrew Bible preserves
  wordplay of the same kind.

**The big correction.** Every published chart calls the **1–8 phonetic table**
"Chaldean": `1` = A I J Q Y · `2` = B K R · `3` = C G L S · `4` = D M T ·
`5` = E H N X · `6` = U V W · `7` = O Z · `8` = F P. Nine is withheld as sacred
and complete. The **1–9** variant (A–I = 1–9, then flat 2s and 3s) is the
minority one. This sheet uses the published table for its Chaldean card and
shows the variant only as a second row on the Modular card.

Charts legitimately differ over **X, Y and W**. This one follows the standard
Cheiro grouping, so **X = 5**. If your source places X at 6, it is a different
lineage, not an error.

**Softened.**

- Lo Shu squares also appear in Jewish and Hinduic material, but whether those
  arose independently of the Chinese is **disputed**, not established.
- Reduced gematria coincides with the published Chaldean table for **fifteen**
  of the twenty-six letters — A B C D E, then J K L M N, then R S T, then Y Z —
  first diverging at **F**. Chaldean is *not* derived from gematria; the
  agreement is coincidence.

---

## Customising

### Adding a view

1. Add an entry to `VIEW_CHOICES`:

   ```js
   { id: "myview", label: "my view", title: "what this is for" }
   ```

2. Add `"myview"` to the `views: [...]` array of every card you want in it. Each
   card lists its views explicitly, including `general`.

3. Add a friendly name in `GROUP_LABELS` if the id appears in a grouping mode
   too.

That is all. Group keys, pill counts and the stats line are computed over the
current view, so a pill can never name a card the view has removed.

### Writing a card

A card is one object in the `SYSTEMS` array:

```js
{
  id: "mycard",
  views: ["general", "name"],
  tag: "mytag",            // shown as the small badge in the header
  trad: "mytag",           // grouping: tradition
  num: "1-9",              // grouping: numeral
  wide: true,              // optional: spans the full row in fixed-column modes
  title: "My Card",
  note: "One line on what it does.",
  html: () => '…',         // the front; use data-n="name" hooks
  update(n, D) { … },      // receives the named nodes and the parsed input
  back: () => "…",         // the long explanation
  kw: ["lead", "keyword"], // optional: the meaning strip
  read: () => "…"          // optional: the Reading block
}
```

Add `kw` / `read` entries to the `MEANING` table (keyed by card `id`) rather than
inline, so all the interpretation stays readable in one block.

### Editing safely

The file is plain UTF-8 with LF line endings, one `<script>` block. Two things
worth knowing:

- **Do not rewrite it with PowerShell string redirection.** PowerShell
  double-encodes UTF-8 and mangles characters like `→` into `→`. Use a text
  editor, or Node's `fs.readFileSync/writeFileSync` with `'utf8'`.
- **Commit before bulk edits.** The interpretation content and the card
  definitions are large and a regex edit can match the wrong occurrence — which
  has happened twice, both times caught by re-reading the output rather than by
  the edit succeeding.

---

## Verification

Every card's headline figure has been checked against an **independent
implementation written from the stated definitions rather than copied from the
sheet**, across 15 inputs including empty, digits-only, punctuation-only,
accents, the full alphabet, and a 40-character run.

```
285 headline comparisons, 0 mismatches
```

Also verified:

- all 19 cards flip without throwing, in every view — 95 flips, zero faults, and
  exactly one face visible at a time in every state
- no console errors on any input
- in all 5 views × 4 grouping modes, the pill counts sum to the visible card
  count
- no card overflows at any of the six column settings, in any view
- every wrapped line in auto mode is filled to within **0 px** at nine widths
  from 520 px to 1700 px
- URL round-trips restore the exact view, including a `result`-mode selection
  whose group keys only exist after the first paint
- the address bar is rewritten without adding history entries

---

## Known limitations

Stated plainly rather than buried.

- **No birth-date cards.** There is no life path, destiny, personal year or
  birthday square. `birthday` view is three cards because digit reduction is the
  whole of what a life-path calculation does, not because the rest is modelled.
- **`by result` grouping is only as stable as the numbers.** Typing can create
  and destroy result groups. Keys that disappear keep their state so switching
  back is stable.
- **The Tree of Life point figure is not the reduction of the sum.** The folding
  is local, so the middle of a long row never reaches the point intact. This is
  a property of the construction, and the card says so.
- **Very long names are truncated in the diagrams.** The drawing caps at 26
  layers, and sooner for long names; the totals are always computed over every
  layer, drawn or not, and the card says how many were omitted.
- **Morse is invented here.** It is on the sheet because it separates names that
  other tables score alike. It should not be presented as a received system, and
  the card does not.
- **Nothing has been visually reviewed at every column count.** Layout is
  verified by measurement and by rendering, but the design has not been signed
  off across all six column settings by eye.

---

## Version history

```
55d6647  Add a view row that slices the sheet, and carry it in the URL
949916a  Auto layout with no dead space, URL state, all/none first, two renames
547c7a3  Add interpretation to every card, and fix three layout faults
fb6392a  Use the published Chaldean table as the main one; align X to 5
b6e5736  Correct factual errors in card explanations
5959370  Numerology digital-reduction cheatsheet
```

The commit messages record the reasoning and, where relevant, the bug that was
being fixed and why the previous behaviour was wrong. Read them before assuming
a number is a typo — most of them were not.

There is no remote configured; this repository is local backup only.



