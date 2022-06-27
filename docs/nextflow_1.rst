.. _nextflow_1-page:

*******************
Nextflow 1
*******************

Introduction to Nextflow
========================
DSL for data-driven computational pipelines. `www.nextflow.io <https://www.nextflow.io>`_.

.. image:: images/nextflow_logo_deep.png
  :width: 400


What is Nextflow?
-----------------

.. image:: images/nextf_groovy.png
  :width: 600

`Nextflow <https://www.nextflow.io>`__ is a domain specific language (DSL) for workflow orchestration that stems from `Groovy <https://groovy-lang.org/>`__. It enables scalable and reproducible workflows using software containers.
It was developed at the `CRG <www.crg.eu>`__ in the Lab of Cedric Notredame by `Paolo Di Tommaso <https://github.com/pditommaso>`__.
The Nextflow documentation is `available here <https://www.nextflow.io/docs/latest/>`__ and you can ask help to the community using their `gitter channel <https://gitter.im/nextflow-io/nextflow>`__ or joining their `slack channel <https://join.slack.com/t/nextflow/shared_invite/zt-11iwlxtw5-R6SNBpVksOJAx5sPOXNrZg>`__. 

In 2020, Nextflow has been upgraded from DSL1 version to DSL2. In this course we will use exclusively DSL2. It is currently developed and mantained by the company `Seqera Labs <https://seqera.io/>`__

What is Nextflow for?
---------------------

It is for making pipelines without caring about parallelization, dependencies, intermediate file names, data structures, handling exceptions, resuming executions, etc.

It was published in `Nature Biotechnology in 2017 <https://pubmed.ncbi.nlm.nih.gov/28398311/>`__.

.. image:: images/NF_pub.png
  :width: 600

Searching Nextflow in Google scholar gives you currently 8,940 results. 

A curated list of `Nextflow pipelines <https://github.com/nextflow-io/awesome-nextflow>`__.

Many pipelines written collaboratively are provided by the `NF-core <https://nf-co.re/pipelines>`__ project.

Some pipelines written in Nextflow have been used for the SARS-Cov-2 analysis too, for example:

- The `artic Network <https://artic.network/ncov-2019>`__ pipeline `ncov2019-artic-nf <https://github.com/connor-lab/ncov2019-artic-nf>`__.
- The `CRG / EGA viral Beacon <https://covid19beacon.crg.eu/info>`__ pipeline `Master of Pores <https://github.com/biocorecrg/master_of_pores>`__.
- The nf-core pipeline `viralrecon <https://nf-co.re/viralrecon>`__.


Main advantages
----------------


- **Fast prototyping**

You can quickly write a small pipeline that can be **expanded incrementally**.
**Each task is independent** and can be easily added to other. You can reuse scripts without re-writing or adapting them.

- **Reproducibility**

Nextflow supports **Docker** and **Singularity** containers technology. Their use will make the pipelines reproducible in any Unix environment. Nextflow is integrated with **GitHub code sharing platform**, so you can call directly a specific version of a pipeline from a repository, download and use it on-the-fly.

- **Portability**

Nextflow can be executed on **multiple platforms** without modifiying the code. It supports several schedulers such as **SGE, LSF, SLURM, PBS, HTCondor** and cloud platforms like **Kubernetes, Amazon AWS, Google Cloud**.


.. image:: images/executors.png
  :width: 600

- **Scalability**

Nextflow is based on the **dataflow programming model** which simplifies writing complex pipelines.
The tool takes care of **parallelizing the processes** without additionally written code.
The resulting applications are inherently parallel and can scale-up or scale-out transparently; there is no need to adapt them to a specific platform architecture.

- **Resumable, thanks to continuous checkpoints**

All the intermediate results produced during the pipeline execution are automatically tracked.
For each process **a temporary folder is created and is cached (or not) once resuming an execution**.

Workflow structure
-------------------

The workflows can be represented as graphs where the nodes are the **processes** and the edges are the **channels**.
The **processes** are blocks of code that can be executed - such as scripts or programs - while the **channels** are asynchronous queues able to **connect processes among them via input / output**.


