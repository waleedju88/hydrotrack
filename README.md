# 💧 HydroTrack

A beautiful, mobile-first hydration tracking web app built with React and Supabase. Install it on your iPhone or iPad home screen for a native app experience with push notification reminders.

![HydroTrack](https://img.shields.io/badge/version-2.0-4fa3e8?style=flat-square) ![React](https://img.shields.io/badge/React-18-61dafb?style=flat-square&logo=react) ![Supabase](https://img.shields.io/badge/Supabase-database-3ecf8e?style=flat-square&logo=supabase) ![PWA](https://img.shields.io/badge/PWA-ready-5a0fc8?style=flat-square)

---

## ✨ Features

- **Animated water tracker** — beautiful wave-fill circle showing your daily progress
- **Quick log buttons** — preset cup sizes: 200, 230, 250, 330, 500, 600 ml
- **Custom amount entry** — log any amount not in the preset list
- **Push notifications** — reminders every 30 minutes between 8am and 9pm
- **Daily goal setting** — choose from presets or set your own custom goal
- **Dashboard & stats** — view your intake by day, week, month, and year
- **Delete entries** — remove any logged entry from today's history
- **Auth** — email/password and Google OAuth sign-in
- **PWA** — installable on iPhone and iPad, works like a native app
- **Fully responsive** — designed for iPhone and iPad screens

---

## 📱 Install on iPhone / iPad

1. Open the live site in **Safari**: `https://waleedju88.github.io/hydrotrack/`
2. Tap the **Share** button (box with arrow icon at the bottom)
3. Tap **Add to Home Screen**
4. Tap **Add**
5. Open the app from your home screen
6. Go to **Settings** tab → tap **Enable** under Notifications
7. Tap **Allow** when iOS prompts

> **Note:** Notifications require the app to be open or running in the background. For best results, always open the app from your home screen (PWA mode) rather than in the browser.

---

## 🗄️ Database Setup (Supabase)

Run this SQL in your Supabase project's **SQL Editor**:

```sql
-- Profiles table (stores daily goal)
create table if not exists profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  daily_goal_ml integer not null default 2000,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

-- Water logs table
create table if not exists water_logs (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  amount_ml integer not null,
  logged_at timestamptz not null default now(),
  created_at timestamptz default now()
);

-- Indexes
create index if not exists water_logs_user_date
  on water_logs (user_id, logged_at);

-- Row Level Security
alter table profiles enable row level security;
alter table water_logs enable row level security;

create policy "Users can read own profile" on profiles for select using (auth.uid() = id);
create policy "Users can insert own profile" on profiles for insert with check (auth.uid() = id);
create policy "Users can update own profile" on profiles for update using (auth.uid() = id);

create policy "Users can read own logs" on water_logs for select using (auth.uid() = user_id);
create policy "Users can insert own logs" on water_logs for insert with check (auth.uid() = user_id);
create policy "Users can delete own logs" on water_logs for delete using (auth.uid() = user_id);
```

---

## 🔐 Authentication Setup

### Email Auth
Enabled by default in Supabase. No extra configuration needed.

### Google OAuth
1. Go to [console.cloud.google.com](https://console.cloud.google.com) → **APIs & Services → Credentials**
2. Create an OAuth 2.0 Client ID (Web application)
3. Add to **Authorized redirect URIs**:
   ```
   https://otvvfifeifuirewagxia.supabase.co/auth/v1/callback
   ```
4. In Supabase → **Authentication → Providers → Google** → paste your Client ID and Secret

### Supabase URL Configuration
In Supabase → **Authentication → URL Configuration**:
- **Site URL:** `https://waleedju88.github.io/hydrotrack/`
- **Redirect URLs:** `https://waleedju88.github.io/hydrotrack/`

---

## 🚀 Deployment

This project deploys automatically to **GitHub Pages** using GitHub Actions.

Every push to the `main` branch triggers a build and deploy. No manual steps needed.

**Live URL:** `https://waleedju88.github.io/hydrotrack/`

### How it works
1. Push any change to `main`
2. GitHub Actions runs `.github/workflows/deploy.yml`
3. Vite builds the project into `dist/`
4. GitHub Pages serves the `dist/` folder
5. Site is live in ~90 seconds

---

## 🏗️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 + Vite |
| Styling | Pure CSS-in-JS (no libraries) |
| Backend / DB | Supabase (PostgreSQL) |
| Auth | Supabase Auth (Email + Google) |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions |
| PWA | Web App Manifest + Service Worker |

---

## 📁 Project Structure

```
hydrotrack/
├── .github/
│   └── workflows/
│       └── deploy.yml        # Auto-deploy to GitHub Pages
├── public/
│   ├── icons/
│   │   ├── icon-192.png      # PWA icon
│   │   └── icon-512.png      # PWA icon
│   ├── drop.svg              # Favicon
│   ├── manifest.json         # PWA manifest
│   └── sw.js                 # Service worker
├── src/
│   ├── App.jsx               # Entire application
│   └── main.jsx              # React entry point
├── .gitignore
├── index.html                # HTML entry point
├── package.json
└── vite.config.js
```

---

## 🛠️ Local Development

If you want to run the project locally:

```bash
# Clone the repo
git clone https://github.com/waleedju88/hydrotrack.git
cd hydrotrack

# Install dependencies
npm install

# Start dev server
npm run dev
```

Then open `http://localhost:5173/hydrotrack/` in your browser.

---

## 📊 Dashboard

The Stats tab shows your water intake across four time ranges:

| Range | What it shows |
|-------|--------------|
| Day | Today's total in liters |
| Week | Last 7 days bar chart |
| Month | Last 4 weeks bar chart |
| Year | All 12 months bar chart |

Bars turn **green** on days you hit your goal, **blue** on days you didn't.

---

## 🔔 Notifications

Push notifications fire every 30 minutes between **8:00 AM and 9:00 PM**, but only if you haven't reached your daily goal yet. Once you hit 100%, reminders stop for the day.

To enable:
1. Open app from home screen (PWA mode)
2. Go to **Settings → Enable Notifications**
3. Tap **Allow** on the iOS permission prompt

> On iPhone, notifications only work when the app is installed as a PWA (Add to Home Screen). They do not work in the regular Safari browser tab.

---

## 📝 License

Personal project by [@waleedju88](https://github.com/waleedju88). Feel free to fork and adapt for your own use.
