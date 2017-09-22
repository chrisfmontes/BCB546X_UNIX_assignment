## BCB456X UNIX Assignment
Christian Montes-Serey

# Initial Steps:
**1st:** Created this repository, called [BCB546X_UNIX_assignment](https://github.com/dormilon/BCB546X_UNIX_assignment)

**2nd:** Cloned the repository into my UNIX BCB456X folder, using:
```
git clone git@github.com:dormilon/BCB546X_UNIX_assignment.git
```
**3rd:** Browsed to my copy of the BCB546X-Fall2017 folder and pulled the latest version from GitHub using:
```
$ cd ~/BCB546x/BCB546X-Fall2017/
$ git pull origin master
```
**4th:** Copied the UNIX assignment files to my BCB546X_UNIX_assignment folder and pushed them into my git repository using:
```
cp ~/BCB546x/BCB546X-Fall2017/UNIX_Assignment/* ~/BCB546x/BCB546X_UNIX_assignment/.
git add .
git commit
git pull
```
# Data Inspection
**1st:** Inspected the contents of the folder using:
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

**2nd:** Inspected both `fang_et_al_genotypes.txt` and `snp_position.txt` files

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
**Summary:** From the data inspection I have found:
1. The file `fang_et_al_genotypes.txt` is a 11051939 bytes file with 2783 lines of data and 986 columns.
2. The file `snp_position.txt` is a 82763 bytes file with 984 lines of data and 15 columns.
3. Both files are ASCII text and have a header on their first line.
