# Jeni Tools 13系統 静的監査報告（公開12 package IDs・28 versions）

監査日: 2026-09-13  
対象: VRC_Tools を正本とする汎用 Jeni Tools 13 source tools、公開 VPM index（package 10 retirement後の12 IDs・28 versions）、MCP Project の package コピー

## 監査の境界

この監査は Unity を新規起動せず、ソース、asmdef、package manifest、ZIP、既存の Editor.log、既存テスト計画を照合したものです。ソース対象は13系統で、公開 VPM package は package 10 のretirement後に12 IDsです。lilToon Material Override 0.2.4では、Preview開始前にlilToon 2.3.4のpackage/API markerを検査するPreflightを追加しています。

1. Avatar Prefab Material Exporter
2. Avatar Reactive Motion
3. Avatar Variant Integrator
4. Avatar Y Offset
5. Cigarette Gimmick
6. Eye Texture Adapter
7. Face Tracking Blendshape Composer
8. FaceTra Prefab Exporter
9. lilToon Material Override
10. lilToon Preset Applicator（旧 package ID。standalone package 10はretired、9へ統合済み）
11. Magic Ray Gimmick Builder
12. Mesh Deform Editor
13. Orbit Asset Generator（Eye Wobbleなし。Eye WobbleはAvatar Reactive Motion）

ソース全体の [MenuItem] / [AddComponentMenu] は33件、空でない表示パスは27件でした。汎用メニューはASCII英語で、許可したルート（Tools/Jeni Tools、GameObject/Jeni Tools、Assets/Create/Jeni Tools、Jeni Tools）から外れる項目は0件でした。MCP Projectの現在のpackageコピーでも33件を読み取れ、非ASCIIのメニュー表示は0件です。空の AddComponentMenu("") はInspectorでコンポーネントを追加する経路を公開しない既存設計であり、名前漏れとは判定していません。

## package別の静的結果

| package | 最新公開版 | 依存 | 表示メニュー（空でない項目数） | 静的判定 | Unityで残る確認 |
|---|---:|---|---:|---|---|
| avatar-prefab-material-exporter | 0.1.1 | Unity Editor | 1 | asmdef、ZIP sidecar、英語メニューを確認 | Prefab生成、元アセット不変性、EditMode |
| avatar-reactive-motion | 0.1.1 | NDMF、Modular Avatar、VRChat SDK、Face Tracking Composer | 2 | Editor/Runtime分離と外部参照を確認 | NDMF Build、parameter/preview、実アバター |
| avatar-variant-integrator | 0.1.1 | NDMF、Modular Avatar、VRChat SDK | 1 | Build asmdefはNDMF導入時だけ有効。入力検証と安全な差分適用をコード照合 | Variant Build、Material/BlendShape/Playable Layer |
| avatar-y-offset | 0.1.0 | NDMF、Modular Avatar、VRChat SDK | 1 | Humanoid検証、±10 m制限、Undo/復元経路を確認 | スケール、親回転、NDMF Preview/Build |
| cigarette | 0.1.1 | NDMF、VRChat SDK | 2 | binding remap、旧インストールのbackup/restore、4 layer出力をコード照合 | Shaderの見た目、同期、Quest、MA Build |
| eye-texture-adapter | 0.1.2 | NDMF | 2 | Profile/PNGの保存経路、sidecar、到達不能分岐除去を確認 | GPU画像、4096/8192、Gamma、実アバターBuild |
| face-tracking-blendshape-composer | 0.1.1 | NDMF | 3 | Composer、template、Installer認識の構造を確認 | VRCFT/FaceTra実ベイク、NDMF、実機 |
| facetra-prefab-exporter | 0.1.1 | NDMF、VRChat SDK、Face Tracking Composer | 4 | FaceTra key、source signature、Installerの失敗停止を確認 | 別FaceTra版・別Mesh・実ベイク |
| liltoon-material-override | 0.2.4 | NDMF、Modular Avatar、VRChat SDK、lilToon | 5 | Preset Applicator、Probe Anchor、Outlineを9へ同梱。Preview前にlilToon 2.3.4 split/API markerをPreflight。0.2.xは旧10への依存なし | 各lilToon版、Preview、後続MA/Optimizer、Quest |
| liltoon-preset-applicator（retired package 10） | — | NDMF、VRChat SDK、lilToon | 1 | standalone package 10は公開対象外。Runtime/Editorは9の0.2.xへ統合済み | 既存プロジェクトのpackage 9 `0.2.x` 移行 |
| magic-ray | 0.1.1 | NDMF、Modular Avatar、VRChat SDK | 1 | Build-onlyのparameter/menu衝突検査、clone内解決を確認 | PC/Quest音、Bloom、負荷、MA API差異 |
| mesh-deform-editor | 0.1.0 | Unity Editor | 1 | package時にEditor asmdef overlayを生成。ソース単体にはasmdefなし | Mesh保存、Shader、Undo/Redo、大頂点数 |
| orbit | 0.2.0 | NDMF、Modular Avatar、VRChat SDK | 2 | Orbit Asset Generatorのみ、固定生成先、未使用legacy field整理を確認。Eye WobbleはAvatar Reactive Motionへ分離 | Scene Preview、clip、Menu/Parameter、scale |

