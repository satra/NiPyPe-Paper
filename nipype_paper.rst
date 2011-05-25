Abstract
--------

Introduction
------------

Over the past twenty years, advances in non-invasive in vivo
neuroimaging have resulted in an explosion of studies investigating
human cognition in health and disease. Current imaging studies acquire
multi-modal image data (e.g., structural, diffusion, functional) and
combine with non-imaging behavioural data, patient and/or treatment
history and demographic and genetic information. Several sophisticated
software packages (e.g., AFNI, BrainVoyager, FSL, FreeSurfer, NiPy, R,
SPM) are used to process and analyze such extensive data. In a typical
analysis, algorithms from these packages, each with its own set of
parameters, process the raw data. However, data collected for a single
study can be diverse (highly multi-dimensional) and large, and
algorithms suited for one dataset may not be optimal for another. This
complicates analysis methods and makes data exploration and inference
challenging and comparative analysis of new algorithms difficult.

Several technical, practical and social issues hinder replicable,
efficient and optimal use of neuroimaging analysis approaches.

No uniform access to neuroimaging analysis software and usage
information.

For current multimodal datasets, researchers typically resort to using
different software packages for different components of the analysis.
However, these different software packages use different languages and
environments for development, such as: C/C++ (FSL), MATLAB (SPM), Java
(Camino), Python (NiPy). This has resulted in a heterogeneous set of
software with no uniform way to use these tools. With the primary focus
on algorithmic improvement, academic software development often lacks a
rigorous software engineering framework that involves extensive testing
and documentation and ensures compatibility with other tools. This often
necessitates extensive interactions with the authors of the software to
understand their parameters, their quirks and their usage.

No framework for comparative algorithm development and dissemination.
Except for some large software development efforts (e.g., SPM, FSL,
FreeSurfer), most algorithm development happens in-house and stays
within the walls of a lab, without extensive exposure or testing.
Furthermore, testing comparative efficacy of algorithms often requires
significant effort (Klein et al., 2010). In general, developers create
software for a single package (e.g., VBM8 for SPM), create a standalone
cross platform tool (e.g., mricron) or simply do not distribute the
software (e.g., normalization software used for the architectonic
atlases - CHECK).

Personnel turnover in laboratories often limit methodological continuity
and training new personnel takes time.

In most cognitive neuroscience laboratories, the aims are to understand
some aspect of cognition. Although, a majority of such laboratories
gather and analyze neuroimaging data, very few of them have the
personnel with the technical expertise to understand methodological
development and modify laboratory procedures to adopt new tools.
Typically, processing steps are created and crystallized till a lab
member with appropriate skills can modify the procedures. Typical
training takes place through self-teaching by following online tutorials
or taking organized courses or most often by learning from existing
members of the lab. While this provides some amount of continuity,
understanding different aspects of neuroimaging is a steep learning
curve. This is even greater in the context of all the different
algorithms and software packages available for analysis.

Neuroimaging software packages do not address computational efficiency.

The primary focus of neuroimaging analysis algorithms is to solve
problems (e.g., registration, statistical estimation, tractography).
While some developers focus on algorithmic or numerical efficiency, most
developers do not focus on efficiency in the context of running multiple
algorithms on multiple subjects, a common scenario in neuroimaging
analysis. Creating an analysis workflow for a particular study is an
iterative process dependent on the quality of the data and participant
population (e.g., neurotypical, presurgical, etc). Researchers usually
experiment with different methods and their parameters to create a
workflow suitable for their application. A computationally efficient
execution allows for multiple rapid-iterations to optimize this tuning
process. Support for optimization of local execution (running
independent processes in parallel, rerunning only those steps that have
been influenced by the changes in parameters or dependencies since the
last run) and exploration of parameter space could ease this prototyping
stage of workflow development.

Methods sections in published articles are inadequate for replicating
results.

A typical neuroimaging analysis process involves several different
algorithms with different parameter choices. However, very few
publications contain necessary information (e.g., data, scripts,
computer code) to replicate results (however, see current manuscript
submission policies for Science and PNAS). Accurately replicating the
results of a study without such details is almost impossible.
Furthermore, with a lack of standards for review, there is significant
variability in the review process for screening of the appropriateness
of algorithms used for various processing steps. For example, it is
still common to see the use of SPM’s normalization even though the
authors themselves point to more accurate options for normalization.

In-depth knowledge of neuroimaging analysis algorithms is limited to few
individuals.

While several analysis packages implement all “procedures” necessary to
perform a particular analyses (e.g., doing a group fMRI study), the
details of the implementation are, however, different. For example, SPM
uses isotropic smoothing and FSL performs anisotropic smoothing based on
the distribution of local voxel intensities. While a potential user (a
cognitive neuroscientist) would like to freely combine processing steps
from different packages to obtain an optimal workflow, such operational
details are often not available within a laboratory knowledgebase. Thus,
the diversity of software solutions in neuroimaging field results in a
steep learning curve and high training costs.

