# RG DS scraper build choices

## ScreenScraper without entering developer credentials on the device

Run `build-rocknix-rgds-private.yml` manually. It reads the existing
`SCREENSCRAPER_DEV_LOGIN` repository secret and embeds that login in the
executable. The developer ID and password fields on the device can then be
empty; the ScreenScraper account username and password remain separate.

This repository is public, so the workflow uploads only an encrypted
executable. The private decryption key is kept on the build owner's computer
at `~/.ROCKNIX/screenscraper-private-build-key.pem`. Keep that file private and
back it up; a new key requires a matching update to the public certificate in
`.github/keys/screenscraper-build-recipient.pem` before another build.

Download the `emulationstation-rocknix-rgds-private` artifact, then decrypt it:

```sh
openssl cms -decrypt -binary -inform DER \
  -in emulationstation.cms \
  -recip .github/keys/screenscraper-build-recipient.pem \
  -inkey ~/.ROCKNIX/screenscraper-private-build-key.pem \
  -out emulationstation
shasum -a 256 emulationstation
```

Compare the printed hash with `emulationstation.sha256` in the artifact. The
decrypted executable contains the developer credential and must be kept
private. The regular `build-rocknix-rgds.yml` workflow does not embed it.

## Scraping without developer credentials anywhere

Choose `ArcadeDB` under **Scrape from**. It needs no developer or user login.
It supports arcade, Neo Geo, and LCD game systems. ScreenScraper cannot use
this path because its API requires a developer login.