メニュー名は機能を示す動詞を含めました（Create、Transfer ... and Export PNG、Normalize ... (Build Time)、Generate ...）。ブランド表記の lilToon と FaceTra はAPI・製品名として保持しています。

## 検証で得られた証拠

- python -m unittest discover -s VRC_Tools/PersonalVpm/tests -v: 11 tests passed。
- python -m py_compile VRC_Tools/PersonalVpm/tools/build_vpm.py VRC_Tools/PersonalVpm/tools/verify_vpm.py: 成功。
- python VRC_Tools/PersonalVpm/tools/verify_vpm.py VRC_Tools/PersonalVpm/dist-ready: verified 27 package versions。
- MCPのパスを意図的に存在しない場所へ切り替えた一時buildでも12公開 packageすべてを生成でき、metadata snapshot/fallbackで自己完結することを確認しました。最新buildのfallback数は順に、3 / 8 / 30 / 8 / 2 / 28 / 16 / 9 / 9 / 7 / 6 / 9です（retired package 10を除く）。
- 最新ZIPはunsafe path、.git、開発用 Tests / Generated / Archive、package.jsonと無関係な孤立 .md.meta を含みません。各ZIP内のGUID重複も0件です。
- 公開indexはHTTP 200で取得でき、12 package ID・28 versionを保持しています。Eye Texture Adapter 0.1.2 と Orbit 0.1.2 のRelease assetはimmutableな既存版として保持します。Orbit 0.2.0はEye Wobble分離版です。lilToon Material Override 0.2.4はPreflight/API marker検査を収録します。
- 現在のMCP Projectでは、MCP manifestのJeni package 12件を vpm-manifest.json に記録し、Eye Texture Adapter / Orbitのpackage manifestとREADMEも最新版へ揃えました。Orbit packageはOrbit Asset Generatorだけを含み、Eye WobbleはAvatar Reactive Motionが所有します。

## 発見事項

### F-001 — package 9/10の同時導入はアセンブリ・GUID・メニューが衝突する（解決済み、P1）

liltoon-material-override 0.2.4 は旧10のソースを Legacy/PresetApplicator/ に同梱し、Preview開始前のlilToon 2.3.4 package/API marker検査を行います。旧 liltoon-preset-applicator 0.1.2 と同時に入れると、次のasmdef名が重複します（この組み合わせはretirement前の状態です）。

- JeniTool.LilToonPresetApplicator.Editor
- JeniTool.LilToonPresetApplicator.Runtime

