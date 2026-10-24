# Modification Map: external/inih

## Source: BlissOS android-x86 reference (external/inih)
- BlissOS commits: 8 commits (778c476 -> 26f2744, hmtheboy154)
- BlissOS fork HEAD: 26f2744 (upstream base of the fork: benhoyt/inih@577ae2d)
- KhillOS port commits: 014d6b6, 1f3b5f4, then post-reaudit fixes 5d71ba0 (merge), 1c88037, 6a0516d, 6569392
- Upstream base: benhoyt/inih@2bbdec4 (master, synced 2026-10-24; previously r62/26254ee — found stale by reaudit 2026-10-22)

## Modifications Applied to UPSTREAM/external_inih (khillos-17 branch)

### 1. Android.bp Build System (ported from BlissOS)
**File:** `Android.bp` (added by 014d6b6, corrected by 1f3b5f4, vendor_available restored by 6a0516d)
- Module types: `cc_library_shared` for libinih and libinih-cpp, `cc_library_headers` for inih_headers and libinih-cpp_headers — **matching the BlissOS reference 26f2744 exactly** (BlissOS used `cc_library_shared` throughout; it never used static `cc_library`).
- History (correcting earlier false statements in this file): our commit 014d6b6 introduced `cc_library` (static) — a deviation from the BlissOS reference introduced during the port, NOT a BlissOS trait. Our follow-up 1f3b5f4 fixed the module types to `cc_library_shared`, but in the same commit wrongly dropped `vendor_available: true`. Commit 6a0516d restored `vendor_available: true` on all four modules to match the BlissOS reference (android-generic/inih@26f2744).
- 4 modules, names matching BlissOS: libinih, libinih-cpp, inih_headers, libinih-cpp_headers.
- libinih-cpp compiles only cpp/INIReader.cpp and links `shared_libs: ["libinih"]` (matches the BlissOS HEAD de-duplication).
- Deliberate deviation from BlissOS, per port plan §licensing: the BlissLabs copyright comment header is replaced by a package-level `external_inih_license` license module (`SPDX-license-identifier-BSD-3-Clause` over LICENSE.txt), which is the AOSP-preferred way to declare package licensing. No functional difference.
- Cosmetic difference: property order in libinih-cpp differs from the BlissOS file (shared_libs before export_include_dirs); Soong-legal, no behavioral impact.

### 2. AOSP 17 Compliance Metadata Files (NEW)
**Files:** `METADATA`, `NOTICE`, `MODULE_LICENSE_BSD`, `OWNERS`
- METADATA: external_updater format; version 2bbdec4 (updated 6569392, was 26254ee), last_upgrade_date 2026-10-24, closest_version r62 (no tag newer than r62 exists).
- NOTICE: short BSD-3-Clause notice.
- MODULE_LICENSE_BSD: license marker file for Soong.
- OWNERS: standard AOSP owners file.

### 3. SPDX headers on source files — ADDED then REVERTED
**History (correcting earlier false statements in this file):** commit 1f3b5f4 added `/* SPDX-License-Identifier: LGPL-2.1-only */` comment blocks to 7 files (examples/ini_dump.c, examples/ini_example.c, examples/ini_xmacros.c, fuzzing/inihfuzz.c, tests/unittest.c, tests/unittest_alloc.c, tests/unittest_string.c). This was a **license mislabeling**: inih is BSD-3-Clause (LICENSE.txt and all core files), not LGPL-2.1-only. An earlier revision of this file falsely claimed the added identifier was "BSD-3-Clause" — that was wrong; the tree contained LGPL-2.1-only.
**Current state:** commit 1c88037 reverted the SPDX insertions entirely, restoring all 7 files byte-for-byte to upstream master (2bbdec4). No per-file SPDX headers are added on top of upstream. Package-level licensing is carried by the `external_inih_license` module, which satisfies AOSP license policy for the tree.

### 4. Upstream sync merge (BlissOS mods 2/5/7 parity)
BlissOS periodically syncs benhoyt/inih into their fork (sync merges 9bbb071, c37c343, 6aac755). KhillOS carries that same sync intent via merge commit 5d71ba0: khillos-17 merged upstream/master at 2bbdec4, bringing in the three commits the stale r62 base lacked:
- 8e06f6b — OOB read / stack-buffer-underflow fix in ini_parse_stream() when discarding an overlong line (security fix, Oct 2025)
- 577ae2d — NULL asserts on ini_parse_stream() args (Jan 2026)
- 2bbdec4 — overridable INI_MAX_SECTION / INI_MAX_NAME in ini.h (Sep 2026)
This puts the KhillOS base at the same commit the BlissOS fork is based on (577ae2d) or later; previously the port sat below the BlissOS fork base and shipped a parser missing the security fix BlissOS ships.

## Summary
- Total net delta vs upstream/master 2bbdec4: 7 added files (Android.bp, METADATA, NOTICE, MODULE_LICENSE_BSD, OWNERS, evidence/modification-map.md, evidence/nothing-else-disturbed.md). Zero upstream files modified (see nothing-else-disturbed.md).
- Upstream source files: pristine at 2bbdec4.
- Logic/behavior changes: none beyond the upstream sync itself.
