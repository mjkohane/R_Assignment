# R_Assignment
---
title: "R_Assignment"
output:
  html_document: default
  pdf_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:



## Including Plots


##Loading Libraries

```{r}
if (!require("tidyverse")) install.packages("tidyverse")
library(tidyverse)
```

##Loading input files

```{r}
fang <- read_tsv("https://github.com/EEOB-BioData/BCB546X-Fall2019/raw/master/assignments/UNIX_Assignment/fang_et_al_genotypes.txt")
View(fang)
SNP <- read_tsv("https://github.com/EEOB-BioData/BCB546X-Fall2019/raw/master/assignments/UNIX_Assignment/snp_position.txt")
View(SNP)
```
##Data Inspection
```{r}
head(fang)
head(SNP)
```
```{r}
tail(fang)
tail(SNP)
```
```{r}
dim(fang)
dim(SNP)
```
```{r}
nrow(fang)
nrow(SNP)
```
```{r}
ncol(fang)
ncol(SNP)
```


```{r}
sapply(fang, class)
sapply(SNP, class)
```
```{r}
print(object.size(fang), units="Mb")
print(object.size(SNP), units="Mb")
```

##Data Processing

##Filtering Data
```{r}
maize <- filter(fang, Group == 'ZMMIL' | Group == 'ZMMLR' | Group == 'ZMMMR')
View(maize)
teosinte <- filter(fang, Group == 'ZMPBA' | Group == 'ZMPIL' | Group == 'ZMPJA')
View(teosinte)
```
##Transposing
```{r}
maize_transposed <- t(maize)
View(maize_transposed)
teosinte_transposed <- t(teosinte)
View(teosinte_transposed)
```
##Selecting Certain Columns in SNP Data
```{r}
SNP_cut <- SNP %>% select(SNP_ID, Chromosome, Position)
View(SNP_cut)
```
##Merging Files
```{r}
maize_transposed_merged <- merge(SNP_cut, maize_transposed, by.x = 1, by.y = 0 )
View(maize_transposed_merged)
teosinte_transposed_merged <- merge(SNP_cut, teosinte_transposed, by.x = 1, by.y = 0)
View(teosinte_transposed_merged)
```
##Increasing Position
```{r}
maize_incr <- arrange(maize_transposed_merged, as.numeric(Position), is.na(Position))
View(maize_incr)
teosinte_incr <- arrange(teosinte_transposed_merged, as.numeric(Position), is.na(Position))
View(teosinte_incr)
maize_decs <- arrange(maize_transposed_merged, desc(as.numeric(Position)), is.na(Position))
View(maize_decs)
teosinte_decs <- arrange(teosinte_transposed_merged, desc(as.numeric(Position)), is.na(Position))
View(teosinte_decs)
```
###It shows a warning but I checked no NAs were introduced, there are six NAs that existed in the original file too.

##Replacing Characters
```{r}
maize_incr_replaced <- data.frame(lapply(maize_incr, as.character), stringsAsFactors=FALSE)
maize_incr_replaced <- data.frame(sapply(maize_incr_replaced,function(x) {x <- sub("?/?","?",x,fixed=TRUE)}))
View(maize_incr_replaced)
teosinte_incr_replaced <- data.frame(lapply(teosinte_incr, as.character), stringsAsFactors=FALSE)
teosinte_incr_replaced <- data.frame(sapply(teosinte_incr_replaced,function(x) {x <- sub("?/?","?",x,fixed=TRUE)}))
View(teosinte_incr_replaced)
teosinte_decr_replaced <- data.frame(lapply(teosinte_decs, as.character), stringsAsFactors=FALSE)
teosinte_decr_replaced <- data.frame(sapply(teosinte_decr_replaced,function(x) {x <- sub("?/?","-",x,fixed=TRUE)}))
View(teosinte_decr_replaced)
maize_decr_replaced <- data.frame(lapply(maize_decs, as.character), stringsAsFactors=FALSE)
maize_decr_replaced <- data.frame(sapply(maize_decr_replaced,function(x) {x <- sub("?/?","-",x,fixed=TRUE)}))
View(maize_decr_replaced)
```

