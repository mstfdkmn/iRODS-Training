
# Exercise Solutions 


## Exercise 1:

On your linux run ` md5sum test-restore.txt` to create the checksum for test-restore.txt file.

On iRODS run: `ils -L`, to see the checksum of the test.txt file

Compare the checksums



## Exercise 2:

```
ils 
imkdir f1
icd f1
iput -K test-restore.txt 
ils -L
irm test-restore.txt
icd ..
ipwd
```

## Exercise 3:

`iput -K -r mathbooks books/`


## Exercise 4:

```
icd books/mathbooks
ils
imeta add -d file1 'author' 'Alice'
imeta add -d file2 'author' 'Bob'
imeta add -d file3 'author' 'Lisa'

imeta add -d file1 'pages' '22'
imeta add -d file2 'pages' '33'
imeta add -d file3 'pages' '12'

imeta ls -d file1

imeta qu -d pages '>=' 20
```
 
## Exercise 5:

```
 imeta add -d test.txt 'author' 'Bob'
 imeta ls -d test.txt
 imeta add -d test.txt 'distance' '36' 'miles'
 imeta mod -d test.txt distance 36 miles u:feet
  imeta mod -d test.txt distance 12 meter v:44
  imeta rm -d test.txt author Alice

```
 