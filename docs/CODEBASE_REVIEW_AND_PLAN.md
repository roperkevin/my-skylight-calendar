# Codebase Review & Improvement Plan

A thorough review of the `my-skylight-calendar` project (Home Assistant DIY Skylight
calendar clone), followed by a phased plan to make it excellent. Reviewed files:
`README.md`, `dashboard.yaml`, `packages/family_calendar.yaml`, `themes/skylight.yaml`,
repo layout and assets.

---

## Part 1 — Review Findings

### 1.1 Bugs / correctness issues

#### B1. JavaScript syntax error hidden in a YAML block scalar (high priority)
`dashboard.yaml` line ~257, inside the `STARTDAY` template:

```yaml
STARTDAY: |
  (() => {
    ...
    return 'monday'; # <--- UPDATE THIS IF NEEDED, valid values are: monday, ...
  })()
```

Because this is a YAML **literal block scalar** (`|`), the `# <--- UPDATE THIS ...`
text is *not* a YAML comment — it becomes part of the JavaScript string that
`config-template-card` evaluates. `#` is not a comment marker in JavaScript, so the
evaluated code contains an invalid token. Fix: use a JS comment (`// <--- UPDATE ...`)
on its own line, or move the note to a YAML comment outside the block.

#### B2. Invalid CSS color `light-grey` (7 occurrences)
The bubble-card button styles use:

```js
background-color: ${... === '.*' ? 'light-grey' : '...'} !important;
```

`light-grey` is not a valid CSS color keyword (valid: `lightgrey` / `lightgray`), so
the "inactive" state silently falls back to no background. Fix all occurrences
(calendar1–4, Family, Birthdays, Holidays buttons).

#### B3. Filter semantics are documented backwards
`packages/family_calendar.yaml` says:

```yaml
# Filter Toggles (RegEx: .* = Show, ^$ = Hide)
```

and the README repeats "`.*` (Show everything) and `^$` (Show nothing)". But
`week-planner-card`'s `filter` option *hides* events whose title matches the regex —
so `.*` hides everything and `^$` effectively shows everything. The button styling in
`dashboard.yaml` (grey when `.*`, colored when `^$`) is only coherent under the
hide-matching interpretation. Action: verify against the installed
`week-planner-card` version, then correct the comments in the package, the README
"How It Works" section, and consider renaming helper values to something
self-explanatory (see M2).

#### B4. `input_boolean.family_calendar_show` is a dead/misleading helper
The Family bubble button uses `entity: input_boolean.family_calendar_show` with
`button_type: switch`, but the tap action runs `script.family_calendar_visible_filter`
— the boolean is never toggled, so its displayed switch state never reflects reality.
Either wire the script to also toggle the boolean, or (simpler) drop the boolean and
make the Family button a `name`-type button like Birthdays/Holidays.

#### B5. "Tomorrow" view shows two days
In the `DAYS` template, `Tomorrow` returns `2` while `STARTDAY` returns `'tomorrow'` —
the result is tomorrow *plus the day after*. If the intent is a single-day view of
tomorrow (Skylight-style), `DAYS` should return `1`.

#### B6. Grid CSS hardcodes 7 columns regardless of view
The card-mod override forces `grid-template-columns: repeat(7, minmax(0, 1fr))`. That
is right for Week/Biweek/Month, but the Today (1 day) and Tomorrow views squeeze into
1/7th of the card width. Make the column count follow the selected view (e.g. inject
it via `config-template-card` the same way `DAYS` is injected, or scope the 7-column
rule to the multi-day views).

#### B7. Weekend header coloring assumes a Monday start
The Sat/Sun red coloring relies on `:nth-of-type(7)` / `:nth-of-type(8)`:

```css
.container > .day.header:nth-of-type(7) .text { color: #d32f2f !important; }
```

If a user changes `startingDay` (which the file explicitly invites them to do), or in
Today/Tomorrow views, the wrong columns turn red. Prefer `data-weekday` attribute
selectors for the header row too, or document the limitation next to the
`startingDay` option.

#### B8. Add-event script has no validation or error handling
`script.add_google_calendar_event`:

- Creates events with an empty title if the user taps "Add" without typing one
  (HA rejects the call and the failure is silent — the popup just closes).
