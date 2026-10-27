# Assignment 9 - Report

**Q1 vmprint.** Added `sys_vmprint` (syscall 22), `vmprint()` in vm.c. It loops over page-directory entries below KERNBASE (like freevm), uses walkpgdir() per page, and prints virtual address, physical address and P/W/U. It ends with the count of valid user pages and page-table pages (1, plus the directory). pttest: 3 pages at start (text/data, a guard page with U=0, stack), 7 after sbrk(4*4096): exactly 4 new pages, PASS.

**Q2 lazy allocation.** sys_sbrk now only increases `sz` for n>0 (shrinking still frees). copyuvm() skips never-allocated pages instead of panicking; deallocuvm() already skips non-present PTEs (commented). trap.c handles T_PGFLT: if rcr2() < sz and the page is not mapped, kalloc + memset + mappages(PTE_W|PTE_U), `pgfaults++`, resume; otherwise print pid and address and kill only that process. New `getfaults()` syscall. faulttest: sbrk(32*4096), touch 5 pages, 5 faults handled; a child touching an address above sz is killed, parent survives. mappages() was made non-static for use from trap.c.

**Q3 FIFO/LRU.** pagerepl.c simulates both, printing frames and hit/fault per reference. String 7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1, 3 frames: FIFO 15 faults, LRU 12 (matches expected). Table: 3 frames 15/12, 4 frames 10/8, 5 frames 9/7 (hit ratios 25/40, 50/60, 55/65 %).

All outputs in each question's `output/` folder come from real runs.
