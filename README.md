# AMYGDALA_REACT_VS_CONNECT
Pipeline to predict emotion task reactivity of the amygdala using resting-state connectivity of the amygdala as seed-region.

> Preliminary note: The Python module [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) used here assumes the scripts and analysis files are stored in one and the same working directory. The emotion task (EMO) and resting-state (REST) fMRI data needs to have been converted to NIFTI format, and stored in a data directory (data_dir) organized such that each combination of dataset (MARS, BETER) and scan type (EMO, REST) has a different subdirectory (i.e., NIFTI_MARS_REST, NIFTI_MARS_EMO, NIFTI_BETER_REST, and NIFTI_BETER_EMO) within the data directory (e.g. data_dir > NIFTI_MARS_REST). Within each of these subdirectories, each subject has to have its own folder, with the name of that folder corresponding to the subject identifier (e.g. xm13101101). The different types of scans (T1, REST, EMO) for each subject need to be stored as subdirectories within that subject directory, and the identifiers of these subdirectories need to be indicated by suffixes that are added to the main subject identifier (e.g. data_dir > NIFTI_BETER_EMO > be1a031010 > **be1a031010_5_1**). The first two letters of each subject identifier specify the dataset whereto the subject belongs; 'xm' indicates the MARS dataset (e.g. xm13101101), and 'be' indicates the BETER dataset (e.g. be1a031010). The base directory of the data also needs to contain, for each dataset (MARS, BETER) seperately, a directory (LOGS_MARS, LOGS_BETER) that contains the log files of the emotion task (e.g. data_dir > LOGS_BETER > be1a031010-3amygdala_hippo_aug2010.log). These log files always need to end with **aug2010.log**, otherwise the program will not find them. Finally, an Excel (.xlsx) file needs to be made that specifies, for each subject, the subject identifier (e.g. xm13101101), the subject number (e.g. MARS011), group membership (e.g. 1 or 0) and the specific suffixes added to the main subject ID in order to differentiate the different scan subdirectories for that subject (e.g. \_4_1, \_5_1, etc.).

## 1. Setting up the experiment

The Python module [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) that is used to run the analyses described here consists of multiple classes. The parent class that is inherited by all the other subclasses is called Experiment. This class has a (constructor) method called \__init__ that constructs all the attributes needed by (and specific to) the experiment. Three user inputs need to be specified in the console:
1. The full working directory that contains all the scripts and auxilliary files needed for the analysis.
2. The full data directory containing each subject identifier as a seperate folder, in which each of the relevant scans of the subjects are specified by subdirectories in the following manner: data_dir > NIFTI_BETER_REST > be1a031010 > be1a031010_5_1 (see also the preliminary note at the beginning of this user manual).
3. The full directory of the Excel file that contains the subject information. The first row in this file needs to contain at least the following column headers: SubjID (or SubjT0ID, containing the subject identifiers), PPN (containing the subject number), Group (containing the group memberships), T1 (containing the anatomical scan directory suffixes for each subject), REST (containing the resting-state directory suffixes for each subject), and EMO (containing the task fMRI directory suffixes for each subject). Other columns containing e.g. questionnaire data can also be specified. Each row in the Excel file specifies a single subject, with the first two letters specifying the dataset that subject belongs to; 'xm' for the MARS dataset, and 'be' for the BETER dataset (see also the preliminary note at the beginning of this user manual)

To initialize the Experiment class along with all its attributes, define the working directory and run the following code in the console:

```
working_dir = r'O:\Project directory\Analysis'

import os
os.chdir(working_dir)

import amygdala_project as Amy
my_experiment = Amy.Experiment()
```
This code generates an output argument called *my_experiment* which contains attributes that define the parameters of the experiment (e.g. *my_experiment.subjects_list* contains a list of all the subject identifiers).

> Note: Since the Experiment class is inherited by all the other subclasses, it does not need to be defined seperately for the analysis to be conducted; it is called automatically when initializing the subclasses later on. Hence, initializing Experiment before initializing the other classes is not strictly necessary.

## 2. Data preprocessing

Many of the data preprocessing methods in the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module are conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command.

