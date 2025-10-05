# CVE-2019-13288 Fix Report: Infinite Recursion Vulnerability in Xpdf 3.02

## Vulnerability Description
In Xpdf 3.02, the PDF object parsing logic contains uncontrolled recursive calls between `Parser::getObj()` and `XRef::fetch()`. When objects in a PDF file form circular references, the parser repeatedly invokes these functions, eventually causing a stack overflow and triggering a `SIGSEGV`

---

## Reproduction Steps

1. Fuzzing Xpdf 3.02 using AFL++ revealed a crashing sample:
```
(gdb) run 
Starting program: 
/root/fuzzing_xpdf/install/bin/pdftotext 
/root/fuzzing_xpdf/out/default/crashes/id:000000,sig:11,src:000001,time:216738,execs:134879,op:havoc,rep:8 output.txt 
Error (3616): Illegal character <3f> in hex string 
Error (3617): Illegal character <78> in hex string 
Error (3618): Illegal character <70> in hex string 
Error (3621): Illegal character <6b> in hex string 
Error (3623): Illegal character <74> in hex string 
Error (3626): Illegal character <6e> in hex string 
Error (3628): Illegal character <3d> in hex string 
Error (3629): Illegal character <27> in hex string 
Error (3630): Illegal character <77> in hex string 
Error (3631): Illegal character <27> in hex string 
Error (3632): Illegal character <3f> in hex string 
Error (3643): Missing 'endstream' 

Program received signal SIGSEGV, Segmentation fault.
```
2. Debug `pdftotext` using GDB and observe the call stack:
<details>
<summary>GDB Backtrace</summary>

```
(gdb) bt 
#0 0x00007ffff7b17e4e in _int_malloc (av=av@entry=0x7ffff7c6cb80 <main_arena>, bytes=bytes@entry=4) at malloc.c:3679 
#1 0x00007ffff7b1a154 in __GI___libc_malloc (bytes=4) at malloc.c:3058 
#2 0x00005555556694b5 in copyString () 
#3 0x000055555561e76a in Lexer::getObj(Object*) () 
#4 0x00005555556276ea in Parser::getObj(Object*, unsigned char*, CryptAlgorithm, int, int, int) () 
#5 0x000055555565a3d2 in XRef::fetch(int, int, Object*) () 
#6 0x0000555555628834 in Parser::makeStream(Object*, unsigned char*, CryptAlgorithm, int, int, int) () 
#7 0x000055555562825d in Parser::getObj(Object*, unsigned char*, CryptAlgorithm, int, int, int) () 
#8 0x000055555565a72b in XRef::fetch(int, int, Object*) () 
#9 0x0000555555628834 in Parser::makeStream(Object*, unsigned char*, CryptAlgorithm, int, int, int) ()
...
```
</details>
3. The sample contains circular object references, triggering infinite recursion and a stack overflow that leads to a crash

---

## Fix Implementation
Add recursive depth limit in `XRef:: fetch()`:

- In `XRef.h`, define a member variable and a depth limit:
```cpp
#define MAX_DEPTH 1000
int recursionDepth;
```
- Initialize recursionDepth to 0 in the constructor.
- In `XRef::fetch()`, increment and check the depth:
```cpp
recursionDepth++;
if (recursionDepth > MAX_DEPTH) {
  error(-1, "Too much recursion in XRef::fetch()");
  recursionDepth--;
  return obj->initNull();
}
```
- Decrement `recursionDepth` before both normal return and error exit.

## Verification
Run the patched `pdftotext` binary：
```
$HOME/fuzzing_xpdf/install/bin/pdftotext CVE-2019-13288-poc.pdf
```
Output:
```
Error (3616): Illegal character <3f> in hex string
Error (3617): Illegal character <78> in hex string
Error (3618): Illegal character <70> in hex string
Error (3621): Illegal character <6b> in hex string
Error (3623): Illegal character <74> in hex string
Error (3626): Illegal character <6e> in hex string
Error (3628): Illegal character <3d> in hex string
Error (3629): Illegal character <27> in hex string
Error (3630): Illegal character <77> in hex string
Error (3631): Illegal character <27> in hex string
Error (3632): Illegal character <3f> in hex string
Error (3643): Missing 'endstream'
Error: Too much recursion in XRef::fetch()
Error (834): Bad 'Length' attribute in stream
...
Error (834): Bad 'Length' attribute in stream
Error: Weird page contents
```
No crash occurred. The `SIGSEGV` is gone, confirming the fix is effective.