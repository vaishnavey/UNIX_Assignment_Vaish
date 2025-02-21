# UNIX Assignment

## Data Inspection



The top lines of the given files are initally inspected with the head -2 command
```
$ head -2 fang_et_al_genotypes.txt

$ head -2 snp_position.txt
```

### Attributes of `fang_et_al_genotypes.txt`
```
$ wc fang_et_al_genotypes.txt
$ du -h fang_et_al_genotypes.txt
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```
By inspecting this file I learned that:

* Number of lines:2783
* Number of words:2744038
* Number of characters (size in bytes):11051939
* File size: 6.7 M
* Number of columns = 986

### Attributes of `snp_position.txt`

```
$ wc snp_position.txt
$ du -h snp_position.txt
$ awk -F "\t" '{print NF; exit}' snp_position.txt
```

By inspecting this file I learned that:
* Number of lines: 984
* Number of words: 13198
* Number of characters (size in bytes):82763
* File size: 49 K
* Number of columns = 15


## Data Processing
To combine the information in the two data files, we use the join command.\
First file (fang_et_al_genotypes.txt) has Sample IDs in its first column\
Second file has SNP IDs in its first columnn\
We need to transpose our data before merging to process data in rows.

### Maize Data
We need to extract certain groups (ZMMIL, ZMMLR, and ZMMMR) from the third column column of the fang_et_al_genotypes.txt file.

```
$ awk '$3 ~ /Group|ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt > maize.txt
$ head maize.txt

```
The genotype data is in the rows, it is convenient to transpose this data before we process it.
```
$ awk -f transpose.awk maize.txt > maize_transposed.txt
```
We need SNPs ordered on increasing position values. This can be done using the sort command.\
Before sorting, based on values, we need to save the header for later use
```
$ head -n 1 maize_transposed.txt > header.txt
```
Sorting genotype data:
The first three lines contain non-numerical values, this is seen using 
```
$ head -n 3 maize_transposed.txt
```
We sort the rest of the numeric data in the file
```
$ tail -n +4 maize_transposed.txt | sort -k1,1 > sorted_maize.txt
```
Now the data is ready after sorting, we add the header 
```
cat header.txt sorted_maize.txt > sorted_maize_with_header.txt
```
A similar procedure is repeated for the other file


#### Processing SNP Position data
```
$ head -n 1 snp_position.txt > snp_header.txt
$ tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp.txt
$ cat snp_header.txt sorted_snp.txt > sorted_snp_with_header.txt
```

When we join the above data, we need it in the following format
* SNP_ID, Chromosome, Position. Genotype data
This means we just need columns 1,3 and 4 from the SNP data
```
$ cut -f 1,3,4 sorted_snp_with_header.txt > snp_data.txt
```
We need to combine genotypes to this snp_data.txt
```
$ sed 's/Sample_ID/SNP_ID/' sorted_maize_with_header.txt > maize_final.txt
$ join -1 1 -2 1 -t $'\t' snp_data.txt maize_final.txt > maize_joined.txt
```
Here, -1 1 denotes column 1 of the first file and -2 1 denotes the second column of the first file.\
#### Increasing Position Values of Chromosomes

Now that we have joined the two datafiles, we need to sort them based on increasing position values and with missing data encoded by this symbol ?\
The unknown data in the file which are denoted by ? are grouped.
```
$ sed 's/unknown/?/g' maize_joined.txt | sort -k3,3n > maize_data_sorted.txt 

$ for i in {1..10}; do
awk -v chr="$i" '$2 == chr' maize_data_sorted.txt > maize_increasing_chr${i}.txt
done
```
We thus have 10 files generated

#### SNPs in Decreasing Position Values 

Next we need to sort them in decreasing order with missing data encoded by a hyphen\
A similar procedure as above is repeated.
```
$ sed 's/unknown/-/g' maize_joined.txt | sort -k 3 -r -n > maize_data_rev_sorted.txt 

$ for i in {1..10}; do
awk -v chr="$i" '$2 == chr' maize_data_rev_sorted.txt > maize_decreasing_chr${i}.txt
done
```
Thus we have 10 files generated\

#### SNPs with unknown positions
```
$ grep -w "unknown" maize_joined.txt > maize_unknown.txt
```
#### SNPs with multiple positions
```
grep -w "multiple" maize_joined.txt > maize_multiple.txt
```

### Teosinte Data
The procedure follows the same workflow as in the case of maize.

```
$ awk '$3 ~ /Group|ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt > teosinte.txt
$ awk -f transpose.awk teosinte.txt > teosinte_transposed.txt
$ head -n 1 teosinte_transposed.txt > headerteo.txt

```
The first three lines of the file contains non-numerical values, and thus the numerical value-based sorting is carried out
```
$ tail -n +4 teosinte_transposed.txt | sort -k1,1 > sorted_teosinte.txt
$ cat headerteo.txt sorted_teosinte.txt > teosinte_with_header.txt
$ sed 's/Sample_ID/SNP_ID/' teosinte_with_header.txt > teosinte_final.txt
$ join -1 1 -2 1 -t $'\t' snp_data.txt teosinte_final.txt > teosinte_joined.txt
```
#### SNPs in increasing position values
```
sed 's/unknown/?/g' teosinte_joined.txt | sort -k3,3n >teosinte_data_sorted.txt
$ for i in {1..10}; do
awk -v chr="$i" '$2 == chr' teosinte_data_sorted.txt > teosinte_inreasing_chr${i}.txt
done
```
#### SNPs in decreasing position values
```
sed 's/unknown/-/g' teosinte_joined.txt | sort -k 3 -r -n  >teosinte_data_rev_sorted.txt
$ for i in {1..10}; do
awk -v chr="$i" '$2 == chr' teosinte_data_rev_sorted.txt > teosinte_decreasing_chr${i}.txt
done
```
#### SNPs with unknown positions
```
$ grep -w "unknown" teosinte_joined.txt > teosinte_unknown.txt
```
#### SNPs with multiple positions
```
grep -w "multiple" teosinte_joined.txt > teosinte_multiple.txt
```

Thus the required files are suitably extracted.
