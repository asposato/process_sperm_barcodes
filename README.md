# Process GESTALT barcodes from serially sampled sperm data

This is a pipeline for filtering lineage barcodes retrieved from sperm. 
It adds essential steps in the Sperm Analysis pipeline developed by Kimberly Truong.

### Workflow: 
#### A. Throw out whole files that have low total reads and write passed-samples-info.csv
##### Files that pass this step are given the extension: .passReadCounts.
##### The passed-samples-info.csv is generated using only the .passReadCounts files. This csv will be important for step E when making the barcode matrix.
#### B. Filter barcoeds by reads and proportion cuttoffs within files
##### Barcodes that pass this step are added to files given the extension: .filteredReadCounts.
#### C. Identify contaminant barcodes
##### To examine barcodes for each fish, a dictionary of barcodes was created for each fish.
##### Fish dictionaries are written to files and stored in the "fish_dictionaries" folder which is at the same level as SpermAnalysis_Muller1.
##### The list of questionable and good barcodes were written to files in the "fish_dictionaries" folder.
#### D. Remove contaminant barcodes from read files where they don't belong 
##### Barcodes that pass this step are added to files given the extension: .filteredQualityReadCounts.
#### E. Make a barcode matrix which can be used to generate Muller plots
##### A barcode matrix file is written to the experiments folder and used for generating Muller plots

### Directory Structure
The directory structure is adapted from Kimberly's approach with some notable differences. 

```
process_sperm_barcodes
  ├── .ipynb_checkpoints
  ├── SpermAnalysis_Muller1
  	├── data
  		├── viz-Output
  			├── fish1_s1_rep1
				├── fish1_s1_rep1.allReadCounts (input)
				├── fish1_s1_rep1.passReadCounts (intermediate output from step A)
				├── fish1_s1_rep1.filteredReadCounts (intermediate output from step B)
				├── fish1_s1_rep1.filteredQualityReadCounts (output from step D)
				├── fish1_s1_rep1_stepB_removed_barcodes.txt (related to .filteredReadCounts)
			├── fish1_s1_rep2
			├── fish1_s1_rep3
			├── fish1_s2_rep1
			.
			.
			.
    	├── passed-samples-info.csv  (generated during step A)
  	├── experiments
  		├── 11.11.2024_01.56_PM
  			├── barcodeMatrix_3filters.tsv (final output before Muller plot generation)
  			├── fish1_barcodeMatrix_3filters.tsv
  			├── fish5_barcodeMatrix_3filters.tsv
  			├── fish10_barcodeMatrix_3filters.tsv
  			├── fishctrl3_barcodeMatrix_3filters.tsv
  			├── full_job_report.txt
  			├── stepA_summary.txt
  			├── stepB_summary.txt
  			├── stepC_summary.txt
  			├── stepD_summary.txt
  			├── stepE_summary.txt
  		.
  		.
  		.
  ├── fish_dictionaries
  	├── fish1_dictionary.tsv
  	├── fish5_dictionary.tsv
  	├── fish10_dictionary.tsv
  	├── fishctrl3_dictionary.tsv
  	├── fishes_dictionaries.tsv
  	├── good_barcodes_list.txt
  	├── questionable_barcodes_list.txt
  ├── process_sperm_barcodes.ipynb
  ├── questionable_barcode_ratios.ipynb
```

`data` holds the `viz-Output` folder which contains all the visualization files generated from a sample during the GESTALT pipeline. 
For this pipeline to run, you need only the `.allReadCounts` file. However, you may find it easier to grab the whole visualization folder for a sample. 
The presence of files such as `.topReadEventsNew` will not disrupt this process.  

`data` also holds the `passed-samples-info.csv` file. Previously, `samples-info.csv` was manually written. In this pipeline, `passed-samples-info.csv` is automatically written following filtering step A. 

`stepB_removed_barcodes.txt` for each sample contains the barcodes that do not pass the reads and proportion threshold from step B. They will be missing from the `.filteredReadCounts` file but present in the `.passReadCounts` file.

