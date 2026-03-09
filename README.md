# 📊 Obsidian Project Roadmap

A dynamic, metadata-driven project dashboard for [Obsidian](https://obsidian.md) using [Dataview](https://github.com/blacksmithgu/obsidian-dataview).

Track features, budget, timelines, and work pace — all auto-calculated from YAML frontmatter in your feature spec notes.

## ✨ Features

- **Live budget tracking** — Spent vs Total auto-calculated from feature specs
- **Dynamic daily targets** — Interpolates between monthly milestones based on today's date
- **Personal progress tracker** — Adjusts for sick days, vacation, and part-time schedules
- **Work pace calculator** — Shows hours completed, required pace, and days remaining
- **Phase completion** — Group features by phase with per-phase budget rollups
- **Status sorting** — Done → In Progress → Not Started, automatically
- **Multi-owner support** — Track work across team members
- **Zero maintenance** — Change `status:` in any spec and the dashboard updates

## 🚀 Quick Start

1. **Install prerequisites**
   - [Obsidian](https://obsidian.md) (free)
   - [Dataview plugin](https://github.com/blacksmithgu/obsidian-dataview) (enable JavaScript queries)

2. **Clone or download this vault**
   ```bash
   git clone https://github.com/MartinJuhler/obsidian-project-roadmap.git
   ```

3. **Open as an Obsidian vault**
   - Open Obsidian → "Open folder as vault" → select the cloned folder

4. **Enable Dataview**
   - Settings → Community Plugins → Browse → Install "Dataview"
   - In Dataview settings, enable **"Enable JavaScript Queries"**

5. **Start customizing**
   - Edit the feature specs in `Project Roadmap/03 - Feature Specifications/`
   - Update budget, timeline, and team in `Project Roadmap/Project Dashboard.md`
   - Add your own phases and categories

## 📁 Vault Structure

```
📂 Project Roadmap/
├── 📄 Project Dashboard.md          ← The live dashboard
├── 📂 03 - Feature Specifications/
│   ├── 📂 Frontend/
│   │   ├── 📄 User Authentication.md
│   │   ├── 📄 Dashboard UI.md
│   │   └── 📄 Search & Filtering.md
│   ├── 📂 Backend/
│   │   ├── 📄 API Gateway.md
│   │   └── 📄 Database Migration.md
│   └── 📂 Integrations/
│       └── 📄 Payment Provider.md
├── 📂 04 - Operations/
│   ├── 📄 Production Deploy.md
│   └── 📄 User Testing.md
└── 📂 _templates/
    └── 📄 Feature Spec Template.md
```

## 📝 Feature Spec Format

Each feature is a markdown note with YAML frontmatter:

```yaml
---
type: feature
phase: Frontend          # Must match folder name
category: Authentication  # Grouping label
status: Done              # Done | In Progress | Not Started
owner: Your Name
hours: 8                  # Estimated hours
cost_nok: 8000            # Budget in your currency
planned: true             # false for bonus/unplanned features
codebase_status: Complete  # Complete | Partial | Not Started
---
```

## ⚙️ Customization

### Change currency
Replace `NOK` with your currency in `Project Dashboard.md`. The budget fields are just numbers — label them however you want.

### Change timeline
Edit the `milestones` array in the DataviewJS blocks:
```javascript
const milestones = [
  [0, 0], [1, 10], [2, 20], [3, 30], ...
];
```

### Add sick/vacation days
Edit `const sickDays = 10;` in the "My Adjusted Progress" block.

### Change phases
1. Create a new folder under `03 - Feature Specifications/`
2. Set `phase:` in your specs to match the folder name
3. Add a new Dataview section in the dashboard

### Change team members
Edit the `otherOwners` array in the Work Pace Tracker:
```javascript
const otherOwners = ["Alice", "Bob", "Charlie"];
```

## 🧠 How It Works

1. **Dataview** scans all `.md` files with `type: feature` frontmatter
2. **DQL queries** build the feature tables
3. **DataviewJS** powers the dynamic calculations:
   - Budget % = sum of done features' `cost_nok` / total
   - Target % = interpolated from monthly milestones based on `Date.now()`
   - Work pace = completed hours / productive days used

No external services, no databases, no build steps. Just markdown files.

## 📄 License

MIT — use it, fork it, adapt it.

## 🙏 Credits

Built by [Martin Juhler](https://linkedin.com/in/martinjuhler) for the DocMatrix project at AKVA Group.
