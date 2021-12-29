# `jq` for JSON

A common format that many projects use for configuration files and dumping state is JSON. While it can be trivial to open/read some JSON files in something like a text editor, it is less possible/feasible for others.

JSON files may be hundreds of megabytes or gigabytes in size, making opening/searching them a time consuming process. Likewise, JSON files may be dumped without whitespace or newline characters for performance reasons, making reading them in their raw format difficult.

In this guide, we'll be looking at how to use the `jq` command to parse and filter JSON files from the command line.

## Contact Information

- Email - CoffeeBeforeArch@gmail.com
- Twitter - @AcceleratorNick

# Filters

## The Identity Filter

`jq` works by applying filters on a stream of JSON. The most simple fileter is the identity filter, `"."`, which will print the entirety of the JSON file contents. However, by default it will "pretty-print" the output (i.e., add whitespace and newline characters):

```txt
 cba@cba$ jq "." example.json
{
  "name": null,
  "sim_quantum": 0,
  "system": {
    "kernel": "",
    "mmap_using_noreserve": false,
    "kernel_addr_check": true,
    "membus": {
      "point_of_coherency": true,
      "system": "system",
      "response_latency": 2,
      "cxx_class": "CoherentXBar",
      "forward_latency": 4,
      "clk_domain": "system.clk_domain",
      "width": 16,
      "eventq_index": 0,
      "default_p_state": "UNDEFINED",
      "p_state_clk_gate_max": 1000000000000,
      "master": {
        "peer": [
          "system.physmem.port"
        ],
        "role": "MASTER"
      },
      "type": "CoherentXBar",
      "frontend_latency": 3,
      "slave": {
...
```

The `-c`/`--compact-output` option can be used to dump the contents of a JSON file w/o whitespace or newline characters.

```txt
cba@cba$ jq "." example.json -c
{"name":null,"sim_quantum":0,"system":{"kernel":"","mmap_using_noreserve":false,"kernel_addr_check":true,"membus":{"point_of_coherency":true,"system":"system","response_latency":2,"cxx_class":"CoherentXBar","forward_latency":4,"clk_domain":"system.clk_domain","width":16,"eventq_index":0,"default_p_state":"UNDEFINED","p_state_clk_gate_max":1000000000000,"master":{"peer":["system.physmem.port"],"role":"MASTER"},"type":"CoherentXBar","frontend_latency":3,"slave":{"peer":["system.system_port","system.cpu.l2cache.mem_side"],"role":"SLAVE"},"p_state_clk_gate_min":1000,"snoop_filter":{"name":"snoop_filter","system":"system","max_capacity":8388608,"eventq_index":0,"cxx_class":"SnoopFilter","path":"system.membus.snoop_filter","type":"SnoopFilter","lookup_latency":1},"power_model":null,"path":"system.membus","snoop_response_latency":4,"name":"membus","p_state_clk_gate_bins":20,"use_default_range":false},"symbolfile":"","readfile":"","thermal_model":null,"cxx_class":"System","work_begin_cpu_id_exit":-1,"load_offset":0,"work_begin_exit_count":0,"p_state_clk_gate_min":1000,"memories":["system.physmem"],"work_begin_ckpt_count":0,"clk_domain":{"name":"clk_domain","clock":[1000],"init_perf_level":0,"voltage_domain":"system.voltage_domain","eventq_index":0,
```

## Accessing Nested JSON Objects

We often want to do more than just dump the contents of a JSON file in its entirety. Instead we often want to access one or more parts of a JSON file. For this, we can index into the JSON structure using the `.foo` operator.

For example, here is me accessing the `.system.cpu` part of my JSON file:

```txt
cba@cba$ jq ".system.cpu" example.json 
[
  {
    "max_insts_any_thread": 0,
    "do_statistics_insts": true,
    "numThreads": 1,
    "fetch1LineSnapWidth": 0,
    "fetch1ToFetch2BackwardDelay": 1,
    "fetch1FetchLimit": 1,
    "executeIssueLimit": 2,
    "system": "system",
    "executeLSQMaxStoreBufferStoresPerCycle": 2,
    "icache": {
      "cpu_side": {
        "peer": "system.cpu.icache_port",
        "role": "SLAVE"
      },
      "clusivity": "mostly_incl",
      "prefetcher": null,
      "system": "system",
      "write_buffers": 8,
      "response_latency": 2,
...
```

Likewise, we can access parts of arrays using the array index operator, `.[#]`:

```txt
cba@cba$ jq ".[0]" cpu_array.json
{
  "max_insts_any_thread": 0,
  "do_statistics_insts": true,
  "numThreads": 1,
  "fetch1LineSnapWidth": 0,
  "fetch1ToFetch2BackwardDelay": 1,
  "fetch1FetchLimit": 1,
  "executeIssueLimit": 2,
  "system": "system",
  "executeLSQMaxStoreBufferStoresPerCycle": 2,
  "icache": {
    "cpu_side": {
      "peer": "system.cpu.icache_port",
      "role": "SLAVE"
    },
    "clusivity": "mostly_incl",
    "prefetcher": null,
    "system": "system",
    "write_buffers": 8,
    "response_latency": 2,
    "cxx_class": "Cache",
    "size": 131072,
...
```

## Combining Filters

Filters can be combined using the `|` operator. For example, we can combine our filter for an object identifier (e.g., `.system.cpu`) and the index of an array at that location (e.g., `.[0]`):

```txt
cba@cba$ jq ".system.cpu | .[0]" example.json 
{
  "max_insts_any_thread": 0,
  "do_statistics_insts": true,
  "numThreads": 1,
  "fetch1LineSnapWidth": 0,
  "fetch1ToFetch2BackwardDelay": 1,
  "fetch1FetchLimit": 1,
  "executeIssueLimit": 2,
  "system": "system",
  "executeLSQMaxStoreBufferStoresPerCycle": 2,
  "icache": {
    "cpu_side": {
      "peer": "system.cpu.icache_port",
      "role": "SLAVE"
    },
    "clusivity": "mostly_incl",
    "prefetcher": null,
    "system": "system",
    "write_buffers": 8,
...
```

Note, the filter `jq ".system.cpu[0]" example.json` is equivilant to the one used above.

# Special Functions

There are a number of builtin functions in `jq` to help with many tasks. In this section, we will take a look at a few of them that help with day-to-day tasks.

## Enumerating `keys`

We don't always know every key in a JSON object ahead of time. Luckily, we don't need to. We can use the `keys` function to query they keys in a JSON object.

```txt
cba@cba$ jq ". | keys" example.json
[
  "cxx_class",
  "eventq_index",
  "full_system",
  "name",
  "path",
  "sim_quantum",
  "system",
  "time_sync_enable",
  "time_sync_period",
  "time_sync_spin_threshold",
  "type"
]
```

## Finding Array `length`

We don't always know the length of arrays contained in our JSON ahead of time. However, we can find the length of these arrays using the `array` function:

```txt
cba@cba$ jq ".system.cpu | length" example.json
1
```
