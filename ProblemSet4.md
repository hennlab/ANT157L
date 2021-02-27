## ANT157L Problem Set 4: Runs of Homozygosity

Name:
```
Write your name here
```

**Due Date:**


### Goals:

We have read about how the proportion of the genome in runs of homozygosity (ROH) is a proxy for inbreeding within an individual (i.e. inbreeding coefficient, f). We have also read about how, for quantitative traits with a dominance effect, individuals that have higher homozygosity, i.e. are more inbred, can differ in their phenotype from individuals with smaller fs. We will test for a correlation between inbreeding and height in African populations (similar to McQuillan et al. 2012) by calculating the cROH for each individual.

## Task 1: Plot and Interpret ROH in different Populations:

Note: The `Ethiopians_All` plink binary files should already be in your directory. Choose a version of the file which is **NOT LD-thinned**. We want the original data.

Begin by reading the plink manual on ROHs or check out the lecture on ROH. Then try out the command line below.
http://zzz.bwh.harvard.edu/plink/ibdibs.shtml#homo

```
plink --bfile Ethiopians_All --homozyg --homozyg-snp 50 --homozyg-window-missing 2 --homozyg-window-het 1 --homozyg-kb 1000 --out myoutputfile
```

Move the .`hom.indiv` file you created to your local computer using scp. (Check a previous assignment if you’ve forgotten how to use scp). Use `R `on your local machine and read in this file:

```R
setwd("[name_of_directory_containing_file]")
data<-read.table("myoutputfile.hom.indiv", header=TRUE)
```

Now we will examine the ROH file (the object you just named data) in greater detail. Type the following command in R to view the first 10 lines.

```
head(data, 10)
```
The NSEG column lists the number of ROH segments for each individual. The KB column lists the total length of the genome in ROH in kilobases (i.e. the is cROH or cumulative ROH). Finally, the KBAVG lists the average length of a ROH segment for an individual.

### Question 1 [1 pt]

What is the relationship between the number of segments in ROH and the cumulative total of ROH in an individual’s genome (i.e. how do you get the value in the KBAVG column from NSEG and KB values)?

```
(Type your answer here)
```

Use the R commands below to plot the relationship between cROH and the number of segments and describe. The lm function is used below to fit a linear model to the data. The `cor` function calculates the Pearson correlation coefficient between x and y. You can type `help()` or `?` for information on the usage of these functions.

```
plot(data$NSEG, data$KB, axes=TRUE, ylab="cROH (Kb)",
xlab= "Number of ROH segments", xlim=c(0,60))
regressLine<-lm(data$KB ~ data$NSEG)
corCoeff<-cor(data$NSEG, data$KB)
abline(regressLine, lty=2, col="red")
legend(5,250000,paste("correlation = ", round(corCoeff, 3)),  box.col="white")
```

### Question 2 [2 pts]

What is the correlation coefficient between segment number and cROH? Include the plot below.

```
(Type your answer here)
```

> Please replace HW4_Q2_correlation.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW4_Q2_correlation.png)

Now examine the distribution of cROH by population using the R code below.

```
hist(data[which(data$FID=="CHABU"),"KB"], breaks=seq(0,300000,10000), border="orange", ylim=c(0,20), xlim=c(0,300000), main="Distribution of cROH by pop", xlab="cROH (Kb)", axes=TRUE)
hist(data[which(data$FID=="BENCH"),"KB"], breaks= seq(0,300000,10000), border="purple", add=TRUE, ylim=c(0,20), xlim=c(0,300000))
hist(data[which(data$FID=="SHEKO"),"KB"], breaks= seq(0,300000,10000), border="red", add=TRUE, ylim=c(0,20), xlim=c(0,300000))
points(rep(250000,3), c(19,16.5,14),pch=0,col=c("orange","purple","red"))
text(260000,19,"Chabu",pos=4,cex=0.9)
text(260000,16.5,"Bench",pos=4,cex=0.9)
text(260000,14,"Sheko",pos=4,cex=0.9)
```

### Question 3 [4 pts]

Which population has highest cROHs per individual? Name two possible reasons that this population has higher cROH than the other populations. Modify the above code to include the Shekkacho and Majang and include the plot you created below

```
(Type your answer here)

```

> Please replace HW4_Q3_cROH.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![cROH](HW4_Q3_cROH.png)

## Task 2 : Testing parameters of ROH

Go back to your terminal on tadpole. We will now test what happens when you change parameters for ROH size. Modify the plink code you used in the prior section to answer the questions below. Remember to provide unique output file names. Read the resulting files into R as before and look at the same columns. Hint: You can use the functions `mean()` and `median()` to describe the results of different parameter sets.

### Question 4.  [2 pts]

What happens to mean/median cROH and segment number when you adjust the minimum ROH segment size to 500kb? 100kb? Explain.

```
(Type your answer here)
```

### Question 5.  [1 pt]
What happens to mean/median cROH and segment number when you set the minimum segment size back to 1000 kb, but you allow NO heterozygotes in a given ROH?

```
(Type your answer here)
```

### Question 6.  [2 pts]