One attempt to address some of these issues has resulted in the SPM
batch execution system. Unfortunately it supports only SPM modules and
does not run in parallel. A much more extensible and feature rich
solution is the LONI Pipeline (I. D. Dinov et al., 2009; I. Dinov et
al., 2010; Rex, Ma, & A. W. Toga, 2003). It provides an easy to use
graphical interface for choosing processing steps or nodes from a
predefined library and defining their dependencies and parameters. It
also has extensive support for parallel execution on an appropriately
configured cluster (including data transfer, pausing execution, and
combining local and remote software). Additionally, the LONI Pipeline
saves information about executed steps (such as software origin, version
and architecture) providing provenance information (A. J.
Mackenzie-Graham, J. D. Van Horn, R. P. Woods, Crawford, & A. W. Toga,
2008).

Processing nodes are defined in the LONI Pipeline using eXtensible
Markup Language (XML). This one “size fits all” method makes it easy to
add new nodes as long as they are well behaved command lines. However,
many software packages do not meet this criterion. For example, SPM,
written in MATLAB, does not provide a command line interface.
Furthermore, for several command line programs arguments are not easy to
describe in the LONI XML schema (e.g., ANTS – Avants & Gee, 2004).
Although it provides a wonderful graphical interface, the LONI Pipeline
environment does not provide an easy option to script a workflow or for
rapidly exploring parametric variations within a workflow (e.g.,
VisTrails\ :sup:``[1] <#ftnt1>`_`\ ). Finally, due to restrictive
licensing it is not straightforward to modify and redistribute the
modifications.

To address issues with existing workflow systems and the ones described
earlier, we present NiPyPE (Neuroimaging in Python: Pipelines and
Interfaces), an open source, community-developed, Python-based software
package that easily interfaces with existing software for efficient
analysis of neuroimaging data and rapid comparative development of
algorithms. NiPyPE uses a flexible, efficient and well designed
programming language – Python – as its foundation. Processing modules
and their inputs and outputs are described in an object-oriented manner
providing the flexibility to interface with any type of software (not
just well behaved command lines). The workflow execution engine has a
plug-in architecture and supports both local execution on multicore
machines and remote execution on clusters. NiPyPE is distributed with a
BSD License allowing anyone to make changes and redistribute it.
Development is done openly with collaborators from many different labs,
allowing rapid adaptation to the varied needs of the neuroimaging
community.

Methods
-------

Outline:
''''''''

#. Overview of the architecture
#. Basic building blocks – Interfaces

#. Big table with all the software currently wrapped

3. Workflows
4. Parallel execution

#. Supported backends
#. Plugin API

5. Parameter space exploration – Iterfield
6. Ease of deployment

#. NeuroDebian
#. PyPI

7. Community (development process)

Content:
''''''''

Overview
~~~~~~~~

NiPyPE consists of three components (see Figure XXX): 1) interfaces or
wrappers around external tools that provide unified way for setting
inputs, executing and retrieving outputs; 2) a workflow engine that
allows creating analysis pipelines by connecting inputs and outputs of
interfaces as a directed acyclic graph (DAG); and 3) plugins that
execute workflows either locally or in a distributed processing
environment (e.g., Torque - REF, SGE/OGE). In the following sections, we
describe key architectural components and features.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Interfaces
~~~~~~~~~~

