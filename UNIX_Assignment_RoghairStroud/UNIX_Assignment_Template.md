#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
wc fang_et_al_genotypes
du -h fang_et_al_genotypes
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt 
```

By inspecting this file I learned that:

* This file has 2783 lines, 2744038 words, and 11051939 characters
* The size of this file is 11 MB
* This file has 986 columns! This is definitely not the type of file you could easily inspect with the "head" command.


###Attributes of `snp_position.txt`

```
wc snp_position.txt
du -h snp_position.txt
awk -F "\t" '{print NF; exit}' snp_position.txt 
```

By inspecting this file I learned that:

* This file has 984 lines, 13198 words, and 82763 characters
* The size of this file is 84 KB
* This file has 15 columns


##Data Processing

###Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does
