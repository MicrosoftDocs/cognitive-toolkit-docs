Suppose you are training a sequence to sequence model for translating from English to Hungarian with inputs like
```
<s> I will not buy this record, it is scratched <s/> Nem fogok vásárolni ezt a lemezt, azt karcos </s>
<s> My hovercraft is full of eels <s/> A légpárnás hajóm tele van angolnákkal </s>
...
```
using the LUSequenceReader. There's a chance you'll encounter the cryptic exception
```
EXCEPTION occurred: AddSequence: Sequences must be a least one frame long.
```
The way to avoid it is to ensure that the `<s/>` token is in your vocabulary and its position corresponds to the variable `separatorRow` (0-based) in your config file.

We also recommend to not use LUSequenceReader, but instead convert your text using the
[`txt2ctf.py`](https://github.com/Microsoft/CNTK/blob/master/Scripts/txt2ctf.py) tool
and then reading it with the [`CNTKTextFormatReader`](CNTKTextFormat-Reader).
