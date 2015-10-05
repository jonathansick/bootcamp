# LSST Bootcamp Notes

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

   - TODO Check slides

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
