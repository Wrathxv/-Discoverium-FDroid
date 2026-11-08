# Discoverium Personal F-Droid Repository

This repository publishes the **official, unmodified Discoverium APK** from
[cygnusx-1-org/Discoverium](https://github.com/cygnusx-1-org/Discoverium)
as a signed third-party F-Droid repository.

It does not rebuild Discoverium or re-sign its APK. F-Droid's repository
index is signed with a separate key that you control.

## One-time setup

### 1. Create a repository signing key

On a computer with Java's `keytool` installed, run:

```sh
keytool -genkeypair -v \
  -keystore fdroid-keystore.jks \
  -alias fdroid \
  -keyalg RSA -keysize 4096 -validity 10000 \
  -dname "CN=Discoverium Personal Repository, O=Personal, C=US"
```

Choose strong passwords and keep the keystore and passwords backed up
somewhere safe. **Never commit the keystore or share it publicly.** Losing
this key means clients will no longer trust future updates from this repo.

Encode the keystore as one Base64 line:

```sh
base64 -w 0 fdroid-keystore.jks
```

On macOS, use `base64 < fdroid-keystore.jks | tr -d '\\n'` instead.

### 2. Add three GitHub Actions secrets

Open **Settings → Secrets and variables → Actions → New repository secret**
in this repository and add:

| Secret name | Value |
|---|---|
| `F_DROID_KEYSTORE_BASE64` | The complete Base64 output from the previous step |
| `F_DROID_KEYSTORE_PASS` | The keystore password |
| `F_DROID_KEY_PASS` | The key password (often the same password) |

Do not paste these secrets into an issue, commit, or chat.

### 3. Enable GitHub Pages

Open **Settings → Pages** and set **Build and deployment → Source** to
**GitHub Actions**.

### 4. Run the publisher

Open **Actions → Build and publish Discoverium F-Droid repository → Run workflow**.
The workflow downloads the latest APK from the official Discoverium GitHub
release, checks its published SHA-256 digest when available, reads its Android
package/version information, builds the signed F-Droid indexes, and deploys
the repository to GitHub Pages.

A scheduled run checks for a new upstream release every day.

## F-Droid repository address

After the first successful workflow run, add this URL in the F-Droid client:

```
https://wrathxv.github.io/-Discoverium-FDroid
```

The repository is not ready to add until the first deployment succeeds.
F-Droid will ask you to verify the repository signing fingerprint; compare it
against the fingerprint shown by the repository's signing certificate. Do not
accept a fingerprint you have not independently verified.

## Important notes

- Discoverium's application ID is `org.cygnusx1.discoverium`.
- The app APK remains signed by the upstream Discoverium publisher. The key
  generated above signs only this repository's index.
- The workflow only accepts the official repository's latest release and
  expects exactly one APK asset.
- The repository key must remain the same for the lifetime of this repository.