```{r}
for (i in 1:10) {
  maize1 <- filter(maize_incr_replaced, Chromosome == i)
  outpath <- "/Users/mahsa/Documents/R_Assignment"
  nam <- sapply(
    names(maize),function(x){
                    paste("maize_incr_chr", i, ".tsv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.table(maize1, file=out_filePath[i])
}
for (i in 1:10) {
  maize2 <- filter(maize_decr_replaced, Chromosome == i)
  outpath <- "/Users/mahsa/Documents/R_Assignment"
  nam <- sapply(
    names(maize),function(x){
                    paste("maize_decr_chr", i, ".tsv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.table(maize2, file=out_filePath[i])
}
  for (i in 1:10) {
  teosinte1 <- filter(teosinte_decr_replaced, Chromosome == i)
  outpath <- "/Users/mahsa/Documents/R_Assignment"
  nam <- sapply(
    names(maize),function(x){
                    paste("teosinte_decr_chr", i, ".tsv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.table(teosinte1, file=out_filePath[i])
  }
  for (i in 1:10) {
  teosinte2 <- filter(teosinte_incr_replaced, Chromosome == i)
  outpath <- "/Users/mahsa/Documents/R_Assignment"
  nam <- sapply(
    names(maize),function(x){
                    paste("teosinte_incr_chr", i, ".tsv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.table(teosinte2, file=out_filePath[i])
}
```

```{r}
if (!require("reshape2")) install.packages("reshape2")
library(reshape2)
if (!require("ggplot2")) install.packages("ggplot2")
library(ggplot2)
```
```{r}
fang_t <- t(fang)
View(fang_t)
whole_merged <- merge(SNP_cut, fang_t, by.x = 1, by.y = 0)
View(whole_merged)
```
```{r}
tidy <- whole_merged %>% pivot_longer( - "SNP_ID", names_to = "SNP_name", values_to = "SNP_Value")
View(tidy)
ggplot(whole_merged) +geom_bar(aes(x=Chromosome, fill=Chromosome)) + 
  ggtitle("SNPs per Chromosome") + 
  labs(x="Chromosome",y="SNP Count")
```
```{r}
ggplot(fang, aes(Group)) +
  geom_bar(aes(fill = Group))
```



```{r}
melted_geno <- melt(fang, id = c("Sample_ID", "Group", "JG_OTU"))
melted_SNP <- melt(SNP_cut, id = c("SNP_ID", "Chromosome"))
colnames(melted_geno)[4:5] <- c("SNP_ID","SNP_Seq")
melted_data <- merge(melted_SNP, melted_geno, by.x = 1, by.y = 4)
melted_data$SNP_Type <- melted_data$SNP_Seq
melted_data[] <- lapply(melted_data, gsub, pattern = "?/?", replacement = "NA", fixed = TRUE)
melted_data$SNP_Type[melted_data$SNP_Type == "A/A" | melted_data$SNP_Type == "T/T" |
                       melted_data$SNP_Type == "G/G"| melted_data$SNP_Type == "C/C"] <- "Ho"
melted_data$SNP_Type[melted_data$SNP_Type != "Ho" & melted_data$SNP_Type != "NA"] <- "Ht"
sorted_het_data <- arrange(melted_data, Group, JG_OTU)
##Heterozygocity by group
ggplot(sorted_het_data) + geom_bar(aes(x = Group, fill = SNP_Type), position = "fill") +
  ggtitle("Heterozygocity by Group") + labs(x = "Group", y = "SNP Types")
```
```{r}
## percent heterozygosity by SNP ID
##total SNPS = 2782
SNP_Hetero <- melted_data %>% group_by(SNP_ID,SNP_Type) %>% summarize(n=n())
SNP_Hetero$Percent <- SNP_Hetero$n /2782
ggplot(melted_data) + geom_bar(aes(x = SNP_ID, fill = SNP_Type), position = "fill") +
  ggtitle("Heterozygocity per SNP") + labs(x = "SNP ID", y = "SNP Types")
```
```{r}
##heterozygocity by Species
##used JG_OTU with the with the sample number removed to sort by species
sorted_het_data$Species <- as.character(sorted_het_data$JG_OTU)
for (i in 0:9 ) {
  sorted_het_data[10] <- lapply(sorted_het_data[10], gsub, pattern = i , replacement = "", fixed = TRUE)
}
ggplot(sorted_het_data) + geom_bar(aes(x = Species, fill = SNP_Type), position = "fill") +
  ggtitle("Heterozygocity by Species") + labs(x = "Species", y = "SNP Types")
```
