# Release mode (console behind a private network)

The stock generator needs a web server that GitHub can reach: the build
downloads its encrypted settings zip, the icon and the logo from it, and posts
the finished installers back to it. A console that is only reachable on a LAN
or over a VPN (for example CortenDesk on a NAS) cannot do that, so this fork
adds a **release mode** to `generator-windows.yml`:

1. The console creates a **draft release** in this repository and attaches
   `secrets.zip` (the usual AES zip, encrypted with `ZIP_PASSWORD`),
   `icon.png` and `logo.png`.
2. It dispatches `generator-windows.yml` with
   `zip_url = {"release_id": <id of that draft release>}`. Inside the zip,
   `rdgen` is `"release"` and `release_id` is the same id.
3. The workflow reads its inputs from the draft release and, when the build is
   done, uploads `<filename>.exe` and `<filename>.msi` to it.
4. The console polls the run, downloads the installers and deletes the draft
   release.

Draft releases are visible only to people with write access to the repository,
and the settings zip is encrypted on top of that.

Requirements: the repository secret `ZIP_PASSWORD`, Actions enabled, and a
fine-grained token for the console with **Contents: read and write** and
**Actions: read and write** on this repository.

The URL-based mode (`rdgen` = `"true"` / `"false"`) is unchanged.
