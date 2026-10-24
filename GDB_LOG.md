# GDB log — Week 10 (Lab 5)

## Part A: `buggy_rooms.cpp`

The original `move()` returns a null pointer when there is no exit east from the pantry. The next call to `describe()` dereferences that null pointer and crashes. This is the expected diagnosis from inspecting the code, **not a recorded GDB session** (GDB is unavailable in this environment).

Run this in Ubuntu to record the required real session:

```text
cmake --build build --target buggy_rooms
gdb ./build/buggy_rooms
(gdb) break main
(gdb) run
(gdb) next
(gdb) step
(gdb) continue
(gdb) bt
(gdb) frame 0
(gdb) print room
```

**Paste your actual output here:**

- Backtrace (`bt`): [paste output]
- Function and source line (`frame N`): [paste output]
- Crashing pointer (`print room`): [paste output; expected null]
- `break main`, `run`, `next`, `step`: [paste session and describe each]

**Fix:** I added a null check to `move()` and a check before `describe(current)` in the loop. When no exit exists, the program prints `No exit in that direction.` and stops walking. To verify, run `./build/buggy_rooms` and confirm that it exits normally.

## Part B: AddressSanitizer

The following are **real AddressSanitizer observations** from compiling the original `gdb/deck_bugs.cpp` with `-g -fsanitize=address` before applying fixes. Line numbers refer to the original file.

| Bug | Report kind | Allocated at | Freed at | Cause |
| --- | --- | --- | --- | --- |
| 1 | LeakSanitizer: 416 bytes leaked in one allocation | `deal_a_hand()`, line 40 | Never freed | `hand` is allocated with `new[]` but not deleted. |
| 2 | AddressSanitizer: attempting double-free | `RawDeck::RawDeck()` (called at line 47) | `RawDeck::~RawDeck()` twice, during `copy_a_deck()` at line 50 | Default copy constructor copies the pointer, so both objects own the same array. |
| 3 | AddressSanitizer: attempting double-free | `clear_a_table()`, line 54 | `clear_a_table()`, lines 57 and 59 | The same `table` pointer is deleted twice. |

### Actual report excerpts

```text
Bug 1: ERROR: LeakSanitizer: detected memory leaks
SUMMARY: AddressSanitizer: 416 byte(s) leaked in 1 allocation(s).

Bug 2: ERROR: AddressSanitizer: attempting double-free
... RawDeck::~RawDeck() ... copy_a_deck()

Bug 3: ERROR: AddressSanitizer: attempting double-free
... clear_a_table() (original lines 57 and 59)
```

### GDB confirmation — complete on your Ubuntu machine

GDB was not installed in the environment used to prepare this project, so **no GDB output is fabricated here**. To confirm bug 3, build the *original* buggy version from your initial ZIP separately (the included version is already fixed), then run:

```text
gdb ./deck_bugs_original
(gdb) break clear_a_table
(gdb) run 3
(gdb) next
(gdb) watch table
(gdb) x/5gx table
(gdb) finish
(gdb) bt
```

Record the real GDB output and explain: `watch` watches changes to the pointer variable, `x` examines memory at its address, and `finish` runs until the selected function returns (if it can). A watchpoint on `table` does not detect `delete[]` itself, because deletion does not necessarily change the pointer value. For a more direct confirmation, set a breakpoint at the first `delete[] table` and step to the second delete.

**Paste your real GDB session and explanation here:** [to be completed]

### Fixes

1. Added `delete[] hand;` before returning from `deal_a_hand()`.
2. Implemented a deep-copy constructor and assignment operator for `RawDeck`, giving each copy its own heap array.
3. Removed the second `delete[] table;` call.

**Verification:** All three fixed modes (`1`, `2`, `3`) ran without AddressSanitizer error reports in the local test environment.
