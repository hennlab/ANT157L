## ANT157L Problem Set 2: Identifying population structure from genomic data


Name:
```
Write your name here
```

**Due Date:**


### Goals:
This exercise is meant to give you tools for observing population structure in genomic datasets. Additionally you will test whether some individuals have recent admixture from different ancestries and whether individuals within a population are closely related to each other. Some of the work is in R. You will learn how to make your own ADMIXTURE and PCA graphics with the code provided

## Task 1. Plot and Interpret PCA:

Navigate to your directory and identify the binary PLINK files (Ethiopians_Chr1.*) that you QCed in the last problem set. We will begin by loading plink to run a PCA.

>NOTE: Use the files that have the updated sex information. Some of you ran the QC filters on the Ethiopians data set so that each filter built on the previous one. If this is the case, you want to use your datset that has geno, maf, and hwe filters. If you ran your filters separately (i.e. each one on the Ethiopians_chr1 files) then quickly run the following command to produce a QCed set to use from this point onward:

```
plink --bfile Ethiopians_chr1 --geno 0.02 --maf 0.05 --hwe 0.001 --make-bed --out Ethiopians_chr1_geno02_maf05_hwe001

```

You can run a PCA in plink by using the --pca command (see plink website and Ch. 9 of your book for more details). In this instance, modify the plink command below to run the first 4 principal components on your Ethiopians_Chr1 dataset.

```
plink --bfile yourdataset --pca 4 --out EthiopianChr1_PCs4
```

## Question 1 [ pts]

Look at the .eigenvec file. Describe the file contents. What is contained in the row and the columns?

```
(Type your answer here)
```

Now we will plot the PCA. Open a new terminal window (you can do this with Command+T the same way you would open a new window on the internet). DON’T login to tadpole. Instead use pwd to see where you are on your local computer; this is your current working directory. In order to move a file from a remote host (tadpole) to your local host (your computer) we can use the scp command. NOTE this is all in one command line. Fill in the login and file names with yours. The “.” here means your current working directory on your computer, just like you practiced in week 1 (you can change this if you want to download things elsewhere).

```
scp loginName@tadpole.genomecenter.ucdavis.edu:/share/CourseANT157L/students/YourName/filename .
```

Open R. In R console, set your working directory to where you downloaded the file.

```
setwd("[path_to_my_files]/")
```
Then we will install a package called ggplot to visualize the data
```
install.packages(“ggplot2”)
library(ggplot2)
```

Now we will read in the data to a table we call “data” and relabel the columns to be easier to read.

```
columns=c(“population”, “sampleID”, “pc1”, “pc2”, “pc3”, “pc4”)
data<- read.table("EthiopianChr1_PCs4.eigenvec", sep = “”,  header=F, col.names=columns)
ggplot(data, aes(x=pc1,y=pc2, color=population))+geom_point()+ xlab(“PC1”)+ ylab(“PC2”)

```

You can save a plot by clicking on the export button in the RStudio console, just above the image. Or by doing the following, which will save it to your current working directory

```
png(“myplotname.png”)
ggplot(like the above plotting code…)
dev.off()
```

> Note: you can save to a pdf using `pdf("myplotname.pdf")`, but markdown cannot show pdfs, so we are saving as a png.

## Question 2 [4 pts]

Construct and save plots of PC1 vs PC2, and PC3 vs PC4 to pngs. Add them below.

> Please replace image.png with the exact name of each PC plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![PC1 vs PC2](image.png)

![PC1 vs PC2](image.png)


Do we observe population structure in this dataset? Explain. Which populations are most closely related to each other? Which are most distant?

```
(Type your answer here)
```

## Question 3 [2pts]

