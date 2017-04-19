The CNTKTextFormatReader can tolerate a `maxErrors` number of malformed lines (default is 0). The error message 
> Reached the maximum number of allowed errors while reading the input file

means that that the reader has encountered more than `maxErrors` malformed lines. Make sure you have generated the input file correctly (e.g. there are no stray vertical bars in comment streams (`|#`)) or increase `maxErrors` if recreating the input file is close to impossible.