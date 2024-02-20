# Concurrent Hashmap Parallel Implementation

## Summary
This commit contains the code implementation of the logram alogrithm with parellelization.
More specificially, this is the implementation that uses a concurrent hash map crate to replace
the double and triple gram dictionaries.

## Tech Details
This implementation was almost identical to the seperate mapping implementation. The biggest difference between these implementations is that
we are now using Dashmap which is a concurrent hashmap. To pass it into each thread, we put our dashmap in a Arc which gets cloned for each thread. Doing this allows us to create another handle to access our hashmap. This mens that changes made to the cloned DashMap will also be reflected in the original dashmap. Slight tweaks were made to the input types of process_dictionary_builder_line such that this function can now accept Arc<DashMap<String, i32>> instead of HashMap<String, i32>.
 
## Testing (Correctness)
The first form of testing was completed by running the standard commands that were provided in the readme of this repo.
Some with --num-threads, some with -- single-mapping, and some without either. The outputs were copied and compared using pretty print. 

## Testing (Performance)
For this specific project, it makes sense to use latency instead of bandwidth to measure the performance of our programming. In order to this, I employed a timer to measure the time it takes for the process to start and end. We tested two cases against each other and measured the time it took to complete each process:
(1) Concurrent Hashmap
(2) Seperate Mapping w/ 1 thread => Essentially Sequential / baseline model

### Case #1: --raw-hpc data/HPC_2k.log --to-parse "inconsistent nodesets node-31 0x1fffffffe <ok> node-0 0xfffffffe <ok> node-1 0xfffffffe <ok> node-2 0xfffffffe <ok> node-30 0xfffffffe <ok>" --before "running running"

Test #1

Concurrent Mapping Duration: 19.397364ms

Seperate Mapping (1 Thread) / Sequential Duration: 33.225361ms

Test #2

Concurrent Mapping Duration: 23.767892ms

Seperate Mapping (1 Thread) / Sequential Duration: 33.445708ms

### Case #1: --raw-linux data/Linux_2k.log --to-parse "Jun 23 23:30:05 combo sshd(pam_unix)[26190]: authentication failure; logname= uid=0 euid=0 tty=NODEVssh ruser= rhost=218.22.3.51  user=root" --before "

Test #1

Concurrent Mapping Duration: 26.399012ms

Seperate Mapping (1 Thread) / Sequential Duration: 53.183812ms

Test #2

Concurrent Mapping Duration: 26.412994ms

Seperate Mapping (1 Thread) / Sequential Duration: 57.636602ms

### Case #3--raw-spark data/from_paper.log --to-parse "17/06/09 20:11:11 INFO storage.BlockManager: Found block rdd_42_20 locally" --before "split: hdfs://hostname/2kSOSP.log:29168+7292" --after "Found block" --cutoff 3

Test #1

Concurrent Mapping Duration: 7.82816ms

Seperate Mapping (1 Thread) / Sequential Duration: 5.36795ms

Test #2

Concurrent Mapping Duration: 7.240706ms

Seperate Mapping (1 Thread) / Sequential Duration: 5.226573ms

In most cases, the concurrent map + threading version outperformed the sequential version. The only example that failed this would be for the spark data from paper log. This is because there were only 9 lines in the log file. For small applications, using the sequential method may be faster as it avoids the threading setup time. However, for applications that need to scale, threading with concurrent mapping is a better performer.