Do you learn any additional information by looking at PC3 vs PC4 (as opposed to just the first two PCs?

```
(Type your answer here)
```

## Question 4 [2pts]

Which command do you use to remove old files? How about removing multiple files at once?

```
(Type your answer here)
```

Copy the ```/share/CourseANT157L/data/week4/Ethiopians_All_UpdateSex_Sort
.* ```
 plink dataset into your new directory.

 ## Question 5 [2pts]

 How many chromosomes and SNPs are in this file?

 ```
(Type your answer here)
 ```

Now let’s create a new binary dataset with fewer correlated SNPs. Let’s remove SNPs that have an r<sup>2</sup> of 0.5 or higher within a 50 kb window. --indep-pairwise takes 3 parameters, window-size, the number of variants by which the window slides down the chr and the cutoff for the degree of correlation (r<sup>2</sup>). Read Ch. 9.3 for more information.

```
plink --bfile yourdataset --indep-pairwise 50 20 0.5 --out EthiopiansAll_LD050
```
```
$plink --bfile yourdataset --extract EthiopiansAll_LD050.prune.in --make-bed --out EthiopiansAll_LDpruned050
```

## Question 6 [4 pts]

How many SNPs are left in your LD-thinned dataset?

```
(Type your answer here)
```

Re-run PCA on this dataset. Add the PCA (PC1 vs PC2) into the space below. Is this any different from the PCA on just chr1?

![PC1 vs PC2](image.png)

```
(Type your answer here)
```

## Task 2. Start a preliminary run of ADMIXTURE.

You will be submitting a shell script to run `ADMIXTURE` in the background while you work on other things. Shell scripts end in .sh suffix. It can take time for `ADMIXTURE` to run, especially for higher Ks. We’re going submit a script to run `ADMIXTURE` for K = 2,3,4,5 and 6 different ancestral clusters (i.e., at the end you’ll have run `ADMIXTURE` 5 times).


The first step is to run admixture on your dataset of interest (i.e. your LD-pruned Ethiopian dataset) using the example script below. There is a copy of this script in the admixture folder that has been placed in your student folder. You can use nano to edit it. The class will submit the script as a slurm job using:

```
sbatch yourscriptname.sh
```

Where you replace the script name with whatever yours is called.

The script looks like this:

```
#!/bin/bash
#SBATCH --job-name="myjob" # Job name
#SBATCH --nodes=1 # single node, anything more than 1 will not run
#SBATCH --ntasks=1 # equivalent to cpus
#SBATCH --time=1-00:00:00 # expected time of completion in days-hours:minutes:seconds
#SBATCH -p production # partition to submit to
#SBATCH --account=ant157l
#SBATCH --reservation=ant157l
#SBATCH --mail-type=ALL

# Replace your email here!
#SBATCH --mail-user=you@gmail.com


module load admixture
for K in 2 3 4 5 6
do
  admixture -s 35 <path to bed file> $K | tee lof${K}.out
done
```

The output of admixture should be a .P and .Q file for every iteration of "K" that you selected, as well as the log files for every K iteration.

Make sure you change the script to give your job a unique name and call the correct files. The `-s` flag simply specifies a random number to seed the program. You can change this random number if you want (right now it is 35). Remember to write in the correct path to your `.bed` plink file.

Submit this job to the cluster. We’ll get back to plotting the output later. Check back in a few minutes to make sure that your job is running correctly and that the output files were produced for K=2. The whole thing could take 6-8 hours to finish. When it’s done, you should see your output files – a set of .P, .Q and log files.

You can check the status of your job with
```
squeue -u <username>
```

Now, that’s the end of this week. Before quitting, you want to make sure to save all R data (variables, environment, etc.), so that next week when you come back to continue working in R, you don’t have to reload the input file and redo all above analyses. You can save R workspace by typing:

```
save.image(file=”April20_wkspace.RData”)
```

If you typed exactly the same command as above, your saved workspace file April20_wkspace.RData is located in the R working directory that was set up in the beginning (as in previous `setwd` command).

## Task 3. Determine if there are related individuals in the dataset

```
module load king/2.1.8
king -b plinkfile.bed --related
```

KING is a program to identify relatives. We will learn more about the principles behind this later on in the course. But for now, we just want to find close relatives and remove them from our datasets. The syntax for KING is very similar to plink. It reads in plink binary files. http://people.virginia.edu/~wc9c/KING/manual.html


## Question 7 [2 pts]

Load up KING and run it on your full (not LD-pruned) SNP dataset. Let’s just limit ourselves to discovering only 2nd degree relationships, using `--degree 2`.

Check out the summary info that king prints to your screen and also the `king.kin` file you generated. How many parent-offspring (PO) are in the dataset?
```
(Type your answer here)
```

How many 2nd degree relatives?
```
(Type your answer here)
```

Which population has the most relatives?
> HINT: you may need to use grep to figure out which population has the most PO

```
(Type your answer here)
```

## Question 8 [2pts]

Given the relationships inferred in the previous exercise, how would you remove related individuals from the original dataset? Write down the procedure that you would use. Feel free to test this! You will be implementing the procedure later on for ADMIXTURE.

> hint: Think back to your grep and cut commands!


```
(Type your answer here)
```

## Task 4. Plot and Interpret ADMIXTURE:

### 1. Running Pong to plot admixture runs

Pong requires an input file with three **tab-delimited** columns:

- Column 1: A unique run ID for each Q matrix (must begin with a letter, can include numbers and underscores)   
- Column 2: The K value for the Q matrix  
- Column 3: The file path to the Q matrix


To begin, create this “filemap” file in the same directory as your P and Q matrices.

Example filemap:

```
k2  2 /share/CourseANT157L/students/Mira/Ethiopian.2.Q
k2  3 /share/CourseANT157L/students/Mira/Ethiopian.3.Q
k2  4 /share/CourseANT157L/students/Mira/Ethiopian.4.Q
k2  5 /share/CourseANT157L/students/Mira/Ethiopian.5.Q
k2  6 /share/CourseANT157L/students/Mira/Ethiopian.6.Q
```

In order to have population labels on the plot, Pong also requires a file with a single column containing the population IDs for each individual in order. They refer to it as the “ind2_pop” file in the [manual](http://brown.edu/Research/Ramachandran_Lab/files/pong/pong-manual.pdf)

This information can be found in the `Ethiopians_All.fam` file, in the first column. You can use this bash command to extract the first column and save it in a new file
```
cut -d " " -f1 Ethiopians_All.fam > ind2_pop.txt
```

Pong works by opening up a connection to a localhost on a web browser in order to produce an interactive visualization. In order to run pong on the remote server (tadpole), we need to set up port forwarding from your laptop’s port 4000 to the remote cluster’s port 4000.

> Note: only one person at a time can be connecting via a given port number like 4000. So, you will be assigned your OWN number to connect to.

Follow these steps below in order to set up port forwarding and run pong:

1. Open up your terminal and run this command, replacing `4000` with the number you are assigned.
    ```
    ssh -N -L 4000:localhost:4000 username@tadplole.genomecenter.ucdavis.edu
    ```
    You will be prompted to put in your password. The command will then hang: This is normal. **Do not close the terminal window**, just let the command hang there.

2. Open up a new terminal tab and log onto the tadpole server using
```
ssh username@tadpole.genomecenter.ucdavis.edu
```
3. Navigate to the directory containing your P and Q matrices, and the mapfile you created. To run pong, simply use:
 ```
 module load pong
 pong -m filemap -i ind2_pop.txt -p 4000
  ```
Make sure to specify your correct port forwarding number

4. You should see an output that looks like this

```
-------------------------------------------------------------------
                            p o n g
      by A. Behr, K. Liu, T. Devlin, G. Liu-Fang, and S. Ramachandran
                       Version 1.4.7 (2016)
-------------------------------------------------------------------
-------------------------------------------------------------------

Parsing input and generating cluster network graph
Matching clusters within each K and finding representative runs
For K=5, there is 1 mode across 1 run.
Matching clusters across K
Finding best alignment for all runs within and across K
match time: 0.00s
align time: 0.00s
total time: 0.03s
```

5. Open up your web browser of choice (Chrome, Safari etc) and type in http://localhost:4000 (subbing your assigned number for 4000)

6. Pong should open up an interactive visualization in your web browser.

7. When you are done, simply close the terminal tab where the port forwarding ssh command is hanging and the connection will close.

## Question 9 [4 pts]

 If you were to create a population structure plot using ADMIXTURE for k=3, which ancestral clusters would you expect to observe? How about at k=4? Write down your expectations.
  > Hint: you may need to google the population names and do a little research on the groups. Feel free to discuss this with lab partners

K = 3:
 ```
 (Type your answer here)

 ```
K = 4
 ```
 (Type your answer here)
 ```

 ## Question 10

 Copy your `ADMIXTURE` plots for K=2 through K=6 below. Interpret each plot, describing how the changes you observe as you increase K. If you don’t already know, you should research the geographic locations of all the populations you plotted.


 ![admixture](image.png)


 ## Question 11 [2 pts]

 Take a look at K=6. You should see that some individuals no longer look ‘admixed’, but instead have their own unique color that they share with a few other individuals in their population. Why do you think you see these unique clusters? Explain

 ```
 (Type your answer here)
 ```

 ## Question 12 [2pts]

 Look at the K=2 output files from `ADMIXTURE`. At the bottom of the log file, there will be an estimate of Fst between the two ancestral populations. What is this number? Is differentiation between the two ancestral populations very high, very low or moderate, in the context of humans?

 ```
 (Type your answer here)
 ```

 ## Task 5. Rerun ADMIXTURE by removing close relatives

 Since we have inferred related individuals in the Ethiopians populations in Task 3, we can use this information as we continue to explore the structure of these populations using ADMIXTURE.

 ## Question 12 [5 pts]

 Go back to your list of close relatives that you inferred with KING. Filter the original dataset such that there are no close kin. HINT: do you need to remove both parent and child, or just one of them? Now, re-write your script to run ADMIXTURE for only K=5 and 6 on these filtered files. Describe the difference you observe between these Ks with and without close kin. Include the two new bar plots below.

 ![admixture](admixture.png)


 ```
(Type your answer here)
 ```