同じソースを共有するため、7個のUnity GUIDも重複し、Apply Preset Configuration メニューも二重登録されます。package 10 standaloneとpackage 9の`0.1.x`は公開 indexから削除し、package 9の`0.2.x`だけを公開することで、VCCで衝突する組み合わせを選べない状態にしました。

対策は完了しました。公開 indexの12 IDs・28 versionsを検証し、package 10の公開artifact・index entryが無いことを確認しました。package 10を参照する既存プロジェクトにはpackage 9 `0.2.x`への移行を案内します。ビルド時に複数packageのasmdef名・GUID・MenuItemを横断検査するCIは継続します。

### F-002 — ソースリポジトリの .meta 不足をsnapshotで補っている（P1）

各nested source repoには .cs、asmdef、Prefab、画像などのsidecarが無いファイルが多く、builderは PersonalVpm/metadata の182 snapshotまたは決定的UUID fallbackを使います。今回のZIP検証では各assetのsidecarとGUID重複を確認できていますが、snapshotが正本からずれると別PCで同じGUIDにならない、既存Prefab参照が切れる、というリスクが残ります。

短期はmetadata coverageをCIで必須化し、source hash・相対パス・GUIDの差分をrelease前に拒否します。中期は各source repoへ実際のsidecarを戻し、snapshotを移行用の一時データに下げます。

### F-003 — Orbitの未使用legacy field（解決済み、P2）

OrbitAssetGenerator.outputFolder は ORBIT_LEGACY_GENERATOR が無効な現行経路では読み書きされず、CS0414警告になっていました。現行生成先は意図的に Assets/Jeni_tool/Orbit/Generated 固定なので、field宣言をlegacy define内へ移し、動作・シリアライズ面を変えました。Eye Wobble分離に伴うOrbit 0.2.0では、この修正版Orbit実装だけを出荷し、既存Orbit 0.1.2はimmutableに保持します。

### F-004 — Eye Profile Wizardの到達不能分岐（解決済み、P2）

if (false && ...) の旧ボタン群が現行 DrawProcessSteps と重複し、CS0162警告を発生させていました。旧分岐を削除し、実行中の段階UIだけを残して eye-texture-adapter 0.1.2 を再公開しました。

### F-005 — 現行パスとsource READMEリンクの不整合（解決済み、P2）

Eye Texture Adapterの現行保存先は Assets/Jeni_tool/... なのに、先頭の手順が旧 Assets/JeniTool/... を案内していました。現行箇所を修正し、旧フォルダはlegacy移行先として残しました。Cigarette READMEの Assets/Jeni_tool/SPEC.md / TEST_PLAN.md という自己参照も、同じディレクトリを指す相対リンクへ修正しました。

### F-006 — Unity compile以降の実動作に検証空白がある（P1）

ソース、asmdef、ZIP、既存テスト設計は確認済みですが、この監査ではTest Runner、独立したNDMF Build、VRChat SDK Build & Test、GPU表示、実機を起動していません。既存の対話型Unity Editor（Unity 2022.3.22f1）がMCP Projectを所有しているため、別Unityやbatch modeを開始しませんでした。

既存 Editor.log にはJeni assemblyを含むTundra build successがあり、変更前のEye/Orbit警告も記録されています。変更後のファイルimportは記録されていますが、ここから「全体が合格」とは判定しません。ログには別問題として jp.shiranui-isuzu.unity-mcp の未解決型エラー（ReleaseNotice、BuildReportReader、SerializedValues、SrpBatcherCheck、FrameProfiler、CameraBufferCaptureなど）と.NET assembly version重複もあります。これらはJeni packageのソース外ですが、MCP Project全体のUnity検証を妨げます。

### F-007 — MCP manifestの要求版とlock解決版が異なる（P2）

現在の vpm-manifest.json は com.vrchat.avatars 3.10.4、nadena.dev.modular-avatar 1.17.1を要求します。一方 packages-lock.json はそれぞれ3.10.5、1.18.7を解決しており、Jeni VPMの下限（SDK 3.10.5、MA 1.18.7）とはlock側だけが一致します。既存projectが動くことと、別PCの新規resolveが再現することは別です。

