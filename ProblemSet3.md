## ANT157L Problem Set 3: Performing GWAS and Calculating Polygenic Risk Scores


Name:
```
Write your name here
```

**Due Date:**


### Goals:
A common goal in the field of genomics and medicine is to identify SNPs that are associated with specific phenotypes, often disease. This exercise will aid you performing and analyzing such associations.

## Task 1:  Perform single-SNP association and GWAS

Begin by navigating to your student directory and copying the ProblemSet3 folder from the `/share/CourseANT157L/data` directory into your student directory. This folder has all the files you need. You should be working within your own student folder, not the data directory.

In order to perform SNP-phenotype associations, we must have datasets that include phenotype information. Recall that `.fam` files contain a column for individual phenotype information, shown as -9 when no phenotype information is present. Using PLINK, we can utilize the `--pheno` command in conjunction with a text file containing the phenotype information for each individual in our dataset. The syntax of this command should be very familiar; it follows the same structure as when you used the `--remove` `--keep` and `–extract` flags:

```
plink --bfile MyData --command MyFile.txt --make-bed --out MyNewData
```

The dataset you are going to be working is labelled `1kg_EU_qc.*` and the phenotype information for these individuals is contained in `BMI_pheno.txt.`
Use head to take a look at the `1kg_EU_qc.fam` and the `BMI_pheno.txt` files.

### Question 1 [3 pts]

Use the `--pheno` command to create new bfiles named `1kg_EU_BMI` that contain the phenotype information contained in the `BMI_pheno.txt` file. Copy your command below.

```
(Type answer here)
```

What is the individual ID, sex, and BMI of the first individual in your file?

```
(Type answer here)
```

Let’s start by looking at the level of association of a single SNP, rs9674439, with the phenotype. Run the following command:

```
plink --bfile 1kg_EU_BMI --snps rs9674439 --assoc --linear    --out BMIrs9674439
```

and look at the `BMIrs9674439.assoc.linear` output. This output tells us the effect of the A1 allele for a certain SNP under a particular type of model (TEST) reflected as the regression coefficient (BETA) for that allele in the linear test. The p-value is listed in column labelled “P”.
Specifically, your output should tell you that the association of this SNP with the phenotype was tested under an additive model, meaning that each copy of the A1 allele is considered to have the same effect on the phenotype, as opposed to a recessive model where an individual would require two copies of the A1 allele for its effect to manifest. The regression coefficient, -0.02974, indicates that each copy of the A1 allele on this SNP results in a reduction of 0.2974 in BMI.

### Question 2 [3 pts]

What chromosome is this SNP on? What allele is being looked at? Is the association significant? Explain

```
(Type answer here)
```

What happens when we test the association under a different model? Use the following command to test the association of this SNP with BMI under a dominant model:

```
plink --bfile 1kg_EU_BMI --snps rs9674439 --assoc --linear dominant --out BMIrs9674439_dom
```

### Question 3 [3 pts]

Considered under a dominant model, what effect does the SNP have on BMI? Does the allele have a larger, smaller, or the same effect on the phenotype when considered under a dominant rather than an additive model? *Explain*. Is the association significant?

```
(Type answer here)
```

BMI, or body mass index, is a continuous (or quantitative) trait, but we could code it in a binary fashion. This means that instead of viewing the phenotype as a quantitative value, the phenotype is coded as either present or absent. The CDC defines the phenotype “overweight” as BMI >= 25.

Run the shell script with a couple lines of code to create a text file that codes BMI values of 25 or above as 2 (cases) and BMI values below 25 as 1 (controls):

```
$ sh BinaryCoded_Pheno.sh
```

You should now have a file name `Overweight_pheno.txt`. Use this file and the
`--pheno` command to make another set of bfiles named `1kg_EU_Overweight`.

Let’s now investigate the association of the same SNP, rs9674439, with the phenotype when the phenotype is coded as a binary trait (i.e. overweight or not). This is a case-control study and so the regression and statistical test is slightly different. We must use the `--logistic` flag rather than the `--linear` flag in our command.
Run the following command to test for the association in the case-control dataset:
```
plink --bfile 1kg_EU_Overweight --snps rs9674439 --assoc
--logistic --out Overweight_rs9674439
```

### Question 4 [2 pts]

How does the result of this association test compare to the one performed when the phenotype data was represented quantitatively? Your answer should refer to values in the `Overweight_rs9674439.assoc.logistic` output file.

```
(Type answer here)
```

Now, do a GWAS by performing a linear association test under an additive model for all SNPs in the data (not just rs9674439).

> Hint: This is the same type of model and test that we used in our first association test.

### Question 5  [2 pts]

How many SNPs were tested in your GWAS? Recall the p-value threshold of significance for GWAS. Do any of the SNPs tested reach this level of significance?
```
(Type answer here)
```

## Task 2: Visualizing GWAS results with a Manhattan Plot

We will be using R to generate a Manhattan plot in order to visualize our GWAS results. First you will activate a conda environment so that we can easily call a specific R package

```
source /share/CourseANT157L/data/progs/conda/etc/profile.d/ conda.sh
conda activate R-env
```

Now that you have activated the R environment, we will enter the R program. In your terminal type R (as shown below) and hit enter.
```
R
```

You should now be within the R program and your prompt on the left should look like a sideways carrot. Call the `qqman` R package that we are going to use to generate a Manhattan plot.
```
library(qqman)
```

