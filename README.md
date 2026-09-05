# Paper Route

A flappy-bird run in a riso-print sky. The headwind builds from the first
second — but every registration mark you catch stops the press and lets you
**rewrite the level in your favour**.

One HTML file. No build step, no assets, no libraries.

- **Play now:** open `web/index.html` in any browser, or use the published
  artifact link (works on a phone).
- **On a phone:** open the link and use *Add to Home Screen* — it launches
  full-screen with its own icon.
- **As an APK:** see [Building the APK](#building-the-apk).

## How it plays

Tap anywhere to flap (space bar on desktop). Plates scroll at you; fly through
the gaps. Every fourth plate carries a printer's **registration mark** — touch
it and the press stops so you can pick one of three upgrades.

### Difficulty

Difficulty is purely a function of **time survived**, ramping from the first
second and easing off near the top:

```
d = 1 − (1 − min(t / 95, 1))²
```

`d` drives four things at once, so a long run gets harder on every axis:

| Level value      | at `d = 0` | at `d = 1` |
| ---------------- | ---------- | ---------- |
| Gap height       | 31.5% of screen | 20% of screen |
| Press speed      | 175 px/s   | 295 px/s   |
| Space between plates | 340 px | 270 px     |
| Gap drift        | ±10% of screen | ±30% of screen |

The HUD's `HEADWIND` meter is `d`.

### Upgrades

Each pick multiplies into the level values above, so upgrades push directly
back against the clock. Ten of them, each stackable to a cap:

| Upgrade | Effect | Cap |
| --- | --- | --- |
| Wider Skies   | Gap +13%                     | 5 |
| Slow Rollers  | Press speed −10%             | 4 |
| Light Stock   | Gravity −11%, softer flap    | 4 |
| Long Runway   | Space between plates +14%    | 4 |
| Steady Hand   | Gap drift −28%               | 3 |
| Trim Size     | Hitbox −15%                  | 3 |
| Second Sheet  | Survive one crash            | 3 |
| Ink Magnet    | Pull marks in from far off   | 2 |
| Floor Updraft | Half gravity near the deck   | 2 |
| Double Print  | +1 impression per plate      | 3 |

Nine make the level easier; Double Print is the greedy pick — it scores faster
but leaves the headwind unopposed.

## Layout

```
web/index.html      the whole game — the only file that matters
android/            WebView shell that wraps that same file into an APK
.github/workflows/  CI that builds the APK
```

The Android build points its asset directory at `web/`, so the browser, the
published artifact and the APK all ship the identical file.

## Building the APK

The APK was **not** built in this session — the sandbox that produced this
branch has `dl.google.com` blocked by egress policy, so the Android SDK could
not be downloaded. Everything needed to produce one is committed here, and CI
does it on a runner that has the SDK.

**Via GitHub Actions (no local setup):** push this branch, or run the
**Build APK** workflow from the Actions tab. It uploads
`paper-route-debug-apk` as a workflow artifact — download, unzip, transfer the
`.apk` to the phone, and open it with "install unknown apps" enabled.

**Locally**, with Android Studio or a command-line SDK installed:

```bash
cd android
./gradlew assembleDebug
# → app/build/outputs/apk/debug/app-debug.apk
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

Requires JDK 17+ and Android SDK platform 34 with build-tools. The debug APK is
signed with the standard debug key — fine for sideloading, not for Play.

- `applicationId` — `com.tellertales.paperroute`
- `minSdk` 26 (Android 8.0), `targetSdk` 34

The page pulls Anton / Chivo / DM Mono from Google Fonts. Offline — on a plane,
or in a sandbox with egress rules — every face falls back to a declared stack
and the game plays identically; only the lettering changes.
