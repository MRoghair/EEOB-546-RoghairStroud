#UNIX Assignment

Marissa Roghair Stroud

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
wc fang_et_al_genotypes
du -h fang_et_al_genotypes
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt 

grep ZMMIL fang_et_al_genotypes.txt | wc -l
grep ZMMLR fang_et_al_genotypes.txt | wc -l
grep ZMMMR fang_et_al_genotypes.txt | wc -l

grep ZMPBA fang_et_al_genotypes.txt | wc -l
grep ZMPIL fang_et_al_genotypes.txt | wc -l
grep ZMPJA fang_et_al_genotypes.txt | wc -l
```

By inspecting this file I learned that:

* This file has 2783 lines, 2744038 words, and 11051939 characters
* The size of this file is 11 MB
* This file has 986 columns! This is definitely not the type of file you could easily inspect with the "head" command.
* There are 3 maize genotypes here: ZMMIL, ZMMLR, and ZMMMR. I used `grep` to count how many times each of these abbreviations showed up, so I'd know in later steps how many lines my files should have. ZMMIL showed up 290 times, ZMMLR showed up 1256 times, and ZMMMR showed up 27 times. This means my `maize_genotypes.txt` file should have 1573 lines of data
* There are 3 teosinite genotypes: ZMPBA, ZMPIL, and ZMPJA. Using `grep`, I found that there were 900 lines containing ZMPBA, 41 lines containing ZMPIL, and 34 lines containing ZMPJA. This means my `teosinite_genotypes.txt` file should contain 975 lines of data.



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

###SNP Position Data (same for Maize and Teosinite)

```
awk `{ print $1 "\t" $3 "\t" $4 }` snp_positions.txt > snp_abbrev_positions.txt
sort -k1,1 snp_abbrev_position.txt > sort_snp_abbrev_position.txt
```

A breif description of what the code does:

* The first `awk` command prints just columns 1, 3, and 4 to a new file. These columns are the ones needed for the assignment: SNP_ID, Chromosome, and Position
* `sort` is used to sort the abbreviated SNP position file by the first column (SNP_ID)



###Maize Data

```
head -n 1 fang_et_al_genotypes.txt > maize_genotypes.txt
grep ZMMIL fang_et_al_genotypes.txt >> maize_genotypes.txt
grep ZMMLR fang_et_al_genotypes.txt >> maize_genotypes.txt
grep ZMMMR fang_et_al_genotypes.txt >> maize_genotypes.txt
wc -l maize_genotypes.txt

awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
sort -k1,1 transposed_maize_genotypes.txt > sort_transp_maize_genotypes.txt
```

Here, I tried to use `sed` to replace `Sample_ID` with `SNP_ID` so the headers of the two files would merge during the join, but my sed command wasn't running, so I manually edited it with TextEdit, then checked that all my characters were still ASCII using the `file` command. After that, I proceeded to join the SNP position file with the maize genotype file.

```
join -1 1 -2 1 sort_snp_abbrev_position.txt sort_transp_maize_genotypes.txt > maize_snp_positions.txt
awk -v OFS='\t' '{ $1=$1; print }' maize_snp_positions.txt > maize_snp_positions_tabs.txt

grep SNP maize_snp_positions_tabs.txt > maize_snp_positions_header.txt
grep -v SNP maize_snp_positions_tabs.txt >> maize_snp_positions_header.txt
```

Here is my brief description of what this code does

* I first extracted the header of `fang_et_al_genotypes.txt` and created the new `maize_genotypes.txt` file. I then extracted lines containing the pattern ZMMIL using grep and sent the output there. I repeated this with ZMMLR and ZMMMR, appending the new information to the end of the existing file. I used `wc -l` at the end to ensure I had the correct 1573 lines of data I anticipated having, plus one for the header.
* I transposed the data using the `transpose.awk` script 
* I sorted the data by the SNP_ID column in preparation for the join with the SNP position file
* I joined the sorted SNP position file to the sorted maize genotype file by the first column (`SNP_ID`) of each.
* I then used an `awk` command I found online to change the delimiter from spaces to tabs 
* I used two `grep` commands to pull out the header, followed by the rest of the file, into a new file. This way, my header was back at the top of the file. I'm sure there's an easier way to do this, but it worked just fine here.

####Sorting and organizing the maize data 

```
grep SNP maize_snp_positions_header.txt > maize_unknown_snp_positions.txt
grep unknown maize_snp_positions_header.txt >> maize_unknown_snp_positions.txt

grep SNP maize_snp_positions_header.txt > maize_multiple_snp_positions.txt
grep multiple maize_snp_positions_header.txt >> maize_multiple_snp_positions.txt
```

* These commands pulled out the data on each the SNPs with either multiple or unknown positions in the maize genome. For both, I began by pulling out the header of the file, followed by all lines that contained either "multiple" or "unknown". These files are not sorted in any specific way

```
grep SNP maize_snp_positions_header.txt > maize_sorted_snps.txt
grep -v SNP maize_snp_positions_header.txt | sort -k3,3n >> maize_sorted_snps.txt

grep SNP maize_sorted_snps.txt > maize_chrom1_sorted_snps.txt
awk '$2 ~ /1/ && $2 < 2' maize_sorted_snps.txt >> maize_chrom1_sorted_snps.txt
```
* This code was used to pull out snps from chromosome 1 (repeated for the other 10 chromosomes too). Chromosome 1 needed the additional `&& $2 < 2` or it would also pull out chromosome 10's SNPs. This was not included for the other 10 chromosomes.
* For all files, I pulled out the header first, then appended on the rest of the data that matched 

```
sed 's/\?/\-/g' maize_snp_positions_header.txt > maize_snps_missing-data.txt
grep SNP maize_snps_missing-data.txt > maize_inv-sorted-missing-data.txt
grep -v SNP maize_snps_missing-data.txt | sort -k3,3nr >> maize_inv-sorted-missing-data.txt

