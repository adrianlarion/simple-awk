
### Simple pattern

* So you can read this awk program `/bilbo/ {print $0}` as: 
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the whole record (line)

### Field
* How about `/bilbo/ {print $1}`? `$1` represents the first field (word) from the current record (line). `$2` is second field (word) and so on. 
* You can read `/bilbo/ {print $1}` as:
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the first field (word) from the record (line)


### Pattern AND Pattern
* Patterns can be more complex. Check this out `/bilbo/&&/frodo/{print "my precious"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` AND `/frodo/`
>>> print the string "my precious"

### Pattern OR Pattern
* Check this other pattern out `/bilbo/||/frodo/{print "Is it you mister Frodo?"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` OR `/frodo/`
>>> print the string "Is it you mister Frodo?"



### NOT Pattern
* What about `! /frodo/ { print "Pohtatoes" }`? (note the extra spaces put there for clarity. You could also eliminate them to save typing)
* Read it as: 
>> On each record (line) that DOESN'T match `/frodo/`
>>> Print "Pohtatoes"

### IF Pattern present then check for Pattern, ELSE check for Pattern
* Here's a more complex example `/frodo/ ? /ring/ : /orcs/{ print "Either frodo with the ring, or the orcs" }`
* `a?b:c` is a ternary operator. It reads: if a then do b, else do c.
* Read it as:
>> Read record.
>>> If it matches `/frodo/`
>>>> Does it also match `/ring/`? If yes then print "Either frodo with the ring, or the orcs"
>>> If it doesn't match `/frodo/`
>>>> Does it match `/orcs/`? If yes then print "Either frodo with the ring, or the orcs"
* The actions are executed if: a record (line) contains either ("frodo" and the "ring") OR (no "frodo" and "ring") but "orcs". 


### Pattern Range
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


### BEGIN Pattern
* You'll be interested in this one. `BEGIN {print "And so it begins"}`
* BEGIN is a special pattern, triggered right at the beginning, before any input from files is read.
* NOTE - BEGIN will execute it's command even if no files are passed
* It reads:
>> Before any input is read
>>> Print "And so it beggins"

### END Pattern
* If something begins, it has to end, right? `END {print "There and back, by Bilbo Baggins"}`
* END is triggered when awk has finished reading input from all files. Thus it needs files passed to awk in ordered to be triggered
* It reads:
>> After all input was read
>>> Print "There and back, by Bilbo Baggins"


### BEGINFILE, ENDFILE Patterns
* If you pass multiple files to awk - it will treat them as being one contiguous file. But what if you want to call some commands when beginning to read input from a file? You use BEGINFILE and ENDFILE respectively.
* `BEGINFILE {print "A new chapter is beginning mister Frodo"}`
* It reads:
>> Before input is read from a file
>>> print "A new chapter is beginning mister Frodo"

### NO Pattern
* This a simple one. `{print $1}`. 
* When no pattern is provided, just the command in curly braces, it is executed for all records (lines).
* This reads
>> For all records (lines):
>>> print the first word

### Conditional Pattern
* `NF>4{print}`
* The pattern is `NF>4`. If true then exec commands inside curly braces.
* NF is the Number of Fields(words). If the current record (line) has more than 4 fields (words), print the record (line)