Which set of parameters do you think is best? Include the command you used. Justify your choice.
```
(Type your answer here)
```


### Question 7. [3 pts]

What is the correlation between cROH and plink’s estimate of inbreeding? Use the `--ibc` flag and Fhat2 column from the output.

> Hint: Use the correlation coefficient function in R as shown earlier in this HW

```
(Type your answer here)
```

> Please replace HW4_Q7_cROH.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW4_Q7_correlation.png)


## Task 3. Testing the correlation between height and cROH

Inspect the third column in the `Chabu_height.txt` file in the `/share/CourseANT157L/data` directory. This column contains the height, in centimeters, for all individuals in your dataset. You will update your Chabu population with this phenotyptic data. Select only the Chabu population and write it to a new plink binary file. Update the new file with the `--pheno` command from last week. Generate an ROH dataset from the Chabu as well, using a minimum of 1000 kb and 2 heterozgotes per segment.


### Question 8 [1 pt]

Open RStudio on your local machine. Plot the distribution of height using a histogram. Is height in the Chabu normally distributed?

```
(Type your answer here)
```
> Please replace HW4_Q8_hist.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW4_Q8_hist.png)


Plot the relationship between cROH and height for each individual using the plot() function in R. Fit a regression line through the plot and calculate the correlation coefficient as before.


### Question 9 [2 pt]

Is height correlated with cROH in Ethiopians populations? Discuss (compare and contrast) your results with respect to McQuillan et al. (2012)

```
(Type your answer here)
```

> Please replace HW4_Q9.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![correlation](HW4_Q9.png)


## Task 4 Visualizing IBD Segment Sharing:

First, infer IBD between Chabu individuals (generate the `king.seg` and `king.segments.gz` files) using the `--ibdseg` flag.

> Note: the `--cpus` command is really important because it defaults to using 32 cores, so if 3 people ran king at once we would crash the server!

```
module load king
```

NOTE: You will need to modify the king command to include the name of your student folder and may need to modify your path or filename and `--prefix` flag if you named you Chabu-only bfiles something different or are working out of a folder that is not named ProblemSet4.

```
king -b /share/CourseANT157L/students/YourName/ProblemSet4/Chabu.bed --ibdseg --cpus 2 --prefix Chabu
```

Next, generate the plots with the R script. The arguments are the prefix of the `king.segments.gz` and `.seg` files (same as what you specified `--prefix` in the first king command) and the plot type, which is `ibdseg`. We will be reactivating the `R-env` conda environment that you used in the last problem set to utilize specific R packages that are required.

```
source /share/CourseANT157L/data/progs/conda/etc/profile.d/conda.sh
conda activate R-env
```
```
Rscript /share/CourseANT157L/scripts/king_segments_plot.R Chabu ibdseg
```

> NOTE: This might take a second. Don’t freak out if your prompt doesn’t reappear right away like it usually does.

The output is a gzipped folder containing many plots. You can unzip it using:
```
tar -xf Chabu_ibdseg_rplots.tar.gz
```

`cd` into the `Chabu_ibdseg_rplots` directory. You should see two more directories: `2nd_relatives `(i.e. 2nd degree relatives) and `FS_relatives` (i.e. full sibling pairs)

### Question 11 [4 pts]

Look at one of your plots. What do the white, red, and blue portions represent AND what does each mean? (i.e. don’t just give me a key, tell me what it means if two individuals are IBD1 at a locus, etc.)

```
(Type your answer here)
```

### Question 12 [2 pts]

 Paste your plots for S11_S08 and S60_S08 below. What is the relationship between these three individuals (S08, S11, and S60)? Compare and contrast IBD sharing between S08 & S11 with S08 & S60, commenting specifically on chromosomes 12 and 17.

 > Please replace HW4_Q12_S11_S08.png and HW4_Q12_S60_S08.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

 ![S11_S08](HW4_Q12_S11_S08.png)

 ![S60_S08](HW4_Q12_S60_S08.png)


 ```
 (Type your answer here)
 ```

### Question 13 [2 pts]

Paste your plots for S60_S12 and S60_S08 below. What type of relationship is being visualized in each plot? Compare and contrast the two plots

> Please replace HW4_Q13_S60_S12.png and HW4_Q13_S60_S08.png with the exact name of the plot you create. You should see it pop up when you render this document in markdown. Make sure it matches what you submit on canvas, so the graders can see your plot in this document.

![S60_S12](HW4_Q13_S60_S12.png)

![S60_S08](HW4_Q13_S60_S08.png)

```
(Type your answer here)
```

Head back to the server and run KING again. Let’s run a command to summarize the IBD0, IBD1, IBD2 sharing. The `--ibs` command will allow you to look at these summaries of IBD sharing between two individuals.

```
king -b /share/CourseANT157L/students/YourName/ProblemSet4/Chabu.bed --ibs --prefix Chabu
```
### Question 14 [3 pts]

Report the IBD0, IBD1 and IBD2 values for S60 and S12, S60 and S08 below. Do these values make sense? Explain.

```
(Type your answer here)
```
