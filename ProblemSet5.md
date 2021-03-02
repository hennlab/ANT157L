## ANT157L Problem Set 5:  Heritability and Polygenic Scores Continued

Name:
```
Write your name here
```

**Due Date:**


### Goals:

In this problem set, you should learn the sensitivity of heritability to inclusion of relatives, estimate heritability from SNP array dataset comprised of an unrelated cohort and interpretation of h2. Additionally, we will dive into PGS a bit further to understand how a PGS predicts phenotype.

## Task 1. Estimate Heritability Using a GRM:


Return to your `1kg_hm3_qc` plink dataset and the associated BMI files. These are available again in `/share/CourseANT157L/data/ProblemSet5`.

### Question 1. [2 pts]

What is the number of individuals in this dataset? What is the mean of the phenotype Body Mass Index in the dataset?

```
(Type your answer here)
```

### Question 2. [2 pts]

Now, load up KING and examine the relationships in the genetic dataset using the `--related` flag. Are there parent-offspring, siblings, 2nd, 3rd degree relatives in there? If so, please list the counts (inferred) of each pair category.

```
(Type your answer here)
```

GCTA is a software package that will estimate heritability from genetic relatedness matrices. You can load this as a module to run on tadpole.

```
module load gcta/1.93.1beta
gcta --bfile 1kg_hm3_qc --autosome --maf 0.01 --make-grm --out 1kg_gcta
gcta --grm 1kg_gcta --grm-cutoff 0.1 --make-grm --out 1kg_rm075
gcta --grm 1kg_rm075 --pheno BMI_pheno.txt --reml --out 1kg_BMI_h2
```

You can read the heritability output by looking at `1kg_BMI_h2.hsq`.

### Question 3.

Once you have GCTA loaded, estimate the narrow-sense heritability in this dataset using a relatedness (IBS) cutoff of >0.10, as described above. What is the h2SNP for BMI in this dataset? What does V(G) and V(e) refer to? Now adjust the threshold to exclude parent-offspring pairs. Does the heritability differ with different relatedness cutoffs? Why?

```
(Type your answer here)
```

## Task 2. Interpret Polygenic Risk Scores:

When we last left off in Problem Set 3, we used PRSice to compute polygenic risk scores for BMI. However, we didn’t spend much time interpreting them. Make sure you have your PS#3 available and organize the files in there to exclude any versions that you don’t need.

First, we will use your old input data to sort individuals’ PRS into quantiles (0-20%,20-40%, 40-60%, 60-80%, 80-100%) of risk. That is, which individuals are in the top 20th percentile of risk?

```
source /share/CourseANT157L/data/progs/conda/etc/profile.d/ conda.sh
conda activate R-env
Rscript /share/CourseANT157L/data/progs/PRSice/PRSice.R --prsice /share/CourseANT157L/data/progs/PRSice/bin/PRSice --base BMI.txt --target 1kg_hm3_qc --extract SNPs.valid --snp MarkerName --A1 A1 --A2 A2 --stat Beta --pvalue Pval --pheno-file BMI_pheno.txt --thread 1 --bar-levels 5e-08,5e-07,5e-06,5e-05,5e-04 --quantile 5 --binary-target F --out BMI_score_plots
```

Look at your output files. You should have a bar level plot and a quantile plot.

### Question 4 [2 pts]

 Transfer the quantile plot to your computer and paste it below. How does BMI change as the quantiles increase? Is there a significant difference between the lowest and highest quantiles?

 > Please replace HW5_Q4_quantile.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

 ![correlation](HW5_Q4_quantile.png)


```
(Type your answer here)
```

### Question 5 [2 pts]

Transfer the p-value bar plot to your computer and paste it below. It should show the correlation between the individual BMI phenotype and the PRS for each p-value category. How does the model fit (R2) change as the p-value becomes more lenient? Should we be using PRS to predict individuals’ phenotypes, if so which p-value threshold?

> Please replace HW5_Q5_barplot.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW5_Q5_barplot.png)


```
(Type your answer here)
```

### Question 6 [4 pts]

Update the FID column (population affiliation) in plink using the `--update-ids` flag and the `update_pops.txt` file, which has been placed in the `/share/CourseANT157L/data/ProblemSet5` folder. Perform a PCA on the dataset. Do you remember how to do this? Is there significant ancestry heterogeneity among individuals?

> [hint: Refer back to your Problem Set 2 for PCA plotting]

> Please replace HW5_Q6_pca.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW5_Q6_pca.png)

```
(Type your answer here)
```

### Question 7 [4pts]

Rerun the PGS in `PRSice` including the first 3 PCs as covariates. You can do this by adding a `--cov` flag with an appropriate file. The covariate file should include the family (Population) and individual IDs, e.g. see below for a dummy example.

```
FID IID PC1 PC2 PC3
CEU NA50001 -0.002 0.3 0.04
```

our command also needs to substitute the `--target` dataset (`1kg_hm3_qc`) with a dataset that includes the population ID and your `BMI_pheno.txt` file needs to be revised or updated to a file that also contains the population ID specifications.
If you include the PCA covariates, does the model fit (R2) go up or down? Why does this happen, explain.

> Please replace HW5_Q7_plot.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW5_Q7_plot.png)

```
(Type your answer here)
```
