# Exercise 2 Report: Failure to Patch CVE-2009-3895 and CVE-2012-2836 in libexif 0.6.14

## Objective

Fuzz libexif 0.6.14 using AFL++ to reproduce and patch two known vulnerabilities:

- **CVE-2009-3895**: Heap-based buffer overflow
- **CVE-2012-2836**: Out-of-bounds read

## Setup Summary

- **Target Library**: libexif 0.6.14
- **Interface Program**: exif 0.6.15
- **Fuzzer**: AFL++ with `afl-clang-lto`
- **Environment**: Cloud-based Ubuntu 20.04 (no GUI)

## Fuzzing Results

- **12 unique crash samples** were generated.

<details>
<summary>GDB Backtrace</summary>

```
=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000000,sig:11,src:000000,time:5758,execs:5147,op:flip32,pos:1084 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:383
383	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:383
#1  0x000055555558098c in exif_data_load_data_content ()
#2  0x000055555557eedb in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000001,sig:11,src:000000,time:12881,execs:10770,op:arith32,pos:34,val:-9 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
0x000055555557ed36 in exif_data_load_data ()
#0  0x000055555557ed36 in exif_data_load_data ()
#1  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000002,sig:11,src:000002,time:98959,execs:73948,op:flip32,pos:707 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555590527 in exif_mnote_data_canon_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000003,sig:11,src:000002,time:98960,execs:73950,op:flip32,pos:719 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555590527 in exif_mnote_data_canon_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000004,sig:11,src:000002,time:106515,execs:79945,op:havoc,rep:3 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:436
436	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:436
#1  0x0000555555590527 in exif_mnote_data_canon_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000005,sig:11,src:000002,time:122871,execs:92337,op:havoc,rep:8 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:238
238	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:238
#1  0x0000555555590527 in exif_mnote_data_canon_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000006,sig:11,src:000002,time:131726,execs:99056,op:havoc,rep:3 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555590527 in exif_mnote_data_canon_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000007,sig:11,src:000002,time:166648,execs:125606,op:havoc,rep:8 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x000055555558098c in exif_data_load_data_content ()
#2  0x000055555557eedb in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000008,sig:11,src:000007,time:233066,execs:185299,op:inf,rep:3 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x000055555558098c in exif_data_load_data_content ()
#2  0x000055555557eedb in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000009,sig:11,src:000042,time:251751,execs:200447,op:havoc,rep:4 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555595560 in exif_mnote_data_olympus_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000010,sig:11,src:000042,time:254528,execs:202338,op:havoc,rep:3 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555595560 in exif_mnote_data_olympus_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()

=== Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000011,sig:11,src:000085,time:278517,execs:219082,op:havoc,rep:7 ===
Dwarf Error: DW_FORM_strx1 found in non-DWO CU [in module /root/fuzzing_libexif/install/bin/exif]

Program received signal SIGSEGV, Segmentation fault.
__memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
440	../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S: No such file or directory.
#0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
#1  0x0000555555595560 in exif_mnote_data_olympus_load ()
#2  0x000055555557fb3e in exif_data_load_data ()
#3  0x0000555555574300 in main ()
```
</details>

- Crashes confirmed via AFL++ output and manual inspection.

## Triage Challenges

1. **No GUI Debugging**  
   The official tutorial recommends Eclipse IDE for crash triage. However, my cloud host lacks GUI support, making Eclipse unusable.  
   I attempted to use `gdb` and `valgrind`, but without source-level guidance, it was difficult to pinpoint the exact crash lines.