Interfaces form the core of NiPyPE. As stated earlier, the goal of
“interfaces” is to provide a uniform mechanism for accessing analysis
tools from neuroimaging software packages (e.g., FreeSurfer, FSL, SPM).
In addition, one can also implement an algorithm in Python within NiPyPE
and expose it as an interface. Interfaces accommodate the heterogeneous
software that needs to be supported, while providing unified access for
the user. There is no need for the underlying software to be changed
(recompiled or adjusted to conform with a certain standard). There are
four things that one has to know to define a new Interface: (a) what are
the possible inputs, (b) what are the possible outputs, (c) how to run
the software, (d) what the outputs should be given a particular set of
input values. (a) and (b) include names and type and value constraints.
(c) corresponds to specific environment that needs to be set up to run
the software (which is different for command lines, MATLAB scripts, SPM
modules etc.). (d) includes collecting outputs (which do not have to
necessarily be files, but also for example values extracted from
standard output) and filling the possible outputs structure with actual
values.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To achieve an easy, extensible way to define Interfaces we decided to
use Object Oriented Programming. This way possible inputs and outputs
become parameters of a class Interface and running and collecting
outputs is done by methods. We have reduced redundancy by creating a
hierarchy of Interface classes so functionality common for i.e. SPM
modules is shared and defined in one place (see Figure XXX).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Where methods defining execution and inputs collection have to be
flexible parameters for possible inputs and outputs should be fairly
formal and include constraints. Inputs and outputs are defined using
Enthought Traits which allow creating constraints on data types and
value properties. Thanks to this feature malformed inputs (such as
misspelling the name of an optimization scheme or setting negative width
of a smoothing kernel) are detected before the framework even attempts
to run the underlying software. On top of single input wise constraints
we also provide support for defining relations between inputs. For
example some options should not be set together where others do not make
sense alone. NiPyPe supports such scenarios providing tight validation.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The goal of creating an Interface, unifying access, does not always mean
creating a one to one mirror of available options. At some occasions
exposing a inputs under different name or converting its type improves
clarity and helps new users. Special transformation of inputs if
necessary can be handled by an appropriate method. However, this is
rarely necessary. For example the CommandLineInterface has is providing
means which allow wrapping a new command line just by specifying inputs,
outputs, executable name and a method for collecting outputs.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Currently NiPyPe (version 0.4) ships with XXX interfaces (for full list
of supported software
see` <http://nipy.org/nipype/interfaces/index.html>`_`http://nipy.org/NiPyPe/interfaces/index.html <http://nipy.org/nipype/interfaces/index.html>`_).
Adding new Interfaces involves writing a Python class definition. In
case of well defined and behaved pieces of software a general solution
can be provided supporting a whole set of programs at once. This was
possible for Slicer modules which are command line applications with XML
definition of inputs and outputs.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nodes, MapNodes, and Workflows
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Interfaces can be incorporated into any custom Python script or used
interactively in Python console. However NiPyPe provides extensive
support towards the most common use case - joining a set of Interfaces
to create a workflow. Before two Interfaces can be connected they need
to be encapsulated into a Node object. This is necessary because when
Interfaces interact on the same level of one Workflow they have to have
unique names. Those names will define the structure of working directory
storing data from all steps of the workflow. The Node class has also a
special derivative – MapNode. This implements a MapReduce algorithm
(Dean and Ghemawat 2008). In other words for each element of list of
input values a separate instance of an Interface is created and executed
independently. When all interfaces finish running their results are
collected into a list and exposed through MapNodes outputs (see Figure
XXX). This approach improves granularity of the workflow and provides
easy support for Interfaces that can process only one input at a time.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Interfaces encapsulated into Node or MapNode objects can be connected
together within a Workflow object. Connections are unidirectional going
from output of parent Node to input of child Node. For example,
connecting output1 of Node A to input1 of Node B means that Node B will
be run after Node B with input1 value set to Node B output1 value.
Workflow is therefore a Directed Acyclic Graph (DAG) (loops or cycles
are not supported because, without conditional statements, they lead to
infinite execution). At the connection stage Workflow validates if all
the nodes have unique names, there are no cycles, and every connection
has a unique child Node (inputs are not overwritten).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Not only Nodes and MapNodes can be connected within a Workflow, but also
other Workflows. This enables multi level encapsulation architecture.
For example in fMRI processing preprocessing, model fitting and
visualisation can be individual Workflows connected together in the main
Workflow. This not only improves clarity of designed Workflows but also
enables easy exchange of whole subsets of pipelines. Additionally common
processing steps can be shared across different studies within a lab
thus reducing redundancy.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Every Workflow can also be cloned. This creates an identical copy off
all the nodes included in the workflow. This feature is especially
helpful when there is a need to use the same Workflow again with only
some minor changes. Instead of declaring all the Nodes and connections
again user can just clone the old Workflow and change only the few
relevant parts decreasing redundancy.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A definition of a Workflow gives a detailed description of how to
perform processing. Despite the fact it was created to execute pipelines
it is also a source of provenance data. Workflow definitions can be
included as supplementary materials for published studies and/or
exchanged between researchers. This allows ensuring that at least the
data processing part of the published experiment is fully reproducible.
Additionally exchange of Workflows between researchers stimulates
efficient use of methods and experimentation.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Parameter space exploration - Iterables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One of the focuses of NiPyPe is to provide flexibility that would help
in prototyping and experimenting with different processing schemes. This
is achieved by having unified access to variety of software packages
through in a common way (Interfaces) and ability to run workflows on
local multi core workstations (the multi processing execution plug-in)
without having to set up a cluster engine locally.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Additionally we have included a mechanism for cloning parts of the
execution graph and running each copy with different set of parameters.
This property is called Iterables and can be set on any input of any
Node. We also support multiple levels Iterables providing all
combinations of parameters of interest. This feature is especially
useful when dealing when trying to investigate interactions between
parameters of intermediate stages with respect to the final result.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Iterables are also used to execute the same workflow for many subjects,
sessions or tasks. To set Iterables one has to choose an input of a Node
and decide on the list of values this Node is going to be run with.
Setting Iterables on a Node does not only create copies of itself but
also all the nodes dependent on it. Therefore it is a different
mechanism than MapNode (see Figure XXX).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Parallel Distribution and Execution Plugins
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

