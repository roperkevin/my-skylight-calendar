
<div align="right">
  <details>
    <summary >🌐 Language</summary>
    <div>
      <div align="center">
        <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=en">English</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=zh-CN">简体中文</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=zh-TW">繁體中文</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ja">日本語</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ko">한국어</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=hi">हिन्दी</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=th">ไทย</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=fr">Français</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=de">Deutsch</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=es">Español</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=it">Italiano</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ru">Русский</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=pt">Português</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=nl">Nederlands</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=pl">Polski</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ar">العربية</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=fa">فارسی</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=tr">Türkçe</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=vi">Tiếng Việt</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=id">Bahasa Indonesia</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=as">অসমীয়া</a>
      </div>
    </div>
  </details>
</div>

# DIY Smart Home Family Calendar (Skylight Clone)

![Skylight calendar](assets/main_view.jpeg)
![DIY Skylight](assets/sky2.png)

## 📖 Introduction

My wife has been recently bombarded in social media with ads for smart home calendars (Skylight, Cozyla, Hearth) and was ready to spend over $300 on one. Before giving her the green light, I asked for a chance to research them.

I realized most offered similar functionality but differed significantly in price. Most importantly, I didn't see any outstanding feature that I couldn't implement in **Home Assistant**.

**The Goal:** A WAF-approved (Wife Acceptance Factor), countertop-friendly touchscreen calendar that integrates deep into our smart home without monthly fees.

## 💡 Why DIY?

Choosing the DIY route with Home Assistant provided several benefits over buying a Skylight/Hearth display:

* **No Monthly Fees:** Avoids subscriptions for "premium" features.
* **Seamless Integration:** It talks to our lights, chores (Grocy), and presence sensors.
* **Old Hardware:** Repurposed a Mini PC and a standard monitor.
* **Privacy:** No vendor lock-in or risk of the company shutting down.

## 🛠 Hardware Selection

This is currently built to show the dashboard on any HD (1920x1080) display.

In my case, the requirement was for it to "look like" skylight, be touchscreen, be countertop, possibility to move it to different locations. Therefore I went with the hardware described below.
Nevertheless, your case might be different and may need you to adjust it, for example if you want to display it on a tablet or something else.

The hardware I originally used I chose based on what I mentioned above plus with the hope to be able to extend functionality using the webcam, speaker and microphone. Currently I would probably build it differently now in hindsight, since I havent had time to address these additional hardware ideas.