2. **Patch Incompatibility**  
   The solution in [`Fuzzing101/Exercise2`](https://github.com/antonio-morales/Fuzzing101/tree/main/Exercise%202) references fixes not applicable to version 0.6.14.  
   I attempted manual patching based on crash behavior, but only **1 out of 12** samples stopped crashing.
   <details>
   <summary>GDB Backtrace</summary>

   ```
   === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000000,sig:11,src:000000,time:5758,execs:5147,op:flip32,pos:1084 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:383
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:383
        #1  0x00005555555809dd in exif_data_load_data_content ()
        #2  0x000055555557eeb0 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555809dd in exif_data_load_data_content ()
        378	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000001,sig:11,src:000000,time:12881,execs:10770,op:arith32,pos:34,val:-9 ===
        [Inferior 1 (process 1440907) exited with code 01]

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000002,sig:11,src:000002,time:98959,execs:73948,op:flip32,pos:707 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000003,sig:11,src:000002,time:98960,execs:73950,op:flip32,pos:719 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000004,sig:11,src:000002,time:106515,execs:79945,op:havoc,rep:3 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:436
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:436
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        431	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000005,sig:11,src:000002,time:122871,execs:92337,op:havoc,rep:8 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:238
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:238
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        233	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000006,sig:11,src:000002,time:131726,execs:99056,op:havoc,rep:3 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555904b7 in exif_mnote_data_canon_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000007,sig:11,src:000002,time:166648,execs:125606,op:havoc,rep:8 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555809dd in exif_data_load_data_content ()
        #2  0x000055555557eeb0 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555809dd in exif_data_load_data_content ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000008,sig:11,src:000007,time:233066,execs:185299,op:inf,rep:3 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555809dd in exif_data_load_data_content ()
        #2  0x000055555557eeb0 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555809dd in exif_data_load_data_content ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000009,sig:11,src:000042,time:251751,execs:200447,op:havoc,rep:4 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000010,sig:11,src:000042,time:254528,execs:202338,op:havoc,rep:3 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S

        === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000011,sig:11,src:000085,time:278517,execs:219082,op:havoc,rep:7 ===

        Program received signal SIGSEGV, Segmentation fault.
        __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #0  __memmove_avx_unaligned_erms () at ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:440
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        #2  0x000055555557fb13 in exif_data_load_data ()
        #3  0x0000555555574340 in main ()
        #1  0x00005555555954e0 in exif_mnote_data_olympus_load ()
        435	in ../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S
   ```

   </details>

3. **Validation with Latest Version**  
   I compiled libexif 0.6.25 and ran all 12 crash samples.  
   **Result**: None of the samples triggered a crash, confirming that both CVEs were patched in newer versions.
   <details>
   <summary>GDB Backtrace</summary>
   ```
   === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000000,sig:11,src:000000,time:5758,execs:5147,op:flip32,pos:1084 ===
    [Inferior 1 (process 1441186) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000001,sig:11,src:000000,time:12881,execs:10770,op:arith32,pos:34,val:-9 ===
    [Inferior 1 (process 1441192) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000002,sig:11,src:000002,time:98959,execs:73948,op:flip32,pos:707 ===
    [Inferior 1 (process 1441198) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000003,sig:11,src:000002,time:98960,execs:73950,op:flip32,pos:719 ===
    [Inferior 1 (process 1441204) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000004,sig:11,src:000002,time:106515,execs:79945,op:havoc,rep:3 ===
    [Inferior 1 (process 1441222) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000005,sig:11,src:000002,time:122871,execs:92337,op:havoc,rep:8 ===
    [Inferior 1 (process 1441228) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000006,sig:11,src:000002,time:131726,execs:99056,op:havoc,rep:3 ===
    [Inferior 1 (process 1441234) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000007,sig:11,src:000002,time:166648,execs:125606,op:havoc,rep:8 ===
    [Inferior 1 (process 1441240) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000008,sig:11,src:000007,time:233066,execs:185299,op:inf,rep:3 ===
    [Inferior 1 (process 1441255) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000009,sig:11,src:000042,time:251751,execs:200447,op:havoc,rep:4 ===
    [Inferior 1 (process 1441261) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000010,sig:11,src:000042,time:254528,execs:202338,op:havoc,rep:3 ===
    [Inferior 1 (process 1441267) exited with code 01]

    === Analyzing: /root/fuzzing_libexif/out/default/crashes/id:000011,sig:11,src:000085,time:278517,execs:219082,op:havoc,rep:7 ===
    [Inferior 1 (process 1441273) exited with code 01]


   ```

   </details>

## Conclusion

- Vulnerabilities were successfully reproduced in 0.6.14.
- Due to environment constraints and patching complexity, I was unable to fully fix both CVEs in the legacy version.
- Newer libexif versions (≥0.6.20) have resolved these issues, as verified by crash disappearance.
