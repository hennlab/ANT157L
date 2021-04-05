## ANT157L Problem Set 1: Allelic and genotypic frequencies with application to SNP array data


Name:
```
Write your name here
```

**Due Date:**


### Goals:
At the end of this exercise, you should be able to:
- Record allele frequencies from dbSNP and plink
- Understand how SNP array data are formatted
- Perform simple Bash command line executions
- Identifying missing genotypes / individuals with high missing data
- Use Hardy-Weinberg to remove problematic genotypes from a SNP array dataset


## Task 1. Manipulate SNP array data using PLINK
PLINK is a piece of software that allows users to perform various data manipulation and analyses on genome-wide SNP data. Today we are going to focus on the former.

A very useful online manual is available for PLINK here:
http://zzz.bwh.harvard.edu/plink/dataman.shtml
and/or
https://www.cog-genomics.org/plink/1.9/general_usage

#### Log on to the Genome Center cluster.

```
ssh yourUserName@tadpole.genomecenter.ucdavis.edu
```

Then navigate to our CourseANT157L folder and into your `/students/yourname` directory

You will be using PLINK software. In order to use this, you need to load the software module:

```
module load plink2/1.90p
```

The command module load will load different types of software installed on the Genome Center cluster. You need to do this when starting each session.

### Question 1 [3 pts]

Use `head` to explore the first line of the `Ethiopians_chr1.fam` file and the `Ethiopians_chr1.bim` file in the `/share/CourseANT157L/data/Ethiopians/` directory.

What is the population and individual ID of the first individual in the .fam file?
```
(Type your answer here)
```

What is the physical position of the first SNP in the .bim file?
```
(Type your answer here)
```

PLINK files can get very large, containing genotype information for hundreds of thousands of individuals at millions of SNPs, so we do not want to have to use less to view and manipulate the data. Instead we can use PLINK to read the file and tell us something about the structure of our data like so:

```
plink --bfile /share/CourseANT157L/data/Ethiopians/Ethiopians_chr1 --freq
```

The `--bfile` argument informs PLINK what dataset to look for, in this case `Ethiopians_chr1` (it will automatically look for `.bed`, `.bim` and `.fam` endings of the dataset). Running this command will print a summary of the contents of the input dataset to screen. It also prints a summary of the output dataset if any manipulations are performed (in this case, we used the `--freq` flag to calculate allele frequencies).

You can either copy the datafile to your own folder or continue to simply call the dataset from the /data/ folder.

### Question 2 [1 pt]

Write down the command to copy the data files to your own folder.

```
(Type your answer here)
```

### Question 3.1 [3 pt]

After running the PLINK command above in Q1, look at the plink.log file. How many SNPs, males and females are in the `Ethiopians_chr1` dataset?

```
(Type your answer here)
```

### Question 3.2 [3 pt]

Identify the individuals who are missing sex information. [**Hint**: look at the files in your directory] Use nano or plink to update the `.fam `file and replace the missing sex with “2”. List your commands and explain.
```
(Type your answer here)
```

### Question 4 [4 pts]

When you run the first command, the `.bed` file contains the genotype data and is no longer human-readable. However the `.bim` and `.fam` files are. What information is contained in them?
```
(Type your answer here)
```

----

Now let’s start manipulating the dataset. The “family” (in this case, population label) and individual IDs for each of the individuals can be found in the `.fam` file. Let’s take out the last five individuals from the dataset. In order to do this, we need to first make a file containing these individuals that we can point PLINK to. You can do this easily with a combination of the tail and cut commands you learned last week.

```
tail -5 Ethiopians_chr1.fam > temp_file.txt
```

This command takes the last three lines in the `.fam` file and outputs (>) them to `temp_file.txt`.

```
cut –d " " -f 1-2 temp_file.txt > samples2remove.txt
```
> NOTE: If you are getting an error when running this command (such as a print out that includes “No such file or directory”) then type in the command directly rather than copying and pasting)

This command cuts out fields 1 through 2 and outputs (>) them to `samples2remove.txt.` The `–d` flag tells cut what delimiter is defining the columns, in this case, just a white space.

We can also do this with one step rather than two by using the `|` notation, which allows us to “pipe” the output of the first tail command directly into the second cut command like so:

```
tail -5 Ethiopians_chr1.fam | cut -d " " -f 1-2 > samples2remove
```

However way you decide to do it, you should now end up with a file with five rows and two columns that looks like this:
```
SHEKO BYK046
SHEKO BYK047
SHEKO BYK048
SHEKO BYK049
SHEKO BYK050
```

We can now use this file to tell PLINK to remove these five samples from the dataset, and produce a subsetted dataset like so:

```
plink --bfile Ethiopians_chr1 --remove samples2remove --make-bed --out Ethiopians_chr1_rem5samples
```

> NOTE: Again, if you try to copy and paste this command and have issues, just type it directly into your terminal.

### Question 5 [1 pt]

What happens when you use the `--keep` argument instead of the `--remove` argument?

```
(Type your answer here)
```

## Task 2: Quality Filtering for SNP Data

### Question 6 [1 pt]

Use the skills learned so far and the PLINK manual to create a dataset with only the top half of SNPs. What is the genotyping rate is the new dataset?

