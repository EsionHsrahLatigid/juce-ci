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

fail-closedなrelease昇格です。再buildは行いません。`vX.Y.Z` tagは、同じcommitに対する成功済み`main` push CI runが厳密に1件だけ存在する必要があります。Project/tag version、artifact ID、SHA-256、ZIP integrity、最終2 assetを検証します。

## セキュリティと再現性

- 本repositoryとthird-party actionはimmutableな完全SHAへ固定します。
- Caller権限はreusable workflowの権限を狭めることしかできません。
- Releaseは成功した単一の完全一致commitからのみ昇格します。
- Submodule commitはfloating branchではなくcaller repositoryで確定します。

## ライセンス

MIT。詳細は[LICENSE](./LICENSE)を参照してください。
