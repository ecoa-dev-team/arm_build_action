# ARM Embedded CMake Build & Artifact Workflow

A GitHub Actions workflow designed to automate the compilation, binary management, and artifact archiving for ARM Cortex-M/R embedded projects using **CMake**, **Ninja**, and the **GNU Arm Embedded Toolchain** (`gcc-arm-none-eabi`).

---

## 📌 Overview

This workflow triggers on code pushes and performs the following tasks:
1. **Environment Setup:** Installs CMake, Ninja, and the ARM GCC cross-compiler (`gcc-arm-none-eabi`).
2. **Branch Sanitization:** Converts forward slashes `/` in branch names into hyphens `-` to safely name output files.
3. **Dual Compilation:** Builds both **Debug** and **Release** target presets configured in `CMakePresets.json`.
4. **Conditional Binary Renaming:** Automatically prefixes `.bin` artifacts with the sanitized branch name for feature and release-tracking branches (`feature/*`, `FEATURE/*`, `EHD/*`).
5. **Artifact Publishing:** Uploads generated `.bin` build files to GitHub Actions workflow run artifacts for specific target branches (`Develop`, `feature/*`, `EHD/*`, etc.).

---

## 🛠️ Prerequisites

To run this workflow successfully in your repository:

1. **`CMakePresets.json` Configuration:**
   Your repository must contain a valid `CMakePresets.json` file at the root containing configure and build presets named `Debug` and `Release`.

   *Example snippet:*
   ```json
   {
     "version": 3,
     "configurePresets": [
       { "name": "Debug", "generator": "Ninja", "binaryDir": "${sourceDir}/build/Debug" },
       { "name": "Release", "generator": "Ninja", "binaryDir": "${sourceDir}/build/Release" }
     ],
     "buildPresets": [
       { "name": "Debug", "configurePreset": "Debug" },
       { "name": "Release", "configurePreset": "Release" }
     ]
   }
   ```

2. **Toolchain Integration:**
   Ensure your CMake configuration correctly selects the `arm-none-eabi-gcc` toolchain via a toolchain file or CMake variables.

---

## ⚙️ Trigger Logic & Branch Filters

### File Renaming & Branch Filter Rules

Renaming binaries and uploading artifacts apply conditionally to keep workflow runs clean and prevent cluttering artifact storage:

* **Feature Binary Renaming Target Prefixes:**
  * `feature/*`
  * `FEATURE/*`
  * `EHD/*`

* **Artifact Upload Target Branches:**
  * `Develop`
  * Any branch starting with `feature/`, `FEATURE/`, or `EHD/`

---

## 📂 Output Artifacts

Upon successful completion, the workflow generates two zip packages downloadable directly from the GitHub Actions run details page:

| Artifact Name | Description | Path Match Pattern |
| :--- | :--- | :--- |
| `build-artifacts-Debug` | Compiled binaries built using the `Debug` preset | `build/Debug/**/*.bin` |
| `build-artifacts-Release` | Compiled binaries built using the `Release` preset | `build/Release/**/*.bin` |

---
