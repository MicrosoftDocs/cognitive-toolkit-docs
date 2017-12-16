---
title:   BrainScript Performance Profiler
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript Performance Profiler


CNTK has a performance profiler that can help debug performance issues. It generates a summary report and a detailed profile log.  

For details on using the performance profiler in Python, see [here](./Performance-Profiler.md).

## BrainScript config file parameters

The behavior of the profiler is controlled through the following top-level parameters:
* `profilerEnabled`: Enables/disables the profiler (`true` or `false`). Default is `false`.
* `profilerBufferSize`: Size of the memory buffer used to store the event info, in bytes. Default is `33554432` (32 MB).
* `profilerSyncGpu`: Controls whether CNTK waits for GPU processing to finish at the end of an event (`true` or `false`). This enables getting accurate timing for events which involve GPU processing, but might slow down the training since CNTK process is blocked until all pending GPU processing is complete. Default is `true`.

In most cases it is sufficient to add a single line to the config file:

    profilerEnabled="true"
    
## Output location

If `WorkDir` macro is defined in the config file profiler output goes to `$WorkDir$/profiler`. Otherwise, it goes to `./profiler`.

## Summary report

There is a separate summary report for each worker node. It is named `<timestamp>_summary_<MPI_rank>.txt`. This report breaks down the time spent in each of the major phases of mini-batch processing. For each event it provides the average time, standard deviation, minimum/maximum time, total number of events and total time spent in this event while the profiler was turned on.

While mini-batch processing is taking place on the main thread, the data reader prefetches the next mini-batch on a background thread, so this event is shown separately.

## Detailed log
Another set of files contains detailed log of all events recorded during the profiling session. They are named `<timestamp>_detail_<MPI_rank>.csv`. For each event there is a record containing event name, thread ID, begin timestamp and end timestamp (in milliseconds). The .csv file can be loaded into a spreadsheet program for further analysis.

## Notes
* The very first epoch is usually slower compared to subsequent epochs, so the profiler is enabled only at the beginning of the second epoch.
* The overhead of profiling a single event is around 100 ns.
* `profilerSyncGpu` option slightly slows down the training, but enables getting meaningful times. With `profilerSyncGpu="false"` there is no slow-down. Mini-batch processing time is correct, however the distribution of time between sub-events is not accurate (e.g. gradient aggregation event starts before forward/backward processing is complete).
* For ad-hoc profiling of a specific portion of code a custom event can be added using `PROFILE_SCOPE("My custom event")`. Such events are included in the detailed log, but not in the summary report. See [PerformanceProfiler.h](https://github.com/Microsoft/CNTK/blob/master/Source/PerformanceProfilerDll/PerformanceProfiler.h) for more details.
* Summary report and detailed log files are written immediately before the CNTK process finishes. No files are generated if CNTK terminates unexpectedly (e.g. in case of an unhandled exception).