.. image:: images/wf_example.png
  :width: 600


Processes are independent from each another and can be run in parallel, depending on the number of elements in a channel.
In the previous example, processes **A**, **B** and **C** can be run in parallel and only when they **ALL** end the process **D** is triggered. As you can see an operator is used for collecting or reshaping the output channels for generating a new one that is then consumed by the process **D**. 

Installation
------------

.. note::
  Nextflow is already installed on the machines provided for this course.
  You need at least the Java version 8 for the Nextflow installation.

.. tip::
  You can check the version fo java by typing::

    java -version

Then we can install Nextflow with::

  curl -s https://get.nextflow.io | bash

This will create the ``nextflow`` executable that can be moved, for example, to ``/usr/local/bin``.

We can test that the installation was successful with:

.. code-block:: console

  nextflow run hello

  N E X T F L O W  ~  version 20.07.1
  Pulling nextflow-io/hello ...
  downloaded from https://github.com/nextflow-io/hello.git
  Launching `nextflow-io/hello` [peaceful_brahmagupta] - revision: 96eb04d6a4 [master]
  executor >  local (4)
  [d7/d053b5] process > sayHello (4) [100%] 4 of 4 ✔
  Ciao world!
  Bonjour world!
  Hello world!
  Hola world!


This command downloads and runs the pipeline ``hello`` from the Nextflow official GitHub repository.


Nextflow main concepts
========================

Channels and Operators
-------------------------

There are two different types of channels:

- A **queue channel** is a non-blocking unidirectional `FIFO <https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics)>`__ (First In, First Out) queue which **connects two processes or operators**.
- A **value channel**, a.k.a. singleton channel, is bound to a single value and can be read unlimited times without consuming its content.

An **operator** is a method that reshapes or connects different channels applying specific rules.

We can write a very simple Nextflow script: save the following piece of code in a file called ``ex1.nf``. All the examples are in the folder
**/nextflow/examples/** while the scripts are in folders named **/nextflow/test**

.. literalinclude:: ../nextflow/examples/ex1.nf
   :language: groovy

Once the file is saved, execute it with:

.. code-block:: console

	nextflow run ex1.nf

	N E X T F L O W  ~  version 20.07.1
	Launching `ex1.nf` [agitated_avogadro] - revision: 61a595c5bf
	hello
	hola
	bonjour


As you can see, the **Channel** is just a collection of values, but it can also be a collection of **file paths**.

Let's create three empty files with the `touch` command:

.. code-block:: console

	touch aa.txt bb.txt cc.txt


and make another script (ex2.nf) with the following code:

.. literalinclude:: ../nextflow/examples/ex2.nf
   :language: groovy



We can now execute `ex2.nf`:

.. code-block:: console

	nextflow run ex2.nf

	N E X T F L O W  ~  version 20.07.1
	Launching `ex2.nf` [condescending_hugle] - revision: f513c0fac3
	/home/ec2-user/git/CRG_Nextflow_Jun_2022/nextflow/aa.txt
	/home/ec2-user/git/CRG_Nextflow_Jun_2022/nextflow/bb.txt
	/home/ec2-user/git/CRG_Nextflow_Jun_2022/nextflow/cc.txt


Once executed, we can see that a folder named **work** is generated. Nextflow stores in this folder the intermediate files generated by the processes.

In genomics we often have couple of files that have to be processed at the same times, such as the paired end reads etc. For this Nextflow allow using a special method for generating "tuples" from file pairs. 

We can simulate this situation by generating a couple of files:

.. code-block:: console

	touch aaa_1.txt aaa_2.txt

Then we use `fromFilePairs <https://www.nextflow.io/docs/latest/channel.html#fromfilepairs>`__ for generating a tuple (script **ex3.nf**):


.. literalinclude:: ../nextflow/examples/ex3.nf
   :language: groovy

Executing it will show the emission of a tuple which key is the common part of the two input files:

