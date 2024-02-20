# Seperate Hashmap Parallel Implementation

## Summary
This commit contains the code implementation of the logram alogrithm with parellelization.
More specificially, this is the implementation that merges the results from each thread
rather than employing a concurrent data structure. Similiar to the sequential implementation,
this version loads the log file in memory. Chunk segementation occurs on this file to split the logs into
different threads. Once the logram algorithm is run on each chunk, the results are joined together.

## Tech Details
Since this was my first time creating threads, I was originally having trouble with passing data across each thread.
This was especially important for this algorithm as logram requires that you have information on the log line that exists before and after the current one. This can pose a problem when we split the log lines in chunks, as the chunks would need information on the last line from the previous chunk and/or the first line from the next chunk. To achieve this, each chunk was given extra entries. The first chunk was given the first line of the next chunk and the chunks in the middle were given the first and last line of the previous and next chunk, respectively. Lastly, the final chunk was given the last line from the previous chunk.

Arc was used to pass static information across each thread.

## Testing (Correctness)
The first form of testing was completed by running the standard commands that were provided in the readme of this repo.
Some with --num-threads, some with -- single-mapping, and some without either. The outputs were copied and compared using pretty print. 

## Testing (Performance)
Since this section did not require for any perforamce improvements, no testing for performance was conducted.