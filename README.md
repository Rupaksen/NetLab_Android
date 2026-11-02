# NetSignal Lab — Android App (v3.2.0)

Native Android edition of **NetSignal Lab** (ECE/23/12): the 4G/5G analyzer, DSP lab,
C++ DSP/filter programming lab, digital & analog communication kit, line coding lab,
oscilloscope and history/report modules — running fully **offline** on the phone.

## What changed vs. the web edition
| Web edition | Android app |
|---|---|
| `node server.js` serves files + `/api/*` | `LocalBackend.java` serves the bundled `assets/www` and answers `/api/health`, `/api/sessions`, `/api/radio` in-process — no Node, no server, no internet permission |
| "Android Radio Bridge" was a future idea | **Implemented**: `RadioReader.java` reads real serving + neighbour **LTE / 5G NR** cells from the modem (RSRP, RSRQ, SINR, PCI, EARFCN/NR-ARFCN, band, cell ID, operator, LTE / NSA / SA). Shown in a new panel on the *4G/5G* page, with live 2 s refresh and *Save to History* |
| Export CSV / JSON / `.cpp` via browser download | Android "Save as" dialog (pick Downloads, Drive, etc.) |
| `window.print()` | Android print service → *Save as PDF* or a printer |
| Desktop-first layout | Phone layout fixes (no sideways scrolling), hardware **Back** walks back through lab sections |

All original lab files are unchanged; the Android glue lives in
`assets/www/android-bridge-early.js`, `android-bridge.js` and `android.css`
(index.html only gains three include lines). In a normal browser these files do nothing.

## Build
**Android Studio (recommended):** *File → Open* this folder, let Gradle sync, then
*Run ▶* on a phone, or *Build → Build App Bundle(s)/APK(s) → Build APK(s)*.

**Command line** (Android SDK + JDK 17 installed):
```
./gradlew assembleRelease      # app/build/outputs/apk/release/app-release.apk
```

**No Android SDK?** Push this folder to a GitHub repo: the included workflow
(`.github/workflows/build-apk.yml`) builds the APK and attaches it under *Actions → Artifacts*.

- minSdk 26 (Android 8.0), targetSdk 35 (Android 15). No third-party libraries.
- The release build is signed with the debug key so it installs directly; use your own
  keystore before publishing to Play Store.

## Using the radio bridge
Open **4G/5G → Android Radio Bridge → Read Radio Now** and allow *Location* (Android only
exposes cell data with location permission, and device Location must be ON). *Phone*
permission is optional and lets the app distinguish 5G SA from LTE/NSA. Notes:
- Values come straight from the modem; nothing is simulated or uploaded.
- Band names need Android 11+; SINR for LTE is the modem's RSSNR.
- Many phones on NSA 5G report the NR leg only while data is active — start a download
  if the NR row is missing.
- The *Online Test* throughput section is still the local model from the web edition.

## Project layout
```
app/src/main/java/com/netsignal/lab/
  MainActivity.java   WebView host, file save, print, permissions, Back button
  LocalBackend.java   replacement for server.js (assets + /api routes)
  RadioReader.java    LTE / 5G NR measurements via TelephonyManager
app/src/main/assets/www/   the lab (HTML/CSS/JS) + Android bridge files
```