- Does not guard against `end <= start` (for all-day events HA requires
  `end_date` strictly after `start_date` — the classic "one-day event fails"
  gotcha, since HA's end date is exclusive).
- Cleanup resets title/description but not the All-Day toggle or the four
  datetime helpers, so stale values greet the next use of the popup.

Fix: add a guard step (e.g. `condition` template requiring a non-empty title and
`end > start`, with a `browser_mod.notification` explaining what's wrong), auto-bump
the all-day end date by one day when it equals the start date, and reset all form
helpers on success.

#### B9. Misleading script name
`add_google_calendar_event` works for any calendar entity (local or Google). Rename to
`add_calendar_event` (keep an alias or note in the README for existing users).

### 1.2 Maintainability / structure

#### M1. Seven copy-pasted toggle scripts
`family_calendar.yaml` defines seven near-identical `*_visible_filter` scripts that
differ only in the target entity. Replace with **one** parameterized script using
`fields:`:

```yaml
script:
  toggle_calendar_filter:
    fields:
      filter_entity:
        description: input_text filter helper to toggle
    sequence:
      - action: input_text.set_value
        target:
          entity_id: "{{ filter_entity }}"
        data:
          value: "{% if is_state(filter_entity, '.*') %}^${% else %}.*{% endif %}"
```

Buttons then pass `data: { filter_entity: input_text.calendar1_calendar_filter }`.
This cuts ~60 lines and makes adding a family member a one-line change.

#### M2. Magic regex values leak everywhere
`.*` / `^$` appear in the package, the dashboard button styles, and the docs. Consider
documenting them once as named concepts ("HIDDEN" / "VISIBLE") in a single comment
block, and reference that from everywhere else — or at minimum make all comments
agree after resolving B3.

#### M3. Hardcoded hex colors duplicate theme variables
The Family/Birthdays/Holidays buttons hardcode `#4A90E2`, `#33a02c`, `#ff7f00` even
though `themes/skylight.yaml` defines `family-default-primary-color` etc. Use
`var(--family-default-primary-color)` in the dashboard so colors are changed in one
place (the theme), as the theme file's own comments promise.

#### M4. Unnecessary `button-card` dependency
`button-card` is used exactly once (the popup's "Add Event to Calendar" button).
Rebuilding that one button with `bubble-card` (already required) removes an entire
HACS prerequisite from the install list.

#### M5. Repo layout & asset hygiene
- `calbackgrd.png` (912 KB) sits at the repo root; move to `assets/` (installation
  docs point at `/www/` on the HA box anyway) and optimize it — a JPEG/WebP or
  quantized PNG of a soft-gradient background should be well under 200 KB.
- `assets/main_view.jpeg` (236 KB) can also be losslessly optimized.
- No `LICENSE` file — add one (MIT is typical for HA community configs) so people
  can legally reuse/fork.
- `.gitignore` only lists `custom_dashboard.yaml`; add editor/OS noise
  (`.DS_Store`, `*.swp`, etc.).

#### M6. No validation / CI
There is no automated check that the YAML files parse. Add a tiny GitHub Actions
workflow running `yamllint` (relaxed config) on push/PR — it would catch a whole
class of "helped-someone-break-the-file" regressions in community PRs, which this
repo receives.

#### M7. Version requirements buried in comments
`showWeekDayText: false # <--- Requires week-planner-card v1.13.0 or higher` is the
only place a minimum card version is stated. Add a "Requirements" table to the README
listing each HACS card with the minimum tested version.

### 1.3 Documentation (README)

- Typos: "Sklylight" (image alt), "Addning"/"havent"/"youll" (visible in history and
  notes), stray `~~` at the end of the Computer bullet, "you case might be different".
- The language-selector block's last link (line 27) is truncated mid-tag
  (`অসমীয়া</` — unclosed), which can break rendering of the collapsed details block.
- Numbered steps jump 3 → 5 in "The Dashboard" section; heading style switches from
  `### 4. The Dashboard` to `### Step 5: The Theme`.
- The Browser-Mod note opens with `*Note:` but never closes the italic marker.
- "How It Works" repeats the inverted filter description (see B3).
- Missing sections that would help newcomers a lot: **Troubleshooting/FAQ**
  (popup doesn't open → browser_mod integration not added; calendar empty → filter
  semantics; buttons grey → entity IDs), a **screenshot of the Add Event popup**, and
  an explicit **file map** ("what each file is and where it goes on your HA box").

### 1.4 UX polish opportunities

- Clock is hardcoded 24h (`HH:mm`) and `locale: en` — surface both as clearly marked
  options (12h `h:mm a` variant in a comment).
- The popup form doesn't visually confirm success — a short
  `browser_mod.notification` ("Event added to Emma's calendar ✓") closes the loop.
- Add-event start/end datetimes could default to "next full hour / +1 hour" via a
  small automation triggered when the popup opens, instead of stale values.
- The theme is intentionally minimal ("not comprehensive") — extending it with
  background, card radius, and header color variables would make the whole look
  retheme-able from one file, including a dark/evening variant.

---

## Part 2 — The Plan (phased)

### Phase 1 — Correctness (small PRs, immediate value) ✅ DONE
1. ✅ Fix B1 (JS `#` comment → `//`), B2 (`light-grey` → `lightgrey`).
2. ✅ Resolve B3: verified against the `week-planner-card` docs — `filter` is
   documented as "Remove events that match the regular expression", i.e. it hides
   matches. Comments and README corrected, and the `input_text` `initial` values
   changed from `.*` to `^$` so calendars are visible on a fresh install.
3. ✅ Fix B4 (removed `family_calendar_show`; Family button is now a `name`-type
   button), B5 (Tomorrow = 1 day).
4. ✅ B8: added title/date validation, all-day end-date auto-bump, full form reset
   (including the All-Day toggle), and success/failure notifications to the
   add-event script.

### Phase 2 — Simplification & structure ✅ DONE
5. ✅ M1: collapsed the seven toggle scripts into one parameterized
   `script.toggle_calendar_filter`; all seven buttons pass their `filter_entity`.
6. ✅ M3: buttons now use theme variables instead of hardcoded hexes;
   M4: `button-card` dependency dropped (popup button rebuilt with bubble-card).
7. ✅ B9: script renamed to `add_calendar_event` (rename noted in the README's
   upgrade section).
8. ✅ M5: background image moved to `assets/` and converted to WebP
   (932 KB → 16 KB); added MIT `LICENSE`; expanded `.gitignore`.

### Phase 3 — Docs & guardrails ✅ DONE (except screenshot)
9. ✅ README overhaul: typos/markup/numbering fixed, Requirements table with links
   and minimum versions (M7), Troubleshooting/FAQ, file map ("What's in this
   repo"), and a Customizing section (colors via theme, week start, 12/24h clock,
   locale, adding/removing family members) — with the B6/B7 caveats documented as
   comments next to the options in `dashboard.yaml`.
   ⏸ Popup screenshot still pending — requires a live Home Assistant install to
   capture.
10. ✅ M6: added a `yamllint` GitHub Action (`.github/workflows/validate.yaml`)
    with a relaxed `.yamllint` config plus a YAML parse check; missing
    end-of-file newlines fixed. (Markdown link checker left as a possible
    follow-up.)

### Phase 4 — "Super duper awesome" features ✅ DONE
11. ✅ **Chores / To-do panel** — a Chores button opens a `#chores` popup with one
    `todo-list` card per person (HA's built-in Local To-do; zero extra installs).
12. ✅ **View-aware layout (B6/B7 done right)** — new `COLS` and `WEEKEND_CSS`
    `config-template-card` variables drive the grid column count (1 for
    Today/Tomorrow, 7 otherwise) and compute the weekend header cells from the
    starting day, so every view renders correctly at any `startingDay`.
13. ✅ **Meal-planner row** — an opt-in (commented-out) compact `Dinner` calendar
    strip under the header; documented in the README.
14. ✅ **Photo-frame screensaver** — `script.screensaver_start` opens a fullscreen
    browser_mod photo popup with `autoclose` (tap/move to wake), plus an example
    nightly automation. True idle detection is documented as a kiosk-software
    concern (Fully Kiosk).
15. ✅ **Dark / evening theme variant** — `Skylight Dark` added to the theme file;
    the dashboard's card-mod colors now read `--skylight-*` theme variables (with
    the original values as fallbacks), and an example sun-based
    `frontend.set_theme` automation is included in the package.
16. ✅ **Per-person "today" agenda popup** — holding a person's button opens their
    `#agendaN` popup with a compact one-day week-planner-card.

Each phase is independently shippable; Phases 1–2 change no user-facing setup steps
(except the script rename, which is called out), so existing installs upgrade by
re-pasting two files.
