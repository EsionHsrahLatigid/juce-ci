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

Fail-closed release promotion. It rebuilds nothing: a `vX.Y.Z` tag must resolve to exactly one successful `main` push CI run for the same commit. The workflow verifies project/tag version equality, exact artifact IDs, SHA-256 manifests, ZIP integrity, and the final two-asset release set. The macOS candidate is signed with Developer ID Application, notarized, stapled, and reverified before publication; the unsigned CI ZIP is never promoted as a public release asset.

```yaml
jobs:
  release:
    uses: EsionHsrahLatigid/juce-ci/.github/workflows/plugin-release.yml@<full-commit-sha>
    permissions:
      actions: read
      contents: write
    secrets:
      MACOS_CERTIFICATE_P12_BASE64: ${{ secrets.MACOS_CERTIFICATE_P12_BASE64 }}
      MACOS_CERTIFICATE_PASSWORD: ${{ secrets.MACOS_CERTIFICATE_PASSWORD }}
      APPLE_TEAM_ID: ${{ secrets.APPLE_TEAM_ID }}
      APPLE_API_KEY_ID: ${{ secrets.APPLE_API_KEY_ID }}
      APPLE_API_ISSUER_ID: ${{ secrets.APPLE_API_ISSUER_ID }}
      APPLE_API_PRIVATE_KEY_P8_BASE64: ${{ secrets.APPLE_API_PRIVATE_KEY_P8_BASE64 }}
    with:
      product_name: MyPlugin
```

Manual recovery callers may pass `tag_name: vX.Y.Z` to promote an existing semver tag from a non-tag trigger. Normal tag-push callers omit it and use `github.ref_name`.

Every caller repository must configure a protected `release` environment before enabling signing secrets. Apply tag/branch restrictions and required reviewers there. Keep the six signing values as organization or repository secrets mapped by the caller; do not duplicate same-named values as environment secrets. Runs for the same repository and tag are serialized and never cancel an in-flight notarization.

## Security and reproducibility

- Pin this repository and third-party actions to immutable commit SHAs.
- Caller permissions can only reduce the reusable workflow permissions.
- Release promotion accepts artifacts from one exact successful commit only.
- Signing secrets are explicitly mapped by name and are available only to the macOS signing job.
- Signing and publication jobs use the caller repository's protected `release` environment.
- Use an App Store Connect Team API key; Individual API keys are not accepted by `notarytool`.
- The temporary notarization ZIP is not a release asset. The workflow staples each bundle and creates a fresh public ZIP afterward.
- Submodule commits are resolved by the caller repository, not a floating branch.

## Licence

MIT. See [LICENSE](./LICENSE).
