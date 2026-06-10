# Adit Insider — Deep Link Site (GitHub Pages)

Ye folder ko ek **alag GitHub repo** me daalo aur GitHub Pages enable karo.
Iska kaam: link click hone par app khole (App Links / Universal Links) ya app na
ho to Play Store / App Store par bhej de.

Domain banega: `https://<username>.github.io/` — code me jagah-jagah
`aditinsider.github.io` likha hai, agar tumhara username alag hai to wo sab
jagah replace karna (Android manifest + iOS entitlements + niche ki files).

## Files
- `.well-known/assetlinks.json` — Android App Links verification
- `.well-known/apple-app-site-association` — iOS Universal Links verification
- `.nojekyll` — taaki GitHub Pages `.well-known` folder ko serve kare (warna ignore kar deta hai)
- `index.html` — app na ho to store par redirect karne wala page

## Pehle ye 3 values bharo

### 1) Android SHA-256 fingerprint  →  `assetlinks.json`
Debug ke liye:
```
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android | grep SHA256
```
Release ke liye apne release keystore se nikalo. (Play Store par publish karne ke
baad **Play Console → App integrity → App signing** wala SHA-256 bhi yahin add karo.)
Multiple fingerprints array me daal sakte ho.

### 2) Apple Team ID  →  `apple-app-site-association`
Apple Developer account → Membership me 10-character Team ID milega (jaise `A1B2C3D4E5`).
`appID` aisa banega: `A1B2C3D4E5.com.adit.aditinsider`.

### 3) App Store numeric id  →  `index.html`
App publish hone ke baad `idXXXXXXXXX` ko asli id se replace karo.

## Deploy steps
1. Naya GitHub repo banao (public).
2. Is folder ke saare files (including `.well-known/` aur `.nojekyll`) push karo.
3. Repo → Settings → Pages → Source: `main` branch, `/ (root)` → Save.
4. 1–2 min me `https://<username>.github.io/` live ho jayega.
5. Verify:
   - `https://<username>.github.io/.well-known/assetlinks.json` browser me khule
   - `https://<username>.github.io/.well-known/apple-app-site-association` bhi khule (JSON dikhe)

## Verify karna
- **Android:** `https://developers.google.com/digital-asset-links/tools/generator`
  par domain + package daalke test karo. Ya install ke baad:
  `adb shell pm verify-app-links --re-verify com.adit.aditinsider`
- **iOS:** `https://search.developer.apple.com/appsearch-validation-tool/` par AASA check karo.

## Test links (app install hone ke baad)
- `https://<username>.github.io/`            → home
- `https://<username>.github.io/actions`     → actions tab
- `https://<username>.github.io/submissions` → submissions tab
- `https://<username>.github.io/redeem`      → redeem tab
- `https://<username>.github.io/profile`     → profile
- `https://<username>.github.io/notifications` → notifications

Custom scheme se quick test (emulator/device):
```
adb shell am start -W -a android.intent.action.VIEW -d "aditinsider://actions" com.adit.aditinsider
```
