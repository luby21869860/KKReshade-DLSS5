# KKReshade DLSS5 + VideoExport compatibility build

This is a source modification of KKReshade v1.0.3 for the following capture path:

`ReShade finish_effects -> KKReshade -> KKReshade_Screenshot_SHM -> VideoExport`

## Changes

1. Uses ReShade's `effect_runtime::capture_screenshot()` instead of reading a staging texture before the copy has occurred. This avoids the old-frame capture ordering problem in the original v1.0.3 screenshot implementation.
2. Adds support for an `R16G16B16A16_FLOAT` back buffer, which is the format reported by the user's DLSS5 feed at 1920x1080.
3. Converts the FP16 linear/scRGB RGB channels to 8-bit sRGB for the existing VideoExport shared-memory contract. Alpha is copied as a clamped 8-bit value.
4. Keeps the existing SHM names and VideoExport control protocol unchanged.

## Build

The included `.github/workflows/build_and_release.yml` builds the project on `windows-latest` with MSBuild and produces:

`output/x64/Release/KKReshade.addon`

The user does not need Visual Studio locally if GitHub Actions is used.

## Important limitation

The VideoExport/KKReshade SHM protocol stores 8-bit RGBA (`COLOR_CHANNELS = 4`). Therefore this compatibility layer converts the FP16/scRGB frame to 8-bit. It does not preserve full HDR precision through the existing SHM protocol.
