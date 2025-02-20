# UNIX Assignment

## Data Inspection



The top lines of the given files are initally inspected with the head -2 command
```
$ head -2 fang_et_al_genotypes.txt

$ head -2 snp_position.txt
```

### Attributes of `fang_et_al_genotypes`
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

###Attributes of `snp_position.txt`

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

### Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does