* **Monitor:** [HP Engage 15-inch Touchscreen](https://computers.woot.com/offers/hp-engage-16t-fhd-monitor). I chose this over generic portable monitors because it includes a built-in **Speaker, Webcam, and Microphone**, allowing for future voice control or video calls.
* **Computer:** An old Mini PC (NUC/Tiny PC) running Windows/Linux in Kiosk mode, or a Raspberry Pi 4.


## ✨ Features

* **Family-wide & Individual Views:** Toggle specific family members' calendars on/off.
* **Two-way Sync:** Edit events on the screen or on our phones (Google Calendar).
* **"Add Event" Popup:** A custom UI to add events to specific calendars directly from the screen.
* **Weather & Date:** Beautiful, glanceable header.
* **Responsive:** Automatically adjusts day-count based on screen width (Mobile vs Desktop).
* **Chores:** A popup with a per-person to-do list, powered by HA's built-in Local To-do.
* **Per-person Agenda:** Hold a person's button to pop up their day at a glance.
* **Dark Mode:** A `Skylight Dark` theme variant, optionally auto-switched with the sun.
* **Photo Screensaver:** A tap-to-dismiss fullscreen photo, triggerable on a schedule.
* **Meal Planner (opt-in):** A Skylight-style dinner strip under the header.

## 📁 What's in this repo

| File | What it is | Where it goes on your HA box |
|---|---|---|
| [`dashboard.yaml`](dashboard.yaml) | The dashboard layout (header, buttons, calendar, popup) | Pasted into a new dashboard's raw configuration editor |
| [`packages/family_calendar.yaml`](packages/family_calendar.yaml) | All helpers and scripts the dashboard needs | `/config/packages/` |
| [`themes/skylight.yaml`](themes/skylight.yaml) | The Skylight theme (font + per-person colors) | `/config/themes/` |
| [`assets/calbackgrd.webp`](assets/calbackgrd.webp) | The dashboard background image | `/config/www/` (served as `/local/calbackgrd.webp`) |
| `assets/` (other files) | README screenshots | Nowhere — documentation only |

---

## ⚙️ Installation Guide

*Note: This setup uses a **YAML Package** to automatically create all the necessary helpers, scripts, and variables for you. You do not need to create them manually.*

### 1. Prerequisites (HACS)

You must have [HACS](https://hacs.xyz/) installed. Please install the following integrations:

| HACS integration | Type | Minimum version | Used for |
|---|---|---|---|
| [`week-planner-card`](https://github.com/FamousWolf/week-planner-card) | Frontend | 1.13.0 (`showWeekDayText`) | The main calendar grid |
| [`bubble-card`](https://github.com/Clooos/Bubble-Card) | Frontend | latest | Toggle buttons, view selector, Add Event popup |
| [`config-template-card`](https://github.com/iantrich/config-template-card) | Frontend | latest | Injects the per-person filters and view settings into the calendar |
| [`card-mod`](https://github.com/thomasloven/lovelace-card-mod) | Frontend | latest | All the custom styling |
| [`better-moment-card`](https://github.com/power-widgets/better-moment-card) | Frontend | latest | The date/time header |
| [`weather-card`](https://github.com/bramkragten/weather-card) | Frontend | latest | The current-weather header |
| [`layout-card`](https://github.com/thomasloven/lovelace-layout-card) | Frontend | latest | The Sections view layout |
| [`browser_mod`](https://github.com/thomasloven/hass-browser_mod) | Integration | 2.x | Popups and on-screen notifications |

*Note: In Settings → Devices & Services, make sure Browser Mod appears as an Integration (tile) and not only under HACS.
If it isn't there, click Add Integration → Browser Mod and finish the flow, then restart HA.
Installing via HACS only downloads files; you must add the integration so HA registers its actions/entities.*

### 2. The Backend (The Brains)

1. Open your `configuration.yaml` file in Home Assistant.
2. Ensure you have this line added under `homeassistant:` to enable packages:

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

3. Create a folder named `packages` in your HA config directory (if you don't have one).
4. Download [packages/family_calendar.yaml](packages/family_calendar.yaml) from this repo. It is pre-configured for the calendars `Family`, `School`, `Daniel`, and `Weather` (see section 3). If your calendar entity IDs differ, update the `calendar_map` inside `script.add_calendar_event`.
5. Place the file inside your `packages/` folder.
6. **Restart Home Assistant**.

### 3. The Calendars

You can use **Google Calendars** or **Local Calendars**.

#### Option A: Reuse Calendar Names (Easiest)


1. Go to **Settings > Devices & Services**.
2. Add the **Local Calendar** integration.
3. Create calendars named exactly: `Family`, `School`, `Daniel`, `Weather`, `Birthdays`.
    * *These create the entities `calendar.family`, `calendar.school`, `calendar.daniel`, `calendar.weather`, `calendar.birthdays`, which is what the code expects — it works out of the box!*
    * *`Holidays` comes from the Holiday integration (see "Setting up Holidays" below).*

#### Option B: Custom Calendar

1. Go to **Settings > Devices & Services**.
2. Add the **Local Calendar** integration. or **Google Calendar**.
3. Navigate to **Configuration > Integrations > Local Calendar** or **Google Calendar** and select "Add Entry"
4. For each created entry, get the entity ID for updating the dashboard.yaml file.
5. Open `dashboard.yaml` and update the entity IDs in the `calendars:` section of the week-planner card and in the agenda popups (currently `calendar.school`, `calendar.daniel`, `calendar.weather`, `calendar.family`, `calendar.birthdays`, `calendar.holidays`).
6. Do the same for the `calendar_map` in `packages/family_calendar.yaml`.
7. The weather cards use `weather.home` — search `dashboard.yaml` for `# <--- UPDATE THIS ENTITY` to find it and the remaining entities to match your environment.


#### Setting up Holidays

Since Home Assistant updates, Holidays are now added via UI:

1. Go to **Settings > Devices & Services > Add Integration > Holiday**.
2. Select your country.
3. Check the entity ID (e.g., `calendar.holidays`). If it differs from the default, update it in the dashboard YAML.

### 4. The Dashboard (The Look)

1. Go to **Settings > Dashboard**
2. Click on **Add Dashboard** (Select option "New Dashboard from Scratch" make sure to select "Add to sidebar").
3. On the left menu, select the new created dashboard and click on the pencil icon to edit it.
4. Select the 3 dots icon and select "Raw configuration editor".
5. Copy and paste the code from [dashboard.yaml](dashboard.yaml).

### 5. The Theme (Optional)

To get the specific font look (Ovo):

1. Ensure your `configuration.yaml` has this line under `frontend:`

   ```yaml
   frontend:
     themes: !include_dir_merge_named themes
   ```

2. Create a folder named `themes` in your config directory.
3. Download [themes/skylight.yaml](themes/skylight.yaml) and place it in that folder.
4. Use File Editor and upload [assets/calbackgrd.webp](assets/calbackgrd.webp) to /www/ folder, that translates internally to /local on the dashboard.
5. Restart Home Assistant.
6. Go to your Profile (User Icon bottom left) and change **Theme** to `Skylight`.
NOTE: The theme is not comprehensive, so keep that in mind

---

## 📐 How It Works (Under the Hood)

### Filter Logic

The `week-planner-card` does not natively support hiding specific calendars on the fly. To solve this, I used **Input Texts** acting as Regex filters.

* The card's `filter` option **hides** events whose title matches the regex.
* When you click a person's button, it toggles their filter between `^$` (matches nothing, so all events are **shown**) and `.*` (matches everything, so all events are **hidden**).
* `config-template-card` injects these variables into the calendar card dynamically.

### Event Creation Script

The "Add Event" popup uses a single script that handles logic for multiple people and event types (All Day vs Timed).

The script validates the form before creating anything: it refuses an empty title, a timed event whose end is not after its start, and an all-day event whose end date is before its start date (showing a Browser Mod notification instead), automatically extends a same-day all-day event to the next day (Home Assistant treats the end date as exclusive), and resets the form after a successful add.

## 🎨 Customizing

* **Per-person colors** — edit the `*-default-primary-color` variables in [`themes/skylight.yaml`](themes/skylight.yaml). Both the buttons and the calendar events read them, so one edit changes everything. Reload themes afterwards (Developer tools → Actions → `frontend.reload_themes`).
* **Week start day** — in `dashboard.yaml`, change the fallback in the `STARTDAY` template (marked with `UPDATE THIS IF NEEDED`). *Caveat:* the red weekend-header coloring in the `card_mod` styles assumes a Monday start (it targets the 7th/8th header cells); adjust those rules if you move the week start.
* **12-hour clock** — in the `better-moment-card` at the top of `dashboard.yaml`, change `{{moment format=HH:mm}}` to `{{moment format=h:mm a}}`.
* **Language** — change `locale: en` in the `week-planner-card` section of `dashboard.yaml`.
* **Fewer/more family members** — remove or copy a button in `dashboard.yaml` (each passes its own `filter_entity`) and the matching `input_text` helper + calendar entry. The shared `script.toggle_calendar_filter` needs no changes.

## 🧩 Optional Features

### Chores (per-person to-do lists)

1. Go to **Settings > Devices & Services > Add Integration > Local To-do** and create your lists — the dashboard is pre-wired for `Daniel` and `Family` (entities `todo.daniel` and `todo.family`).
2. If you used other names or want more lists, update the `todo.*` entities in the Chores popup in `dashboard.yaml` (marked `UPDATE THIS ENTITY`).
3. Tap the **Chores** button on the dashboard. Kids can check items off right on the screen.
4. Not interested? Delete the Chores button and the `#chores` popup from `dashboard.yaml`.

### Per-person "Today" agenda

**Hold** (long-press) a calendar's button to pop up its agenda for today. The popups (`#agenda-school`, `#agenda-daniel`, `#agenda-weather`) use the same calendar entities as the main view.

### Meal planner strip

A commented-out section in `dashboard.yaml` (search for `MEAL PLANNER STRIP`) renders a compact week of dinner plans under the header. Create a Local Calendar named `Dinner`, uncomment the section, and add meals as all-day events.

### Photo screensaver

`script.screensaver_start` (from the package) opens a fullscreen photo on every registered browser; any tap or mouse movement dismisses it. Upload a photo to `/config/www/photos/` and update the path in the script. An example automation at the bottom of the package file triggers it nightly — for true idle-detection screensavers, kiosk software like Fully Kiosk Browser is the better tool.

### Dark mode

The theme file now also defines **`Skylight Dark`** — same layout and per-person colors on dark surfaces. Select it in your profile, or uncomment the "Skylight Auto Dark Mode" example automation in the package file to switch with the sun (requires the profile theme to be "Backend-selected").

## 🛠 Troubleshooting / FAQ

* **The Add Event popup doesn't open** — Browser Mod is installed via HACS but not added as an integration. See the note in the Prerequisites section, then restart HA.
* **The calendar shows no events at all** — check the filter helpers (Developer tools → States, search `_calendar_filter`). A value of `.*` means that calendar is *hidden*; `^$` means visible. Tapping a person's button toggles between the two.
* **The buttons don't color when tapped** — the `input_text.*_calendar_filter` helpers don't exist, which usually means the package file isn't loaded. Check the packages setup in section 2 and restart HA.
* **Events I add on screen don't appear in Google Calendar** — the `calendar_map` inside `script.add_calendar_event` (in the package file) points at entities that don't exist in your install (it expects `calendar.school`, `calendar.daniel`, `calendar.weather`, `calendar.family`). Update the mapping to your real calendar entities.
* **The background is missing** — `calbackgrd.webp` wasn't uploaded to `/config/www/`, or the dashboard's `background.image` path doesn't match the uploaded filename.
* **Everything is the wrong font** — the theme isn't applied. Set your profile's theme to `Skylight` (per browser/user), and make sure `themes: !include_dir_merge_named themes` is in `configuration.yaml`.

```yaml
# Simplified Logic Example
target_calendar: "{{ calendar_map.get(states('input_select.calendar_select')) }}"

choose:
  - conditions: "All Day Event is ON"
    action: calendar.create_event (start_date, end_date)
  - conditions: "All Day Event is OFF"
    action: calendar.create_event (start_date_time, end_date_time)
```

## NOTES

### Upgrading from an earlier version

If you installed a previous version of this project, note these renames (update any automations or dashboards of your own that referenced them):

* The seven `*_calendar_visible_filter` scripts were consolidated into a single `script.toggle_calendar_filter` that takes a `filter_entity` parameter (the dashboard buttons already pass it).
* `script.add_google_calendar_event` was renamed to `script.add_calendar_event` (it always worked with any calendar type, not just Google).
* The `button-card` HACS dependency is no longer needed.
* The background image is now `calbackgrd.webp` (much smaller download); re-upload it to `/www/` or keep your existing `.png` and point the dashboard `background.image` at it.

My original post was just to give a high level overview of how to do it and allow people to adjust code to make it work in their specific scenarios.

In particular I did this because every display and need is different. I can't develop for all potential sizes of displays, dashboards, etc. So it is built to work in the display I mentioned or any (1920x1080) but should be editable for others.

Talking about display, I originally suggested that one because it was on sale at Woot and was a very economic way to get a touchscreen display at the time. This might not be the case now, so use whatever display works for you. Tablet, touchscreen, phone, whatever. The main thing youll need to edit is the dashboard.

