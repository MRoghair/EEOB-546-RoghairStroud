#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
wc fang_et_al_genotypes
du -h fang_et_al_genotypes
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt 
grep ZMMIL fang_et_al_genotypes.txt | wc -l
grep ZMMLR fang_et_al_genotypes.txt | wc -l
grep ZMMMR fang_et_al_genotypes.txt | wc -l
```

By inspecting this file I learned that:

* This file has 2783 lines, 2744038 words, and 11051939 characters
* The size of this file is 11 MB
* This file has 986 columns! This is definitely not the type of file you could easily inspect with the "head" command.
* There are 3 maize genotypes we're looking at: ZMMIL, ZMMLR, and ZMMMR. I did a quick count using `grep` to see how many times each of these abbreviations showed up, so I'd know in later steps how many lines my files should have. ZMMIL showed up 290 times, ZMMLR showed up 1256 times, and ZMMMR showed up 27 times. This means my `maize_genotypes.txt` file should have 1573 lines of data


###Attributes of `snp_position.txt`

```
wc snp_position.txt
du -h snp_position.txt
awk -F "\t" '{print NF; exit}' snp_position.txt 
grep ZMPBA fang_et_al_genotypes.txt | wc -l
grep ZMPIL fang_et_al_genotypes.txt | wc -l
grep ZMPJA fang_et_al_genotypes.txt | wc -l
```

By inspecting this file I learned that:

* This file has 984 lines, 13198 words, and 82763 characters
* The size of this file is 84 KB
* This file has 15 columns
* There are 3 teosinite genotypes: ZMPBA, ZMPIL, and ZMPJA. Using `grep`, I found that there were 900 lines containing ZMPBA, 41 lines containing ZMPIL, and 34 lines containing ZMPJA. This means my `teosinite_genotypes.txt` file should contain 975 lines of data.


##Data Processing

###Maize Data

```
head -n 1 fang_et_al_genotypes.txt > maize_genotypes.txt
grep ZMMIL fang_et_al_genotypes.txt >> maize_genotypes.txt
grep ZMMLR fang_et_al_genotypes.txt >> maize_genotypes.txt
grep ZMMMR fang_et_al_genotypes.txt >> maize_genotypes.txt
wc -l maize_genotypes.txt


```

Here is my brief description of what this code does

* I first extracted the header of `fang_et_al_genotypes.txt` and created the new `maize_genotypes.txt file. I then extracted lines containing the pattern ZMMIL using grep and sent the output there. I repeated this with ZMMLR and ZMMMR, appending the new information to the end of the existing file. I used `wc -l` at the end to ensure I had the correct 1573 lines of data I anticipated having, plus one for the header.

###Teosinte Data

```
head -n 1 fang_et_al_genotypes.txt > teosinite_genotypes.txt
grep ZMPBA fang_et_al_genotypes.txt >> teosinite_genotypes.txt
grep ZMPIL fang_et_al_genotypes.txt >> teosinite_genotypes.txt
grep ZMPJA fang_et_al_genotypes.txt >> teosinite_genotypes.txt
wc -l teosinite_genotypes.txt


```

Here is my brief description of what this code does

* I first extracted the header of `fang_et_al_genotypes.txt` and created the new `teosinite_genotypes.txt file. I then extracted lines containing the pattern ZMPBA using grep and sent the output there. I repeated this with ZMPIL and ZMPJA, appending the new information to the end of the existing file. I used `wc -l` at the end to ensure I had the correct 975 lines of data I anticipated having, plus one for the header.
