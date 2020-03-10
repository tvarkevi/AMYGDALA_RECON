# AMYGDALA_REACT_VS_CONNECT
Pipeline to predict emotion task reactivity of the amygdala using resting-state connectivity of the amygdala as seed-region.

> Preliminary note: The Python module [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) used here assumes the scripts and analysis files are stored in one and the same working directory. The emotion task (EMO) and resting-state (REST) fMRI data of the experiment needs to have been converted to NIFTI format, and stored in a data directory organized such that each combination dataset (MARS or BETER) and scan type (EMO or REST) has a different subdirectory within that data directory (e.g. NIFTI_MARS_REST, NIFTI_MARS_EMO, NIFTI_BETER_REST, NIFTI_BETER_EMO). Within each of these sub data directories, each subject ID has to have its own folder, with the different types of scans (T1, REST, EMO) stored as different subdirectories that are indicated by suffixes added to the main subject identifier (e.g. data_dir > NIFTI_BETER_EMO > be1a031010 > be1a031010_1_1). Furthermore, the first two letters of each subject identifier need to specify the dataset in question in the following manner: 'xm' for the MARS dataset (e.g. xm13101101), and 'be' for the BETER dataset (e.g. be1a031010). The mother directory of the data also needs to contain, for each dataset seperately, the log files of the emotion task data (e.g. data_dir > LOGS_BETER > be1a031010-3amygdala_hippo_aug2010.log). These log files always need to end with aug2010.log. Finally, an Excel (.xlsx) file needs to be made that specifies, for each subject, the subject identifier (e.g. xm13101101), the subject number (e.g. MARS011, and specific suffixes added to the subject ID to differentiate the subdirectories (e.g. \\_4_1).

## 1. Setting up the experiment

The Python module [amygdala_project.py](https://github.com/tvarkevi/AMYGDALA_REACT_VS_CONNECT/blob/master/amygdala_project.py) that is used to run the analyses described in this manual consists of multiple classes. The main class that is inherited by all the other child classes is called Experiment. This class has a (constructor) method called __init__ that constructs all the attributes needed by (and specific for) the experiment. Three user inputs need to be specified in the console:
1. The full working directory that contains all the scripts and auxilliary files needed for the analysis.
2. The full data directory containing each subject identifier as a seperate folder, in which each of the relevant scans of the subjects are specified by subdirectories in the following manner: data_dir > subject001 > subject001_1_1.
3. The full directory of the Excel file that contains the subject information. The first row in this file needs to contain at least the following column headers: SubjT0ID (containing the subject identifiers), T1 (containing the anatomical scan directory suffixes for each subject), REST (containing the resting-state directory suffixes for each subject), and EMO (containing the task fMRI directory suffixes for each subject).