NiPyPe provides a flexible framework to define how Nodes in a Workflow
are going to be executed. Currently there is support for local linear
and parallel execution, IPython and Sun Grid Engine (SGE). Each of those
modes of execution is defined through a plug-in. Extending the system by
adding support for additional cluster management system does not require
changes in NiPyPe, but only writing a plug-in conforming to the API.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before a list of tasks is going to be send to a plug-in a topological
sort is performed on the dependency graph. This provides order of
execution that maximizes parallel execution, by finding independent
Nodes. Parallel execution using local multi processing plug-in does not
require any additional software (such as cluster manager) and therefore
makes prototyping on a local multicore workstation easier.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

However for bigger studies and more complex workflows using a high
performance computing cluster can provide substantial speed gain. NiPyPe
provides support for such scenarios through SGE plug-in. Jobs in form of
Python scripts (one for every Node) are submitted to the grid. As in the
case of all other execution modes, NiPyPe engine takes care of
collecting outputs and feeding them to the right inputs.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Thanks to clear separation between definition of the Workflow and its
execution, Workflows do not need to be modified to be executed in
parallel (locally or on a cluster). Transition from testing a processing
pipeline on one subject on a local workstation to executing it on a
bigger cohort on cluster is therefore seamless.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Function Interface
~~~~~~~~~~~~~~~~~~~~~~

One of the Interfaces implemented in NiPyPe requires special attention:
The Function Interface. Its constructor takes as arguments Python
function pointer or code, list of inputs and list of outputs. This
allows running any Python code as part of a Workflow. When combined with
libraries such as nibabel (neuroimaging data input and output),
numpy/scipy (array representation and processing) and scikits-learn
(machine learning and data mining) Function Interface provides means for
quick prototyping of complex data processing methods. By using Function
Interface user can avoid writing own Interfaces which is especially
useful for ad-hoc solutions.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Workflow Visualisation
~~~~~~~~~~~~~~~~~~~~~~

To be able to efficiently manage and debug Workflow one has to have
access to a graphical representation. NiPyPe provides this by generating
static graphs representing Nodes and connections between them. In the
current version four types of graphs are supported: orig – does not
expand inner Workflows, flat – expands inner workflows, exec – expands
workflows and provides detailed connection information, and hierarchical
– expands workflows but maintains their hierarchy. Graph can be saved in
a variety of file formats including Scalable Vector Graphics (SVG) and
Portable Network Graphics (PNG).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Config Options
~~~~~~~~~~~~~~

Certain options concerning verbosity of output and execution efficiency
can be controlled through configuration files or variables. Those
include (among others) remove\_unecessary\_outputs and hash\_method. As
explained before upon rerunning NiPyPe recomputes only those Nodes which
outputs where changed since the last run. This is achieved by recording
a hash of the inputs. For files there are two ways of calculating the
hash (controlled by the hash\_method): timestamp – based only on the
size and modification time and content – based on the content of the
file. The first one is faster, but does not deal with situation when a
file is overwritten by identical copy properly. The second one can be
slower especially for big files, but can tell that two files are
identical even if they have different modification times.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To allow efficient recomputation NiPyPe has to store outputs of all
Nodes. This can generate a significant amount of data. However, not all
outputs being used as inputs to other Nodes. User can decide to remove
those outputs (and save some disk space) by setting the
remove\_unecessary\_outputs to true.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Deployment
~~~~~~~~~~

NiPyPe supports Linux and Mac OS X operating systems. We currently
provide three ways of deploying it on a new machine: manual installation
from sources, PyPi repository, and NeuroDebian repository (Hanke et al.
2010). Manual installation involves downloading a source code archive
and running a standard Python installation script (distutils). This way
user has to take care of installing all of the dependencies. Installing
from PyPI repository lifts this constraint by providing dependency
information and automatically installing required packages. NeuroDebian
is a similar solution but based on Debian/Ubuntu Linux distributions
(therefore it does not work on Mac OS X). In addition to resolving
dependencies and automatic updates NeuroDebian provides some of the
software packages supported by NiPyPe.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Development
~~~~~~~~~~~

NiPyPe is trying to address the problem of interacting with ever
changing universe of neuroimaging software. Therefore the way its
development is managed is part of the solution.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

NiPyPe is distributed under Berkeley Software Distribution license which
allows freely copying and sharing it. This way we are not inhibiting a
paradigm change in the direction the software is heading. In other words
anyone at any time can current NiPyPe source and start a new project
based on it. In fact NiPyPe meets all the points outlined by the Open
Source Initiative in the Open Source
Definition\ :sup:``[2] <#ftnt2>`_`\ .
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Development is also carried out in the open in on online community. Most
current version of the source code with complete history is accessible
to everyone. Discussions between developers and design decisions are
done using an open access mailing list. All of this encourages a broader
community of developers to join the project and allows sharing of the
financial burden. The project is more sustainable and has better chances
to carry on in the future. This is a key element considering provenance
tracking and interoperability.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Results
-------

