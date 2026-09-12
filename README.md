# Personal VPM repository builder

`tools/build_vpm.py` packages the 13 tools currently installed under
`MCP Project/Assets/Jeni_tool` from the external `VRC_Tools` source of truth.
It writes only to `dist-ready/`; it never edits or synchronizes the Unity project.

```powershell
python tools/build_vpm.py --out dist-ready --version 0.1.0
python tools/verify_vpm.py dist-ready
# A later release for one package preserves all older index entries:
python tools/build_vpm.py --out dist-ready --package orbit --version 0.1.1
```

Each ZIP has a deterministic timestamp and is refused if the same package
version already exists with different bytes. `dist-ready/index.json` is shaped for
VCC/VPM. The public repository is [jeni-tools-vpm](https://github.com/Suzuki-Y-0/jeni-tools-vpm),
and its index is available at
`https://raw.githubusercontent.com/Suzuki-Y-0/jeni-tools-vpm/main/index.json`.
All 13 `0.1.0` package releases are available from that repository.

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
