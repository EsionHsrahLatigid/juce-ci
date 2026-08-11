# JUCE CI

Reusable GitHub Actions workflows for JUCE CMake projects in the EHL organization.

## Current workflows

- `juce-dsp-tests.yml`: cached CMake configure/build/test for JUCE-oriented DSP or plugin projects.

## Example

```yaml
jobs:
  juce-dsp-tests:
    uses: EsionHsrahLatigid/juce-ci/.github/workflows/juce-dsp-tests.yml@v1
    with:
      runs-on: ubuntu-latest
      source-directory: .
      build-directory: build/dsp
      build-type: Release
      cmake-configure-args: >-
        -DPLITCH_BUILD_PLUGIN=OFF
        -DPLITCH_BUILD_TESTS=ON
      run-tests: true
```