Outline:
''''''''

#. Building a workflow from scratch

#. Iteration 1

#. Preprocessing

#. Realignment
#. Smoothing

2. Modelling

#. Modelspec
#. Level1design
#. Estimate model
#. Estimate contrast

3. Connecting everything together

#. DataGrabber
#. DataSink

2. Iteration 2

4. Add artefact detection

3. Iteration 3

#. Add BET mask

4. Iteration 4

#. Add thresholding and data visualisation

2. Comparisons

5. Two smoothing levels
6. Surface smooth, SUSAN, and 3D isotropic.

3. Example of a more complicated workflow – reliability study.

Content:
''''''''

Building a workflow from scratch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the following section, to showcase NiPyPe, we will describe how to
create and extend a typical fMRI processing pipeline. We will begin with
a basic processing layout and follow with extending it by
adding/exchanging different components.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Most fMRI pipeline can be divided into two sections – preprocessing and
modelling. First one deals with cleaning data from confounds and noise
and the second one fits a model based on the experimental design.
Preprocessing stage in our first iteration of a pipeline will consist of
only two steps: realignment and smoothing. In NiPyPe Every processing
step consist of an Interface (which defines how to execute corresponding
software) encapsulated in a Node (which defines for example a unique
name). For realignment (motion correction achieved by coregistering all
volumes to the mean) and smoothing (convolution with 3D Gaussian kernel)
we will use SPM implementation. Definition of appropriate nodes can be
found in Listing 1 (TODO). Inputs (such as register\_to\_mean from
listing 1) of nodes are accessible through the inputs property. Upon
setting any input its type is verified to avoid errors during the
execution.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To connect two nodes a Workflow has to be created. connect() method of
Workflow allows to specify which outputs of which Nodes should be
connected to which inputs of which Nodes (see Listing 2). By connecting
realigned\_files output of realign to in\_files input of Smooth we have
created a simple preprocessing workflow (see Figure TODO).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Creating a modelling workflow which will define the design, estimate
model and contrasts follows the same suite. We will again use SPM
implementations. NiPyPe, however, adds extra abstraction layer to model
definition which allows using the same definition for many model
estimation implemantations (for example one from FSL or nippy).
Therefore we will need four nodes: SpecifyModel (NiPyPe specific
abstraction layer), Level1Design (SPM design definition), ModelEstimate,
and ContrastEstimate. The connected modelling workflow can be seen on
Figure TODO. Model specification supports block, event and sparse
designs. Contrasts provided to ContrastEstimate are defined using the
same names of regressors as defined in the SpecifyModel.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Having preprocessing and modelling workflows we need to connect them
together add data grabbing facility and save results. For this we will
create a master workflow which will host preprocessing and model
Workflows as well as DataGrabber and DataSink Nodes. NiPyPe allows
connecting Nodes between workflows. We will use this feature to connect
realignment\_parameters and smoothed\_files to modelling workflow.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

DataGrabber allows to define flexible search patterns which can be
parameterized by user defined inputs (such as subject ID, session etc.).
This allows to adapt to a wide range of file layouts. In our case we
will parameterize it with subject ID. In this way we will be able to run
it for different subjects. We can automate this by iterating over a list
of subject Ids, by setting an iterables property on the subject\_id
input of DataGrabber. Its output will be connected to realignment node
from preprocessing workflow.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

DataSink on the other side provides means to storing selected results to
a specified location. It supports automatic creation of folder stricter
and regular expression based substitutions. In this example we will
store T maps.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A pipeline defined this way (see Figure TODO, for full code see
Supplementary material) is ready to run. This can be done by calling
run() method of the master Workflow.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adding artefact detection
~~~~~~~~~~~~~~~~~~~~~~~~~

