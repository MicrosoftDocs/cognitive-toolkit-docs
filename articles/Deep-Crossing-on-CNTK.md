**Table of Contents**

- [Introduction to Deep Crossing](#introduction-to-deep-crossing)
 - [Embedding: Simple Embedding for Dimensionality Reduction](#embedding-simple-embedding-for-dimensionality-reduction)
 - [Stacking: Combining Inputs into a Single Vector](#stacking-combining-inputs-into-a-single-vector)
 - [Residual Units: Applying Deep Learning](#residual-units-applying-deep-learning)
- [Deep Crossing in BrainScript](#deep-crossing-in-brainscript)
 - [Inputs](#inputs)
 - [Embedding Layer](#embedding-layer)
 - [Stacking Layer](#stacking-layer)
 - [ResNet Layer](#resnet-layer)
 - [Finishing BrainScript](#finishing-brainscript)

# Introduction to Deep Crossing

Deep Crossing is a model introduced by Shan et. al. [1] as a solution to the problem of prediction given heterogeneous inputs without manual feature generation. The method has been used with a variety of raw (i.e., minimal processing, no combining/crossing features into more complex representations) dense, sparse, and text features to produce a model that provides superior performance to a state of the art model which used manual tuning.

<img src="./Articles3/deepCrossing.png" alt="Deep Crossing Model" width="500"/>

Deep Crossing can be conceptually thought of as three separate operations combined into one. To motivate this, consider the problem that Deep Crossing seeks to solve: training in the presence of heterogeneous (especially textual) inputs. Due to the success of deep learning on a variety of vision/speech tasks using raw inputs, Deep Crossing seeks to use as raw of features as possible. However whereas images and speech tend to have (relatively) low dimensionality (e.g., a 32x32x3 image has a dimension of 3,072) when compared to text inputs which can have vocabularies hundreds of thousands to even millions of words. Applying a deep learning scheme to this size of inputs is undesirable for many reasons, not limited to storage and performance constraints. Thus if we seek to apply these new techniques to text ([which we do in the last section](#residual-units-applying-deep-learning)), we must first reduce our input dimensionality to be roughly the same dimensionality as images/speech data (i.e., ~1,000).

One technique used to reduce the dimensionality of the input is, instead of considering a bag of words approach where each vector index corresponds to a word in the vocabulary, to instead consider a character n-gram approach where each vector index instead corresponds to a sequence of n characters. While the vocabulary size in the word case can be in the hundreds of thousands, the dimensionality of a 3-gram (i.e., 3 character sequences in English) is ~50,000. This is already a significant reduction, and has previously been used to train models based on text inputs [2].

While using 3-grams as input provides a significant (order of magnitude) reduction in dimensionality, in order to use the deep networks desired above, another large (~2 order of magnitude) reduction in dimensionality is required. In order to achieve this, Deep Crossing employs an ["embedding" layer](#embedding-simple-embedding-for-dimensionality-reduction), the sole purpose of which is to achieve this reduction. For this version of deep crossing, we recommend merely using a low (~128) dimensionality embedding matrix which is learned holistically, along with the rest of the model.

Once all of the high-dimensionality inputs have been embedded in this lower dimensional space, we [combine them into a single input](#stacking-combining-inputs-into-a-single-vector), and then [apply the aforementioned deep learning techniques to this lower dimensional data](#residual-units-applying-deep-learning), resulting in a model that provides state of the art performance.

## Embedding: Simple Embedding for Dimensionality Reduction

Since Deep Crossing treats each input individually, the embedding step of Deep Crossing is only concerned with high-dimensional inputs. Inputs that are low dimensional are given an identity embedding, and passed directly through to the stacking layer. Inputs that are high dimensional (most notably text, however other high dimensional inputs are also considered here) are converted into low dimensionality via an embedding layer. The embedding layer can be of any dimensionality. By default we chose a dimensionality of 128 as for our problem it was the largest we could choose while keeping the sum of all of the embeddings to be ~1,024. It is a configurable parameter, however, and one that can be optimized based on the specific problem at hand.
 
## Stacking: Combining Inputs into a Single Vector

The stacking step is actually a pseudo-step in Deep Crossing. Its purpose is to combine the results of the embedding layer for each input into a single vector that can be used in the remainder of the network. One can consider this as part of the embedding step, however since the embedding step specifically handles each inputs individually, while the stacking step combines all of those inputs into a single output, conceptually they make sense as separate steps in the network, albeit one that has no free parameters.

## Residual Units: Applying Deep Learning

Residual units are powerful building blocks in neural networks introduced by He et. al. [3] in the ResNet architecture to achieve state of the art performance on the ImageNet competition. The image below is a high-level depiction of a residual unit. As the name suggests, the goal of a residual unit is to learn the residual between the (given) input and the (learned) output. This is accomplished by adding the input to the output of the last layer before the activation function. In this way, the identity transformation is explicitly encoded (by adding the input), and thus the layers must only learn the difference between the input and the "true" output. In [3] the authors hypothesize that optimizing the residual mapping is easier than the non-residual version.

<img src="./Articles3/residual.png" alt="Residual Unit" width="300">

Due to their success in the image domain, residual units form the basis of the final component of the Deep Crossing model. 

# Deep Crossing in BrainScript

[BrainScript](https://github.com/Microsoft/CNTK/wiki/BrainScript-Network-Builder) is a network description language used to define networks in CNTK. [In this section we will provide an overview of an implementation of Deep Crossing in BrainScript.](./Articles3/DeepCrossing.cntk)

## Inputs

Before describing the embedding layer, we first need to define the inputs for the network. For this part, we assume we have text inputs T1, T2, and T3 with dimensionality T1Dim, T2Dim, and T3Dim, dense inputs D1, D2, D3, and Label with dimensionality D1Dim, D2Dim, D3Dim, and 1, and sparse inputs with S1, and S2 with dimensionality S1Dim, and S2Dim. Then


    T1Dim = XXX
    T2Dim = XXX
    T3Dim = XXX
    D1Dim = XXX
    D2Dim = XXX
    D3Dim = XXX
    S1Dim = XXX
    S2Dim = XXX
    T1 = SparseInput(T1Dim)
    T2 = SparseInput(T2Dim)
    T3 = SparseInput(T3Dim)
    D1 = Input(D1Dim)
    D2 = Input(D2Dim)
    D3 = Input(D3Dim)
    S1 = SparseInput(S1Dim)
    S2 = SparseInput(S2Dim)

    
The first thing to note is that we have defined all of the dimensions as constants. In your code, you will change each "XXX" to be whatever dimensionality your actual inputs are. Since we have no real data, we will ignore the actual values, and just focus on the constants.

The next important thing to understand is that T1, T2, T3, S1, and S2 are instantiated using SparseInput, but D1, D2, and D3 are instantiated as Input. S1 and S2 are obviously sparse inputs, and D1, D2, and D3 are obviously dense inputs, and they are thus defined as such. The text features, as we described previously, are also sparse inputs, and thus we define them in the sparse way.

Now that all of the inputs have been defined, we can continue to the embedding layer.

## Embedding Layer

For the embedding layer, assume that all of the text features (T1, T2, and T3) are too high dimensional, and the sparse feature S2 is also too high dimensional. We now embed each of these features into a lower dimensinoality (say, EDim for "embedded dimensionality").


    EDim = XXX # Previously we stated 128 as a potential default
    T1E = DenseLayer {EDim, activation = ReLU, init = "gaussian"}( T1 )
    T2E = DenseLayer {EDim, activation = ReLU, init = "gaussian"}( T2 )
    T3E = DenseLayer {EDim, activation = ReLU, init = "gaussian"}( T3 )
    S2E = DenseLayer {EDim, activation = ReLU, init = "gaussian"}( S2E )

Note that we have defined 4 DenseLayers (i.e., fully connected layers of a feed forward neural network with bias) with "EDim" outputs, the "ReLU" activation function, and gaussian initialization, and we have created the outputs T1E, T2E, T3E, and S2E as the embeddings of the original inputs. 

Since the other inputs don't require an embedding, we can either leave them, or for consistency we can define the following:

    D1E = D1
    D2E = D2
    D3E = D3
    S1E = S1

This will enable us to be consistent during the stacking phase, however it is not necessary.

## Stacking Layer

Given the embedded inputs T1E, T2E, T3E, S2E, D1, D2, D3, and S1 (or alternatively D1E, D2E, D3E, S1E), we must now combine them into one output for the subsequent ResNet layers. This is accomplished via the Splice command as follows:

    c = Splice(( T1E : T2E : T3E : D1 : D2 : D3 : S1 : S2E ))

Note the syntax of Splice requires a ":" as the delimiter for arrays, and not ",".

```c``` is now all of the other inputs concatenated together. Using this, we can proceed to the ResNet Layer

## ResNet Layer

The ResNet Layer is the final layer of the network, built up from the residual units defined before. Before defining the rest of the network, we must therefore define a residual unit in BrainScript.

    ResUnit(InnerDim, OutDim, INNODE)
    {
        L1 = DenseLayer {InnerDim, activation = ReLU, init = "gaussian"}( INNODE )
        L2 = DenseLayer {OutDim, activation = Pass, init = "gaussian"}( L1 )
        ResUnit = ReLU(Plus(INNODE, L2))
    }.ResUnit

Note here that while ```DenseLayer``` doesn't require the input dimension, since ```InnerDim``` and the input dimension don't have to be the same (i.e., there can be contraction inside the residual unit), we must pass the output dimension into the macro. Furthermore, note that the activation function passed to L2 is ```Pass```. ```Pass``` is a special function that, given any input, returns that as output. Since the residual unit defined above doesn't apply an activation function to the second output, instead adding the input and then applying the activation function after, we direct the ```DenseLayer``` macro to not apply an activation function.

Putting this together, to define a 5 layer ResNet, with inner dimensions ```Inner1```, ```Inner2```, ```Inner3```, ```Inner4```, and ```Inner5``:

    AllDim = EDim + EDim + EDim + EDim + D1Dim + D2Dim + D3Dim + S1Dim
    Inner1 = XXX
    Inner2 = XXX
    Inner3 = XXX
    Inner4 = XXX
    Inner5 = XXX

    r1 = ResNet( Inner1, AllDim, c )
    r2 = ResNet( Inner2, AllDim, r1 )
    r3 = ResNet( Inner3, AllDim, r2 )
    r4 = ResNet( Inner4, AllDim, r3 )
    r5 = ResNet( Inner5, AllDim, r4 )

Now that we've defined the ResNet, we must finish off the network. In our architecture, we take the final layer of the ResNet, and add a final Sigmoid layer in order to use log loss as the training criterion. In BrainScript, we write:

    s = DenseLayer {1, activation = Sigmoid, init = "gaussian"}( r5 )
    CE = logistic(Label, s)

## Finishing BrainScript

While above we have defined the network, there are still a few pieces missing. Specifically, BrainScript requires explicitly naming any criterion nodes, evaluation nodes, and output nodes of the network. For the network above, this would be defined as:

    criterionNodes = (CE)
    evaluationNodes = (err)
    outputNodes = (s)

Furthermore, while the above defines the network architecture, one must also define the rest of the config to use that network architecture. In order to give full understanding, [see the full config, which includes the network above, and a skeleton for a complete config to get started with your own data.](./Articles3/DeepCrossing.cntk)

- [1] [Ying Shan, T. Ryan Hoens, Jian Jiao, Haijing Wang, Dong Yu, and JC Mao, “Deep Crossing: Web-Scale Modeling without Manually Crafted Combinatorial Features”, KDD 2016.](http://www.kdd.org/kdd2016/papers/files/adf0975-shanA.pdf)
- [2] [Po-Sen Huang, Xiaodong He, Jianfeng Gao, Li Deng, Alex Acero, Larry Heck, "Learning Deep Structured Semantic Models for Web Search using Clickthrough Data", ACM International Conference on Information and Knowledge Management (CIKM), October 1, 2013](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/DSSM_cikm13_talk_v4.pdf)
- [3] [He, Kaiming, et al. "Deep residual learning for image recognition." arXiv preprint arXiv:1512.03385 (2015).](https://arxiv.org/pdf/1512.03385v1.pdf)