grep SNP maize_inv-sorted-missing-data.txt > maize_chrom1-inv-sorted-snps.txt
awk '$2 ~ /1/ && $2 < 2' maize_inv-sorted-missing-data.txt >> maize_chrom1-inv-sorted-snps.txt
```

* The `sed` command was used to replace `?` with `-` for missing data. 
* The two `grep` commands pulled out the header, then the rest of the file and sorted it in reverse numerical order by column 3. 
* I then pulled out the header from this file using `grep`, followed by data for each chromosome using `awk`. Again, only chromosome 1 needed the additional `&& $2 < 2`, to prevent it from also grabbing chromosome 10's SNPs.




###Teosinte Data

Most of the code (and descriptions) used in this section are identical to the maize data in the previous section. 

```
head -n 1 fang_et_al_genotypes.txt > teosinite_genotypes.txt
grep ZMPBA fang_et_al_genotypes.txt >> teosinite_genotypes.txt
grep ZMPIL fang_et_al_genotypes.txt >> teosinite_genotypes.txt
grep ZMPJA fang_et_al_genotypes.txt >> teosinite_genotypes.txt
wc -l teosinite_genotypes.txt

awk -f transpose.awk teosinite_genotypes.txt > transposed_teosinite_genotypes.txt
sort -k1,1 transposed_teosinite_genotypes.txt > sort_transp_teosinite_genotypes.txt
```

Here, I did the same thing as with the Maize data, manually editing the header to read `SNP_ID` instead of `Sample_ID`. 

```
join -1 1 -2 1 sort_snp_abbrev_position.txt sort_transp_teosinite_genotypes.txt \
	> teosinite_snp_positions.txt
awk -v OFS='\t' '{ $1=$1; print }' teosinite_snp_positions.txt > \
	teosinite_snp_positions_tabs.txt

grep SNP teosinite_snp_positions_tabs.txt > teosinite_snp_positions_header.txt
grep -v SNP teosinite_snp_positions_tabs.txt >> teosinite_snp_positions_header.txt
```

Here is my brief description of what this code does

* I first extracted the header of `fang_et_al_genotypes.txt` and created the new `teosinite_genotypes.txt` file. I then extracted lines containing the pattern ZMPBA using grep and sent the output there. I repeated this with ZMPIL and ZMPJA, appending the new information to the end of the existing file. I used `wc -l` at the end to ensure I had the correct 975 lines of data I anticipated having, plus one for the header.
* I transposed the data using the `transpose.awk` script 
* I sorted the data by the SNP_ID column in preparation for the join with the SNP position file
* I joined the sorted SNP position file to the sorted teosinite genotype file by the first column (`SNP_ID`) of each.
* I then used an `awk` command I found online to change the delimiter from spaces to tabs
* I used the same two `grep` commands as earlier to put my header back at the top of my file. 


####Sorting and organizing the teosinite data

```
grep SNP teosinite_snp_positions_header.txt > teosinite_unknown_snp_positions.txt
grep unknown teosinite_snp_positions_header.txt >> teosinite_unknown_snp_positions.txt

grep SNP teosinite_snp_positions_header.txt > teosinite_multiple_snp_positions.txt
grep multiple teosinite_snp_positions_header.txt >> teosinite_multiple_snp_positions.txt
```

Brief description of the code:

* These commands pulled out the data on each the SNPs with either multiple or unknown positions in the teosinite genome. For both, I began by pulling out the header of the file, followed by all lines that contained either "multiple" or "unknown". These files are not sorted in any specific way


```
grep SNP teosinite_snp_positions_header.txt > teosinite_sorted_snps.txt
grep -v SNP teosinite_snp_positions_header.txt | sort -k3,3n >> teosinite_sorted_snps.txt

grep SNP teosinite_sorted_snps.txt > teosinite_chrom1_sorted_snps.txt
awk '$2 ~ /1/ && $2 < 2' teosinite_sorted_snps.txt >> teosinite_chrom1_sorted_snps.txt
```
* This code was used to pull out snps from chromosome 1 (repeated for the other 10 chromosomes too). Chromosome 1 needed the additional `&& $2 < 2` or it would also pull out chromosome 10's SNPs. This was not included for the other 10 chromosomes.
* For all files, I pulled out the header first, then appended on the rest of the data that matched 

```
sed 's/\?/\-/g' teosinite_snp_positions_header.txt > teosinite_snps_missing-data.txt
grep SNP teosinite_snps_missing-data.txt > teosinite_inv-sorted-missing-data.txt
grep -v SNP teosinite_snps_missing-data.txt | sort -k3,3nr >> teosinite_inv-sorted-missing-data.txt

grep SNP teosinite_inv-sorted-missing-data.txt > teosinite_chrom1-inv-sorted-snps.txt
awk '$2 ~ /1/ && $2 < 2' teosinite_inv-sorted-missing-data.txt >> teosinite_chrom1-inv-sorted-snps.txt
```

* The `sed` command was used to replace `?` with `-` for missing data. 
* The two `grep` commands pulled out the header, then the rest of the file and sorted it in reverse numerical order by column 3. 
* I then pulled out the header from this file using `grep`, followed by data for each chromosome using `awk`. Again, only chromosome 1 needed the additional `&& $2 < 2`, to prevent it from also grabbing chromosome 10's SNPs.