The example pipeline so far uses only SPM components and with the
exception of DataGrabber and DataSink it could have been executed using
SPM batch manager. We can extend it by adding non SPM components. Apart
from motion correction and smoothing one can try to detect volumes
confounded by acquisition or motion artefacts and add them to the design
matrix as confound regressors. NiPyPe has a build in implementation of
Artifact Detection Tool (TODO reference) which using motion parameters
and global signal estimates which volumes should be omitted in the
analysis. ArtifactDetect Node takes two inputs: realigned volumes and
realignment parameters. Its output can be send to SpecifyModel node to
include new regressors in the design matrix. Workflow with added
artefact detection can be seen in Figure TODO.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adding brain mask estimation from structural image
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Without specifying a mask explicitly SPM will estimate one from EPI
sequence. However, this is not the best signal to estimate border
between brain and skull. T1 sequence is usually used for this purpose.
Additionally one can overlay the results (thresholded T-maps) on it for
presentation purposes. FSL provides a brain extraction tool (BET)
(Stephen M Smith, 2002). To incorporate it into the example workflow we
will need to coregister functional images to the structural.
Coregistration will be done using SPM implementation estimated on the
mean functional image from the realignment Node and applied to the whole
series. Coregistered images will become an input of the smoothing Node.
We will also add a BET node which will get input from DataGrabber (the
raw T1 volume) and provide inputs for ArtifactDetect (to restrict
artefact detection only to relevant areas) and Level1Design. The
improved Workflow can be seen in Figure TODO.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Thresholding and visualising statistical maps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Another step that is missing from the example workflow is thresholding
of the statistical maps estimated by the EstimateContrast Node. For
thresholding we will use topological (random field theory based) false
discovery rate corrected thresholding calculated on cluster sizes
implemented in SPM. For visualisation we will use combination of two
tools from FSL: Overlay (to merge structural and thresholded statistic
volumes) and Slicer (to create a bitmap of slices). Bitmaps will also be
sent to DataSink. Resulting overlay bitmaps and extended workflow can be
seen on Figure TODO.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Comparison of different smoothing methods
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One of the goals of NiPyPe is make comparison between different
parameters and algorithms easier. For example the Full Width Half
Maximum (FWHM) of the smoothing kernel is usually set to an arbitrary
value. Because smoothing take place in the middle of the pipeline (after
realignment but before model estimation) it can influence all the steps
following it. It would be therefore useful to branch the processing just
before Smooth Node and run it and all its direct and indirect children
with different FWHM. NiPyPe support this scenario through iterables. In
the same matter as with subject IDs user can iterate over a set of
FWHMs, effectively cloning relevant branches. A comparison between 4mm
and 8mm FWHM with corresponding workflow is presented in Figure TODO.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Another common scenario involves comparison between different methods
trying to achieve the same goal. In the case of smoothing those include
(but are not limited to): isotropic volumetric smoothing, anisotropic
volumetric smoothing, isotropic surface smoothing. First one is the
method used in the example pipeline. Second one involves smoothing only
voxels of similar intensity in attempt to retain structure. This was
implemented in SUSAN from FSL (S.M. Smith, 1992). Third method involves
reconstructing surface of the cortex and smoothing along it (Hagler Jr.,
Saygin, & Martin I. Sereno, 2006). This avoids bleeding of signal over
sulci.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Establishing parameters from data and smoothing using SUSAN is a
Workflow build into NiPyPe. It can be created using
create\_susan\_smooth() function. It has similar inputs and outputs as
SPM Smooth Interface.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Smoothing on surface involves doing a full cortical reconstruction from
T1 volume using FreeSurfer (Fischl, M I Sereno, & Dale, 1999) followed
by coregistering functional images to the reconstructed surface using
BBRegister. Finally surface smoothing algorithm from FreeSurfer is
called.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Statistical maps along with the pipeline used to generate them can be
found in Figure TODO.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

More complex workflows
~~~~~~~~~~~~~~~~~~~~~~

The example workflow outlined above was kept oversimplified for
demonstration purposes. NiPyPe, however, scales well for more
complicated designs. As a proof we have analyzed real world fMRI
reliability study using NiPyPe. Processing has iterated over subjects,
tasks (motor, 3 x language, and line bisection), sessions, thresholding
methods (topological FDR with Gamma-Gaussian Mixture Model or FWE
cluster forming threshold), and Regions of Interest (full brain or
relevant cortical area). For each combination of iterables PDF reports
including realignment parameters, histograms of T values, thresholded
and unthresholded T maps were created. Diagram of this Workflow can be
seen on Figure TODO. Basing on output of this Workflow within (Figure
TODO) and between (Figure TODO) subject variability estimation Workflows
were created. Overlap maps were also saved as PDFs and Dice and Jaccard
coefficient were recorded in local database.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Despite of the complexity of this analysis thanks to support for
encapsulating workflows we were able to divide it into independent,
reusable, and manageable parts.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Discussion
----------

Outline:
''''''''

#. Why Python?
#. Scripts vs XML
#. Future directions
#. Open Science
#. Provenance
#. What features of LONI should we implement in the future?

Content:
''''''''

Python
~~~~~~