Read in your results. You will need to substitute the name of your `.assoc.linear` file here:
```
gwas <- read.table("BMIgwas.assoc.linear", header=T)
Generate the plot:
png(file="ManhattanPlot_BMIgwas.png", width=1200, heaight=600)
manhattan(gwas, chr="CHR", bp="BP", snp="SNP", p="P") *typo in word height
dev.off()
```
You should now have a `ManhattanPlot_BMIgwas.png` file in your folder. To exit R, type `q().` You will be asked if you want to save your workspace image. Type `y` and hit enter.
You should now have exited R and can see your `ManhattanPlot_BMIgwas.png` file sitting in your directory. You can use scp to transfer it to your computer to view and insert into Q6 below.

### Question 6  [4 pts]

Paste your Manhattan plot below. On what chromosome is your highest peak? Do you have multiple significant loci identified in the GWAS?

> Please replace HW3_Q6_manhattan.png with the exact name of the manhattan plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![manhattan](HW3_Q6_manhattan.png)

```
(Type answer here)
```


## Task 3: Calculating Polygenic Risk Scores (PRS)

Calculating a polygenic risk score involves summing up the effects of many SNPs across the genome to determine an individual’s overall “risk” for a certain phenotype. We will begin investigating this process by looking at a single SNP.

For a given risk allele, an individual can have 0, 1, or 2 copies (because humans are diploid). If a person is homozygous for the non-risk allele, then he or she has 0 calculated risk. If they are heterozygous, then they have 0.5 or 50% of the potential risk, and if they are homozygous for the risk allele, then they have the full or maximum possible (1 or 100%) risk associated with the allele. (Keep in mind that this is under an additive model.)

In PLINK, we will be using the `--score` flag to calculate an individual’s risk based off SNP rs9930506. The score flag takes a .txt file containing 3 columns: the SNP of interest, the risk allele, and the weight/effect of the risk allele. This `.txt` file for SNP rs9930506 has been provided. Open the file to view its contents.


### Question 7 [1 pt]

What is the risk allele for rs9930506?

```
(Type answer here)
```

Now run the following command using the score flag. We will also be using the `--pheno` flag to add in the BMI phenotype data as it is not already in this set of bfiles (which is just a larger set than the previous bfiles we were working with).

```
plink --bfile 1kg_hm3_qc --score score_rs9930506.txt 1 2 3    --pheno BMI_pheno.txt --out 1kg_FTOscore
```

Now, look at your output file `1kg_FTOscore.profile`. You should see six columns. The fourth column (CNT) lists how many alleles were counted for the individual in the corresponding row. Because we only looked at one SNP, this number should be 2 in every row unless an individual is missing genotype data at that locus. The fifth column (CNT2) lists the number of alleles that were risk alleles, and the last column (SCORE) is the risk score, or rather the proportion of counted alleles that were risk alleles (CNT2/CNT).


### Question 8 [4 pts]

The lowest and highest BMI scores in this dataset are 16.0136 and 34.3742, respectively. Who do these scores belong to (i.e. sample ID) and what is the risk score for each individual? Briefly discuss your interpretation of this finding.

```
(Type answer here)
```

Now, let’s look at a polygenic risk score, rather than the risk associated with one allele. To do this, we will be using the `PRSice` program. This program is based in` R` and `PLINK` and takes in information from multiple sources to compute a PRS.

Reactivate your `R-env` conda environment (if not already activated). You do not need to `module load PRSice` (we had to make some modifications to get it to run properly).

I have put a new file in the `/share/CourseANT157L/data/ProblemSet3` folder called `SNPs.valid.` Please copy this file over to your ProblemSet3 folder within your student directory. This file will be used with an extract flag to avoid an error alerting you to duplicated SNP IDs when running `PRSice`.
Also note that we are using a slightly different dataset. The dataset we have been using before now, the `1kg_EU_qc` is just a subset of the larger dataset we are using here.
Please run the new command below:

```
Rscript /share/CourseANT157L/data/progs/PRSice/PRSice.R --prsice /share/CourseANT157L/data/progs/PRSice/bin/PRSice  --base BMI.txt --target 1kg_hm3_qc --extract SNPs.valid --snp MarkerName --A1 A1 --A2 A2 --stat Beta --pvalue Pval --pheno-file BMI_pheno.txt --bar-levels 1 --fastscore --binary-target F --out BMI_score_all
```

Look at your `.summary` and `.best` output files.

### Question 9  [3 pts]

How many SNPs were considered in the PRS calculations? What are the PRS for the following individuals: HG00097, HG00099, and HG00100

```
(Type answer here)
```


Clumping is a step that is automatically performed in PRSice (unless the user specifies `--no-clump`) when calculating PRS. Let’s take a closer look at clumping.
When calculating a PRS, we need to “choose” which SNPs should be a part of this calculation. You may have noticed that SNPs that are in close proximity may have similar p-values in the association analysis (like when you see multiple SNPs in a peek on a Manhattan plot).


### Question 10 [2 pts]

Why would it be important to calculate a PRS based on fewer SNPs (with strong association to the phenotype) rather than every SNP shown to have a strong association?

> Hint: How would the calculated score differ between the two approaches? Would one be over- or under-estimated?

```
(Type answer here)
```

We will use the plink command `--clump` along with the specifier flag `--clump-best` to identify the SNPs that should be used to calculate the PRS. The clump flag takes SNPs that are significant at a p<0.0001 threshold and clumps them with other SNPs that they are in LD with. The `clump-best` flag identifies the “best” SNP within each clump (i.e. the SNP with the highest p-value (strongest association with the phenotype) from the `.assoc.linear` file).

Run the following command:

```
plink --bfile 1kg_EU_BMI --clump YourTest.assoc.linear        --clump-best
```

You should have two output files: `plink.clumped` and `plink.clumped.best`

### Question 11 [3 pts]

Looking at your output files, answer the following questions for SNP rs11725348: What chromosome is it on? What is the rsID of its proxy SNP? What is the r-squared and p values? What are the alleles for each SNP?

```
(Type answer here)
```