```
(Type your answer here)
```

PLINK can also be used to filter data based on some criteria. One common operation is to filter the data for SNPs below a certain minor allele frequency threshold. For example, the remove all SNPs with a sample allele frequency less than 5% we can run the following command:

```
plink --bfile Ethiopians_chr1 --maf 0.05 --make-bed --out Ethiopians_chr1_freq0.05
```

### Question 7 [1 pt]

How many SNPs are left when you filter the data using a minor allele frequency of 5% command?

```
(Type your answer here)
```

Another common quality control measure is to filter for missingness. This allows us to filter out SNPs that are missing (not genotyped) in some proportion of our samples. The command we use to do this is the `--geno` flag followed by a decimal number indicating the threshold at which to filter. For example, to filter out all SNPs that are missing in at least 5% of my data, I would use `--geno 0.05` in my command

### Question 8 [1 pt]

How many SNPs are left when you filter the data using a genotyping QC command specifying that a SNP can have at most 2% missing data?

```
(Type your answer here)
```

Another common quality control filter for SNP array data is to remove loci whose genotypes are not in Hardy-Weinberg equilibrium. This pattern at a locus may indicate that the SNP calling algorithm that identifies the "AA  AB  BB" genotypes has not correctly identified the proportions of the three different genotypes using measures of fluorescence from the array probes.

### Question 9 [2 pts]

What is another reason that a given locus may not have genotype proportions in Hardy-Weinberg equilibrium? Explain

```
(Type your answer here)
```

Read the page on Hardy-Weinberg calculations in plink:
http://zzz.bwh.harvard.edu/plink/summary.shtml#hardy


Run the plink Hardy-Weinberg equilibrium test. This will generate a “plink.hwe” output summary of the SNPs in this array dataset. Then check the SNP rs12075 on chr1 in your plink.hwe output file

> Hint: look for a flag to use with the grep function that will let you search for exact matches.

### Question 10 [3 pts]

What is the observed proportion of heterozygotes in these five populations? What is the expected proportion of heterozygotes based on allele frequencies? Is this SNP in Hardy-Weinberg equilibrium?

```
(Type your answer here)
```

Now read the command line information for a HWE filter:
http://zzz.bwh.harvard.edu/plink/thresh.shtml#hwd

We can also filter SNPs that violate Hardy-Weinberg expectations like so:

```
plink --bfile Ethiopians_chr1 --hwe 0.001 --make-bed --out Ethiopians_chr1_hwe0.001
```
Here, the number after the `--hwe` argument is the p-value threshold that PLINK will use to decide to remove SNPs.

### Question 11 [1 pts]

Are more or fewer SNPs left when you filter the data for a minor allele frequency of 5% versus a HW p-value of 0.001?
```
(Type your answer here)
```

### Question 12 [1 pts]

How many SNPs are removed when the five populations are considered as a single group for estimating HW?
```
(Type your answer here)
```

Next, we want to find out how many are removed when you perform HW filtering on each population separately with a p<0.001.
> Hint: Use grep command to extract a certain population from the .fam file.

 Then you will need to generate 5 new input files from the original binary files by using `--keep` (as we have done before) and then apply the HWE filter again for each of them.

Refer to http://zzz.bwh.harvard.edu/plink/dataman.shtml#keep

### Question 13 [5 pts]

List the number of SNPs excluded for all 5 groups tested separately: Sheko, Bench, Chabu, Majang, Shekkacho
```
(Type your answer here)
```

### Question 14 [3 pts]

Why does the Hardy-Weinberg filter produce different results for pooled populations versus separate populations (~1 paragraph)? References to the actual HW equation are suggested / appropriate.

```
(Type answer here)
```

Use plink to query the allele frequency of rs12075 in each population using the
`--snp` and another flag. For help, refer to  https://www.cog-genomics.org/plink/1.9/basic_stats#freq

### Question 15 [3 pts]
What is the minor allele of rs12075 and what is its frequency in each population?

Please fill your answers in the chart below:
> Note: spacing isn't important for the chart to show up in markdown, only the lines and dashes

| Pop     | Minor Allele (A1) | MAF     |
|---------|-------------------|---------|
|Bench    |                   |         |
|Chabu    |                   |         |
|Majang   |                   |         |
|Shekkacho|                   |         |
|Sheko    |                   |         |

### Question 16 [3pts]
 Calculate the expected heterozygosity of rs12075 for the Chabu using the allele frequencies obtained in Q15. Use the equation from lecture. Let’s assume that this heterozygosity is the starting H0 and the Ne for the Chabu is 1,000. What is the expected heterozygosity Ht in 5 generations? Hint: Check the lecture slides for Crow and Kimura eq 3.11.2 and show your work

 ```
 (Type your answer here)
 ```

 ### Question 17 [2pts]
 Check out rs12075 in the dbSNP online database: http://www.ncbi.nlm.nih.gov/projects/SNP/

 What gene does this SNP lie in? Focusing on the HapMap populations, is there anything interesting about the geographic distribution of this SNP (i.e. which populations have the highest/lowest frequencies of the minor allele)?

 ```
(Type your answer here)
 ```