`fish_dictionaries` contains files that are generated during step C. It is created by the pipeline. The purpose is for a dictionary of each fish to store the list of all barcodes that ever show up in that animal, which samples each was detected in, how many reads were associated, and what proportion of the file's reads it comprised. These dictionaries are then used to detect questionable barcodes. 
Questionable barcodes are barcodes that show up in multiple fish. During step D, if a barcode has a clear source fish, it is assigned to that fish. If the source of contamination cannot be determined, it is considered a contaminant everywhere (A.K.A. promiscuous barcode). `questionable_barcodes_list.txt` contains the list of barcodes that show up in multiple animals. 

`experiments` holds the output file `barcodeMatrix_3filters.tsv` within a folder that is named after the date and time the pipeline finished running. You can run the pipeline multiple times and a new output folder will be created for each run. This output file will be used to generate Muller plots in R. Most of step E in this pipeline is original code from Kimberly. Additionally, a barcode matrix file is generated for each fish in your data. 
Subfolders in `experiments` also contain summary text files of the progress of the pipeline. A full summary is written to `full_job_report.txt`. 

### Adapting to run for your data

To get started with your own dataset, first set up your directory. It should look like this: 

```
directory_name
  ├── SpermAnalysis_Muller1 
  	├── data
  	├── experiments
```
Next, populate `directory_name/SpermAnalysis_Muller1/` with the viz folders. You will need a folder for each sample replicate like `fish1_s1_rep1` and within that folder the `fish1_s1_rep1.allReadCounts` file at the minimum. 
Beware: if you change the name of the SpermAnalysis_Muller1 folder, you will also need to do so anywhere it appears within the jupyter notebook `process_sperm_barcodes.ipynb`. 

Next, download and save the `process_sperm_barcodes.ipynb` to `directory_name`. It should be at the same "level" as `SpermAnalysis_Muller1`

Everything else will be generated during the running of the pipeline. Your directory will look more like the example in the above Directory Structure section. 

Now you're ready to run the pipeline. Open the jupyter notebook from Terminal (on Mac) or a shell emulator on a PC. Andy recommends Git Bash. 
To open the notebook, navigate to the directory where it exists (i.e. `cd directory_name`) and run the command `jupyter notebook`. This will launch a new window in your default browser. 
From here, you should be able to open the `process_sperm_barcodes.ipynb` and run each cell individually. You can also run the whole script by selecting "Run All Cells" from the menu. 
Andy recommends you run the cell that imports modules individually first to determine if you need to install anything. For example, you may not have `natsort` locally installed. This can be installed at the command line with pip. For any errors for missing modules, be sure to look up the best approach to install it for your system. 

### Installing a complete copy of this repository
Maybe you're still learning and don't have data ready to analyze. You can practice working with this pipeline by cloning this repository. 
Open up a terminal and clone the repository onto your local computer: 
```
git clone https://github.com/Gagnon-lab/process_sperm_barcodes.git
cd process_sperm_barcodes
```
##### The data in this example:
These are monthly sperm samples from 3 double-edited fish starting around 4 months old. `passed-samples-info.csv` contains some interesting metadata about these animals if you're curious. 

### Warnings 

Make sure the ipython notebook file is stored at the same level as "SpermAnalysis_Muller1".

This pipeline will break if you change your naming system for sample replicates (i.e. `rep1_s2_1fish`) or mess with the data structure Kimberly established.
Please stick with `fish1_s1_rep1`.

Windows does some things differently than Mac (the computer this code was written with). One example is the way filepath slashes are handled with the .split() method. While `read_file.split('/')` will work on a Mac, this should be changed to `read_file.split('\\')` on Windows. 
You may need to spend some time adapting the code to run without error because of these syntax differences. 

### Misc 
`questionable_barcode_ratios.ipynb` is an exploratory script. Sections of it are incorporated into step C of the pipeline. 
