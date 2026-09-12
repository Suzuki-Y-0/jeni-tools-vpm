# Personal VPM repository builder

`tools/build_vpm.py` packages the 13 tools currently installed under
`MCP Project/Assets/Jeni_tool` from the external `VRC_Tools` source of truth.
It writes only to `dist-ready/`; it never edits or synchronizes the Unity project.

```powershell
python tools/build_vpm.py --out dist-ready --version 0.1.0
python tools/verify_vpm.py dist-ready
# A later release for one package preserves all older index entries:
python tools/build_vpm.py --out dist-ready --package orbit --version 0.1.2
```

Each ZIP has a deterministic timestamp and is refused if the same package
version already exists with different bytes. `dist-ready/index.json` is shaped for
VCC/VPM. The public repository is [jeni-tools-vpm](https://github.com/Suzuki-Y-0/jeni-tools-vpm),
and its index is available at
`https://raw.githubusercontent.com/Suzuki-Y-0/jeni-tools-vpm/main/index.json`.
All 13 generic package IDs are available from that repository. The
`liltoon-material-override` package has the latest unified `0.2.2` release
containing the Preset Applicator sources, so new projects should install that
one package instead of installing the two lilToon packages side by side. The
separate `liltoon-preset-applicator` `0.1.x` entries remain available for
projects that still depend on the legacy package ID.

## VCC install

1. In VCC or ALCOM, open `Settings > Packages > Add Repository`.
2. Add `https://raw.githubusercontent.com/Suzuki-Y-0/jeni-tools-vpm/main/index.json`.
3. Add the Jeni package you want to the project. Use the green update arrow when a newer release is available.

ALCOM and VCC share the repository list. After adding the URL once, use ALCOM's
project manager to install or update the package versions; the latest unified lilToon
release is `com.suzuki-y0.jeni.liltoon-material-override@0.2.2`. Eye Texture Adapter
and Orbit have `0.1.2` cleanup releases. The generic package menus use English,
descriptive paths under `Tools/Jeni Tools` (with matching
`GameObject`, `Assets/Create`, and `Jeni Tools` component roots where applicable).

Register the NDMF, Modular Avatar, lilToon, and other external repositories in each project as needed.

## lilToon package 9/10 integration

`com.suzuki-y0.jeni.liltoon-material-override@0.2.2` is the canonical unified
release. It includes the former `com.suzuki-y0.jeni.liltoon-preset-applicator`
Runtime and Editor sources under `Legacy/PresetApplicator/` and no longer has
an internal dependency on that package. Do not add the legacy package 10 to a
project that already has package 9 `0.2.x`; the separate `0.1.x` package remains
for compatibility with existing manifests and falls back to its own window when
the merged package is absent.

The package set deliberately excludes the empty `EyeWobble` folder, legacy
`FaceTraBlendshapeComposer` documents, generated avatar assets, test fixtures,
archives, nested `.git` directories, and the source-only `Outline`, `QVPenSigner`
and `URLDisplay` folders that are not present in the MCP project. `Orbit`
contains the canonical EyeWobble component; `LilToonMaterialOverride` contains
ProbeAnchorNormalizer.

The initial scope is the 13 generic Jeni tools currently present in MCP
`Assets/Jeni_tool`; the empty EyeWobble folder is excluded because the current
implementation is in Orbit. Outline, QVPenSigner, URLDisplay, Cazalis and
CyberTranslucence/CyberpunkTransparentMaterials are separate future work.

Before switching an existing project, back up and remove its old
`Assets/Jeni_tool/<tool>` copy manually. No `legacyFolders` deletion is emitted,
so installing a package cannot silently delete user assets.
