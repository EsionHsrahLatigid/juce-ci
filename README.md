# JUCE CI

Reusable GitHub Actions workflows for CMake-based JUCE projects in the EsionHsrahLatigid organization.

All caller repositories are checked out with recursive git submodules. Pin reusable workflow calls to a full 40-character commit SHA.

## Workflows

### `juce-cmake.yml`

General Linux/macOS CMake configure, build, CTest, FetchContent cache, compiler cache, and optional artifact upload.

```yaml
jobs:
  test:
    uses: EsionHsrahLatigid/juce-ci/.github/workflows/juce-cmake.yml@<full-commit-sha>
    with:
      build-directory: build/ci
      cmake-options: -DMYPROJECT_BUILD_TESTS=ON
      build-targets: myproject_tests
```

### `plugin-ci.yml`

JUCE audio plug-in portfolio CI with:

- conservative path classification;
- macOS arm64 VST3, Standalone, and AU staging;
- Windows x64 VST3 and Standalone staging;
- `sccache` on macOS;
- CTest execution;
- latest platform ZIPs plus SHA-256 manifests;
- recursive submodule checkout.

The caller retains triggers, concurrency, permissions, and product inputs.

```yaml
jobs:
  ci:
    uses: EsionHsrahLatigid/juce-ci/.github/workflows/plugin-ci.yml@<full-commit-sha>
    permissions:
      contents: read
    with:
      product_name: MyPlugin
      product_slug: myplugin
      cmake_option_prefix: MYPLUGIN
      debug_targets_json: '["myplugin_dsp_tests"]'
      release_test_targets_json: '["myplugin_dsp_tests","myplugin_plugin_tests","myplugin_editor_tests"]'
```

### `plugin-release.yml`

Fail-closed release promotion. It rebuilds nothing: a `vX.Y.Z` tag must resolve to exactly one successful `main` push CI run for the same commit. The workflow verifies project/tag version equality, exact artifact IDs, SHA-256 manifests, ZIP integrity, and the final two-asset release set.

```yaml
jobs:
  release:
    uses: EsionHsrahLatigid/juce-ci/.github/workflows/plugin-release.yml@<full-commit-sha>
    permissions:
      actions: read
      contents: write
    with:
      product_name: MyPlugin
```

## Security and reproducibility

- Pin this repository and third-party actions to immutable commit SHAs.
- Caller permissions can only reduce the reusable workflow permissions.
- Release promotion accepts artifacts from one exact successful commit only.
- Submodule commits are resolved by the caller repository, not a floating branch.

## Licence

MIT. See [LICENSE](./LICENSE).
