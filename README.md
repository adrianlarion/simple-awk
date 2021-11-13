# Simple Awk

# Short Tut
#### Prerequisites
* Make sure you have 'gawk' installed. It has more features than the usual default 'mawk'. You can see what implementation af awk you're using by typing `man awk` and looking at the header. If you have 'mawk' just install awk with `sudo apt-get install gawk`

#### Intro
* Awk operates on records and fields. A record is by default a line. A field is a "word" by default. You can change how a record/field is defined by changing the field separator var (FS) and the record separator (RS).
* You give awk some files on which to operate. Awk starts reading records (lines) from these files one by one. It splits each record (line) into fields (words). 
* In your awk program you have code that will look like this  `/bilbo/ {print $0}`. `/bilbo/` is a pattern. `{print $0}` is an action (inside the curly braces). 
* On each record (line) that matches pattern (`/bilbo/`) execute actions inside curly braces (`{print $0}`). 
* The special variable `$0` represents the whole record (line). `print` prints.


#### Note about lines/records fields/words
* Usually you'll want to keep working with records which are "lines". That is - strings delimited by newlines. I'll also be refering to records as lines to make things simpler. 
* You can change how awk interprets records. Then records will be other things which are not "lines" (like strings separated by commas for example).  
* The same goes for fields. Fields are strings separated by space by default. You can change this of course to have the separator be a comma or something else.
* Because I just told you you'll know that a record is a line by default. But you could change it to something else (by changing the RS - the Record Separator). You also know that a field is a word (separated by space) by default - but you could change it by changing the FS - Field Separator. 


#### Example 1 

* So you can read this awk program `/bilbo/ {print $0}` as: 
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the whole record (line)

#### Example 2 
* How about `/bilbo/ {print $1}`? `$1` represents the first field (word) from the current record (line). `$2` is second field (word) and so on. 
* You can read `/bilbo/ {print $1}` as:
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the first field (word) from the record (line)


#### Example 3
* Patterns can be more complex. Check this out `/bilbo/&&/frodo/{print "my precious"}`
* You can read this as:
>> On each record (line) that matchs `/bilbo/` AND `/baggins/`
>>> print the string "my precious"

#### Example 4
* Check this other pattern out `/bilbo/||/frodo/{print "Is it you mister Frodo?"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` OR `/baggins/`
>>> print the string "Is it you mister Frodo?"



#### Example 5
* What about `! /frodo/ { print "Pohtatoes" }`? (note the extra spaces put there for clarity. You could also eliminate them to save typing)
* Read it as: 
>> On each record (line) that DOESN'T match `/frodo/`
>>> Print "Pohtatoes"

#### Example 6
* Here's a more complex example `/frodo/ ? /ring/ : /orcs/{ print "Either frodo with the ring, or the orcs" }`
* `a?b:c` is a ternary operator. it reads: if a then do b, else do c.
* Read it as:
>> Read record.
>>> If it matches `/frodo/`
>>>> Does it also match `/ring/`? If yes then print "Either frodo with the ring, or the orcs"
>>> If it doesn't match `/frodo/`
>>>> Does it match `/orcs/`? If yes then print "Either frodo with the ring, or the orcs"
* The actions are executed if: a record (line) contains either "frodo" and the "ring" OR no "frodo" and "ring" but "orcs". 


#### Example 7
* How about this one? `/Shire/ , /Osgiliath/ { print $0 }`?
* The comma separated regex expressions are a "pattern range". 
* Read this as:
>> Execute command for each record (line) 
>>> Between the record (line) that matches `/Shire/` (including that record (line))
>>> And record (line) that matches `/Osgiliath/` (including that record(line))
* If you have a file that looks like:
```
What is it mister Frodo? 
Do you miss the Shire?
I miss the shire too.
This Osgiliath is to drab for me. 
Too many orcs.
```
* The above command will simply print the records(lines):
```
Do you miss the Shire?
I miss the shire too.
This Osgiliath is to drab for me.
```


#### Example 8
* You'll be interested in this one. `BEGIN {print "And so it begins"}`
* BEGIN is a special pattern, triggered right at the beginning, before any input from files is read.
* NOTE - BEGIN will execute it's command even if no files are passed
* It reads:
>> Before any input is read
>>> Print "And so it beggins"

#### Example 9
* If something begins, it has to end, right? `END {print "There and back, by Bilbo Baggins"}`
* END is triggered when awk has finished reading input from all files. Thus it needs files passed to awk in ordered to be triggered
* It reads:
>> After all input was read
>>> Print "There and back, by Bilbo Baggins"


#### Example 10
* If you pass multiple files to awk - it will treat them as being one contigous file. But what if you want to call some commands when beginning to read input from a file? You use BEGINFILE and ENDFILE respectively.
* `BEGINFILE {print "A new chapter is beginning mister Frodo"}`
* It reads:
>> Before input is read from a file
>>> print "A new chapter is beginning mister Frodo"

#### Example 11
* This a simple one. `{print $1}`. 
* When no pattern is provided, just the command in curly braces, it is executed for all records (lines).
* This reads
>> For all records (lines):
>>> print the first word



#### About commands
* All commands MUST be inside curly braces. You can't just call `print $0`. You have to put that command inside curly braces - `{print $0}`


#### Variables
* Awk has some built in variables. They are written in uppercase. 
* They can be useful in a variety of cases. 
* `FILENAME` is the name of the current file being processed. 
* `BEGINFILE {print "we are beginning to process " FILENAME}` will print the string followed by the name of the file when awk begin processing file. 
* `NR` is the Record Number (or Number Record if you'd like). In simple terms it's the record (line) count. If awk is processing line number 5 then NR is 5. 
* Let's say you have a 10 line file and a 5 line file. You pass both files to awk. Awk finishes the first 10 lines and is now ot line 3 from the second file. How much will the NR be? 
* You might be tempted to say 3 - but it's 13. NR stands for ALL the lines that awk processes, not lines belonging to file.
* If you want to refer to line count for file you would use FNR. F - from File. File Number Record. 
* In the case above NR would be 13 but FNR would be 3. 

#### More Variables
* Here's a list of the most important variables with a short description
* `FILENAME` - name of file
* `FNR` - File Number Record (input record number for current file -  according to official docs)
* `FS` - Field separator (word separator if you'd like). Space by default. 
* `IGNORECASE` - if set to 1 case is ignored. Very important. `/frodo/` would match `Have you seen my old ring Frodo?` if IGNORECASE is 1. If set to 0 it would not. Use it like this:
```
BEGIN {IGNORECASE=1}
/frodo/ {print "do you remember the taste of strawberries Frodo?"}
```

