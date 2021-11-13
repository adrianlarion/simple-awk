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


#### How to call awk
* You call it like `awk '{print $0}' file1 file2`
* In the examples below I only show the text inside '', not the full blown bash command.
* If there are multiple files you have to use an awk script. Make a new file and put inside it:
```
#!/usr/bin/awk -f
BEGIN {print "BEGINNING"}
/Gollum/ {print "I like it raw and riggling"}
```
* Name it something like `myscript.awk` and make it executable with `chmod +x myscript.awk`
* Call it with `./myscript.awk file1 file2`


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
* You might be tempted to say 3 - but it's 13. NR stands for ALL the records (lines) that awk processes, not records (lines) belonging to file.
* If you want to refer to record (line) count for file you would use FNR. F - from File. File Number Record. 
* In the case above NR would be 13 but FNR would be 3. 

#### More Variables
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


#### Programming intro
* Awk is a full blown programming language. It has all the operators & syntax you would expect from a "regular" programming language such as C or python. 
* This is taken directly from the manual
```
 Operators
       The operators in AWK, in order of decreasing precedence, are:

       (...)       Grouping

       $           Field reference.

       ++ --       Increment and decrement, both prefix and postfix.

       ^           Exponentiation (** may also be used, and **= for the assignment operator).

       + - !       Unary plus, unary minus, and logical negation.

       * / %       Multiplication, division, and modulus.

       + -         Addition and subtraction.

       space       String concatenation.

       |   |&      Piped I/O for getline, print, and printf.

       < > <= >= == !=
                   The regular relational operators.

       ~ !~        Regular expression match, negated match.  NOTE: Do not use a constant regular expression (/foo/) on the left-hand side of a ~ or !~.  Only use one on the right-hand side.  The expression
                   /foo/ ~ exp has the same meaning as (($0 ~ /foo/) ~ exp).  This is usually not what you want.

  &&          Logical AND.

       ||          Logical OR.

       ?:          The  C  conditional  expression.  This has the form expr1 ? expr2 : expr3.  If expr1 is true, the value of the expression is expr2, otherwise it is expr3.  Only one of expr2 and expr3 is
                   evaluated.

       = += -= *= /= %= ^=
                   Assignment.  Both absolute assignment (var = value) and operator-assignment (the other forms) are supported.

   Control Statements
       The control statements are as follows:

              if (condition) statement [ else statement ]
              while (condition) statement
              do statement while (condition)
              for (expr1; expr2; expr3) statement
              for (var in array) statement
              break
              continue
              delete array[index]
              delete array
              exit [ expression ]
              { statements }
              switch (expression) {
              case value|regex : statement
  ...
              [ default: statement ]
              }

```


#### Programming usage
* Here's how you could use the "programming" part of awk:
```
#!/usr/bin/awk -f
BEGIN {
	IGNORECASE=1
	hobitses=0
}
/fellowship/ {
	if (index($0,"samwise") >0 ) {
		hobitses+=1
		print "Hurry up hobitses"
	}
}
END {
	print "Found a total of " hobitses " hobitses"
}
```
* Let's break this down.
* You see that this is an awk script because of the shebang (the line beginning with `#!/...`)
* Before reading any input set the built in var IGNORECASE to 1. "frodo" will match "Frodo", "FRODO", "frodo", etc.
* We also set a custom variable for our own usage and set its initial value to 0 (hobitses)
* Check all records (lines) and on those that match `/fellowship/` execute the following:
>> Check if we have the string "samwise" inside the record (line). index is a built in function. It takes two strings. If the second string is contained whithin the first it will return a value bigger than 0. If the second string is not present in the first return 0.
>> If index() returns a value bigger than 0 ("samwise" was found inside the curent record (line)) do the following:
>>> increase hobitses by 1
>>> print a message
* After processing all the records print a message with value of our custom variable "hobitses".

