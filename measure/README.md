# Measure Execution Cycles 
- [Measure Execution Cycles](#measure-execution-cycles)
  - [Description](#description)
    - [Execution Environment](#execution-environment)
  - [Preparation](#preparation)
    - [Tools](#tools)
    - [Insert macros to get execution cycle](#insert-macros-to-get-execution-cycle)
      - [Run **run.sh**](#run-runsh)
      - [Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*](#make-sure-that-c_src_macro-directory-is-created-in-insertmacroc_src)
      - [Cross-compile the programs in *c_src_Macro*.](#cross-compile-the-programs-in-c_src_macro)
  - [Run programs on a actual device and get execution cycle](#run-programs-on-a-actual-device-and-get-execution-cycle)
    - [Note](#note)
      - [Startup *PMU*](#startup-pmu)
      - [Fix operating frequency](#fix-operating-frequency)
      - [How to output the results](#how-to-output-the-results)
        - [Explanation of each column](#explanation-of-each-column)

## Description

This directory provides functions to cross-compile the measurement program for the actual device and acquire the execution cycles.

The procedure is as follows.

+ [Insert macros to get execution cycle](#Insert-macros-to-get-execution-cycle)
+ Cross-compile to run on a actual device
+ [Run programs on a actual device and get execution cycle](#Run-programs-on-a-actual-device-and-get-execution-cycle)

### Execution Environment

We have confirmed that the programs runs on the following platform.

+ Environment
  + *Ubuntu ver.16.04LTS*
<br>

+ Target Hardware
  + *Raspberry Pi3 Model B+*
    + *CPU*：*Cortex-A53*
    + *OS*：*Linux ver4.14.68*

## Preparation

This section describes the flow of inserting a macro into the measurement programs for execution.

### Tools

You will need the following tools:

+ Execution environment of *Python*
  + We have confirmed executing *ver 2.7.12*  
+ Environment to cross-compile for the target hardware
  + We used *aarch64-poky-linux* compiler

The files to be prepared are as follows:

+ The mesurement programs: [*c_src*](../c_src/)
    + Place *c_src* directory in *insertMacro*

### Insert macros to get execution cycle

The procedure to insert is as follows:

  1. [Run **run.sh**](#Run-runsh)
  2. [Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*](#[Make-sure-that-*c_src_Macro*-directory-is-created-in-*insertMacro/c_src*)
  3. [Cross-compile the programs in *c_src_Macro*](#Cross-compile-the-programs-in-*c_src_Macro*)


#### Run **run.sh**

The command to run is as follows:

`sudo ./run.sh c_src/*`

#### Make sure that *c_src_Macro* directory is created in *insertMacro/c_src*

Make sure that *c_src_Macro* is created in *c_src*, and that there is a measurement programs in that directory with the macro inserted.

※ The insertion of the macro does not work because there is more than one *return* in **GaussianElimination.c**. Deleting the macro in lines 97 to 122 will solve the problem.

#### Cross-compile the programs in *c_src_Macro*. 

Cross-compile the programs so that it can run on the target hardware.

## Run programs on a actual device and get execution cycle

Move the compiled executable files to the target hardware via USB memory, and execute them.

### Note
Please pay attention to the following three points when doing this.

1. [Startup *PMU*](#Startup-PMU)
2. [Fix operating frequency](#Fix-operating-frequency)
3. [How to output the results](#How-to-output-the-results)

#### Startup *PMU*

Load the three kernel modules(***.ko*) in [*PMU_module*](PMU_module) with the following command.

`insmod (kernel module)`

#### Fix operating frequency

Move **cpusetup.sh** in [*PMU_module*](PMU_module) with the following command.

`./cpusetup3.sh`

#### How to output the results

Please output the execution result to csv file as shown in the following example.
(*BubbleSort* is file compiled from **BubbleSort.c**.)

`./BubbleSort > res_BubbleSort.csv`

Make sure that the csv file outputs the following results.

```bash
L1I-Refill,,,,L1D-Refil,,,,L1D-Access,,,,L1I-Access,,,,L2D-Access,,,,L2D-Refill,,,,
Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,Total,Start,End,lap,
775,7131,7906,775,163,5122,5285,163,74496,3968026,4042522,74496,92733,9955366,10048099,92733,1101,22235,23336,1101,237,2071,2308,237,
1403,6366,6994,628,251,4948,5036,88,147372,3646561,3719437,72876,181942,9158109,9247318,89209,1910,20774,21583,809,601,1719,2083,364,
.
.
.
35050,6353,7044,691,6781,4417,4543,126,7072631,1807635,1881169,73534,8500710,4620092,4710709,90617,50606,19746,20707,961,18628,1888,2255,367,
35816,6667,7433,766,6917,4294,4430,136,7146198,1811331,1884898,73567,8590754,4627120,4717164,90044,51650,20123,21167,1044,18965,1853,2190,337,
-----------------------
TotalTime,22501440,Iteration,100
exec count,225014
L1access,71261s
L1missrate,0.000943
L2missrate,0.367183
```
<br>

##### Explanation of each column

They are getting *Refill* and *Access* for *Iteration* and *L1I/L1D/L2D* respectively.
*PMU* is used to get the number of events above. *Total、Start、End、lap* are expressed as follows.

|Name of column|Description|
|---|---|
|Start|Number of event counters when starting the measurement program|
|End|Number of event counters at the end of the measurement program|
|lap|*lap = End - Start*|
|Total|Sum of *lap*|

The final output parameters are as follows.

|Name of parameters|Description of parameters|
|---|---|
|TotalTime|Sum of the execution cycles executed *Itration* times|
|Iteration|Number of times to execute the program defined by the macro|
|exec count|Average number of execution cycles (*TotalTime / Iteration*)|
|L1access|Average number of L1 accesses|
|L1missrate|L1 cache miss rate (*L1-Rifill / L1-Access*)|
|L2missrate|L2 cache miss rate (*L2-Refill / L2-Access*)|
