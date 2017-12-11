---
title:   Performance Profiler
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   12/11/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python
---

# Performance Profiler


CNTK has a performance profiler that can help debug performance issues. It generates a summary report and a detailed profile log.

## Usage
The following details usage the performance profiler in Python.

### Profiler parameters
The behavior of the profiler is controlled through the following parameters of the `start_profiler` function:
* `dir`: directory for the profiler output. Default is `'profiler'`.
* `sync_gpu`: Controls whether CNTK waits for GPU processing to finish at the end of an event (`True` or `False`). 
This enables getting accurate timing for events which involve GPU processing, but might slow down the training since CNTK process
is blocked until all pending GPU processing is complete. Default is `True`.
* `reserve_mem`: Size (in bytes) of the memory buffer used to store the event info. Default is `33554432` bytes (32 MB).
    
### Profiler APIs

There are profiler APIs that control the profiler's behavior. A typical usage with `training_session` is like:

    from cntk.debugging import start_profiler, stop_profiler
    start_profiler()
    training_session.train()
    stop_profiler()

Note that profiler is not enabled after `start_profiler`, and that `training_session enable_profiler()` is called after the first checkpoint. This is to skip the first epoch, which may have more latency due to prefetching. When not using `training_session`, the user needs to do something like the following:

    from cntk.debugging import start_profiler, stop_profiler, enable_profiler
    start_profiler()        
    for epoch in range(max_epochs):       # loop over epochs
        sample_count = 0
        while sample_count < epoch_size:  # loop over minibatches in the epoch
            data = reader_train.next_minibatch(min(minibatch_size, epoch_size-sample_count), input_map=input_map) # fetch minibatch.
            trainer.train_minibatch(data)                                   # update model with it
            sample_count += trainer.previous_minibatch_sample_count         # count samples processed so far
        enable_profiler() # begin to collect profiler data after first epoch
    stop_profiler()
    

### Output location
Unless the `dir` parameter of `start_profiler` is otherwise specified, the profiler output goes to `./profiler`.

## Summary report

There is a separate summary report for each worker node. It is named `<timestamp>_summary_<MPI_rank>.txt`. This report breaks down the time spent in each of the major phases of minibatch processing. For each event it provides the average time, standard deviation, minimum/maximum time, total number of events and total time spent in this event while the profiler was turned on.

While minibatch processing is taking place on the main thread, the data reader prefetches the next minibatch on a background thread, so this event is shown separately.

## Detailed log
Another set of files contains a detailed log of all events recorded during the profiling session. They are named `<timestamp>_detail_<MPI_rank>.csv`. For each event there is a record containing event name, thread ID, begin timestamp and end timestamp (in milliseconds). The .csv file can be loaded into a spreadsheet program for further analysis.

## Notes
* The very first epoch is usually slower compared to subsequent epochs, so the profiler is enabled only at the beginning of the second epoch.
* The overhead of profiling a single event is around 100 ns.
* The `sync_gpu=True` option slightly slows down the training, but enables getting meaningful times. With `sync_gpu=False` there is no slow-down. Minibatch processing time is correct; however, the distribution of time between sub-events is not accurate (e.g. the gradient aggregation event starts before forward/backward processing is complete).
* For ad-hoc profiling of a specific portion of code a custom event can be added using `PROFILE_SCOPE("My custom event")`. Such events are included in the detailed log, but not in the summary report. See [PerformanceProfiler.h](https://github.com/Microsoft/CNTK/blob/master/Source/PerformanceProfilerDll/PerformanceProfiler.h) for more details.
* Summary report and detailed log files are written immediately before the CNTK process finishes. No files are generated if CNTK terminates unexpectedly (e.g. in case of an unhandled exception).