The following preprocessing steps are supported by the pipeline:
1. Realignment of the functional scans (section 2.1)
2. Extraction of an inclusive mask of the functional images (section 2.2)
3. Calculation of framewise displacements (section 2.3)
4. Slice-timing correction of the functional scans (section 2.4)
5. Coregistration of the anatomical scan to the mean functional image (section 2.5)
6. Segmentation of the anatomical image (section 2.6)
7. Erosion of the segmentation volumes (section 2.7)
8. Normalization of the beta (connectivity) maps (or other functional images; section 2.8)
9. Smoothing of the normalized functional images (section 2.9)
10. Filtering of the timeseries data (section 2.10)

### 2.1 Realignment

Realignment of the functional data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the realignment process are [Realignment.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Realignment.m) and [Realignment_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Realignment_job.m)

To conduct the realignment process, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described above (see section 1) need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 1 for realignment.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. This option should be skipped at this stage. Simply press the enter key to continue.

> Note, the above block of code assumes that the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module has already been imported. If this is not the case, run the following code beforehand in the console:

```
working_dir = r'O:\Project directory\Analysis'

import os
os.chdir(working_dir)

import amygdala_project as Amy
```

### 2.2 Inclusive mask extraction

Extraction of a subject-specific inclusive voxel mask is supported by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module via the *extract_inclusive_FOV_mask* method of the Preprocessing class. Enter the following code in the console to execute this process:

```
my_experiment = Amy.Preprocessing()
my_experiment.extract_inclusive_FOV_mask()
```

Since this class inherits from the main Experiment class, the same three user inputs as described above (see section 1) need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. An optional prefix to indicate the exact scan identifiers to base the extraction on. Enter r for the realigned scans.
3. The last input asks whether an across-subjects inclusive mask should be constructed. Enter 1 (yes) or 0 (no).

### 2.3 Framewise displacement

