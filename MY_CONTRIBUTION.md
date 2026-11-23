# My First Open-Source Contribution: GoogleTest

I'm [@ammarazizdev](https://github.com/ammarazizdev), and this is a record of
my first contribution to [google/googletest](https://github.com/google/googletest),
Google's C++ testing framework, used by thousands of C++ projects (Chromium,
LLVM, OpenCV and many more).

> This file lives only on the `my-contribution-journey` branch of my fork. It is
> **not** part of the pull request.

## What I contributed

| | |
|---|---|
| **Issue** | [#3299: Missing documentation for InitGoogleMock](https://github.com/google/googletest/issues/3299) (open since 2021) |
| **Type** | Documentation fix |
| **My branch** | [`docs-init-google-mock`](https://github.com/ammarazizdev/googletest/tree/docs-init-google-mock) |
| **Files changed** | `docs/gmock_cook_book.md`, `docs/gmock_cheat_sheet.md` |

### The problem

gMock has command-line flags such as `--gmock_verbose=error`, which change how
much gMock prints. The docs explained these flags, but they never said that the
flags only work if your test's `main()` calls `testing::InitGoogleMock()` (or
links with `gmock_main`). Many people write `main()` with
`testing::InitGoogleTest()`, and then the gMock flags are **silently ignored**.

### How I proved it

I didn't just read the code. I built a tiny test program in two versions and
ran both with `--gmock_verbose=error`:

| `main()` calls | `GMOCK WARNING` lines printed | Flag worked? |
|---|---|---|
| `InitGoogleTest()` | 1 | ❌ ignored |
| `InitGoogleMock()` | 0 | ✅ yes |

Reading `gtest.cc` also showed one exception: if GoogleTest is built with
**Abseil Flags**, every flag is parsed, so I worded the docs carefully to stay
accurate.

### The fix

- Added a note to the "Controlling How Much Information gMock Prints" section of
  the gMock Cookbook, with a short `main()` example.
- Added a short note under the Flags table in the gMock Cheat Sheet.

## The flow I followed

```
 Fork on GitHub ─► Clone ─► Set up tools ─► Build & run all tests
                                                     │
   ┌─────────────────────────────────────────────────┘
   ▼
 Find an issue nobody is working on ─► Comment on the issue ─► Sign Google CLA
                                                                    │
   ┌────────────────────────────────────────────────────────────────┘
   ▼
 New branch ─► Make the change ─► Verify it's true ─► Re-run all tests
                                                            │
   ┌────────────────────────────────────────────────────────┘
   ▼
 Commit ─► Push to my fork ─► Open pull request ─► Respond to review ─► Merged 🎉
```

### Step by step

1. **Fork**: on GitHub, click **Fork** on `google/googletest` to get my own
   copy at `ammarazizdev/googletest`.
2. **Clone and connect to the original:**
   ```bash
   git clone https://github.com/ammarazizdev/googletest.git
   cd googletest
   git remote add upstream https://github.com/google/googletest.git
   ```
   `origin` is my fork (I can push there). `upstream` is Google's repo (used to
   get the latest changes).
3. **Set up tools (Windows):** Git, Python 3, CMake 4.4 and the Microsoft C++
   compiler (Visual Studio Build Tools). GoogleTest now needs **C++17** and
   **CMake 3.16+**.
4. **Build and run every test before changing anything**, to know the starting
   point is healthy:
   ```bash
   cmake -S . -B mybuild -Dgtest_build_tests=ON -Dgmock_build_tests=ON
   cmake --build mybuild --config Debug -j 4
   ctest --test-dir mybuild -C Debug --output-on-failure
   ```
   Result: **62 / 62 tests passed**.
5. **Learn the basics**: `TEST` (a standalone test), `TEST_F` (a test that uses a
   shared setup, called a "fixture"), `EXPECT_*` (record the failure and keep
   going) and `ASSERT_*` (record the failure and stop the test).
6. **Find a good first issue.** My first ideas (an outdated CMake version in
   `CONTRIBUTING.md`, and a list of typos) already had open pull requests from
   other people, so I skipped them to avoid duplicate work. Issue #3299 was
   still a real gap: `InitGoogleMock` wasn't mentioned anywhere in `docs/`.
7. **Create a branch:** `git checkout -b docs-init-google-mock`
8. **Make the change**, then **verify it is true** with code reading and a real
   experiment (above).
9. **Re-run all tests**: 62 / 62 still passing.
10. **Commit** with a clear message that says `Fixes #3299`, then **push**:
    ```bash
    git push -u origin docs-init-google-mock
    ```
11. **Open the pull request** from my branch to `google/googletest:main`, linking
    the issue.
12. **Sign the Google CLA** (Contributor License Agreement). Google can't accept
    any code or docs without it.

## What I learned

- **Check before you start.** Search issues *and* pull requests first. Two of my
  first ideas were already being fixed by someone else.
- **Docs fixes are real contributions**, and they must be *correct*. Testing my
  claim found a case (Abseil Flags) I would otherwise have got wrong.
- **One change per pull request** makes it easy for maintainers to review.
- **Keep your fork's `main` clean.** Do work on branches, and keep personal
  notes (like this file) out of the pull request.
- The Git workflow: fork → clone → branch → commit → push → PR.

## Status

- [x] Fork, clone, build, all tests passing
- [x] Fix written, verified and pushed to my fork
- [ ] Comment on issue #3299
- [ ] Sign the Google CLA
- [ ] Open the pull request
- [ ] Review and merge
