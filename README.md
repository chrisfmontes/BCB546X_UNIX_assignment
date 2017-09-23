# BCB456X UNIX Assignment
Christian Montes-Serey

## Initial Steps:
### Created this repository:
Called [BCB546X_UNIX_assignment](https://github.com/dormilon/BCB546X_UNIX_assignment)

### Cloned the repository into my UNIX BCB456X folder
Using:
```
git clone git@github.com:dormilon/BCB546X_UNIX_assignment.git
```
### Browsed to my copy of the BCB546X-Fall2017 folder and pulled the latest version from GitHub
Using:
```
$ cd ~/BCB546x/BCB546X-Fall2017/
$ git pull origin master
```
### Copied the UNIX assignment files to my BCB546X_UNIX_assignment folder and pushed them into my git repository
Using:
```
cp ~/BCB546x/BCB546X-Fall2017/UNIX_Assignment/* ~/BCB546x/BCB546X_UNIX_assignment/.
git add .
git commit
git pull
```
## Data Inspection
### Inspected the contents of the folder
Using:
```
$ ls -l
```
And I got the following:
```
-rw-rw-rw- 1 pollo pollo 11051939 Sep 18 17:07 fang_et_al_genotypes.txt
-rw-rw-rw- 1 pollo pollo      747 Sep 19 16:02 README.md
-rw-rw-rw- 1 pollo pollo    82763 Sep 18 17:07 snp_position.txt
-rw-rw-rw- 1 pollo pollo      353 Sep 18 17:07 transpose.awk
-rw-rw-rw- 1 pollo pollo     4847 Sep 18 17:07 UNIX_Assignment.md
-rw-rw-rw- 1 pollo pollo    56704 Sep 18 17:07 UNIX_Assignment.pdf
```
From this I conclude that the file `fang_et_al_genotypes.txt` has a size of 11051939 bytes and the file `snp_position.txt` has a size of 82763 bytes and I can also conclude that these two files are the only `.txt` files.

### Inspected both `fang_et_al_genotypes.txt` and `snp_position.txt` files

1. Used `less -S` command on both files to get a quick view of them (-S argument gives a column ordered view):
```
$ less -S fang_et_al_genotypes.txt
$ less -S snp_position.txt
```
From this I concluded that both files have headers and neither of them seems to have metadata entries. I also realize that the file `fang_et_al_genotypes.txt` has an impressively high number of columns.

2. Used `wc -l` and the `awk` code to get number of lines and columns, respectively:
```
$ wc -l *.txt
     2783 fang_et_al_genotypes.txt
     984 snp_position.txt
```
```
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
986
```
```
$ awk -F "\t" '{print NF; exit}' snp_position.txt
15
```
The file `fang_et_al_genotypes.txt` has 2783 lines and 986 columns, whereas the file `snp_position.txt` has 984 lines and 15 columns.

3. Used the `file` command to check for non-ASCII characters:
```
$ file *.txt

fang_et_al_genotypes.txt: ASCII text, with very long lines
snp_position.txt:         ASCII text
```
### Summary:
From the data inspection I have found:
1. The file `fang_et_al_genotypes.txt` is a 11051939 bytes file with 2783 lines of data and 986 columns.
2. The file `snp_position.txt` is a 82763 bytes file with 984 lines of data and 15 columns.
3. Both files are ASCII text and have a header on their first line.

**Notes:** Using `cat`, `head` and `tail` commands on these files resulted in a mess on my screen. This was due to the elevated number of columns on them.

## Data Processing

### Prepared files for 'join':
Used grep to extract maize- and teosinte-specific data, respectively. In both cases a pattern for extracting the header was included. Piped the output to the provided `awk` script in order to transpose the data. Next, I piped the transposed data to `tail` to get rid of the headers and piped that output to `sort`.
```
$ grep -E "ZMMIL|ZMMLR|ZMMMR|Sample" fang_et_al_genotypes.txt | awk -f transpose.awk | tail -n +4 | sort -k1,1 \
> sorted_headless_transposed_maize_genotypes.txt

$ grep -E "ZMPBA|ZMPIL|ZMPJA|Sample" fang_et_al_genotypes.txt | awk -f transpose.awk | tail -n +4 | sort -k1,1 \
> sorted_headless_transposed_teosinte_genotypes.txt
```
Used `sort` on column 1, which is the common column between the "genotypes" files and the "snp_position" file.
```
$ tail -n +2 snp_position.txt | sort -k1,1 > sorted_headless_snp_position.txt
```

### Joining the files and preparing for the extraction of the data:

```
$ join -1 1 -2 1 sorted_headless_snp_position.txt sorted_headless_transposed_maize_genotypes.txt \
> joined_snp_maize_genotypes.txt

$ join -1 1 -2 1 sorted_headless_snp_position.txt sorted_headless_transposed_teosinte_genotypes.txt \
> joined_snp_teosinte_genotypes.txt
```
After joining the files, I extracted the columns corresponding to the "SNP_ID", "Chromosome", "Position", and subsequent columns of genotype data from either maize or teosinte individuals.
First I need to know the number of columns, for this I used the awk code provided in class:
```
 $ awk -F " " '{print NF; exit}' joined_snp_teosinte_genotypes.txt
 $ awk -F " " '{print NF; exit}' joined_snp_maize_genotypes.txt
 ```
 After getting the number of columns, I selected only the ones needed, using `cut -f`:
 ```
 $ cut -d" " -f1,3,4,14-988 joined_snp_teosinte_genotypes.txt > subset_joined_snp_teosinte_genotypes.txt
 $ cut -d" " -f1,3,4,14-1586 joined_snp_maize_genotypes.txt > subset_joined_snp_maize_genotypes.txt
 ```
 
 ### Extracting chromosome data
 For this I used a `for` loop in bash, but passing the variable to `awk`. Then, piped the output to `awk` in order to eliminate non-numerical position data. Finally sorted the result according the requirement (either ascending or descending, accordingly).
 ```
$ for CHR in {1..10}; do awk -v CHR=$CHR '$2=CHR' subset_joined_snp_maize_genotypes.txt | awk \
'($3 !~ /[a-zA-Z]/)' | sort -n -k3,3 > maize_increasing_CHR$CHR.txt; done
$ for CHR in {1..10}; do awk -v CHR=$CHR '$2=CHR' subset_joined_snp_teosinte_genotypes.txt | awk \
'($3 !~ /[a-zA-Z]/)' | sort -n -k3,3 > teosinte_increasing_CHR$CHR.txt; done
```
For the decreasing data I used and extra pipe through `sed`, in order to change the "?" to "-":
```
$ for CHR in {1..10}; do awk -v CHR=$CHR '$2=CHR' subset_joined_snp_maize_genotypes.txt | awk \
'($3 !~ /[a-zA-Z]/)' | sort -n -k3,3 -r | sed 's/?/-/g' > maize_decreasing_CHR$CHR.txt; done

$ for CHR in {1..10}; do awk -v CHR=$CHR '$2=CHR' subset_joined_snp_teosinte_genotypes.txt | awk \
'($3 !~ /[a-zA-Z]/)' | sort -n -k3,3 -r | sed 's/?/-/g' > teosinte_decreasing_CHR$CHR.txt; done
```
 Finally, for the "unknown" and multisite snp I used `awk`:
```
$ awk '$3 ~ /unknown/' subset_joined_snp_maize_genotypes.txt > subset_joined_snp_maize_genotypes_unknown.txt

$ awk '$3 ~ /unknown/' subset_joined_snp_teosinte_genotypes.txt > subset_joined_snp_teosinte_genotypes_unknown.txt

$ awk '$3 ~ /multi|Chr/' subset_joined_snp_maize_genotypes.txt > subset_joined_snp_maize_genotypes_multi.txt

$ awk '$3 ~ /multi|Chr/' subset_joined_snp_teosinte_genotypes > subset_joined_snp_teosinte_genotypes_multi.txt
```
### File estructure:
The 44 resultant files are located in the `results\` folder
The intermediate and test files are located in the `workdir\` folder

