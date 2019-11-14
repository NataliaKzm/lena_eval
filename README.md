# LENA evaluation

## Prerequisites

First, you can clone (and move to) this repository by typing :

```bash
git clone https://github.com/jsalt-coml/lena_eval.git
cd lena_eval
```

Make sure that [pip](https://pypi.org/project/pip/), [conda](https://docs.conda.io/en/latest/) and [R](https://www.r-project.org/) are installed on your system.
Once everything has been installed, we can create the [conda](https://docs.conda.io/en/latest/) environment containing all the necessary python packages.

```bash
# Create environment
conda create --name lena_eval python=3.6

# Activate the environment (this command should be run each time you open a new terminal !)
# might be *source activate lena_eval* depending on how you set up conda
conda activate lena_eval

# Install necessary packages
pip install pyannote-metrics
```

You must also verify that [R](https://www.r-project.org/) is installed and have the following packages : dplyr, magrittr, stringr, stringi, and [rlena](https://github.com/HomeBankCode/rlena).
Once you installed everything, you can check that everything went well by launching :

```bash
./scripts/0_check_config.sh
```

which will tell you either what's missing if something's missing or if everything's went as expected.

## Get the data

To download necessary data, you can launch :

```bash
./scripts/1_get_data.sh <habilis_username>
```

where <habilis_username> is the username of your habilis account. 
The script will ask you to type your password.

This will download ACLEW and Tsimane files. 
We need both human-made annotations (gold) and lena-made annotations.

## Run the evaluations

You can run all the evaluations by typing :

```bash
./scripts/2_evaluate.sh
```

This will take a bit of time (but probably less than 10 mn ! Go get yourself a cup of coffee...)

## Understanding the results

All the steps described above are generating something in the _**evaluations**_ folder.

### Identification Error Rate 

You can type : 

```bash
tail -1 evaluations/gold_lena_sil_same/ider_report.csv
```

and you should get :

```bash
item,identification error rate %,total,correct,correct %,false alarm,false alarm %,missed detection,missed detection %,confusion,confusion %
TOTAL,123.87,24664.27,8860.30,35.92,14748.43,59.80,5469.55,22.18,10334.42,41.90
```

Which gives you the **identification error rate** aggregated over all of the files for the classes [CHI, OCH, MAL, FEM, ELE, OVL].

Or : 

```bash
tail -1 evaluations/gold_lena_sil_same/only_CHI_deter_report.csv
```

gives you :

```bash
TOTAL,82.90,5881.48,1912.53,32.52,2963.21,50.38
```

which is the detection error rate aggregated over all of the files for the **CHI** class.
The second folder indicates the mapping that has been used for running the evaluations :

- gold_lena_sil_same for when the classes have been mapped to [CHI, OCH, MAL, FEM, ELE, OVL].
- gold_no_ele_lena_sil_no_tv_same for when ELE has been mapped to SIL
- gold_no_ele_no_ovl_lena_sil_no_tv_no_oln_same for when ELE and OVL have been mapped to SIL.

### Confusion Matrices
You can also type :

```bash
head -5 evaluations/all_cm.txt
```

that should return :

```bash
"CHN" "FAN" "OLN" "SIL" "CXN" "TVN" "MAN"
"CHI" 292047 19167 115415 70443 67488 23664 1529
"FEM" 29041 288019 250749 207098 38179 42612 36886
"SIL" 102053 85137 652443 4888170 71942 521342 38700
"OVL" 35929 49562 111186 24663 22517 14193 11967
```

which is the confusion matrix over all of the files, obtained by considering "N" classes of LENA annotations files.
Results are also available at the corpora scale - TSI, BER, ROW, WAR, SOD -.

### Vocalization statistics

You can type :

```bash
head -5 evaluations/evaluations/gold_key_child_voc_child_level.csv
```

You should get :

```bash
"child_id" "age_mo_round" "CV_cum_dur" "CV_mean" "CV_count" "CNV_cum_dur" "CNV_mean" "CNV_count" "CTC_count"
"BER_0396" 15 147.024 0.986738255033557 149 25.211 1.80078571428571 14 71
"BER_1196" 9 189.982 0.922242718446602 206 2.942 2.942 1 31
"BER_1618" 7 52.272 0.843096774193548 62 11.889 1.69842857142857 7 47
"BER_1844" 10 76.787 1.37119642857143 56 57.291 3.8194 15 73
```

*CV* stands for canonical vocalizations.
*CNV* stands non canonical voocalizations.
For each of these vocalizations, you have its number, its cumulated duration, and its average duration.
*CTC_count* correspond to the number of conversational turn-takings.

Similar files exist across multiples scales, at the scale of the child, the file, or aggregated across all of the files.
You'll also find similar files for the LENA system.