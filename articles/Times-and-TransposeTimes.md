CNTK matrix product.

    A * B
    Times (A, B, outputRank=1)
    TransposeTimes (A, B, outputRank=1)

### Parameters

* `A` first argument of matrix product. Can be a time sequence.
* `B` second argument of matrix product. Can be a time sequence.
* `outputRank` (default: 1): number of axes of `A` that constitute the output dimension. See 'Extended interpretation for tensors' below.

### Return Value

Resulting matrix product (tensor). This is a time sequence if either input was a time sequence.

### Description

The `Times()` function implements the matrix product, with extensions for tensors. The `*` operator is a short-hand for it. `TransposeTimes()` transposes the first argument.

If `A` and `B` are matrices (rank-2 tensor) or column vectors (rank-1 tensor), `A * B` will compute the common matrix product, just as one would expect.

`TransposeTimes (A, B)` computes the matrix product `A^T * B`, where `^T` denotes transposition. `TransposeTimes (A, B)` has the same result as `Transpose (A) * B`, but it is more efficient as it avoids a temporary copy of the transposed version of `A`.

#### Time sequences
Both `A` and `B` can be either single matrices or time sequences. A common case for recurrent networks is that `A` is a weight matrix, while `B` is a sequence of inputs.

Note: If `A` is a time sequence, the operation is not efficient, as it will launch a separate GEMM invocation for every time step. The exception is `TransposeTimes()` where both inputs are column vectors, for which a special optimization exists.

#### Sparse support
`Times()` and `TransposeTimes()` support sparse matrix. The result is a dense matrix unless both are sparse. The two most important use cases are:

* `B` being a one-hot representation of an input word (or, more commonly, an entire sequence of one-hot vectors). Then, `A * B` denotes a word embedding, where the columns of `A` are the embedding vectors of the words. The following is the recommended way of realizing embeddings in CNTK:

      ```
      Embedding (x, dim) = Parameter (dim, 0/*inferred*/) * x
      e = Embedding (input, 300)
      ```

* `A` being a one-hot representation of an label word. The popular cross-entropy criterion and the error counter can be written using `TransposeTimes()` as follows, respectively, where `z` is the input to the top-level Softmax() classifier, and `L` the label sequence which may be sparse:

      ```
      CrossEntropyWithSoftmax (L, z) = ReduceLogSum (z) - TransposeTimes (L,          z)
      ErrorPrediction         (L, z) = BS.Constants.One - TransposeTimes (L, Hardmax (z))
      ```

#### Multiplying with a scalar
The matrix product can *not* be used to multiply a matrix with a scalar. You will get an error regarding mismatching dimensions. To multiply with a scalar, use the element-wise product `.*` instead. For example, the weighted average of two matrices could be written like this:

    z = Constant (alpha) .* x + Constant (1-alpha) .* y

#### Multiplying with a diagonal matrix
If your input matrix is diagonal and stored as a vector, do not use `Times()` but an element-wise multiplication (`ElementTimes()` or the `.*` operator).
For example

    dMat = ParameterTensor {(100:1)}
    z = dMat .* v

This leverages broadcasting semantics to multiply every element of `v` with the respective row of `dMat`.

#### Extended interpretation of matrix product for tensors of rank > 2
If `A` and/or `B` are tensors of higher rank, the `*` operation denotes a generalized matrix product where all but the first dimension of `A` must match with the leading dimensions of `B`, and are interpreted by flattening. For example a product of a `[I x J x K]` and a `[J x K x L]` tensor (which we will abbreviate henceforth as  `[I x J x K] * [J x K x L]`) gets reinterpreted by reshaping the two tensors as matrices as `[I x (J * K)] * [(J * K) x L]`, for which the matrix product is defined and yields a result of dimension `[I x L]`. This makes sense if one considers the rows of a weight matrix to be patterns that activation vectors are matched against. The above generalization allows these patterns themselves to be multi-dimensional, such as images or running windows of speech features.

It is also possible to have more than one non-matched dimension in `B`. For example `[I x J] * [J x K x L]` is interpreted as this matrix product: `[I x J] * [J x (K * L)]` which thereby yields a result of dimensions `[I x K x L]`. For example, this allows to apply a matrix to all vectors inside a rolling window of `L` speech features of dimension `J`.

If the *result* of the product should have multiple dimensions (such as arranging a layer's activations as a 2D field), then instead of using the `*` operator, one must say `Times (A, B, outputRank=m)` where `m` is the number of dimensions in which the 'patterns' are arranged, and which are kept in the output. For example, `Times (tensor of dim [I x J x K], tensor of dim [K x L], outputRank=2)` will be interpreted as the matrix product `[(I * J) x K] * [K x L]` and yield a result of dimensions `[I x J x L]`.