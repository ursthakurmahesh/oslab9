# Operating Systems - Assignment 9 (xv6)

Worked solution, one folder per question. Base: mit-pdos/xv6-public, commit eeb7b415dbcb12cc362d0783e41c3d1f44066b17.

| Folder | Question | User program |
|---|---|---|
| Q1_page_table | vmprint() system call | pttest.c |
| Q2_lazy_allocation | Lazy sbrk + page-fault handler, getfaults() | faulttest.c |
| Q3_page_replacement | FIFO vs LRU | pagerepl.c |

Each folder has `kernel/` (full modified files, every change marked "Assignment 9"), `user/`, `xv6.patch` (against the base commit), and `output/` (terminal output and screenshots).

## Run it
```
git clone https://github.com/mit-pdos/xv6-public && cd xv6-public
git checkout eeb7b41
patch -p1 < ../os-assignment-9/Q1_page_table/xv6.patch   # or Q2 / Q3
make qemu-nox      # then run: pttest | faulttest | pagerepl
```
Apply one question's patch at a time. Q1 and Q2 both change sbrk behaviour tests, so they are kept separate (with lazy sbrk, Q1's "four new pages" would not appear until touched).

## How it was tested
All three were built with the xv6 Makefile (-Werror) and booted and run in a real x86 emulator (v86), not QEMU, because QEMU was not available here. The output files and screenshots are rendered from the captured serial console of those real runs. They are text renders, not photos of a window. Q2 also ran usertests ("ALL TESTS PASSED") and Q3's algorithm was cross-checked on a Linux host.