One of the core development decisions was selecting a programming
language. We have decided to use Python for several reasons. It was an
imperative that the language we will use should support scientific
computing and Python meets those criteria thanks to packages such as
SciPy and NumPy (Millman & Aivazis, 2011; Pérez, Granger, & Hunter,
2010) . What is more it also supports reading and writing common in
neuroscience file formats such as NIFTI, ANALYZE and DICOM (through
nibabel library). From design point of view it was important that Python
supports rapid prototyping and is easy to adopt (which is important for
community based projects). Another crucial aspect was freedom of
availability and openness. Other popular within scientists and engineers
platforms such as MATLAB are commercial in nature and therefore restrict
theirs user base. Finally Python has been already embraced by
neuroscientific community and slowly is gaining popularity (Bednar,
2009; Goodman & Brette, 2009).
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Flexibility vs Standardization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Despite trying to provide common way of calling and using diverse set of
software we have decided not to impose declarative description of
Interfaces. Solutions such as LONI Pipeline decided to follow a contrary
path by using XML. Our decision was motivated by flexibility. Within
neuroimaging community software is not only limited to well behaved
command line tools. We wanted to be able to support scripting language
software requiring an interpreter (such as SPM with MATLAB).
Additionally non declarative transformations of inputs and outputs have
been necessary for many Interfaces. At the same time through a hierarchy
of classes and helper methods we provide mechanisms that reduce time of
wrapping new commands that do not require such transformations. In this
way we achieve the best of both worlds.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Despite de differences between declarative (XML) and non-declarative
(methods, classes) approach to wrapping software interoperability should
be easily achievable. It is foreseeable to write and automated XML
interface description generator based on NiPyPe Interface classes. Such
solution would probably have to call python and use NiPyPe as
intermediate stage in communication, but at the same it would allow
systems such as LONI Pipeline to support less standard software
solutions (such as SPM). Transformation in the other direction could
also be possible. Interface classes could be generated based on XML
descriptions. In fact this is being already done for modules provided by
Slicer 3D.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Openness and provenance tracking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Openness lies at heart of NiPyPe in both design and development process.
Source code is freely distributed under BSD license allowing anyone to
fork the project at any given time. There are no restrictions for usage
and redistribution. NiPyPe conforms to the Open Software Definition of
the Open Source Initiative.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Development process is fully transparent. Proposed changes and
discussions between developers are accessible by anyone on the Internet.
This stimulates community to get involved in the development which
results in contributions from users from all around the world. Diverse
and geographically distributed user and developer base makes NiPyPe a
flexible project that takes into account needs of many scientists.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Improving openness and transparency of research is also a goal of
NiPyPe. A workflow definition is in principle sufficient to replicate
the analysis. Since it was used to actually analyze the date it is more
detailed and accurate than description in a paper. Thus by accompanying
publication with formal definition of processing pipeline (such as a
NiPyPe script) increases reproducibility an transparency or research.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This said NiPyPe can improve in the field of provenance tracking by
including information of the release version of the software and
architecture used for each step. Also creating provenance reports in a
standardized XML format (Mackenzie-Graham, Van Horn, Woods, Crawford, &
Toga, 2008) is planned in future releases.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Comparisons and pipeline optimization studies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Increased diversity of data processing software made systematic
comparison of performance and accuracy difficult. Despite the fact that
some research has been done in this field, for example comparing
coregistration methods (Klein et al., 2009; 2010), we hope that NiPyPe
will make such comparisons easier and therefore more prevalent.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Another way of evaluating software is to investigate the optimal
combination of preprocessing steps. Recently relation between motion
correction and regression has been researched coming with a method of
finding optimal per subject preprocessing pipeline (Churchill et al.,
2011). NiPyPe can make such investigation easier resulting in more
efficient data analysis.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Summary
-------

We present a novel extensible framework for creating interfaces and
pipelines for neuroimaging data processing. Community based open in
nature development provides flexibility required in scientific use.
Prototyping, algorighm comparison and parameter space exploration are
some of the core design features. Our framework also improves
reproducibility by providing provenance tracking. Exchangibility of
pipelines created using NiPyPE stimulates collaboration in the broader
neuroimaging community.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

--------------

`[1] <#ftnt_ref1>`_ http://www.vistrails.org/

`[2] <#ftnt_ref2>`_http://www.opensource.org/docs/osd

`[a] <#cmnt_ref1>`_helenlramsden:

until

`[b] <#cmnt_ref2>`_davclark:

delete? Verbose and (to my eye) counter to the clearly evident truth
("in fact" often cues "you might not have thought XXX")

`[c] <#cmnt_ref3>`_krzysztof.gorgolewski:

I could not find a shorter example of a well known program with
dependecies in inputs. We can alternatively show only part of this.

--------------

davclark:

You might say that Bet does skull stripping (or more idiosyncratically,
"Brain Extraction"). I think the point could be made with an ellipsis
and perhaps textually say that you cover the 18 different arguments. Of
particular interest, I would leave in the xor\_flags, as that's a nice
feature.

--------------

cindeem:

Second Davs comment, dont let them get lost in the code, just outline
the impt parts, use highlighting? Im not sure what the journal allows,
but this would help.

`[d] <#cmnt_ref4>`_cindeem:

here you use "interfaces" so I would stick with this common term

`[e] <#cmnt_ref5>`_davclark:

I mention this in my high level notes