.. code-block:: console

	nextflow run ex3.nf 
	N E X T F L O W  ~  version 21.10.6
	Launching `ex3.nf` [reverent_ampere] - revision: 87d78a151f
	[aaa, [/nfs/users/bi/lcozzuto/aaa/CRG_Nextflow_Jun_2022/nextflow/examples/aaa_1.txt, /nfs/users/bi/lcozzuto/aaa/CRG_Nextflow_Jun_2022/nextflow/examples/aaa_2.txt]]


We can reshape the channels in several ways and / or cross them using operators so that they can be used for a particular purpose. In brief, each "emission" of a channel can be used by a process for a specific purpose.  


Exercise
-------------------------
Using again the previous 3 `.txt` files ("aa.txt", "bb.txt", "cc.txt"), reshape the channels to emit:

  - A single channel with a **single emission** with all the files
  - A channel with each possible file combination ( A vs A, A vs B, A vs C etc..)
  - A tuple with a custom id, i.e. something like ["custom id", ["aa.txt", "bb.txt", "cc.txt"]]


See here the list of `Operators <https://www.nextflow.io/docs/latest/operator.html#>`__ available at the official documentation.


.. raw:: html

   <details>
   <summary><a>Solution</a></summary>

.. literalinclude:: ../nextflow/examples/sol1.nf
   :language: groovy




.. raw:: html

	</details>
|
|

Processes
-------------

Let's add a process to the previous script `ex1.nf` and let's call it `ex1_a.nf`

.. literalinclude:: ../nextflow/examples/ex1_a.nf
   :language: groovy



The process can be seen as a function that is composed of:

- An **input** part where the input channels are defined.
- An **output** part where we specify what to store as a result, that will be sent to other processes or published as final result.
- A **script** part where we have the block of code to be executed using data from the input channel, and that will produce the output for the ouput channel.

Any kind of code / command line can be run there, as it is **language agnostic**.


.. note::
	You can have some trouble with escaping some characters: in that case, it is better to save the code into a file and call that file as a program.

.. tip::
	Before the input, you can indicate a **tag** that will be reported in the log. This is quite useful for **logging / debugging**.


Workflow
------------

The code above will produce nothing (actually a warning), because it requires the part that will actually **call the process** and connect it to the input channel.

.. code-block:: console

	nextflow run ex1_a.nf -dsl2
	N E X T F L O W  ~  version 22.04.3
	Launching `ex1_a.nf` [irreverent_leakey] DSL2 - revision: 224d75e0c7
	=============================================================================
	=                                WARNING                                    =
	= You are running this script using DSL2 syntax, however it does not        = 
	= contain any 'workflow' definition so there's nothing for Nextflow to run. =
	=                                                                           =
	= If this script was written using Nextflow DSL1 syntax, please add the     = 
	= setting 'nextflow.enable.dsl=1' to the nextflow.config file or use the    =
	= command-line option '-dsl1' when running the pipeline.                    =
	=                                                                           =
	= More details at this link: https://www.nextflow.io/docs/latest/dsl2.html  =
	=============================================================================

This part is called a **workflow**.

Let's add a workflow to our code `ex1_a.nf`. Now we will have our first prototype of a Nextflow pipeline, so we can rename it `test0.nf`. You can find this code in **/nextflow/test0/** folder:

.. literalinclude:: ../nextflow/test0/test0.nf
   :language: groovy
   :emphasize-lines: 63-66


We can run the script sending the execution in the background (with the `-bg` option) and saving the log in the file `log.txt`.

.. code-block:: console

	nextflow run test0.nf -bg > log.txt


Nextflow log
================

Let's inspect the log file:

.. code-block:: console

	cat log.txt

	N E X T F L O W  ~  version 20.07.1
	Launching `test0.nf` [high_fermat] - revision: b129d66e57
	[6a/2dfcaf] Submitted process > printHello (hola)
	[24/a286da] Submitted process > printHello (hello)
	[04/e733db] Submitted process > printHello (bonjour)
	hola in Italian is ciao
	hello in Italian is ciao
	bonjour in Italian is ciao


The **tag** allows us to see that the process **printHello** was launched three times using the *hola*, *hello* and *bonjour* values contained in the input channel.


At the start of each row, there is an **alphanumeric code**:

.. code-block:: console

	**[6a/2dfcaf]** Submitted process > printHello (hola)

