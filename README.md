# Operating Systems - Assignment 9 (xv6)

Worked solution for three xv6 questions on virtual memory. Each question has its own folder with the kernel changes, a test program, a patch, and the real output from running it.

| Folder | Question | Test program | Result |
|---|---|---|---|
| [Q1_page_table](Q1_page_table) | `vmprint()` system call: print the user page table | `pttest.c` | 2 user pages at start, 6 after `sbrk(4*4096)`: 4 new |
| [Q2_lazy_allocation](Q2_lazy_allocation) | Lazy `sbrk()` and a page-fault handler, plus `getfaults()` | `faulttest.c` | 5 faults for 5 touched pages; bad access kills only the child |
| [Q3_page_replacement](Q3_page_replacement) | FIFO vs LRU page replacement | `pagerepl.c` | 3 frames: FIFO 15 faults, LRU 12 |

Also in this repo: [REPORT.md](REPORT.md), the one-page report.

## Folder layout

```
Qn_*/
  kernel/   full modified xv6 source files (changes marked "Assignment 9")
  user/     the test program
  xv6.patch patch against the base commit
  output/   terminal_output.txt and screenshot PNGs
```

## Run a question

Base: [mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public) commit `eeb7b41`. Apply one question's patch per fresh copy, because Q1 and Q2 change how `sbrk` behaves in the tests.

```
git clone https://github.com/mit-pdos/xv6-public
cd xv6-public && git checkout eeb7b41
patch -p1 < ../os-assignment-9/Q1_page_table/xv6.patch    # or Q2 / Q3
make qemu-nox
# at the xv6 prompt: pttest | faulttest | pagerepl
```

Q3 usage: `pagerepl` (demo), `pagerepl 3 7,0,1,2,0,3,0,4,2,3,0,3,2,1,2,0,1,7,0,1`, or `pagerepl table <refs>`. Use commas in the reference string, since the xv6 shell accepts only 10 arguments.

## How it was tested

Every question was built with the xv6 Makefile (`-Werror`) and run for real. The xv6 boot disk and file system were booted in the v86 x86 emulator (QEMU was not available in the build environment), and the xv6 serial console was captured. `output/terminal_output.txt` is that raw capture and the PNGs are renders of the same text. Q2 also passed `usertests`, and the Q3 algorithm was cross-checked as a normal Linux program. Nothing in `output/` is simulated.

## Notes

- A "valid user page" in Q1 is a present page with the user bit set. The stack guard page (present, not user) is printed and labelled but not counted.
- Q2 makes `mappages()` non-static so the handler in `trap.c` can call it.
