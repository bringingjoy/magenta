
<img src="magenta-logo-bg.png" height="75">

[![Build Status](https://travis-ci.org/tensorflow/magenta.svg?branch=master)](https://travis-ci.org/tensorflow/magenta)
 [![PyPI version](https://badge.fury.io/py/magenta.svg)](https://badge.fury.io/py/magenta)

**Magenta** is a research project exploring the role of machine learning
in the process of creating art and music.  Primarily this
involves developing new deep learning and reinforcement learning
algorithms for generating songs, images, drawings, and other materials. But it's also
an exploration in building smart tools and interfaces that allow
artists and musicians to extend (not replace!) their processes using
these models.  Magenta was started by some researchers and engineers
from the [Google Brain team](https://research.google.com/teams/brain/),
but many others have contributed significantly to the project. We use
[TensorFlow](https://www.tensorflow.org) and release our models and
tools in open source on this GitHub.  If you’d like to learn more
about Magenta, check out our [blog](https://magenta.tensorflow.org),
where we post technical details.  You can also join our [discussion
group](https://groups.google.com/a/tensorflow.org/forum/#!forum/magenta-discuss).

This is the home for our Python TensorFlow library. To use our models in the browser with [TensorFlow.js](https://js.tensorflow.org/), head to the [Magenta.js](https://github.com/tensorflow/magenta-js) repository.

## Getting Started

* [Installation](#installation)
* [Using Magenta](#using-magenta)
* [Playing a MIDI Instrument](#playing-a-midi-instrument)
* [Development Environment (Advanced)](#development-environment)

## Installation

### Python Pip

Magenta maintains a [pip package](https://pypi.python.org/pypi/magenta) for easy
installation. We recommend using Anaconda to install it, but it can work in any
standard Python environment. We support both Python 2 (>= 2.7) and Python 3 (>= 3.5).
These instructions will assume you are using Anaconda.

Note that if you want to enable GPU support, you should follow the [GPU Installation](#gpu-installation) instructions below.

#### Automated Install (w/ Anaconda)

If you are running Mac OS X or Ubuntu, you can try using our automated
installation script. Just paste the following command into your terminal.

```bash
curl https://raw.githubusercontent.com/tensorflow/magenta/master/magenta/tools/magenta-install.sh > /tmp/magenta-install.sh
bash /tmp/magenta-install.sh
```

After the script completes, open a new terminal window so the environment
variable changes take effect.

The Magenta libraries are now available for use within Python programs and
Jupyter notebooks, and the Magenta scripts are installed in your path!

Note that you will need to run `source activate magenta` to use Magenta every
time you open a new terminal window.

#### Manual Install (w/o Anaconda)


<b> Note:</b> To install python-rtmidi, the ALSA library development must first be installed. To install in Ubuntu/Debian, use
`sudo apt-get install libasound2-dev`
To fix build failure for python-rtmidi, install the following dependencies (in order):
```bash 
sudo apt-get install libjack0
sudo apt-get install libjack-dev
sudo apt-get install libjack-jackd2-0
sudo apt-get install jack
sudo apt-get install libjack-dev
pip install python-rtmidi
```

(There is a clash between `libjack-dev` and `libjack-jackd2-dev`, so it may be suitable to install one or the other instead of reinstalling libjack-dev after the other pkg installations. After following the above steps, `pip install magenta` and `pip install magenta-gpu` should be successful!)

If the automated script fails for any reason, or you'd prefer to install by
hand, do the following steps.

Install the Magenta pip package:

```bash
pip install magenta
```

**NOTE**: In order to install the `rtmidi` package that we depend on, you may need to install headers for some sound libraries. On Linux, this command should install the necessary packages:

```bash
sudo apt-get install build-essential libasound2-dev libjack-dev
```

The Magenta libraries are now available for use within Python programs and
Jupyter notebooks, and the Magenta scripts are installed in your path!

#### GPU Installation

If you have a GPU installed and you want Magenta to use it, you will need to
follow the [Manual Install](#manual-install) instructions, but with a few
modifications.

First, make sure your system meets the [requirements to run tensorflow with GPU support](
https://www.tensorflow.org/install/install_linux#nvidia_requirements_to_run_tensorflow_with_gpu_support).

Next, follow the [Manual Install](#manual-install) instructions, but install the
`magenta-gpu` package instead of the `magenta` package:

```bash
pip install magenta-gpu
```

The only difference between the two packages is that `magenta-gpu` depends on
`tensorflow-gpu` instead of `tensorflow`.

Magenta should now have access to your GPU.

### Docker
Another way to try out Magenta is to use our Docker container.
First, [install Docker](https://docs.docker.com/engine/installation/). Next, run
this command:

```bash
docker run -it -p 6006:6006 -v /tmp/magenta:/magenta-data tensorflow/magenta
```

This will start a shell in a directory with all Magenta components compiled,
installed, and ready to run. It will also map port 6006 of the host machine to
the container so you can view TensorBoard servers that run within the container.

This also maps the directory `/tmp/magenta` on the host machine to
`/magenta-data` within the Docker session. Windows users can change
`/tmp/magenta` to a path such as `C:/magenta`, and Mac and Linux users
can use a path relative to their home folder such as `~/magenta`.
**WARNING**: only data saved in `/magenta-data` will persist across Docker
sessions.

The Docker image also includes several pre-trained models in
`/magenta/models`. For example, to generate some MIDI files using the
[Lookback Melody RNN](magenta/models/melody_rnn#lookback), run this command:

```bash
melody_rnn_generate \
  --config=lookback_rnn \
  --bundle_file=/magenta-models/lookback_rnn.mag \
  --output_dir=/magenta-data/lookback_rnn/generated \
  --num_outputs=10 \
  --num_steps=128 \
  --primer_melody="[60]"
```

**NOTE**: Verify that the `--output_dir` path matches the path you
mapped as your shared folder when running the `docker run` command. This
example command presupposes that you are using `/magenta-data` as your
shared folder from the example `docker run` command above.

One downside to the Docker container is that it is isolated from the host. If
you want to listen to a generated MIDI file, you'll need to copy it to the host
machine. Similarly, because our
[MIDI instrument interface](magenta/interfaces/midi) requires access to the host
MIDI port, it will not work within the Docker container. You'll need to use the
full Development Environment.

You may find at some point after installation that we have released a new version of Magenta and your Docker image is out of date. To update the image to the latest version, run:

```bash
docker pull tensorflow/magenta
```

**NOTE**: Our Docker image is also available at `gcr.io/tensorflow/magenta`.

## Using Magenta

You can now train our various models and use them to generate music, audio, and images. You can
find instructions for each of the models by exploring the [models directory](magenta/models).

To get started, create your own melodies with TensorFlow using one of the various configurations of our [Melody RNN](magenta/models/melody_rnn) model; a recurrent neural network for predicting melodies.

## Playing a MIDI Instrument

After you've trained one of the models above, you can use our [MIDI interface](magenta/interfaces/midi) to play with it interactively.

We also have created several [demos](https://github.com/tensorflow/magenta-demos) that provide a UI for this interface, making it easier to use (e.g., the browser-based [AI Jam](https://github.com/tensorflow/magenta-demos/tree/master/ai-jam-js)).

## Development Environment
If you want to develop on Magenta, you'll need to set up the full Development Environment.

First, clone this repository:

```bash
git clone https://github.com/tensorflow/magenta.git
```

Next, install the dependencies by changing to the base directory and executing the setup command:

```bash
python setup.py develop
```

You can now edit the files and run scripts by calling Python as usual. For example, this is how you would run the `melody_rnn_generate` script from the base directory:

```bash
python magenta/models/melody_rnn/melody_rnn_generate --config=...
```

You can also install the (potentially modified) package with:

```bash
python setup.py install
```

Before creating a pull request, please also test your changes with:

```bash
python setup.py test
```

## PIP Release

To build a new version for pip, bump the version and then run:

```bash
python setup.py test
python setup.py bdist_wheel --universal
python setup.py bdist_wheel --universal --gpu
twine upload dist/magenta-N.N.N-py2.py3-none-any.whl
twine upload dist/magenta_gpu-N.N.N-py2.py3-none-any.whl
```
