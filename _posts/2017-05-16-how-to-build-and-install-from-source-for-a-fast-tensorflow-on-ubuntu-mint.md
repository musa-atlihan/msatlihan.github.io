---
layout: 
title: How to build and install from source for a fast TensorFlow on Ubuntu/Mint
description: How to build and install from source for a fast TensorFlow on Ubuntu/Mint
header: How to build and install from source for a fast TensorFlow on Ubuntu/Mint
comments: true
---

If you are using `conda` or `pip` to install TensorFlow, you might get warnings telling that TensorFlow wasn't properly compiled for your hardware and thus it might be slow. First time i installed TensorFlow by just issuing the command `conda install tensorflow` and got these warnings:

```
The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.

The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.

The TensorFlow library wasn't compiled to use AVX instructions, but these are available on your machine and could speed up CPU computations.
```

What i understand here is, if i compile TensorFlow in a right way, it would become compatible with my harware and run fast.

So, lets build it from source and install later on.

First make a directory for tensorflow like:

```
mkdir ~/tensorflow
```

And download the [latest release of TensorFlow](https://github.com/tensorflow/tensorflow/releases) in that directory. From now on, we will need [bazel](https://bazel.build/) to build TensorFlow from source, so lets [install](https://bazel.build/versions/master/docs/install.html) it.

Before using bazel, cd into your TensorFlow directory `cd ~/tensorflow` to run the configuration: `./configure`.

It is a good tool to configure the features you would like to build for TensorFlow. Here we will build TensorFlow without GPU support (it requires extra work with NVIDIA's [CUDA toolkit installed](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/).)

The output of your configuration progress will be something like this:

```
Please specify the location of python. [Default is /your_path/bin/python]: 
Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 
Do you wish to use jemalloc as the malloc implementation? [Y/n] 
jemalloc enabled
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N] 
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with Hadoop File System support? [y/N] 
No Hadoop File System support will be enabled for TensorFlow
Do you wish to build TensorFlow with the XLA just-in-time compiler (experimental)? [y/N] 
No XLA support will be enabled for TensorFlow
Found possible Python library paths:
  /your_path/lib/python2.7/site-packages
Please input the desired Python library path to use.  Default is [/your_path/lib/python2.7/site-packages]

Using python library path: /your_path/lib/python2.7/site-packages
Do you wish to build TensorFlow with OpenCL support? [y/N] 
No OpenCL support will be enabled for TensorFlow
Do you wish to build TensorFlow with CUDA support? [y/N] 
No CUDA support will be enabled for TensorFlow
Configuration finished
INFO: Starting clean (this may take a while). Consider using --expunge_async if the clean takes more than several minutes.
................................................................
INFO: All external dependencies fetched successfully.
```

Then run bazel to build it:

```
bazel build --config=opt //tensorflow/tools/pip_package:build_pip_package
```

(*If you get errors and want to run bazel again, don't forget to do `bazel clean` or `./configure`, before you try to build again.*)

If the whole progress is successful, you will notice there are extra folders in your `~/tensorflow` directory. 

Now issue this command:

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

Before we install, using virtualenv is suggested with TensowFlow. Lets install and use it.

```
pip install virtualenv
```

Define a work directory for the environment,

```
virtualenv --system-site-packages ~/tensorflow
```

and activate this new environment

```
source ~/tensorflow/bin/activate
```

After activating desired envorinment we will finally install TensorFlow (*You might need to install python-wheel before.*)

```
pip install /tmp/tensorflow_pkg/tensorflow-1.1.0-cp27-cp27mu-linux_x86_64.whl
```

And check if TensorFlow is there

```
pip show tensorflow
```

This approach helped me to run tensorflow without those warnings. I think it is compatible with my hardware now.