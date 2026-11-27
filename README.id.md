# CheatSheet

Aplikasi menu bar kecil di macOS untuk command yang sering dipakai. Pencet hotkey, cari, isi placeholder kalau ada, salin, lanjut kerja.

> English: lihat [README.md](README.md).

Repo ini adalah fork dari [ahmadazharrivaldy/CheatSheet](https://github.com/ahmadazharrivaldy/CheatSheet). Ide dan kode dasarnya dari sana; fork ini menambahkan perubahan yang dijelaskan di bawah.

## Fitur

- Tinggal di menu bar, buka dengan hotkey global (default `⌃⌥Space`)
- Cari di judul, kategori, dan isi command; filter pakai `#label`
- Placeholder di command seperti `{{nama}}`, `{{nama:default}}`, atau `{{nama:a|b|c}}`, diisi lewat form singkat sebelum disalin
- Syntax highlighting untuk beberapa bahasa umum
- Impor/ekspor sebagai JSON, plus lapisan backup/restore ke iCloud (lihat di bawah)
- UI Bahasa Indonesia dan Inggris

## Instalasi

Butuh Xcode command line tools (untuk `swiftc`) di macOS 14+.

```bash
./build.sh
open CheatSheet.app
```

Perintah ini build dan ad-hoc code-sign `CheatSheet.app` di folder project. Pindahkan ke `/Applications` kalau mau tampil normal dan bisa pakai "Open at Login".

## Data dan backup

Data harian tetap disimpan lokal di `~/Library/Application Support/CheatSheet/cheatsheet.json`, sama seperti versi aslinya, tidak diubah.

Di atas itu, fork ini menambahkan lapisan backup opsional ke iCloud Drive, terpisah dari file lokal:

- Settings → Backup iCloud: pilih Nonaktif, Harian, atau Mingguan, atau langsung klik "Backup sekarang"
- Backup disimpan di `~/Library/Mobile Documents/com~apple~CloudDocs/CheatSheet/Backups/`, 5 terakhir yang disimpan
- "Pulihkan..." menampilkan daftar backup tersebut dan bisa digabung atau menggantikan data yang sedang aktif
- Kalau iCloud Drive tidak tersedia, muncul pesan yang jelas, bukan gagal diam-diam, dan data lokal tidak tersentuh

## Arsitektur

Dibuat oleh [GitDiagram](https://gitdiagram.com/arturafriansyah/cheatsheet) dari struktur source code. Ini ringkasan buatan AI, jadi cek lagi ke kode aslinya untuk hal yang penting.

```mermaid
flowchart TD
subgraph group_interaction["Cheatsheet interaction"]
  node_app["SwiftUI app<br/>[main.swift]"]
  node_browse["Search and browse<br/>[main.swift]"]
  node_note_actions["Note actions<br/>[main.swift]"]
  node_editor["Note editor<br/>[main.swift]"]
  node_settings["Settings<br/>[main.swift]"]
end
subgraph group_content["Content and presentation"]
  node_store[("Cheatsheet store<br/>[main.swift]")]
  node_defaults["Sample cheatsheets<br/>[main.swift]"]
  node_placeholders["Placeholder completion<br/>[main.swift]"]
  node_highlight["Code highlighting<br/>[main.swift]"]
  node_localization["Localization<br/>[main.swift]"]
end
subgraph group_platform["macOS services"]
  node_clipboard["System clipboard"]
  node_hotkey["Keyboard monitor<br/>[main.swift]"]
  node_login["Login item service"]
end
node_user(("User"))
node_user -->|"uses"| node_app
node_app -.->|"presents"| node_browse
node_browse -.->|"selects notes"| node_note_actions
node_note_actions -.->|"updates notes"| node_store
node_editor -.->|"edits notes"| node_store
node_defaults -.->|"seeds content"| node_store
node_note_actions -.->|"fills values"| node_placeholders
node_note_actions -.->|"formats code"| node_highlight
node_note_actions -.->|"copies command"| node_clipboard
node_app -->|"monitors keys"| node_hotkey
node_settings -->|"registers app"| node_login
node_app -.->|"uses labels"| node_localization
classDef toneNeutral fill:#f8fafc,stroke:#334155,stroke-width:1.5px,color:#0f172a
classDef toneBlue fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#172554
classDef toneAmber fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
classDef toneMint fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
class node_app,node_browse,node_note_actions,node_editor,node_settings,node_user toneBlue
class node_store,node_defaults,node_placeholders,node_highlight,node_localization toneAmber
class node_clipboard,node_hotkey,node_login toneMint
```

Versi interaktif: [gitdiagram.com/arturafriansyah/cheatsheet](https://gitdiagram.com/arturafriansyah/cheatsheet)

## Rilis

Push tag seperti `v1.1` memicu GitHub Actions, yang build aplikasi di runner macOS dan melampirkan `.app` yang di-zip ke GitHub Release.

## Lisensi

MIT, sama seperti aslinya. Lihat [LICENSE](LICENSE).