クリーンcloneでVCC resolveを行い、manifestの要求版をlockと同じ方針にするか、builderの下限を正式に下げるかを決めます。手動でSDKを更新する前に、各toolのEditMode/NDMF結果を比較します。

### F-008 — SDK/API版差異と実アバター条件（P1〜P2）

Reactive Motion、Face Tracking、FaceTra、Variant、lilToon、Magic Ray、Orbitは、NDMF・Modular Avatar・VRChat SDK・VRCFT・lilToonのAPI名とBuild順に依存します。FaceTraは 0.9.0 を固定し、他のpackageはmajor範囲を制限していますが、最新SDKへの追従を自動で保証するmatrixはありません。各README/TEST_PLANの NOT VERIFIED 記載は正しく、Passに読み替えてはいけません。

### F-009 — 固定された生成先と旧フォルダの移行（P2）

多くのtoolは後方互換のため Assets/Jeni_tool/<Tool>/... にPrefab、Profile、Controller、Animation、PNGを保存します。VPM packageは生成物を含めず、legacyFolders による削除も行いません。これは安全側ですが、既存の同名スクリプトとPackageの二重導入、旧Profileの参照先、再生成時の古いAnimation残置は利用者が確認する必要があります。移行手順に「生成物のbackup、script重複確認、compile、NDMF Build」を必須化します。

### F-010 — Mesh Deformのasmdef overlay（P2）

Mesh Deform Editorはsource rootにasmdefを持たず、builderがpackageの Editor/JeniTool.MeshDeformEditor.Editor.asmdef を生成します。VPM artifactでは自己完結し、検証も通りますが、sourceを直接Assetsへコピーした場合のコンパイル境界が異なります。READMEでVPM導入を正本と明記し、overlayの参照・GUIDをCIで固定します。将来はsource側へEditor asmdefを追加するか、直接コピー経路を廃止します。

### F-011 — NDMF pluginの表示名と内部UIには日本語が残る（P2、今回のメニュー範囲外）

Unityメニューの表示パスは英語に統一しましたが、NDMFの DisplayName や既存Window内の説明文には日本語が残ります。動作上の問題はありませんが、英語UIを完全な配布基準にするなら、表示名、HelpBox、エラー文を対象に翻訳し、テストで表示文字列を固定します。ブランド名・API名は翻訳対象から除外します。

## vrc.schoolとの照合

VRChat Schoolの手順に合わせ、次の順序で受入れを行います。

