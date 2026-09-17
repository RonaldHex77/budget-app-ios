# Budget App — iOS wrapper (experimental)

This is a native iOS wrapper around the "Our Ledger" budget app, built with
[Capacitor](https://capacitorjs.com/). It packages the same app you use in
the browser inside a real installable iOS app shell, so it gets its own icon
and runs full-screen without any browser address bar.

## Important: this build runs in local-only mode

The web version of Our Ledger talks to Claude's artifact platform to sync
data between you and your partner. That connection (`window.claude`) only
exists inside claude.ai's own artifact viewer — it does **not** exist inside
a plain Capacitor/iOS app. That means this native build will fall back to
the app's local-device mode automatically: it saves data only on the phone
it's installed on, in its own separate storage, completely disconnected from
the shared web version and from your partner's copy.

In short: right now, this is a good way to try what the app *feels like* as
a real installed app — the screens, the navigation, the budget math — but
it is **not** yet synced to your shared household data. Making it actually
sync would mean building real backend/login infrastructure, which is
separate, bigger work (already flagged in the "Our Ledger — Mobile App
Conversion Notes" doc).

## How the build works

1. `www/index.html` is a copy of the Our Ledger app.
2. `.github/workflows/ios-build.yml` runs automatically on every push to
   `main` (or can be triggered manually from the Actions tab). It uses
   GitHub's free macOS runners to:
   - install the iOS platform files via Capacitor,
   - run CocoaPods,
   - archive the app **without any code signing** (since there's no Apple
     Developer Program membership involved),
   - repackage that into `BudgetApp-unsigned.ipa`.
3. The finished `.ipa` shows up as a downloadable build artifact on the
   finished workflow run (Actions tab → the latest run → Artifacts section
   at the bottom).

## Installing it on an iPhone

1. Download the `BudgetApp-unsigned-ipa` artifact from the finished GitHub
   Actions run (it downloads as a `.zip` — unzip it to get the `.ipa`).
2. Install [Sideloadly](https://sideloadly.io/) on a Windows or Mac computer
   (official site only).
3. Plug the iPhone in with a cable, open Sideloadly, drag the `.ipa` in,
   sign in with an Apple ID (a spare/free one dedicated to this is a good
   idea), and install.
4. Apple's free-tier signing expires after 7 days — repeat step 3 weekly to
   keep the app working.

## Status

First attempt at the CI pipeline. The code-signing-free archive/export step
is a community workaround, not an officially documented Apple path, so the
first run may need a round of debugging.
