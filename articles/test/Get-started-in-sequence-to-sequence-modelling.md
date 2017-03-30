This [hands-on lab](Hands-On-Labs-Language-Understanding) describes the main ingredients for getting started on sequence processing such as the CNTK text format and how to configure the reader to use short aliases for the various input sequences.
The [grapheme-to-phoneme (G2P) example](https://github.com/Microsoft/CNTK/blob/master/Examples/SequenceToSequence/CMUDict/BrainScript/G2P.cntk)
demonstrates an actual sequence-to-sequence task.

An important issue for sequence-to-sequence modeling is how to decode test data with beam search.
This can be done with in a section of your config where the top level action is "write". Decoding requires a search for the most probable sequence of outputs. CNTK has a [beam search](https://en.wikipedia.org/wiki/Beam_search) decoder while you can call like this
```
BrainScriptNetworkBuilder = (BS.Seq2Seq.BeamSearchSequenceDecoderFrom (
                                        BS.Network.Load (decodeModelPath), beamSize))
```
and will execute beam search with the specified beam size. For a beam size of 1 there is a specialized greedy decoder
```
BrainScriptNetworkBuilder = (BS.Seq2Seq.GreedySequenceDecoderFrom (
                                        BS.Network.Load (decodeModelPath)))
```
Both decoders have specific requirements to the network, as shown
in the [G2P example](https://github.com/Microsoft/CNTK/blob/master/Examples/SequenceToSequence/CMUDict/BrainScript/G2P.cntk)
