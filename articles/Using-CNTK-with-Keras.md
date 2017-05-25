# Using CNTK with Keras (Beta)

**This page is under development**

## Steps to follow

We assume you have followed the Anaconda installed on your Windows or Linux machines.
 
1. We highly recommend creating a new anaconda environment.

```conda create --name cntkkeraspy35 python=3.5 numpy scipy h5py jupyter```
 
2. Activate the new environment

```activate cntkkeraspy35```

3. Install [Keras](https://keras.io/#installation) 
 
4. Install a GPU build of CNTK

```
pip install <URL to CNTK GPU wheel>
```
 
4. Update Keras to use CNTK as backend

On Windows: 

```SET KERAS_BACKEND=cntk```

On Linux:

```export KERAS_BACKEND=CNTK```

change “backend” value to “cntk”
ensure “image_dim_ordering” is set to “tf”

If you don’t have a keras.json, it means you have not run keras on the machine. Run Python and import keras and it will create the file.
7.	Unzip the Keras examples to your machine
unzip \\stcvm-ls426\share\cntk\keras_examples.zip -d c:\local\keras_examples
(or whatever unzip mechanism you’d like to use)
8.	Try out the Keras examples
python c:\local\keras_examples\examples\addition_rnn.py
 
9.	Test your own scripts!
 
10.	Send your feedback to cntkkeras
 
 
Known issues:
•	The following Keras APIs are only supported on GPU: normalize_batch_in_training, batch_normalization
•	The following Keras APIs are not yet supported: clear_session, cast, resize_images, resize_volumes, print_tensor, softplus, hard_sigmod, ctc_batch_cost, ctc_decode, map_fn, foldl, foldr
•	The following examples do not work yet: mnist_swwae.py, neural_doodle.py, neural_style_transfer.py, pretrained_word_embeddings.py
•	The following Keras layers are not supported yet: convolution with dilation rate, cropping, noise, merge dot
•	CPU performance needs to be improved
•	Models cannot be saved to HDFS

