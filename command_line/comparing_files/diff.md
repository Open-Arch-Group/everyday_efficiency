# Comparing Files with `diff`

A common task for software developers is comparing pieces of code, configuration files, etc.. Doing this manually can be time consuming and prone to errors. Tools like `diff` help make the process of comparing files easier.

In this guide, we'll take a look at a few basic ways we can use the `diff` command.

## Contact Information

- Email - CoffeeBeforeArch@gmail.com
- Twitter - @AcceleratorNick

# Basic Usage

We can compare two files by passing them as positional arguments to `diff`:

```txt
cba@cba$ diff inc_bench_1.cpp inc_bench_2.cpp
19c19
<   for (int i = 0; i < 100000; i++) {
---
>   for (int i = 0; i < 100; i++) {
```

The first line says which lines have differences, what category the difference falls in (changed, added, or deleted). In this example, `19` is the line number in the original file, `c` means that the line was changed, and `19` is the corresponding line number in the second file.

The next lines show the text differences. Lines starting with `<` correspond to the first file, `---` separates lines from the two files, and `>` marks lines from the second file.

If ther file has no difference, `diff` will not report anything by default:

```txt
cba@cba$ diff inc_bench_1.cpp inc_bench_2.cpp

```

However, you can use the `-s`/`--report-identical-files` option to tell `diff` to explicitly say that two files are identical.

```txt
cba@cba$ diff inc_bench_1.cpp inc_bench_2.cpp -s
Files inc_bench_1.cpp and inc_bench_2.cpp are identical
```

# Comparing With Context

It is often useful to view file differences with the surrounding context. `diff` gives us two modes with which to do this:

1. Unified
2. Copied

## Comparing with Unified Context

Unified context prints the modified lines next to each other with lines of the surrounding context (3 above and below the changes default). This can be enabled with the `-u`/`--unified` flag:

```txt
cba@cba$ diff inc_bench_1.cpp inc_bench_2.cpp -u
--- inc_bench_1.cpp	2021-12-28 18:54:55.333403259 -0600
+++ inc_bench_2.cpp	2021-12-29 14:46:48.680347588 -0600
@@ -16,7 +16,7 @@
 
 // Function to increment using a lock
 void inc_mutex(std::mutex &m, long long int &val) {
-  for (int i = 0; i < 100000; i++) {
+  for (int i = 0; i < 100; i++) {
     std::lock_guard<std::mutex> g(m);
     val++;
   }
```

The first two lines show the marking that will be used to differentiate between the files (`-` and `+` for the two files). The next line shows the starting line and number of total lines printed from each file (7 lines starting from line 16 for both files in this case) The next changed lines with the unified context. The lines that have been changed in some way are marked with a `-` and `+` next to them.

## Comparing with Copied Context

Compied context prints the modified lines separate from each other with context from their respective files (3 lines above and below by default). This can be enabled using the `-c`/`--context` flag:

```txt
cba@cba$ diff inc_bench_1.cpp inc_bench_2.cpp -c
*** inc_bench_1.cpp	2021-12-28 18:54:55.333403259 -0600
--- inc_bench_2.cpp	2021-12-29 14:46:48.680347588 -0600
***************
*** 16,22 ****
  
  // Function to increment using a lock
  void inc_mutex(std::mutex &m, long long int &val) {
!   for (int i = 0; i < 100000; i++) {
      std::lock_guard<std::mutex> g(m);
      val++;
    }
--- 16,22 ----
  
  // Function to increment using a lock
  void inc_mutex(std::mutex &m, long long int &val) {
!   for (int i = 0; i < 100; i++) {
      std::lock_guard<std::mutex> g(m);
      val++;
    }
```

The first two lines show the marking that will be used to differentiate between the two files (`***` and `---`). The next line (a series of `*`) marks where the comparison starts. What follows next are the differences in each file with the surrounding context. The total lines of context are listed for each section (e.g., lines 16 to 22), and lines with changes are marked with a `!`.