Extraction of framewise displacement (FD) data is supported by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module. The module uses the [Jenkinson](https://pdfs.semanticscholar.org/291c/9d66cf886ed27d89dc03d42a4612b66ab007.pdf) method and is based on the motion parameters generated by the realignment procedure (see section 2.1). Enter the following code in the console to extract the FD Jenkinson data:

```
my_experiment = Amy.Preprocessing()
my_experiment.extract_FD_jenkinson()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The reference scan that is used to calculate the framewise displacement data. Enter 1 for the first image, or M for the mean image.

### 2.4 Slice-timing correction

Slice-timing correction of the functional data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the slice-timing correction are [SliceTimingCorrection.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/SliceTimingCorrection.m) and [SliceTimingCorrection_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/SliceTimingCorrection_job.m)

To conduct the slice-timing correction, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 2 for slice-timing correction.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. Slice-timing correction needs to be performed on the realigned functional scans. Enter r for the realigned scans.

### 2.5 Coregistration

Coregistration of the raw anatomical image to the mean functional image is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the coregistration are [Coregistration.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Coregistration.m) and [Coregistration_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Coregistration_job.m)

To conduct the coregistration procedure, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 3 for coregistration.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. The raw anatomical volume needs to be used as source image at this stage. Simply press the enter key to continue.

### 2.6 Segmentation 

Segmentation of the anatomical data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the segmentation are [Segmentation.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Segmentation.m) and [Segmentation_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Segmentation_job.m)

To conduct the segmentation procedure, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 4 for segmentation.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. The segmentation can be conducted on either the raw or coregistered anatomical data. Simply press the enter key to conduct the segmentation on the raw T1 image. Enter c for the coregistered anatomical image.

### 2.7 Erosion 

Erosion of the segmentation data is supported by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module, via the *erode_segmentation_mask* method of the Preprocessing class. Enter the following code in the console to execute this process:

```
my_experiment = Amy.Preprocessing()
my_experiment.erode_segmentation_mask()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. An optional prefix to indicate the exact scan identifiers on which the erosion needs to be applied. In theory, the erosion can be conducted on all possible outputs of the segmentation procedure (c1, c2, c3, c4, c5). To perform the erosion on the white-matter segmentation, enter c2. To perform the erosion on the CSF segmentation, enter c3.

### 2.8 Normalization

Normalization of the functional data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the normalization procedure are [Normalization.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Normalization.m) and [Normalization_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Normalization_job.m)

To conduct the normalization procedure, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 5 for normalization.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. In theory, the normalization process can be conducted on any series of functional images. Enter b_map_ for the raw beta maps (output of the connectivity analysis).

### 2.9 Smoothing

Smoothing of the functional data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via a shell call command. The MATLAB scripts that are responsible for the smoothing procedure are [Smoothing.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Smoothing.m) and [Smoothing_job.m](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/Smoothing_job.m)

To conduct the smoothing procedure, enter the following code in a console:

```
my_experiment = Amy.Preprocessing()
my_experiment.run_preprocessing()
```

Since this class inherits from the main Experiment class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data .
2. The specific preprocessing step that needs to be conducted. Enter 6 for smoothing.
3. An optional prefix to indicate the exact scan identifiers on which the preprocessing needs to be conducted. In theory, the smoothihng procedure can be applied on any series of functional images. Enter nb_map_ for the normalized beta (connectivity) maps.

### 2.10 Filtering

Filtering of the timeseries data is supported by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module, via the *run_filtering* method of the Preprocessing class. The filtering procedure is integrated in many of the first-level methods described below, and does not need to be conducted manually. Nevertheless, the method *can* be conducted manually. For instance, to bandpass filter a given signal (your_signal) at 0.01-0.08 Hz, at a given repetition time (your_TR), enter the following code in a console:

```
TR = your_TR

my_experiment = Amy.Preprocessing()

unused, your_signal = my_experiment.run_filtering(your_signal, 1/TR, 0.01)
your_signal, unused = my_experiment.run_filtering(your_signal, 1/TR, 0.08)
```

## 3. Emotion task

The emotion task described here is based on the paradigm of [Van Buuren et al. (2011)](https://www.sciencedirect.com/science/article/pii/S000632231100254X) (see also [Heesink et al., 2018](https://www.cambridge.org/core/journals/european-psychiatry/article/neural-activity-during-the-viewing-of-emotional-pictures-in-veterans-with-pathological-anger-and-aggression/C3179D960B2B1DA0C0C33D34B5FCA2D6)).

The analysis of the emotion task data is conducted by the EmotionTask class of the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module. This class inherits all attributes and methods of the Preprocessing class (which itself inherits from the Experiment base class). The first-level analysis of the emotion task data is conducted using [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) in [MATLAB R2016b](https://nl.mathworks.com/products/matlab.html), via shell call commands.

The following preprocessing pipeline is recommended for the emotion task data:
1. Realignment of the raw functional images (see section 2.1)
2. Extraction of the FD Jenkinson data (see section 2.3)
3. Slice-timing correction of the realigned functional images (see section 2.4)
4. Coregistration of the anatomical image to the mean function image (see section 2.5)
5. Extraction of inclusve field-of-view (FOV) voxel mask, based on the realigned functional images (see section 2.2)
6. Segmentation of the coregistered anatomical image (see section 2.6)
7. Erosion of the white-matter segmentation (see section 2.7)
8. Erosion of the cerobrospinal fluid (CSF) segmentation (see section 2.7)

As part of the first-level analysis pipeline of the emotion task data, the following steps need to be conducted after the preprocessing steps:
1. Extraction of the task data from the log files (see section 3.1)
2. Extraction of confound regressors (see section 3.3)
3. Extraction of spike regressors (see section 3.4)
4. First-level analysis in [SPM12](https://www.fil.ion.ucl.ac.uk/spm/software/spm12/) (see section 3.5)
5. Creation of a region-of-interest (ROI) mask in native space (see section 3.2)
6. Extraction of ROI-masked statistical paramatric map data (see section 3.7)

### 3.1 Behavioral data

Before the first-level analysis of the emotion task data can be conducted, the task data needs to be extracted from the raw logfiles. As described in the preliminary note of this manual, the raw logfiles need to be copied to the data directory (data_dir), in a subfolder named after the dataset in question; i.e., data_dir > LOGS_MARS, or data_dir > LOGS_BETER. Furthermore, all individual logfiles need to end with **aug2010.log**, otherwise the program will not be able to find them. 

The extraction of the behavioral data from the raw logfiles is performed by the *extract_behavioral_data* method of the EmotionTask class. This method creates a comma seperated (.csv) file that contains the onsets, picture identifiers, conditions, responses, and reaction times (RT) of all emotional picture trial blocks. This CSV file is written to a new subdirectory in the data directory (data_dir), named after the dataset in question: i.e., data_dir > ONSETS_MARS, or data_dir > ONSETS_BETER.

In order to extract the task data from the raw logfiles, enter the following code in a console:

```
my_experiment = Amy.EmotionTask()
my_experiment.extract_behavioral_data()
```

> Note, the above block of code assumes that the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) module has already been imported. If this is not the case, run the following code beforehand in the console:

```
working_dir = r'O:\Project directory\Analysis'

import os
os.chdir(working_dir)

import amygdala_project as Amy
```

### 3.2 Confound regressors

Before the first-level analysis of the emotion task data can be conducted, the confound regressor model needs to be extracted from the data. The confound regressors used by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) are extracted by the *extract_confound_regressors* method of the EmotionTask class. This method computes a total of 36 nuisance parameters, based on the six realignment parameters (6P). their derivatives (12P), quadratic terms (18P), and quadratic terms of their derivatives (24P), as well as the white-matter, CSF, and global mean signal (27P), their derivatives (30P), quadratic terms (33P), and quadratic terms of their derivatives (36P), following recent recommendations by [Satterthwaite et al. (2013)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3811142/), [Ciric et al. (2017)](https://www.sciencedirect.com/science/article/pii/S1053811917302288), [Parkes et al. (2018)](https://www.sciencedirect.com/science/article/pii/S1053811917310972), [Ciric et al. (2018)](https://www.nature.com/articles/s41596-018-0065-y), and [Satterthwaite et al. (2019)](https://onlinelibrary.wiley.com/doi/full/10.1002/hbm.23665).

To execute the *extract_confound_regressors* method, enter the following code in the console:

```
my_experiment = Amy.EmotionTask()
my_experiment.extract_confound_regressors()
```

Since this class inherits from the both main Experiment class, via the Preprocessing class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data (this input-dependent attribute is inherited from the \__init__ method of the Preprocessing class).
2. An optional prefix to indicate the gray matter scan to base the confound model on. It is recommended that this option is skipped at this stage. Simply press the enter key to continue.
3. An optional prefix to indicate the white matter scan to base the confound model on. It is recommended that the eroded white-matter segmentation is used at this stage. Enter ec2 for the eroded white-matter segmentation.
4. An optional prefix to indicate the CSF scan to base the confound model on. It is recommended that the eroded CSF segmentation is used at this stage. Enter ec3 for the eroded CSF segmentation.

The process creates an output CSV file in the emotion task scan directory called (e.g.) data_dir > NIFTI_MARS_EMO > xm13101101 > xm13101101_3_1 > **xm13101101_3_1_confound_regressors.csv**. This file can be used in the first-level analysis described in section 3.4.

### 3.2 Spike regressors

Before the first-level analysis of the emotion task data can be conducted, the spike regressors need to be extracted from the FD Jenkinson data. The spike regressors used by the [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) are extracted by the *extract_spike_regressors* method of the EmotionTask class. The number and identity of the spike regressors computed by this method are defined by which frames exceed a pre-specified threshold (e.g. 0.2 mm). .

To execute the *extract_spike_regressors* method, enter the following code in the console:

```
my_experiment = Amy.EmotionTask()
my_experiment.extract_spike_regressors()
```

Since this class inherits from the both main Experiment class, via the Preprocessing class, the same three user inputs as described in section 1 need to be entered. Furthermore, the program will ask for the following additional inputs to be specified in the console:
1. The type of scans on which the preprocessing needs to be conducted. Enter REST for resting-state data or EMO for emotion task data (this input-dependent attribute is inherited from the \__init__ method of the Preprocessing class).
2. The FD Jenkinson threshold above which frames (i.e., individual functional volumes) are marked as outlier. A threshold of 0.2 mm is recommended at this stage.

The process creates an output CSV file in the emotion task scan directory called (e.g.) data_dir > NIFTI_MARS_EMO > xm13101101 > xm13101101_3_1 > **xm13101101_3_1_spike_regressors.csv**. This file can be used in the first-level analysis described in section 3.4. The process also creates an overview text files for the dataset in question (MARS, BETER), containing, for all subjects, the number of outliers and mean framewise displacement (MFD). This file can be found in the working directory as either **MARS_Outliers_FD_Jenkinson_EMO.txt** or **BETER_Outliers_FD_Jenkinson_EMO.txt**.

