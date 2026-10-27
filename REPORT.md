# Assignment 9 - Report

**Q1 vmprint.** Added `sys_vmprint` (syscall 22), `vmprint()` in vm.c. It loops over page-directory entries below KERNBASE (like freevm), uses walkpgdir() per page, and prints virtual address, physical address and P/W/U. It ends with the count of valid user pages and page-table pages (1, plus the directory). A "valid user page" is present with PTE_U. The stack guard page (0x1000, present, U=0) is printed but marked guard and not counted. pttest: 2 user pages at start, 6 after sbrk(4*4096): exactly 4 new pages, PASS.

**Q2 lazy allocation.** sys_sbrk now only increases `sz` for n>0 (shrinking still frees). copyuvm() skips never-allocated pages instead of panicking; deallocuvm() already skips non-present PTEs (commented). trap.c handles T_PGFLT: if rcr2() < sz and the page is not mapped, kalloc + memset + mappages(PTE_W|PTE_U), `pgfaults++`, resume; otherwise print pid and address and kill only that process. New `getfaults()` syscall. faulttest: sbrk(32*4096), touch 5 pages, 5 faults handled; a child touching an address above sz is killed, parent survives. mappages() was made non-static for use from trap.c.

**Q3 FIFO/LRU.** pagerepl.c simulates both, printing frames and hit/fault per reference. String 7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1, 3 frames: FIFO 15 faults, LRU 12 (matches expected). Table: 3 frames 15/12, 4 frames 10/8, 5 frames 9/7 (hit ratios 25/40, 50/60, 55/65 %).

All outputs in each question's `output/` folder come from real runs.

## Build and run evidence
- Each question's patch was applied to a clean xv6-public checkout (eeb7b41) and built with the stock Makefile (`-Werror`): kernel, mkfs, fs.img and xv6.img all built with no errors.
- QEMU was not available. xv6.img (boot disk) and fs.img were booted in the v86 x86 emulator (Node). The xv6 serial console (UART) was captured byte by byte and commands were typed into it. The .txt files are that raw capture; the PNGs are renders of the same text, labelled as real runs.
- Q1 note: the guard page (0x1000, P=1 W=1 U=0) is shown, labelled guard, and excluded from the user count. Counts: 2 at start, 6 after sbrk(4*4096), so 4 new pages.
- Q3 was also cross-checked by compiling pagerepl.c on Linux (same 15/12).
- No illustrative or mock images were used. Everything shown was actually run.
