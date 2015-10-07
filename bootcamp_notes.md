# LSST Bootcamp Notes

See slides at: https://github.com/lsst-dm/Oct15_bootcamp

## Introduction to LSST (Mario)

Specs:

- 18K deg sq
- 10mas astrometry
- r<24.5 (<27.5 after 10 years)
- ugrizy
- 0.5-1% photometry at bright end
- 3.2 Gpix camera
- 30 sec exposure (split into 15 sec); 4 sec readout
- 825 visits
- 15 TB/night
- 37B objects

End goal is to turn the sky into a database.
People will use the survey catalogs and images entirely.

### Areas

1. Time domain

   - GRB, nova, supernova
   - Source characterization of objects
   - instantaneous discovery of new things

2. Census of the solar system

   - NEOs, Cometa, KBOs, Oort Cloud

3. Map the Milky Way's tidal streams and galactic structure. Use RR Lyrae etc for distances. LSST covers just narrow beams through MW.

4. Dark energy and dark matter.

   - Strong lensing
   - Weak lensing
   - Constraining the nature of dark energy

### Site

Cerró Pachon. Summit is called? XX

Dome is built to get the best ground effect and optimal seeing.

### Camera

- 3.2 Gigapixels
- 0.2 arcsec pixels
- 9.6 sq deg FOV
- 2 sec readout
- 6 filters
- camera diameter is 1.65 m (5' 5")

### Data Management

- 5 PB/yr of imaging
- Each data release costs $120M (given project costs)

Our challenges

### Processing data in a way that enables science

- `Model <- inference <- Data`
- Ideally you'd *want* to do completely degenerative modelling of telescope+camera+galaxy to fit the galaxy, for example
- Instead do `Model <- inference <- catalog <- data processing <- Data` (data processing means instrumental calibration + measurement)
- In other words, the data is *reprojecting* and possibly *compressing* the dataset to make it easier to performance **inference** against.
- LSST does more than most observatories! Most observatories require astronomers to do inference against raw data.

Guiding Principles of how to process data and what to measure into catalogs:

1. Maximize science enabled by the catalogs. Most LSST science will come from just the catalogs.
2. Minimize information loss
3. Provide and document the transformation (software)

This is captured in the LSE-163 Data Products Defintion Document. http://ls.st/dpdd

### LSST from a user's perspective

- **Level 1.** Stream of 10M time domain events per night. Detected and transmitted to brokers within 60 seconds. L1 also updates a catalog orbits for 6M bodies in the Solar System.
- **Level 2.** Deep coadded images. Catalog of 37 billion objects (20B galaxies, 17B stars). 7T single-epoch detections and 30T forced sources.
- **Level 3.** Ability to work with the catalogs and add analyses to the pipelines.

### Project

- KT: Planning and execution
- Mario: vision
- Jeff: resources and standards compliance

LSST Team is about 50 people.

### DM System Vision

We will enable LSST science by creating a well documented, state-of-the-art, high-performance, scalable, multi-camera, open-source, O/IR survey data processing and analysis system.

We will extensively test this system on pre-cursor data and surveys.

We will make it easy for anyone to deploy and run the software.

### Sites

- Summit/base: 
- 2 long-haul networks (200 Gbps to La Serena; 2x40 Gbit long hault)
- NCSA pope

### Applications

Applications (Science Pipelines) carry the core scientific algorithms that process or analyze raw LSST data to generate output Data Products

LSST Science Pipelines encompass a huge variety of analysis tasks. Slide of WBS numbers

### Software choices

- Python 2.7 codebase. Use Python whenever possible.
- C++ for computationally intensive code made available via SWIG.
- Modularity. Virtually everything is a Python module. 100+ packages.
- Scons, Eups, git.

We're re-writing out own pipeline because the existing ones were hard to adapt.

### Middleware.

1. NSCA: Enable execution of science poplines on hundreds of thousands of cores

   - Frameworks to construct pipelines
   - Orchestration to execute on thousands of cores
   - Control and monitor the whole DM system

2. SLAC:

   - FIXME Check slides

### Delivery DBs with a UI

- SLAC: Provide Qserve database. Parallel, distributed fault-tolerant relational database
- Science User Interfaces to enable and access LSST data

### Level 1

Rapid follow-up.
Need to transmit alerts within 60 seconds of readout.

Image differencing algorithms need to work in 60 secs without adding false positives and measure interesting quantities.

- position
- flux, size and shape
- light curves in all bands (up to a year; stretch: all time)
- Variability characterization
- Cut-outs centered on the object

Timeline: see slides.

NOTE: it'd be cool to make a D3 timeline with the sequence diagram.

Close collaboration is needed to make this pipeline hum.

### Level 2

- Year 1 will have two data releases
- Each DR will encompass all data take up to that time
- DR1 will have 11B objets; 37B by DR10.

Multifit: fit models of objects on individual images rather than measuring on the co-adds.

### Level 3

To enable the community to create new products using LSST's software, services or computing resources.
This means

- Providing the software primitives to construct custom measurement/inference codes
- Enabling the users to run those codes at the LSST data center, leveraging the investment in I/O (piggyback on LSST's data trains).

Software may well be the long-term legacy of LSST.

**Users may create the most successful catalogs to come out of LSST; DR catalogs may just be one example that is most broadly applicable.**

**We want to engineer our sofware and hardware to make this possible.**

### Conclusions

1. Keep the system vision in mind at all times.
2. Question, learn, own the problem. You need to be a part of DM team culture.

   - **Understand**: Think! Understand your work and why you're doing it. Don't just make it work; make it the best. Always understand the big picture.
   - **Question and be curious**: Always strive to learn about why things are the way they are. **But don't settle; propose and argue for improvements. Don't fear giving feedback.** But also expect to be questioned about your decisions. Understand our Benevolent Dictator-ish model and commit to decisions that are taken.
   - **Work with the Team, Deliver, and Earn Trust**. The ability to work with the team, to solve problems, and (ultimately) to deliver, is the currency of the team. This is how we earn trust from each other, and how we make progress.

Read:

- LSST Overview Paper arxiv.org/pdf/0805.2366
- LSE-163 DPDD
- LDM-148 System Design
- LDM-151 Science Pipelines
- LDM-152 Middleware
- LDM-153 Database

## Basic afw and Data Butler Concepts (KT-Lim)

A summary of how to use the Stack from an end-user's perspective. More details about AFW tomorrow.

### Why a stack?

- Build a toolkit/framwork that can be plugged together from individual parts
- Need standardized interfaces
- Built to be scalable and portable (high performance computing)

### Task abstraction

(FITS not used internally? All data in memory in tasks?)

- Tasks (algorithms) operate on *objects*, not physical representations (i.e., on files)
- Escaping from the binary program + file metaphor
- Allow tasks to be invoked in many contexts (CLI, large-scale production, from SUI/T, SDQA)
- Allows data to be stored in many formats?

  - Filesystem, tape, object store (like an archive), database
  - Local or remote

### What is afw?

- Applications framework
- Applications is really Science Pipelines
- Framework is really a library or toolkit
- Therefore: Library of astronomical image processing obects that can be used to build algorithms and pipelines

### Image Data

#### Image

- 2D array of pixels
- Pixel types: uint16, int32, float, double

#### Masks are special types of images; bit masks (16-bits typically; probably 32-bit).

- Every bit has a name associated with it.
- Eventually add custom bits
- Mask object contains metadata information (dict) that describes the bits.
- TODO: we should publish a table of standard bits.

#### Exposure

Three planes plus metedata

1. Image
2. Mask
3. variance
4. plus metadata and additional objects (PSF)

Metadata includes

- WCS
- PSF
- Filter
- Calib
- Detector
- PropertyList (key-value pairs of metadata)

### Skymap

- Overlapping **tangent plane projections (tracts)**

  - Dodecahedral, declination band, specific positions, rings, HEADPixes.

- Each divided into overlapping rectangular segments (patches)

  - Inner regions exactly tile tract, overlap border within tract

Note: we *could* decide to use a different pixelization in the future.

### Table/Record

- Like a relational (SQL) table or a FITS table
- Columns of varying types, defined by a schema
- Rows (records) for different measurements

This is completely custom. Lots of maintenance for a generic data type. But it is convenient.

SQL aspect: tables can be related to other tables. **But** afw tables don't actually do SQL-like joins.

### Data Butler

Aka *Data Client Access Services*.

- Butler gets data, puts data, lists data
- Butler manages translations between physical formats and internal afw objects
- Butler does not do I/O itself; it is a framework for allowing I/O to be configured. The afw objects themselves know how to do I/O. Think of afw as a router or a switch.

Butler is really an interface layer. The actual work is implemented in a mapper

#### Butler manages Repositories

- A repository is a collection of datasets referenced by a path or a URL
- In practice, today, it is a directory tree.
- Repository structure defined by its mapper (and its mapper's configuration).
- The mapper is selected by the `_mapper` file in a repository.
- Mappers typically written by the camera/instrument/observatory experts that know the layout of camera's file.

##### Repositories can be chained

Output repositories are automatically chained to the parent input repository.
Datasets from anywhere in the parent chain can be retrieved.
Pointing to a child repository also lets you see/get objects from the parent repositories.
This is implemented in such a way that it *looks* like the parent data is part of the child's repository.

#### Datasets

- Anything that can be persisted or retrieved
- Some datasets can *only* be retrieved; possibly only persisted
- Range from numbers to images with metadata to entire catalogs

##### Identifying Datasets

- Dataset type

   * Not Python or C++ dtype
   * Each mapper can have its own list of dataset types (though there are some standard ones)

Hello world.

- Data `id`
  * key/value pairs
  * Each dataset type can have its own list of keys
  * Different mapper can require different keys
- **Documentation of dataset types and data id keys is sparse.** Currently documented ad-hoc in `obs_*` package policy files.
  * Need docs for available keys
  * Need docs for available types
- Data reference (for programming)
  * Combination of Butler and data id
  * It *does not refer to a dataset type.* Can be applied to multiple dataset types (if appropriate).

##### Common Data ID Keys

- `visit`, `ccd`, `filter`
- `tract`, `patch`

##### Partial Identification

Only unique key/value pairs need to be provided in a data id:

- others are looked up in a registry database (registry is created by a script for a given camera)
- registry typically generated from raw input data
- allows for data to be looked up if there is no degeneracy to identify a dataset

Rendezvous:

- Info from one dataset can be used to look up another dataset
- For example: used to determine what calibration images apply

You could write your own rendezvous code and put it in the mapper.

##### Common Dataset Types

- Images and exposures

  - raw (postISRCCD, visitim, icSrc, icMatch)
  - calexp
  - coaddTempExp
  - deepCoadd-calexp (deepCoadd)

- Calibration Exposures

  - bias, dark, flat, fringe

- Detection and Measurement Tables

  - `src` (`srcMatch`), `src_schema`, `transformed_src`, `transformed_src_schema`

##### Other Metadata Types

FIXME See slides.

### Summary

**Tasks** use the Butler interface to operate on datasets, often Images or Exposures, identified by data ids within repositories, producing new dtasets, often new Exposures or Tables, in an output repository chained to the input repository. The Butler uses a camera-specific Mapper to define the repository structure, available datasets, and data id keys.

## Using Tasks (John Swinbank)

### What is a task

- A coherent unit of work
  - Unit of work varies from the trivial (add two numbers) to the complex (do everything needed to detect and measure sources, including ISR, calibration, source finding, etc)).
- Tasks are combined hierarchically.

```
setup pipe_tasks -t v11_0
cd ${PIPE_TASKS_DIR}/examples
```

Grab some data from afwdata (FIXME)

### Run on CLI

```
./exampleStatsTask.py small.fits
```

### Run from Python

Tasks can also be run form Python

```
task = ExampleSimpleStatsTask()
result = task.run(maskedImage)
```

Note; there's nothing special about `run()`. In theory you can call any method on a task object. We don't use ``_call__()`` in order to be more transparent.

The `result` is a struct. You access results from attributes on the Struct.

### Configuration

```
config = ExampleSigmaClippedStatsTask.ConfigClass(numSigmaClip=1)
config.numSigmaClip = 2  # change attribute later
task = ExampleSigmaClippedSatsTask(config=config)
```

### CLI

You could wrap all the tasks in a homebrewed command line interface; but we shouldn't.

Using CmdLineTask provides us with a standard interface across all our tasks.

- Includes interaction with Butler (read/write data, store task config and metadata)
- Set and show config, parallelization
- FIXME a third point.

### Processing Model

- Butler integration adds complexity
- Rather than specify a filename on the CL, we specify the path to a repository and the data id

```
myTask.py /path/to/repository --id data_id [options]
```

The middleware will iterate over everything in the repository that matches `data_id` and call the task's `run()` method

Tasks come with a set of default configuration.
These defaults can be overridden within the hierarchy of sub-tasks.

``--id`` on a task, without arguments, run on *all data in a repository*.

``--show data`` will tell you what data it will work on.

``--id filter=g`` selects data with that metadata

``--id visit=1..3`` (include range)

``--id visit=1..3:2`` with a *stride* of 2.

``--id visit=1^3`` does an OR.

Sometimes you see both `id` and `--selectID`.

### Configuration

`--show config` is essentially python code

Some Python configs can be done on the command line; but not all.

Instead, use a Python config file. Pass it on the command line with ``-C``.

Most tasks store their configuration ot the repositoy when they are run.

Tasks will refuse to run again if config on CL is different from that already persisted in the repository.

### Composition

Want to compose large tasks out of simple tasks.

`--show tasks`

We can swap (or retarget) another task that has the same interface.

### Additional repositories

- Most tasks produce data products
- These are normally written to the repo provided on the CL
- We can use the `--output` option to specify a different repository

### Debugging

`--debug`. This causes it to import a `debug.py`. This `debug.py` has a `DebugInfo` class that can be called to help produce debugging information.

NOTE: this needs to be combined with the logging framework.

The debugging options are stored separately of the tasks, so its easy for those docs to bit rot.

Can also `--loglevel DEBUG`.

Note: this is *not* the same as `--debug`.
Use `--debug` to open DS9, etc.

To abort on error, `--doraise`. This way it stops before going on to the next data.

### Parallelism

Use `-j N` to run multiple data ids at once. This uses Python's `multiprocessing`.

Large scale parallelization will be done by the NCSA middleware.

### Documentation

See `pipe_base` doxygen and the list of available tasks and their documentation on Doxygen.

## EUPS (John Swinbank)

Extended Unix Product Support.

It is a tool for managing multiple versions of interdependent software packages.

It allows for multiple version of the packages installed at once for both development and science.

- Develop with today's version of the stack
- fix bugs in yesterdays
- reproduce your science run from last year

EUPS manipulates your environment to make this tractable.

```
loadLSST.bash
eups path
# retarget
unsetup lsst
mkdir -p
```

NOTE: need to get product/package terminology straight.

Most commands are in `eups`. `setup` and `unsetup` are special and used on their own.

Packages Table (.table) files are used tell eups how to prepend the PYTHONPATH to include source from the package.

```
eups list
eups tags
```

How are the current/latest tags being uploaded? Is that only if you're sharing a stack?

Commands to try

```
eups list
eups list -s
setup -v lsst_apps
more ${LSST_APPS_DIR}/ups/lsst_apps.table
```

### Eups distrib

The package distribution mechanism used with eups.

```
eups distrib path
```

Fetch and install packages defined on some remote server

```
eups distrib list lsst_apps
eups distrib install -t v11_0 lsst_apps
```

To see tags go to https://sw.lsstcorp.org/eupspkg/tags

### Conclusions

- EUPS is a third party package
- GitHub issue tracker for problems with EUPS itself
- Report stack installation problems on JIRA
- Tips on trac: https://dev.lsstcorp.org/trac/wiki/EupsTips

## Data Management Organization and Management (Jeff Kantor)

We're a NSF MREFC Project. More on that later.

### DM Team

- 28 new Team members added nsince MREFC
- DM Staff is now 48 people
- FY16 is to have 50 FTE
- Trick is to keep the expertise/experience high as we staff up
- DM is a highly distributed team. Multi-technology. Multi-cadence.
  - IPAC
  - SLAC
  - UC Davis
  - UW
  - NCSA
  - SLAC
  - NOAO
  - FIU
  - REUNA

The project organization is described in [LDM-294](http://ls.st/LDM-294).

### Institution Organization

- Each institution has a Technical/Cost Account Manager (T/CAM) and a Scientific/Technical Lead (S/TL). They can two people or a single person.
- T/CAM plays the management role (staffing, etc)
- DM Leadership Team of S/TLs and T/CAMs meets weekly.
- Science/Architecture Team (SAT)
  - Meets monthly (or as superseded by RFD).
  - Working Groups (WG): Applications, Middleware, Infrastructure, Database, Operations
- Technical Control Team (TCT)
  - Collection-2511 - this is the technical baseline/commitment for what we deliver.
  - Meets as necessary

### LSST DM is part of a NSF MREFC

- We are members of the LSST Project
- The LSST Project is being funded and conducted as a joint project by the NSF and the DOE, with participation by Chile and other International Partners
- The LSST Camera is funded as Major Item of Equipment (MIE)
- The other parts of the project (PMO, DM, EPO, System Engineering) are funded as Major Research Equipment and Facility Construction (MREFC).
- We get money yearly; overall Construction has $1B from NSF, other amount from DOE.
- There are **many** rules about what is allowed on project funding
  - The rules vary somewhat between NSF and DOE
  - With NSF, DM pipelines is *ok*, but doing general science is *not ok*.
  - E.g. Can write papers about the pipelines, but not about the science.

### Regularly Scheduled Meetings

- Online meetings
  - Online meetings (SAT, DMLT, TCT, WGs)
  - WBS Oriented Meetings (SUI, Infrastructure)
  - Technical Coordination Meetings (e.g. coordination stand-ups)
- Face to Face meetings
  - with Entire DM Team
    - LSST Project and Community Workshop in August, location varies.
    - Annual DM All hands Meeting in February, location varies. Usually February.
  - DM LT: At DM Team Meetings plus in October/November, May/June, location varies.

For schedule DM Meetings and DM Travel google calendars.

### Collaboration Tools

- Mailing lists (lists.lsst.org). dm-announce, dm-staff, dmlt, etc. Some are *controlled* subscription to reach out to certain people. Others are self-subscribing.
- [HipChat](http://hipchat.org). Use for informal discussions. May end up retiring it/moving to SLAC.
- [Community Forum](http://community.lsst.org)
- [Confluence](http://confluence.lsst.org). Project standard.
- [JIRA](http://jira.lsstcorp.org). Project standard.
- [URL shortener ls.st](http://http://ls.st)
- [Google Hangouts](http://ls.st/sup)
- [project.lsst.org](http://project.lsst.org)
  - Travel
  - Contacts
  - Calendars
  - Interactions database: add your interaction there to give NSF advance notice. If you're not representing NSF/DOE/or funding profile, it doesn't matter.
  - Risk Management

### DM Planning Process

1. Top Level
   * LSST System Requirements and Operations Plans
   * Proejct Risks and Milesotones
2. DM System Reqs and Roadmap (LDM-240)[http://ls.st/LDM-240]
   - High-level picture
   - 6-month granularity
3. PMCS
   - Institutional-level resource assignments
   - Budgeting
   - Earned-value management
   - Basis of montly reports
4. FIXME see slides for next level

### JIRA

- Software Development and sme non-software efforts are planned and managed at the task-level using JIRA and JIRA Agile
  - Web based interface
  - Issues cover essentially all task-based work
  - Tracks all history and actions on the Issue being updated
  - Excellent tool to collect status on the work being performed
- Add JIRA issue as soon as you suspect something is an issue or needs to be done
- Use Request for Comment to notify and discuss an issue or need that is more complex or larger scope.
  - If no one raises issues, you have implicit approval

### JIRA Agile

JIRA agile adds agile process to JIRA in the form of stories, epics and sprints, kanbans.

- Software development is generally schedule in sprints, with other activity are generally kanbands
- Developers and T/CAMs create stories (new developments) improvements (to existing code) and bugs (fixes)
- Stories are preferably 2-20 story points
- Stories are complete or not; we don't track progress in side stories
- Story completion adds to the completion level of their Epic
- New Stories can be added to an Epic during the cycle
- Report when a story is done to your T/CAM (same day if possible) or mark it done in JIRA. Real-time update is necessary to show monthly progress to NSF.
- Deprecate stories by marking 'Invalid' or 'Won't Fix'.
- If a Story or Epic becomes irrelevant, smaller, or larger, let your T/CAM know.

### JIRA to PMCS

JIRA activities get sucked into the Project Management Control System (PMCS) for scheduling and Earned Vallue Management (EVM).

To enable EVM, we do a "rolling wave" plan for six-month release cycles.

### Documentation and Document Management

- Working versions of documents are developed in a variety of tools (wikis, LaTeX, Acrobat.
- Official baseline versions are in docushare
- LSST requirements and interface control documents are in collection-2808 and collection-2807.
- LSST monthly technical progress reports are in collection-3826
- DM requirements and design documents are in collection-2511
- DM monthly progress is in collection-221
- There are many more related documents, both management and technical, ask your T/CAM if you need to find something.

## DM Code Structure (Tim Jenness)

### Overview

- C++11 and Python 2.7
  - Use python whenever possible
  - Use C++ for performance only
- We do not use Cython/Numba.
- Intending to support Python 3 soon
- GCC 4.8 minimum supported compiler
- Clang supported
- SUI uses Java 1.6 server side, JavaScript on client side
- Linux CenOS 6 and 7; OS X Yosemite, Mavericks are test platforms. No El Cap
- All code in lsst namespace

### Package overview

- orchestration: crtl
- Data access framework (Butler): daf
- pipeline execution frameworks: pex
- afw
- Image Processing: ip
- Measurement: meas
- Coaddition: coadd
- Pipeline infrastructure: pipe
- Control display tools: display
- Camera specific mappings: obs
- Web vis of images/catalogs: firefly (Java)
- Data access: dax

See confluence for a list of pages

### Middleware: log

- log package has C++ / Python interfaces
- Runs on log4cxx
- INFO/WARN/DEBUG/TRAVEL levels

### Middleware: exceptions

- pex_exceptions proves a set of C++ exceptions that can be caught in Python code.
- Use native Python exceptions when appropriate.
- Exceptions include: LogicError, DomainError, InvalidParameterError, etc

### Support packages

- `daf_base`:
  - Datetime handling
  - PrpertySet/List
- `ndarray`: C++ implementation of numpy. Translated into numpy on Python side; use `eigen` for fancy stuff.
- `geom`: cartesian and spherical geometry
- `db`: database access utilities

### Third-party packages

- 3rd party packages are distributed as EUPS-managed packages installable with `eups distrib` or `lsstsw`
  - They are versioned
  - LSST packages list them as explicity dependencies
- Numpy and matplotlib are presumed to have been installed by other means (but this is checked)
- Some 3rd party packages are not expected to be called directly
- New packages can be requested via the RFC process
  - Using 3rd party packages is encouraged rather than reimplementing a wheel
  - It's easy to make a new eups package
- 3rd party Python packages are currently distributed in this way and not via pip (there's an RFC for this)

Available packages include:

- cfitsio
- eigen (linear algebra)
- boost (though we're trying to move away from boost towards C++11)
- wcslib
- fftw
- gsl
- minuit2

Full list at https://confluence.lsstcorp.org/display/DM/DM+Third+Party+Software

TODO: move this page to git/the docs.

### Structure of a package

```
bin  # executables / python scripts
doc  # doxygen
examples  # examples
include
lib
python
src
tests
ups
```

### Building a package: scons

- Scons is used to build LSST software
- To build and test a package
  - `setup -k -r .` to ensure tests use the newly-built code
  - `scons -j 4 opt=3` for parallelized build
- `scone -j 4 python` will just build the python code
- Sconstruct file is used by scons to configure the build
- SConstruct files are used for subsidiary configuration in subdirs.

### LSST extensions to Scons: sconsUtils

- EUPS version and dependency tracking
- Compiler detection (clang vs gcc and how to add C++11 support)
- Swig interface building
- How to run tests

See Sconsfile example in slide

### Python code

- Lives in the `python/lsst` directory
- If your package is named something_else the code will be located in python/lsst/something/else
- Each sub-directory (lsst and below) must have an `__init__.py` that contains

```
import pkgutil, lsstimport
__path__ = pkgutil.extend_path(__path__, __name__)
```

(`import lsstimport` seems to be for Swig support)

This boilerplate is required to setup the python namespace.

### Swig

- http://www.swig.org
- Parses C++ header files and generated Python wrapper code
- Interface is defined in `.i` files that live in the `python/lsst` tree.
- `meas_base` `.i` files are in `python/lsst/meas/base`
- Swig generates .py file and shared library. For `meas_base` baseLib.i generates baseLib.py and `_baseLib.so`.
  - The other .i files are included by baseLib.i
  - The Sconscript file in the same directory tells scons that only baseLib is relevant
  - Look in the `_wrap.cc` file to see what Swig has generated.

### Third-party package structure

- 3rd party packages are wrappers around the standard distribution files
- EUPS, via eupspkg, knows how to build different styles of distro: Python's setup.py, autoconf and cmake.

Directories:

- `ups`: EUPS configuration, including dependencies and build instructions
- `upstream`: upstream unmodified distribution tar file
- `patches`: patches to be applied to the distribution before building it (this is generally an anti-pattern)

### Testing

- Each package has associated unit tests
- Python tests use unittest
  - `assertEqual`, `assertLess`
  - Only use `assertTrue` if you are really testing truth
- Use decorators to skip tests; don't comment those tests out
- C++ tests use boost
- Aim for new code to come with associated unit tests
  - Code coverage is less than ideal at present but aiming to begin gathering metrics on this
  - Integration tests are used to test the stack as a whole
- Python tests will soon be run via a standard python test environment such as `nose` or `py.test`.

  These will give significantly better test output handling in the Jenkins continuous integration system.

### Documentation

- Currently the doc directory.
- Doxygen format used for method and class descriptions inline.
- Currently moving to reST and numpydoc format and aiming to integrate into ReadTheDocs.

### The ups directory

- The `ups` directory teaches EUPS and `sconsUtils` how the pacakge relates to other pacakges and how to configure it when it is setup.
- The `.table` file lists dependencies and environment variables for EUPS
- The `.cfg` file contains configuration information for `sconsUtils`
- The `eupspkg.cfg.sh` provides overrides and additional information to allow eupspkg to build a package.

### Coding standards

- We want to move our code standard to a 'diff' against PEP8
- **New code can immediate use PEP8 naming**
- C++ coding standard

## afw (Simon Krughoff)

### cameraGeom

- A system for representing transformation between different coordinate systems in the optical system
- Utilities for building cameras

### coord

- Coordinate construction and conversion
- Implements the following
  - FK5, etc
  - FIXME add more

### detection

- threshold
- footprint (collection of pixels + metadata)
- HeavyFootprint (FIXME what is a HeavyFootprint?)
- Psf

### geom

- low-level image geometry
- Angle, Box, Extent, Point, Span
- Ellipse, Polygon
- XYTransforms: Affine, Identity, Inverted, Multi, Radial, Separable
  - these are *reversible* transformations

### image

- utilities to actually manipulate images
- Image things:
  - Image: single grid of pixels (float, double, int, uint)
  - Mask: grid of bit mask pixels with associated mask plane definitions
  - DecoratedImage - Image with metadata (deprecated)
  - MaskedImage - Image + Mask + Variance. Most of the stack uses these
  - Exposure - MaskedImage with associated image things: WCS, Psf, metadata, calibration info.
  - Side note: we should *always* use Exposures as the interface between tasks
- Other associated things:
  - Defect
  - Filter
  - Calib (this is really photometric calibration), Wcs

### math

- Statistics (mean, stdev, var, median, inner quartile range, clipped stats, min
- Kernels
- Convolution
- Interpolation and approximation
- Fitting
- Functions
- Splines
- Random number generator
- Warping - Lanczos, bilinear, etc

### table

- Tables are really catalogs with fixed schema. The schema is flexible and can be set up to do lots of things
  - Store amp electronigcs info
  - Source catalogs
  - Match catalogs

### How to find things

- Doxygen: but it's hard to find Python documentation in the doxygen
- GitHub code
- unit tests (ugh, so bad we use unit tests for docs)
- Help strings in Python (but not useful when Swig'd)
- Search with an editor

### Example

See code on bootcamp repo

Modifiers on method names (e.g., BoxI) are due to Swig.

```
box - afwGeom.BoxI(afwGeom.PointI(200, 500),
                   afwGeom.ExtendI())
im = afwImage.ImageF(box)
```

**Gotcha** the LLC is not 0, 0. This bounding box is relative to a global coordinate system called PARENT (the default)

Sub images are *views* on the original array

Can't add images of different types. Need to explicity use the *convertX()* method.

afw.detection can be used for low-level detection based on thresholding to create *footprints*.

```
afw_detect.FootprintSet(masked_im, threshold, 'DETECTED')
```

where DETECTED is the name of a bit in the mask.

afw_math has background estimation tools.

Once you have footprints set, you can easily do detections on each footprint.

```
im, mask, var = masked_im.getArrays()
```

(those are views)

The `>>=` operator does?? reflections??

You can also do operations on the numpy arrays.

Numpy uses (y, x) indexing with afw.image uses (x,y) indexing.

### Understanding afw

The original intent was to keep the C++ environment rich.
This led to classes not just functions defined in C++.

Takeaway: the Python/C++ line is hard to draw. May be better not to expose all of afw C++ to python.

## Source Measurements and Tables (Jim Bosch)

Measurement and tables are related because measurements are delivered in tables.
the talk will discuss how to write a measurement task and plugin.

### Footprint

Regular footprints describe on regions of pixels.

Heavy footprints describe both regions and the values of the pixels.

### Flow

#### Detect

1. Exposure
2. pixels, Psf
3. SourceDetectionTask
4. parent Footprints
5. Source Catalog
6. SourceDetectionTask sends DETECT mask plane back to the Exposure

The DETECT step creates Footprints

#### Deblend

Takes Footprints. May split them to create Heavy footprints (i.e., child footprints) that divide flux of the regular (parent) footprint.

#### Measure

Take footprints, wcs information, etc., and now measure quantities for the 

```
replace all detections with noise

for record in catalog:
    restore pixels from HeavyFootprint
    run plugins
    re-replace pixels with noise

apply aperture corrections
```

Note this potentially double-counts flux.

### Running a Single Frame Measurement Task

1. Initialize a minimal schema: `lsst.afw.table.SourceTable.makeMinimalSchema()`
2. Customize the detection task
3. Call the Detection task, passing the config and the schema.
4. Initialize other tasks (deblend, etc) to fully setup the schema

#### Notes on configuring a measurement task

- list of active plugins is a set
- need to 'bless' which plugin's output is called the "Model Flux"

#### SourceTable

- SourceTable is really a factory for SouceRecords, not a container for them
- Pass Source table and an Exposure to the Task.run() method.
- Pass Exposure and Catalog to  SourceDeblendTask.
- Finally run the measure task.

### Writing a  SingleFramePlugin

1. Config class
2. Plugin
   - registers with `@lsst.meas.base.register('ext_BoxFlux')`
   - `ConfigClass = BoxFluxClass`
   - Classmethod `getExecutionOrder`
   - All Plugins have hte same `__init__` signature
   - `measure` method takes a measRecord and exposure where all neighbours are replaced with noise

The Plugin has a run order. BasePlugin has the execution orders.

See slides/video for tutorial in writing a task.

Discussion of schema column names. There are strong conventions.

Use `schema.join()` to add fields to the schema. Some algorithms have strong naming conventions.

```
measRecord.get()  # get and set always work
measRecord[]  # does not always work;
```

### Error Handling in Plugins

- If the plugin will fail on all sources because of mis-configuration, raise `lsst.eas.base.FatalAlgorithmError`
- If a known failure mode, set at least two flags
  - Set flags as fields in `measure()` method, or
  - Re-raise as `lsst.meas.base.MeasurementError` and set flags in `fail()`.
- All other exceptions will trigger warnings, and `fail()` flags should be set

### Unit Transformations

See slides from talk.

### Plugins in C++

Plugins can also be written in C++ (and most current ones are). Unfortunately, the APIs make it easier to actually write plugins in C++.

### Forced Measurement

Measure sources on an image while holding the centroids fixed (or other values).

### Tables

#### Records and Keys

Base record has

- data
- block
- table (a record remembers the table that created it and relies on it to know is own schema)

A Key has an offset

Record access is some kind of magic BS.

### Records and Tables

Table table has

- data
- metadata (`PropertyList`)
- block

This is why a table is actually just a factory that knows its schema.

A BaseCatalog is really just a vector of pointers to records.

- No guarantee the recors are in the orer they were allocated in
- no guarantee that the reords are from the same Block
- No guarantee that records are even from the same Table or schema.

### Source Records

Source Records differ from BaseRecords in that they have a **footprint**.

SourceRecords have a minimal schema

SourceRecords and SourceCatalogs have special getters for certain predefined field names. E.g.,

- `getX` -> get(`slow_Centroid_x`)

### Record/Table subclasses

- `SimpleRecord` used by reference entries for astrometry
- `ExposureRecord` holds metadata about an exposure, including the Psf and Wcs. Mostly used by CoaddInputs and CoaddPsf
- `AmpInfoRecord` used by the camera geomtry module to store both structured and flexible information about amplifiers
- `PeakRecord` used to store peaks within a Footprint

### Getting arrays from columns in Catalogs

```
array = catalog['field']
```

gets a numpy view of a column.

This only works for contiguous in memory; otherwise

```
catalog = catalog.copy(deep=True)
```

Use the `isContiguous` method to test.

### Catalog Indexing

- Pass strong or Key to extract column (return numpy ndarray)
- Pass integer, a slice, or boolean array as `catalog[x]` to index the rows (returning a record or a subset catalog)

Gotchas

- You can't pass an array of row indices (not implemented yet)
- Boolean indices don't force a copy so they generally return a noncontiguous Catalog (this is intentional - we want all copies explicit)

### Adding/removing columns

Once we've created a table, we can't change the Schema.

- Whenever you ask a Record/Table/Catalog you get a copy, so modifying it won't do what you want.

### To add a column (schema mappers)

Need to

1. Get a SchemaMapper
2. Add a minimal schema from the original catalog
3. Add a field to the schema
4. Create an empty catalog with the Schema Mapper's output schema
5. Add all records from the old catalog to the new one, using the SchemaMapper to copy values

### Flag Fields

Key for a flag field has

- offset
- bit

Flag column arrays are copies, not views.

Only get/set are supported, not `[]`.

### Slots and Aliases

We've already mentioned the "slot" system, which adds getters to SourceRecord and SourceCatalog for some predefined names. Those names are usually *aliases* to real field names.

A Schema holds its aliases in an attached AliasMap. Aliases are *just* string mappings. There is some globbing-ish functionality to define lots of aliases at once.

### FunctorKeys

`FunctorKeys` provide a mechanism to get first-class objects our of one or more fields in a record.

For example, creating coordinates from separate x/y columns (i.e., packing/unpacking data structures)

Can even do things like get magnitudes from flux values.

Should be possible to create FunctorKeys in Python (there are separate implementations in Python and C++).

### Summary of afw.table Idiosyncracies

- [record, table, catalog.schema returns a copy. **But** that copy shares an AliasMap wth the original!
- Can't use `[]` on Flag fields (only get/set)
- Using an array of booleans to index a catalog returns a noncontiguous view
- another one

### More info

- afw.table reference doxygen page is decent
- print(schema) to get a useful introspection of the schema
- docstrings are useful
- Doxygen reference documentation is quite complete, but often only applies to the C++ interface.
- If something seems weird, just ask particularly on community.lsst.org.

## Orchestration and Control (Steve at NCSA)

- Develop and test locally
- Take same code and run it in production

### LSST package ctrl_orca - Orca

- software setup, execution, monitoring and shutdown across multiple machines
- internall uses condor, but the user doesn't need to know
- User specified parameters in Config files

### ctrl_execute

- Simplifies the execution or orca
- writes configuration files for orca
- duplicates local execution environment remotely

Local packages that are setup with eUPS are duplicated remotes to more easily replicate any errors locally

### Quickstart

```
setup ctrl_execute
setup ctrl_platform_lsst
```

```
runOrca.py -p lsst -c "processCcdSdss.py sdss /home/user/input --output ./output [more args]"
```

Simple test

```
runOrca.py -p lsst -c "/bin/echo" -i $HOME/ids.txt -e ~/lsstsw
```

use

```
condor_q
```

to see the status of jobs.

HTCondor output files are put into FIXME a directory.

The application output is put into `/lsst/DC3root/{usr}_{date}_{id}`

You can use `condor_rm` to quit jobs.

See https://confluence.lsstcorp.org/display/DM/Orchestration for more information.