- [VCCでのアバターアップロード手順](https://vrc.school/docs/Avatars/Uploading-An-Avatar/) は、VCCで必要packageを揃え、Consoleの赤エラーを解消してからSDKのBuild & Publishを行う流れです。VPMのindex登録だけでSDKやNDMFの実動作が保証されるわけではありません。
- [Avatar Descriptor](https://vrc.school/docs/Avatars/Avatar-Descriptor/) と [SDK Components](https://vrc.school/docs/Avatars/SDK-Components/) は、Descriptor、Animator、viseme/eye、PhysBoneなどの構成を前提にします。Reactive、Variant、Y Offset、Cigarette、Magic Ray、Orbit、FaceTra系は、対象GameObjectとDescriptorの境界をBuild前に検査します。
- [Avatar Rigs](https://vrc.school/docs/Unity-Animations/Avatar-Rigs/) にあるHumanoid/Genericの差は、Y OffsetやOrbitの骨・アニメーション解決に直結します。Humanoid専用の入力を明示し、Generic/Legacy/Noneを誤って通さないテストを行います。
- [Scale-Friendly](https://vrc.school/docs/Other/Scale-Friendly/) のスケール注意点から、固定Y値、radius、constraint、eye lookの相対位置をアバター倍率ごとに確認します。Y Offset、Reactive Motion、Orbit、Eye Texture Adapterを優先対象にします。
- [Write Defaults](https://vrc.school/docs/Unity-Animations/Write-Defaults/) と [Animator Controllers](https://vrc.school/docs/Unity-Animations/Animator-Controllers/) の注意点から、生成ControllerのWrite Defaults、mask、playable layer、Direct Blend Treeの組み合わせをBuild後に検査します。Cigarette、Magic Ray、Orbit、Outline Animation、Y Offsetが対象です。
- [Troubleshooting](https://vrc.school/docs/Avatars/Troubleshooting/) が案内するAvatar 3.0 Emulator / Gesture ManagerとPlay Mode外の切り分けを使い、Expressions Menu/Parameters、Contact、PhysBone、視点を順番に再現します。

## 解決計画

### Phase 0 — 今回完了した静的・配布整備

1. 13 source toolsのメニューを英語の機能名へ統一し、9/10統合方針とpackage 10 retirementをREADMEとindexへ反映。
2. Orbit/Eyeの警告とパス・リンク不整合を修正し、Orbit 0.2.0でEye WobbleをAvatar Reactive Motionへ分離（公開済みOrbit 0.1.2はimmutable保持）。
3. MCP ProjectのEye/Orbit package manifest、README、vpm-manifest.jsonをOrbit 0.2.0へ同期。
4. ZIP、index、SHA-256、GUID、asmdef、メニュー、決定性テストを再実行。

### Phase 1 — release CIの不足を埋める

1. source file、sidecar、metadata snapshotのcoverageとGUID driftを必須チェックにする。
2. 全latest packageを横断してasmdef名、GUID、MenuItemの衝突を検査する。retired package 10は公開衝突matrixから除外し、source側の重複検査は継続する。
3. package.jsonの依存範囲、asmdef参照、READMEの版表示、公開index/Release assetのSHAを一つの検証結果へまとめる。
4. Mesh Deform overlayを単独packageとしてcompileできる静的fixtureを追加する。

### Phase 2 — Unityを占有できる時の受入れ

1. MCP Projectを閉じた状態で新しいUnity 2022.3.22f1プロセスを1つだけ使い、package resolve/import/compileを確認。
2. Test Runnerの既存EditMode suiteをXMLで保存し、Pass/Fail/Skipped/Not Runを分離する。現在のREADMEにあるComposer 26/26、FaceTra 19/19などの過去結果を新しい結果として流用しない。
3. MCP/EditorEye由来のエラーを解消または除外し、Jeni assemblyだけのcompile logを保存。

### Phase 3 — NDMF/Editor受入れ

各toolについて、入力不備、Undo/Cancel、clone-only変更、生成物の所有権、再実行、既存名衝突を確認します。特にBuild-only pluginは、nested avatar拒否、parameter/menu重複、source signature、missing dependencyを失敗として記録します。

### Phase 4 — アバター・実機matrix

最低限、Humanoid/Generic、PC/Quest、スケール0.5/1.0/2.0、lilToon有無、VRCFT/FaceTra導入有無、既存MAレイヤー有無を組み合わせます。各ケースでDescriptor/Animator、Expressions、PhysBone/Contact、Material、視点、音声、同期、生成assetの元データ不変性を確認します。

### Phase 5 — 配布ガバナンス

1. patchは警告・文書・後方互換修正、minorは機能追加、majorはassembly/GUID/生成物移行を伴う変更と定義します。
2. package 10はretired状態を維持し、これを参照する既存プロジェクト向けにpackage 9 `0.2.x`への移行ガイドと自動検出を用意します。
3. 毎回、公開前にclean VCC projectへindexを登録し、12 published package IDs（13 source tools）のうち必要な組み合わせだけを追加してresolve・compile・Buildを確認します。

この文書の「静的判定」は、Unity Editor、NDMF、VRChat実機の合格を意味しません。Unity受入れが終わるまで、各toolの未検証範囲は NOT VERIFIED / NOT RUN のまま扱います。
