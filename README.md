# Latchkey for iOS (TestFlight, no Mac needed)

This wraps the Latchkey website in a native iOS app (Capacitor). GitHub's macOS runners build it
with Xcode 26 and upload it to TestFlight, so you can do everything from Windows.

## One-time setup

1. **Apple Developer Program.** Enroll at developer.apple.com/programs ($99/year). Approval can take a day or two.
2. **Create the app record.** App Store Connect > Apps > + > New App.
   - Platform iOS, Name "Latchkey" (names are global; if taken, pick another, it's only the store name),
     Bundle ID `com.doorknobinteractive.latchkey` (register it first under developer.apple.com > Certificates,
     Identifiers & Profiles > Identifiers if it isn't in the dropdown), SKU anything.
   - If you pick a different bundle ID, change `appId` in `capacitor.config.json` to match.
3. **API key.** App Store Connect > Users and Access > Integrations > App Store Connect API > Team Keys > +.
   Access: **Admin** (needed for automatic signing). Download the `.p8` once, and note the **Key ID** and **Issuer ID**.
4. **Team ID.** developer.apple.com > Membership details > Team ID (10 characters).
5. **GitHub.** Create a private repo and upload this folder's contents (keep the `.github` folder).
   Settings > Secrets and variables > Actions > New repository secret, add:
   - `ASC_KEY_ID`: the Key ID
   - `ASC_ISSUER_ID`: the Issuer ID
   - `ASC_KEY_P8`: the whole contents of the .p8 file, including the BEGIN/END lines
   - `TEAM_ID`: your Team ID
6. **Website address.** In `www/index.html`, set `web:` in the `CONFIG` line near the top of the script to your
   public site (for example `https://yourname.pages.dev/`). Invite links and password-reset emails then point at the website
   instead of the app's internal address.

## Build and send to TestFlight

GitHub > Actions > "Build and upload to TestFlight" > Run workflow. (Pushing a tag like `v1.0.1` also runs it.)
It takes roughly 10 to 20 minutes. Apple then processes the build for another 5 to 30 minutes.

## Install on your iPhone

1. Install **TestFlight** from the App Store.
2. App Store Connect > Users and Access: make sure you're a user. Then your app > TestFlight > Internal Testing > + group, add yourself.
   Internal testers (up to 100 people on your team) get builds right away with no Apple review.
3. Open TestFlight on the phone and install Latchkey.

## Updating the app

Replace `www/index.html` with the new site file (keep your `CONFIG` values), commit, and run the workflow again.
Each run gets a new build number automatically. Bump `APP_VERSION` in the workflow for a new version number.

## If a build fails

Open the failed run in the Actions tab and read the red step. Common causes: a wrong secret, the Admin role missing on the API key,
a bundle ID that doesn't match the app record, or a Capacitor/Xcode version mismatch.

## Sideloading an IPA (no TestFlight, no paid account)

GitHub > Actions > "Build IPA for sideloading" > Run workflow. When it finishes, download `Latchkey-unsigned-ipa` from the run page and unzip it to get `Latchkey.ipa`.

That file is **unsigned**. Each person installs it by signing it with their own free Apple ID on a computer:

- **Sideloadly** (Windows or Mac): plug in the iPhone, drag in the IPA, sign in with an Apple ID, click Start.
- **AltStore / SideStore**: install the store app once, then add the IPA from inside it. It can refresh the app automatically over Wi-Fi.

On the phone, trust the developer under Settings > General > VPN & Device Management, and turn on Developer Mode (Settings > Privacy & Security) if iOS asks.

Limits of the free route: the app expires after 7 days until it's refreshed, each Apple ID can only keep a few sideloaded apps at once, and every person has to do the signing step themselves.
