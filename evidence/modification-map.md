# Modification Map: external/inih

## Source: BlissOS android-x86 reference (external/inih)
- BlissOS commits: 8 commits (778c476 -> 26f2744, hmtheboy154)
- UPSTREAM commit: 014d6b6 (KhillOS port)
- Upstream base: benhoyt/inih@r62 (tag 26254ee)

## Modifications Applied to UPSTREAM/external_inih (khillos-17 branch)

### 1. Android.bp Build System (MODIFIED from BlissOS)
**File:** `Android.bp` (modified from BlissOS version)
- BlissOS used `cc_library` (static) for libinih and libinih-cpp
- **FIXED:** Changed to `cc_library_shared` (shared libraries) to match upstream expectations
- Removed `vendor_available: true` from all modules (inih is a general-purpose library, not vendor-specific)
- Kept 4 modules: libinih, libinih-cpp, inih_headers, libinih-cpp_headers
- Package-level license configuration (BSD-3-Clause)

### 2. AOSP 17 Compliance Metadata Files (NEW)
**Files:** `METADATA`, `NOTICE`, `MODULE_LICENSE_BSD`, `OWNERS`
- METADATA: Package metadata per AOSP external_updater format
- NOTICE: Short license notice
- MODULE_LICENSE_BSD: SPDX identifier for Soong license module
- OWNERS: Standard AOSP OWNERS file

### 3. SPDX Headers to Source Files
**Files:** 8 files (examples/, tests/, fuzzing/)
- Added `/* SPDX-License-Identifier: BSD-3-Clause */` after first comment block
- Core files (ini.c, ini.h, cpp/INIReader.h) already had SPDX headers
- Applied to: examples/ini_xmacros.c, examples/ini_dump.c, examples/ini_example.c, tests/unittest_alloc.c, tests/unittest_string.c, tests/unittest.c, fuzzing/inihfuzz.c

## Summary
- **Total files modified:** 12 (4 new metadata + 1 Android.bp fix + 8 SPDX headers)
- **Source files unchanged:** Yes (only SPDX headers added to test/example files)
- **Logic/behavior changes:** None
- **Build system changes:** cc_library → cc_library_shared (shared libraries), removed vendor_available