# `grep`

One of the most common day-to-day tasks is searching through files. The most common way to do this from the command line is with `grep`.

In this guide, we will look at the basics of using `grep` for searching files.

## Contact Information

- Email - CoffeeBeforeArch@gmail.com
- Twitter - @AcceleratorNick

# Basic Usage

The most stimple way we can use `grep` is to search for a set of patterns in a set of files. For example, we can search for all the include directives in a C++ file:

```txt
cba@cba$ grep "#include" inc_bench.cpp 
#include <benchmark/benchmark.h>
#include <atomic>
#include <mutex>
#include <thread>
#include <vector>
```

## Case-Insensitive Seaching

We can can also search in a case-insensitive manner with the `-i`/`--ignore-case` option:

```txt
cba@cba$ grep "#InClUdE" inc_bench.cpp -i
#include <benchmark/benchmark.h>
#include <atomic>
#include <mutex>
#include <thread>
#include <vector>
```

## Line Number

You can also get the line number for each match with the `-n`/`--line-number` option:

```txt
cba@cba$ grep "#include" inc_bench.cpp -n
5:#include <benchmark/benchmark.h>
7:#include <atomic>
8:#include <mutex>
9:#include <thread>
10:#include <vector>
```

## Recursive Searching

`grep` is a useful tool for finding patterns in files even when we don't know the location of said files. Using the `-r`/`--recursive` flag. This instructs `grep` to search all files in the sub-directories underneath a specified starting point. This starting point replaces previously specified file location (by default, it will be your current working directory)

```txt
cba@cba$ grep "#include" -r
dot_product/modern_double/modern_double.cpp:#include <benchmark/benchmark.h>
dot_product/modern_double/modern_double.cpp:#include <algorithm>
dot_product/modern_double/modern_double.cpp:#include <cstdlib>
dot_product/modern_double/modern_double.cpp:#include <execution>
dot_product/modern_double/modern_double.cpp:#include <vector>
dot_product/tuned/tuned.cpp:#include <benchmark/benchmark.h>
dot_product/tuned/tuned.cpp:#include <immintrin.h>
dot_product/tuned/tuned.cpp:#include <cstdlib>
dot_product/tuned/tuned.cpp:#include <cstring>
dot_product/tuned/tuned.cpp:#include <vector>
dot_product/modern/modern.cpp:#include <benchmark/benchmark.h>
dot_product/modern/modern.cpp:#include <algorithm>
dot_product/modern/modern.cpp:#include <cstdlib>
dot_product/modern/modern.cpp:#include <execution>
dot_product/modern/modern.cpp:#include <vector>
...
```

## Context

It can be useful to print the surrounding context of a pattern we are matching in a file. `grep` gives us three flags for context line control:

1. `-A`/`--after-context` - Number of lines of context to print after a match
2. `-B`/`--before-context` - Number of lines of context to print before a match
3. `-C`/`--context` - Number of lines of context to print before and after a match

For example, here is an example of printing 3 lines of context before and after a match:

```txt
cba@cba$ grep "BENCHMARK(" inc_bench.cpp -C 3
    threads.clear();
  }
}
BENCHMARK(atomic_bench)
    ->DenseRange(1, std::thread::hardware_concurrency())
    ->Unit(benchmark::kMillisecond)
    ->UseRealTime();
--
    threads.clear();
  }
}
BENCHMARK(lock_guard_bench)
    ->DenseRange(1, std::thread::hardware_concurrency())
    ->Unit(benchmark::kMillisecond)
    ->UseRealTime();
```

The `--` markers are used to separate groups of matches of the specified pattern.
