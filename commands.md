### About commands
* All commands MUST be inside curly braces. You can't just call `print $0`. You have to put that command inside curly braces - `{print $0}`

### Variables
* Awk has some built in variables. They are written in uppercase. 
* They can be useful in a variety of cases. 
* `FILENAME` is the name of the current file being processed. 
* `BEGINFILE {print "we are beginning to process " FILENAME}` will print the string followed by the name of the file when awk begin processing file. 
* `NR` is the Record Number (or Number Record if you'd like). In simple terms it's the record (line) count. If awk is processing line number 5 then NR is 5. 
* Let's say you have a 10 line file and a 5 line file. You pass both files to awk. Awk finishes the first 10 lines and is now at line 3 from the second file. How much will the NR be? 
* You might be tempted to say 3 - but it's 13. NR stands for ALL the records (lines) that awk processes, not records (lines) belonging to file.
* If you want to refer to record (line) count for file you would use FNR. F - from File. File Number Record. 
* In the case above NR would be 13 but FNR would be 3. 

### More Variables
* Here's a list of the most important variables with a short description
* `FILENAME` - name of file
* `FNR` - File Number Record (input record number for current file -  according to official docs). File record (line) count.
* `FS` - Field separator (word separator if you'd like). Space by default. 
* `IGNORECASE` - if set to 1 case is ignored. Very important. `/frodo/` would match `Have you seen my old ring Frodo?` if IGNORECASE is 1. If set to 0 it would not. Use it like this:
```
BEGIN {IGNORECASE=1}
/frodo/ {print "do you remember the taste of strawberries Frodo?"}
```
* `NF` - number of fields (words) in the current record (line). You could use this to count words for example. 
* `NR` - Number Record. Global record (line) count if you will.
* `RS` - Record Separator. A newline by default.
