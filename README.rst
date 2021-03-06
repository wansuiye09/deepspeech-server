==================
DeepSpeech Server
==================

.. image:: https://travis-ci.org/MainRo/deepspeech-server.svg?branch=master
    :target: https://travis-ci.org/MainRo/deepspeech-server

.. image:: https://badge.fury.io/py/deepspeech-server.svg
    :target: https://badge.fury.io/py/deepspeech-server

Key Features
============

This is an http server that can be used to test the Mozilla DeepSpeech project.
You need an environment with DeepSpeech and a model to run this server.

This code uses the DeepSpeech 0.7 APIs.

Installation
=============

You first need to install deepspeech. Depending on your system you can use the
CPU package:

.. code-block:: console

    pip3 install deepspeech

Or the GPU package:

.. code-block:: console

    pip3 install deepspeech-gpu

Then you can install the deepspeech server:

.. code-block:: console

    python3 setup.py install

The server is also available on pypi, so you can install it with pip:

.. code-block:: console

    pip3 install deepspeech-server

Note that python 3.5 is the minimum version required to run the server.

Starting the server
====================

.. code-block:: console

    deepspeech-server --config config.json

You can use deepspeech without training a model yourself. Pre-trained
models are provided by Mozilla in the release page of the project (See the
assets section of the release note):

https://github.com/mozilla/DeepSpeech/releases

Once your downloaded a pre-trained model, you can untar it and directly use the
sample configuration file:

.. code-block:: console

    cp config.sample.json config.json
    deepspeech-server --config config.json

Server configuration
=====================

The configuration is done with a json file, provided with the "--config" argument.
Its structure is the following one:

.. code-block:: json

    {
      "deepspeech": {
        "model" :"deepspeech-0.7.1-models.pbmm",
        "scorer" :"deepspeech-0.7.1-models.scorer",
        "beam_width": 500,
        "lm_alpha": 0.931289039105002,
        "lm_beta": 1.1834137581510284
      },
      "server": {
        "http": {
          "host": "0.0.0.0",
          "port": 8080,
          "request_max_size": 1048576
        }
      },
      "log": {
        "level": [
          { "logger": "deepspeech_server", "level": "DEBUG"}
        ]
      }
    }

The configuration file contains several sections and sub-sections.

deepspeech section configuration
--------------------------------

Section "deepspeech" contains configuration of the deepspeech engine:

**model**: The model that was generated by deepspeech. Can be a protobuf file or a memory mapped protobuf.

**scorer**: [Optional] The scorer file. The scorer is necessary to set lm_alpha or lm_beta manually

**beam_width**: [Optional] The size of the beam search

**lm_alpha** and **lm_beta**: [Optional] The hyperparmeters of the scorer

Section "server" contains configuration of the access part, with on subsection per protocol:

http section configuration
--------------------------

**request_max_size** (default value: 1048576, i.e. 1MiB) is the maximum payload
size allowed by the server. A received payload size above this threshold will
return a "413: Request Entity Too Large" error.

**host**  (default value: "0.0.0.0") is the listen address of the http server.

**port** (default value: 8080) is the listening port of the http server.

log section configuration
-------------------------

The log section can be used to set the log levels of the server. This section
contains a list of log entries. Each log entry contains the name of a **logger** 
and its **level**. Both follow the convention of the python logging module.


Using the server
================

Inference on the model is done via http post requests. For example with the
following curl command:

.. code-block:: console

     curl -X POST --data-binary @testfile.wav http://localhost:8080/stt
