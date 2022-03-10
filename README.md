# Demonstrate the Bytelines iterator for logreduce

The goals of this iterator are:

- Split sub line to handle cmd output embedded as a long oneliner.
- Work with Read object, such as file decompressors or network endpoints.
- CPU and Memory efficient.

Here are some benchmark implementation:

- [readline.py](./readline.py): Python readline
- [readline.rs](./readline.rs): Rust read_line
- [byteslines.rs](./byteslines.rs): Zero copy implementation using [Bytes](https://docs.rs/bytes/):

Using /bin/time and valgrind to measure performance with a 91MB journal.txt file:

```
$ wc journal.txt
  266400  6408707 90348877 journal.txt
$ /bin/time --format "Run in %e sec, Max RSS: %M KB" true
Run in 0.00 sec, Max RSS: 1988 KB
$ valgrind true |& grep "total heap usage"
   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
```

## Results:

| Implementation | Max RSS  | Allocs    | Frees     | Bytes allocated | Run time |
|----------------|----------|-----------|-----------|-----------------|----------|
| readline.py    | 11280 KB | 1,814,409 | 1,810,434 |     475,434,838 | 0.49 sec |
| readline.rs    |  2260 KB |   692,114 |   692,112 |     285,799,923 | 0.15 sec |
| byteslines.rs  |  2068 KB |        24 |        22 |         265,577 | 0.12 sec |