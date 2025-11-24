
# How to install and run your own version of biomedical image analysis tools?

There are multiple complexes to handle biomedical image analysis tools on HPC environments. In general, these software runs as a desktop software application. Most of the software also provide plugins, and these plugins can only be installed in a specific folder. 

## 1.	QuPath

QuPath is an open-source software for bioimage analysis. QuPath is often used for whole slide images common in pathology, but it can be used for other microscopy images too. Indeed, QuPath was published and designed under the merits of being user-friendly and specific for visualization and computational challenges posed by whole slide images. Furthermore, it includes features with batch-processing and scripting functionality. The software runs as a desktop software application.

Copy /software/rhel9/manual/install/qupath/0.6.0/QuPath-v0.6.0-Linux.tar.xz to your own folder.

[fangping@login3 qupath]$ pwd
/ix1/bioinformatics/qupath # replace this folder to your own folder
[fangping@login3 qupath]$ cp /software/rhel9/manual/install/qupath/0.6.0/QuPath-v0.6.0-Linux.tar.xz .
[fangping@login3 qupath]$ tar xf QuPath-v0.6.0-Linux.tar.xz
[fangping@login3 qupath]$ ls
QuPath  QuPath-v0.6.0-Linux.tar.xz

Logon ondemand.htc.crc.pitt.edu, Click Interactive Apps -> Desktop, Choose Number of Hours and Number of Cores, click Launch.

[fangping@htc-n28 ~]$ cd /ix1/bioinformatics/qupath/QuPath/ # go to your path
[fangping@htc-n28 QuPath]$ ls
bin  lib
[fangping@htc-n28 QuPath]$ cd bin
[fangping@htc-n28 bin]$ ls
QuPath
[fangping@htc-n28 bin]$ export XDG_RUNTIME_DIR="/tmp/${UID}" 
[fangping@htc-n28 bin]$ module load java/24.0.1
[fangping@htc-n28 bin]$ ./QuPath

You will be running your own version of QuPath. You can use the GUI to install your own plugins.

Note that QuPath includes features with batch-processing and scripting functionality. Thus you can submit batch jobs to run the scripts.
