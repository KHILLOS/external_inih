# Nothing Else Disturbed: external/inih

## Verification Method
Compared UPSTREAM/external_inih (khillos-17 branch, post-merge at 2026-10-24) against:
1. Upstream benhoyt/inih@master 2bbdec4 (current merge-base after merge commit 5d71ba0)
2. BlissOS android-x86 reference at external/inih (fork HEAD 26f2744, upstream base 577ae2d)

Command: `git diff --name-status upstream/master khillos-17` →
```
A	Android.bp
A	METADATA
A	MODULE_LICENSE_BSD
A	NOTICE
A	OWNERS
A	evidence/modification-map.md
A	evidence/nothing-else-disturbed.md
```
(Correcting an earlier revision of this file: the BlissOS reference HEAD is commit 26f2744, not 014d6b6 — 014d6b6 is a KhillOS port commit.)

## Source Files - Content Verification
(Correcting an earlier revision of this file, which falsely claimed all source files were identical to the BlissOS reference: before the 2026-10-24 sync, our ini.c was missing the OOB-read fix 8e06f6b and NULL asserts 577ae2d that the BlissOS reference carries — the port sat below the BlissOS fork base.)
After merge commit 5d71ba0 and SPDX revert 1c88037:
- `git diff upstream/master khillos-17 -- ini.c ini.h cpp/ README.md meson.build meson_options.txt examples/ tests/ fuzzing/ LICENSE.txt` → **empty**. All upstream source, example, test and fuzzing files are byte-identical to upstream master 2bbdec4 (which includes security fix 8e06f6b, asserts 577ae2d, INI_MAX_* 2bbdec4).
- The SPDX comment blocks that 1f3b5f4 had added to 7 example/test/fuzzing files were reverted in 1c88037; those files are again pristine upstream content.
- `git rev-list --left-right --count khillos-17...upstream/master` → `6 0`: zero upstream commits missing; the 6 on our side are port commits/merges only.

## Build System - Relation to BlissOS reference
| Property | BlissOS 26f2744 | KhillOS khillos-17 | Status |
|----------|----------------|--------------------|--------|
| Module types | cc_library_shared x2, cc_library_headers x2 | identical | match (014d6b6 regressed to cc_library, 1f3b5f4 fixed) |
| vendor_available | true on all 4 modules | true on all 4 modules | match (014d6b6 had it; 1f3b5f4 wrongly dropped it; 6a0516d restored) |
| libinih-cpp srcs | cpp/INIReader.cpp only, links libinih | identical | match |
| License declaration | BlissLabs copyright comment | external_inih_license module (BSD-3-Clause over LICENSE.txt) | documented deliberate deviation |
| Property order in libinih-cpp | export before shared_libs | shared_libs before export | cosmetic only |

The earlier revision of this file presented the vendor_available removal as a "correction" matching AOSP conventions; that claim was false — it matched neither the BlissOS reference nor the port's own upstream-analysis plan. It is restored.

## Added Files (AOSP 17 compliance + port evidence only)
- METADATA (version 2bbdec4, last_upgrade_date 2026-10-24, closest_version r62)
- NOTICE, MODULE_LICENSE_BSD, OWNERS
- evidence/modification-map.md, evidence/nothing-else-disturbed.md (this file)
No SPDX headers are added on top of upstream files (reverted by 1c88037; package-level license module covers compliance).

## Diff Summary
```
git diff --stat upstream/master khillos-17
 Android.bp                         | 36 +++++++++
 METADATA                           | 21 ++++++
 MODULE_LICENSE_BSD                 |  1 +
 NOTICE                             | 31 ++++++
 OWNERS                             |  1 +
 evidence/modification-map.md       | 35 ++++++
 evidence/nothing-else-disturbed.md  | 43 ++++++
 7 files changed, 168 insertions(+)
Files changed: 7 (all additions; zero modifications to upstream files)
Source logic changes vs upstream: 0
```

## Verification Output (2026-10-24)
- Host compile: `gcc -c ini.c` (gcc 13.3.0) → OK; `g++ -std=c++17 -I. -Icpp -c cpp/INIReader.cpp` → OK.
- Upstream unit tests: `bash tests/unittest.sh` regenerates all 14 baselines; `git status` clean afterwards → all outputs byte-identical to upstream baselines.
- ASAN: unittest suite under `-fsanitize=address` → 0 errors, output matches baselines.
- Security-fix proof (8e06f6b): with a harness that makes the discard loop read an empty abyss (embedded NUL at a 15-byte fgets chunk boundary, INI_MAX_LINE=20), the pre-fix r62 ini.c aborts with `AddressSanitizer: stack-buffer-underflow READ of size 1 ... ini_parse_stream ini.c:167`; the same harness against the merged tree runs clean (same return code, zero ASAN reports).

## Conclusion
**NOTHING ELSE DISTURBED** — the entire delta vs upstream master 2bbdec4 is the intended port surface: Android.bp (parity with BlissOS 26f2744 including vendor_available), AOSP compliance metadata, and port evidence files. No upstream file content is modified.