`[f] <#cmnt_ref6>`_satrajit.ghosh:

chris: what do you mean by this?

--------------

krzysztof.gorgolewski:

For example if you are collecting data over several years and you are
analysing them manually each time new subject comes in. Over the years
staff members can change which can result in subjects being analysed in
a different way.One should either use the same automated workflow each
time or save raw data and analyse all the subjects at the end of the
study. I admit the latter option is better and it makes the whole
argument a bit artificial.

`[g] <#cmnt_ref7>`_krzysztof.gorgolewski:

Is this something different than iterables\_vs\_mapnode?

`[h] <#cmnt_ref8>`_cindeem:

While this provides some amount of continuity, understanding different
aspects of neuroimaging has a steep learning curve, made steeper by
incorporating the time needed to learn the different package interfaces
and algorithms.

`[i] <#cmnt_ref9>`_helenlramsden:

spoken English, use 'due to'

`[j] <#cmnt_ref10>`_krzysztof.gorgolewski:

Isn't it a bit of an overkill to show all different types of graphs?
Maybe we should point just to one of the workflow graphs from Result
section?

`[k] <#cmnt_ref11>`_cindeem:

I think this is fine, but for a reader not familiar with SPM, this may
be hard to follow?

`[l] <#cmnt_ref12>`_cindeem:

Node and MapNode objects provide interfaces with additional properties
(e.g., hash checking of inputs, caching of results, ability to iterate
over inputs). Additionally they execute the underlying interfaces in
their own uniquely named directories, thus providing a mechanism to
isolate and track the outputs resulting from executing the Interfaces.
These mechanisms allow not only for provinence tracking, but aid in
efficient pipeline execution. They provide the framework necessary to
create a data analysis Workflow.

`[m] <#cmnt_ref13>`_duncancarmichael:

extensive?

`[n] <#cmnt_ref14>`_krzysztof.gorgolewski:

I am a bit afraid to make provenance tracking a big point. UCLA
implementation has the following advantages: it's independent from LONI
Pipeline, its standardized using an XML Schema, it includes architecture
and version tracking.

`[o] <#cmnt_ref15>`_krzysztof.gorgolewski:

What figure dis you have in mind here?

--------------

satrajit.ghosh:

i was thinking of a simple doctest code

`[p] <#cmnt_ref16>`_cindeem:

Unless you want to be more qualitative you may need more info on the
system here, or make it more general??

`[q] <#cmnt_ref17>`_davclark:

I assume you'll fix the formatting here - it might confuse people with
moderate familiarity with python

--------------

krzysztof.gorgolewski:

Yes.

`[r] <#cmnt_ref18>`_davclark:

I think this is a big deal. You kind of gloss over it.

`[s] <#cmnt_ref19>`_cindeem:

I like this graph, but it is a little hard to follow the 3 components
listed above...possibly interfaces could have a separate shape? combine
workflow-engine? and the idea of nodes and mapnodes are intserted
without much explanation. Maybe also add an example of how the interface
wraps an external package?

`[t] <#cmnt_ref20>`_yarikoptic:

It doesn't matter really for a user in what language it is written. It
is important on how to interface/use it. E.g. shell scripting (FSL,
AFNI, Camino), Matlab (SPM), Python (NiPy).

--------------

krzysztof.gorgolewski:

Good point: "However, these different software packages are accessed and
interfaced with in different ways, such as: shell scripting (FSL, AFNI,
Camino), Matlab (SPM) and Python (NiPy)."?

--------------

yarikoptic:

something like that ;-)

`[u] <#cmnt_ref21>`_helenlramsden:

This is compounded by?

`[v] <#cmnt_ref22>`_satrajit.ghosh:

need to create this page

`[w] <#cmnt_ref23>`_cindeem:

With Nipype, the developer can create one Interface and expose this new
tool , written in any language, to a greater range of users, knowing it
will work with the wide range of software currently supported by Nipype.

`[x] <#cmnt_ref24>`_cindeem:

might be nice to state this in the opening paragraph....

The goal of “Interfaces” is to provide a uniform mechanism for accessing
analysis tools from neuroimaging software packages (e.g., FreeSurfer,
FSL, SPM). Interfaces can be used directly as a Python object,
incorporated into custom Python scripts or used interactively in a
Python console. They are the core of the nipype pipeline.

`[y] <#cmnt_ref25>`_krzysztof.gorgolewski:

Needs incorporating into the section.

`[z] <#cmnt_ref26>`_yarikoptic:

what kind of script was meant so that it is different from command line
tool? probably you meant native "Python module" like in the case of
nipy?

`[aa] <#cmnt_ref27>`_cindeem:

choose one term and use, or this gets harder to follow...since we call
them interfaces in the code, we should stick to it for now?

--------------

cindeem:

1) interfaces wrap around external tools providing a unified way for
setting inputs, executing, and retrieving outputs.