This code indicates **the path** in which the process is "isolated" and where the corresponding temporary files are kept in the **work** directory.

.. note::
	**IMPORTANT: Nextflow will randomly generate temporary folders so they will be named differently in your execution!!!**

Let's have a look inside that folder:

.. code-block:: console

	# Show the folder's full name

	echo work/6a/2dfcaf*
	  work/6a/2dfcafc01350f475c60b2696047a87

	# List was is inside the folder

	ls -alht work/6a/2dfcaf*
	total 40
	-rw-r--r--  1 lcozzuto  staff     1B Oct  7 13:39 .exitcode
	drwxr-xr-x  9 lcozzuto  staff   288B Oct  7 13:39 .
	-rw-r--r--  1 lcozzuto  staff    24B Oct  7 13:39 .command.log
	-rw-r--r--  1 lcozzuto  staff    24B Oct  7 13:39 .command.out
	-rw-r--r--  1 lcozzuto  staff     0B Oct  7 13:39 .command.err
	-rw-r--r--  1 lcozzuto  staff     0B Oct  7 13:39 .command.begin
	-rw-r--r--  1 lcozzuto  staff    45B Oct  7 13:39 .command.sh
	-rw-r--r--  1 lcozzuto  staff   2.5K Oct  7 13:39 .command.run
	drwxr-xr-x  3 lcozzuto  staff    96B Oct  7 13:39 ..


You see a lot of "hidden" files:

- **.exitcode**, contains 0 if everything is ok, another value if there was a problem.
- **.command.log**, contains the log of the command execution. It is often identical to `.command.out`
- **.command.out**, contains the standard output of the command execution
- **.command.err**, contains the standard error of the command execution
- **.command.begin**, contains what has to be executed before `.command.sh`
- **.command.sh**, contains the block of code indicated in the process
- **.command.run**, contains the code made by nextflow for the execution of `.command.sh`, and contains environmental variables, eventual invocations of linux containers etc.

For example, the content of `.command.sh` is:

.. code-block:: console

	cat work/6a/2dfcaf*/.command.sh

	#!/bin/bash -ue
	echo hola in Italian is ciao


And the content of `.command.out` is

.. code-block:: console

	cat work/6a/2dfcaf*/.command.out

	hola in Italian is ciao


You can also name the sub workflows to combine them in the main workflow. 
For example, using this code you can execute two different workflows that contain the same process. As you can see the named workflows work similarly to the process: the input is defined by the **take** keyword, while the **script** part is represented by the **main**. We also have an equivalent of **output** that is **emit** that will be described later on. The following script can be found in `test0_b.nf` file


.. literalinclude:: ../nextflow/test0/test0_b.nf
   :language: groovy
   :emphasize-lines: 27-32,39-44



We can add the **collect** operator to the second workflow that would collect the output from different executions and return the resulting list **as a sole emission**.

Let's run the code:

.. code-block:: console

	nextflow run test0_b.nf -bg > log2
	
	cat log2

	N E X T F L O W  ~  version 20.07.1
	Launching `test0_b.nf` [irreverent_davinci] - revision: 25a5511d1d
	[de/105b97] Submitted process > first_pipeline:printHello (hello)
	[ba/051c23] Submitted process > first_pipeline:printHello (bonjour)
	[1f/9b41b2] Submitted process > second_pipeline:printHello (hello)
	[8d/270d93] Submitted process > first_pipeline:printHello (hola)
	[18/7b84c3] Submitted process > second_pipeline:printHello (hola)
	hello in Italian is ciao
	bonjour in Italian is ciao
	[0f/f78baf] Submitted process > second_pipeline:printHello (bonjour)
	hola in Italian is ciao
	['hello in Italian is ciao\n', 'hola in Italian is ciao\n', 'bonjour in Italian is ciao\n']



We can change the pipeline to produce files instead of `standard output <https://www.nextflow.io/docs/latest/dsl2.html#process-outputs>`__. The script is named **test0_c.nf**.


.. literalinclude:: ../nextflow/test0/test0_c.nf
   :language: groovy
   :emphasize-lines: 13,14,18,27,28,32



