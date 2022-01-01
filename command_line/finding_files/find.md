# GNU `find`

We often find ourselves searching for a particular file or set of files in a codebase. One of the most common ways this is done is through the `find` command.

In this guide, we'll be taking a look at some basic usage of the `find` command.

## Contact Information

- Email - CoffeeBeforeArch@gmail.com
- Twitter - @AcceleratorNick

# Basic Usage

The default behavior of the `find` command (when no options are provided) is to list all files and subdirectiories below the directory where the command was run.

```txt
cba@cba$ find
./dot_product
./dot_product/modern_double
./dot_product/modern_double/modern_double.cpp
./dot_product/tuned
./dot_product/tuned/tuned.cpp
./dot_product/modern
./dot_product/modern/modern.cpp
./dot_product/base
./dot_product/base/base.cpp
./peterson
./peterson/peterson_hw_barrier.cpp
./peterson/peterson.cpp
...
```

You can also pass a directory to `find` to dump all files and subdirectories below a specified starting point.

```txt
cba@cba$ find cmake_example/
cmake_example
cmake_example/PowersConfig.h.in
cmake_example/CMakeLists.txt
cmake_example/powers.cpp
cmake_example/local_functions
cmake_example/local_functions/exponent.cpp
cmake_example/local_functions/CMakeLists.txt
cmake_example/local_functions/exponent.h
```

# Filtering

We rarely want to dump all files and directory names. In this section, we'll look at the different ways we can filter the results of the `find` command.

## Filtering by File Type

When we are searching through files, it is often useful to be able to filter matches based on the file type. Two types we are often concerned with are regular files and directories. We can limit matches from the `find` command to one or more types using the `-type` option:

```txt
cba@cba$ find -type d
.
./dot_product
./dot_product/modern_double
./dot_product/tuned
./dot_product/modern
./dot_product/base
./peterson
./dod
./clamp
./strength_reduction
./task_group
./cmake_example
./cmake_example/local_functions
./inc_bench
./biased_branches
./sum_reduction
```

By specifying `-type d`, `find` only prints out directories (not regular files, symbolic links, etc.).

## Filtering by File Name

We often want to filter by more than just file type. In general, we are searching for files based on their name (or extension). We can do this with `find` using the `-name` option and passing a pattern we want to match (the exact file name `sorting.cpp` in this example):

```txt
 cba@cba$ find . -name "sorting.cpp"
./sorting/sorting.cpp
```

The `-name` option does matching using the `fnmatch` library function. This means that you can use metacharacters like the wildcard character (`*`). For example, we can find all C++ files with the `.cpp` extension using `*.cpp` as the input patters:

```txt
 cba@cba$ find . -name "*.cpp"
./dot_product/modern_double/modern_double.cpp
./dot_product/tuned/tuned.cpp
./dot_product/modern/modern.cpp
./dot_product/base/base.cpp
./peterson/peterson_hw_barrier.cpp
./peterson/peterson.cpp
./dod/dod.cpp
./clamp/clamp_bench.cpp
./strength_reduction/mod_bench.cpp
./task_group/task_group.cpp
./cmake_example/powers.cpp
./cmake_example/local_functions/exponent.cpp
./inc_bench/bad_inc.cpp
./inc_bench/inc_bench.cpp
./biased_branches/random.cpp
./sorting/sorting.cpp
...
```

Additionally, you can use the `-iname` option to filter by file name but in a case-insensitive manner:

```txt
 cba@cba$ find . -iname "*.CPP"
./dot_product/modern_double/modern_double.cpp
./dot_product/tuned/tuned.cpp
./dot_product/modern/modern.cpp
./dot_product/base/base.cpp
./peterson/peterson_hw_barrier.cpp
./peterson/peterson.cpp
./dod/dod.cpp
./clamp/clamp_bench.cpp
./strength_reduction/mod_bench.cpp
./task_group/task_group.cpp
./cmake_example/powers.cpp
./cmake_example/local_functions/exponent.cpp
./inc_bench/bad_inc.cpp
./inc_bench/inc_bench.cpp
./biased_branches/random.cpp
./sorting/sorting.cpp
...
```

Additionally you can also use `-regex`/`-iregex` to perform pattern matching using regular expressions instead of the default `fnmatch`/`glob` pattern matching.

## Combining Filters

We can combine filters using operators like `-and`, `-or`, `!`, etc.. For example, we can search for files starting with the name `fast` or starting with `sw_`:

```txt
cba@cba$ find . -name "fast*" -or -name "sw_*"
./code_scheduling/fast
./code_scheduling/fast/fast_random.cpp
./code_scheduling/fast/fast.cpp
./hw_barrier/sw_barrier.cpp
```

The default behavior is to `and` two filters (e.g., `-name "fast" -and -type f` and `-name "fast" -type f` are equivilant).
