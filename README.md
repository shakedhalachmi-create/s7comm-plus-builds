# s7comm-plus-builds

Ready-made builds of the **S7CommPlus Wireshark dissector** (Siemens S7-1200/S7-1500 traffic)
for several Wireshark versions, processors and operating systems.

This repository holds no dissector code, only the build automation. The dissector itself is
`packet-s7comm_plus.c` by **Thomas Wiens**, licensed GPL-2.0-or-later:

| | |
|---|---|
| Original project | https://sourceforge.net/projects/s7commwireshark/ |
| GitHub mirror (built from) | https://github.com/JuergenKosel/s7commwireshark |
| Backup fork (used if the mirror disappears) | https://github.com/shakedhalachmi-create/s7commwireshark |

## Download

Go to [Releases](../../releases/latest). Each release is named after the plugin source commit
and contains one file per Wireshark version and platform:

```
s7comm_plus-wireshark<VERSION>-<PLATFORM>.<dll|so>
```

| Your Wireshark | File to take |
|---|---|
| Windows, x64 build (Intel/AMD, or x64 Wireshark on an ARM PC) | `...-windows-x64.dll` |
| Windows, ARM64 build | `...-windows-arm64.dll` |
| Linux, x86_64 | `...-linux-x64.so` |
| Linux, aarch64 | `...-linux-arm64.so` |
| macOS, Apple Silicon | `...-macos-arm64.so` |

Only the first two numbers of the version have to match. For example, the `4.6.9` file
works with any Wireshark 4.6.x, but not with 4.4 or 4.8.

To check your version, open Help > About Wireshark. On Windows, see which build you have
(x64 or ARM64) in Task Manager > Details > Architecture column, next to `Wireshark.exe`.

## Install

1. Rename the file to `s7comm_plus.dll` (Windows) or `s7comm_plus.so` (Linux/macOS).
2. In Wireshark, open Help > About Wireshark > Folders and double-click **Personal Plugins**.
   It points to `.../plugins/<major.minor>`, for example `plugins/4.6`. On macOS the folder
   name uses a dash instead, for example `plugins/4-6`.
3. Inside it, create a folder named `epan` and copy the file into it.
4. Restart Wireshark. *Reload Lua Plugins* is not enough for binary plugins.
5. Check that `s7comm_plus` appears under Help > About Wireshark > Plugins, and that the display
   filter `s7comm-plus` matches S7CommPlus packets (TCP port 102).

Windows may warn about an unsigned file downloaded from the internet. If it does, open the file's
Properties and tick *Unblock*.

The Linux files are built on Ubuntu 24.04 and need glibc 2.39 or newer. On an older distribution,
build the plugin yourself from the source above.

## How the builds run

[`.github/workflows/build.yml`](.github/workflows/build.yml):

- **Plugin source:** the `local-trunk` branch of the GitHub mirror. If the mirror can't be
  reached, it uses the backup fork.
- **Wireshark versions:** the minor versions listed in [`wireshark-versions.txt`](wireshark-versions.txt).
  Each one is built against its latest patch release from gitlab.com/wireshark/wireshark.
- **Platforms:** Windows x64 and ARM64, Linux x64 and ARM64, and macOS ARM64, each on a native
  GitHub runner.
- **Schedule:** runs every Monday. A new plugin commit or a new Wireshark patch release is built
  automatically, and files that are already released are skipped.
- **Manual runs:** Actions > *Build S7CommPlus plugin* > *Run workflow*. You can choose versions
  and platforms, or force a rebuild.

To support a new Wireshark version, add it to `wireshark-versions.txt`, for example `4.8`.

## License

GPL-2.0-or-later, the same license as the dissector. The binaries are built from the
unmodified source linked above.
