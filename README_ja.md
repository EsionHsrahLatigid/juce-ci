# JUCE CI

EsionHsrahLatigid組織のCMakeベースJUCE project向け再利用GitHub Actionsです。

すべてのcaller repositoryを再帰git submodule付きでcheckoutします。Reusable workflowは必ず40文字の完全なcommit SHAへ固定してください。

## Workflow

### `juce-cmake.yml`

Linux/macOS向けの汎用CMake configure、build、CTest、FetchContent cache、compiler cache、任意artifact uploadです。

### `plugin-ci.yml`

JUCE audio plugin portfolio向けCIです。

- 保守的な変更path分類。
- macOS arm64のVST3、Standalone、AU staging。
- Windows x64のVST3、Standalone staging。
- macOSの`sccache`。
- CTest実行。
- latest platform ZIPとSHA-256 manifest。
- 再帰submodule checkout。

Trigger、concurrency、権限、製品inputはcaller側に残します。

### `plugin-release.yml`

fail-closedなrelease昇格です。再buildは行いません。`vX.Y.Z` tagは、同じcommitに対する成功済み`main` push CI runが厳密に1件だけ存在する必要があります。Project/tag version、artifact ID、SHA-256、ZIP integrity、最終2 assetを検証します。macOS候補はDeveloper ID Applicationで署名し、公証・staple・再検証してから公開します。未署名のCI ZIPを公開releaseへ昇格しません。

Callerは`MACOS_CERTIFICATE_P12_BASE64`、`MACOS_CERTIFICATE_PASSWORD`、`APPLE_TEAM_ID`、`APPLE_API_KEY_ID`、`APPLE_API_ISSUER_ID`、`APPLE_API_PRIVATE_KEY_P8_BASE64`を明示的なnamed secretsとして渡します。

手動復旧用のcallerは、tag trigger以外から既存semver tagを昇格するために`tag_name: vX.Y.Z`を渡せます。通常のtag push callerは省略し、`github.ref_name`を使います。

署名secretを有効化する前に、各caller repositoryへ保護された`release` environmentを設定します。tag/branch制限とrequired reviewerを設定してください。6つの署名値はcallerが明示的に渡すorganization/repository secretとして保持し、同名のenvironment secretを重複定義しません。同じrepository/tagのrunは直列化し、進行中の公証をcancelしません。

## セキュリティと再現性

- 本repositoryとthird-party actionはimmutableな完全SHAへ固定します。
- Caller権限はreusable workflowの権限を狭めることしかできません。
- Releaseは成功した単一の完全一致commitからのみ昇格します。
- 署名secretは名前を明示して渡し、macOS署名jobだけで利用します。
- 署名jobと公開jobはcaller repositoryの保護された`release` environmentを使います。
- `notarytool`にはApp Store ConnectのTeam API keyを使用します。Individual API keyは利用できません。
- 公証提出用の一時ZIPはrelease assetにしません。各bundleをstapleした後、新しい公開ZIPを作成します。
- Submodule commitはfloating branchではなくcaller repositoryで確定します。

## ライセンス

MIT。詳細は[LICENSE](./LICENSE)を参照してください。
