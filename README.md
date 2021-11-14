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


#### Simple pattern

* So you can read this awk program `/bilbo/ {print $0}` as: 
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the whole record (line)

#### Field
* How about `/bilbo/ {print $1}`? `$1` represents the first field (word) from the current record (line). `$2` is second field (word) and so on. 
* You can read `/bilbo/ {print $1}` as:
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the first field (word) from the record (line)


#### Pattern AND Pattern
* Patterns can be more complex. Check this out `/bilbo/&&/frodo/{print "my precious"}`
* You can read this as:
>> On each record (line) that matchs `/bilbo/` AND `/baggins/`
>>> print the string "my precious"

#### Pattern OR Pattern
* Check this other pattern out `/bilbo/||/frodo/{print "Is it you mister Frodo?"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` OR `/baggins/`
>>> print the string "Is it you mister Frodo?"



#### NOT Pattern
* What about `! /frodo/ { print "Pohtatoes" }`? (note the extra spaces put there for clarity. You could also eliminate them to save typing)
* Read it as: 
>> On each record (line) that DOESN'T match `/frodo/`
>>> Print "Pohtatoes"

#### IF Pattern present then check for Pattern, ELSE check for Pattern
* Here's a more complex example `/frodo/ ? /ring/ : /orcs/{ print "Either frodo with the ring, or the orcs" }`
* `a?b:c` is a ternary operator. it reads: if a then do b, else do c.
* Read it as:
>> Read record.
>>> If it matches `/frodo/`
>>>> Does it also match `/ring/`? If yes then print "Either frodo with the ring, or the orcs"
>>> If it doesn't match `/frodo/`
>>>> Does it match `/orcs/`? If yes then print "Either frodo with the ring, or the orcs"
* The actions are executed if: a record (line) contains either "frodo" and the "ring" OR no "frodo" and "ring" but "orcs". 


#### Pattern Range
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


#### BEGIN Pattern
* You'll be interested in this one. `BEGIN {print "And so it begins"}`
* BEGIN is a special pattern, triggered right at the beginning, before any input from files is read.
* NOTE - BEGIN will execute it's command even if no files are passed
* It reads:
>> Before any input is read
>>> Print "And so it beggins"

#### END Pattern
* If something begins, it has to end, right? `END {print "There and back, by Bilbo Baggins"}`
* END is triggered when awk has finished reading input from all files. Thus it needs files passed to awk in ordered to be triggered
* It reads:
>> After all input was read
>>> Print "There and back, by Bilbo Baggins"


#### BEGINFILE, ENDFILE Patterns
* If you pass multiple files to awk - it will treat them as being one contigous file. But what if you want to call some commands when beginning to read input from a file? You use BEGINFILE and ENDFILE respectively.
* `BEGINFILE {print "A new chapter is beginning mister Frodo"}`
* It reads:
>> Before input is read from a file
>>> print "A new chapter is beginning mister Frodo"

#### NO Pattern
* This a simple one. `{print $1}`. 
* When no pattern is provided, just the command in curly braces, it is executed for all records (lines).
* This reads
>> For all records (lines):
>>> print the first word

#### Conditional Pattern
* `NF>4{print}`
* The pattern is `NF>4`. If true then exec commands inside curly braces.
* NF is the Number of Fields(words). If the current record (line) has more than 4 fields (words), print the record (line)



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


#### Options
* You can pass options to awk. Here are some useful ones:
* `-f` read awk source file. `awk -f source.awk file.txt`. You put all awk code in the source.awk file. NOTE - don't put the shebang (`#!/usr/bin/awk -f`)
* `-F` - field separator. Use to define "words". For example if you have a csv you could make the separator a comma. Like this: `awk -F, '{print $2} file.txt ' - will print the second "word" separated by comma.
* `-v` assign a variable. Eg: `awk -v count=0 '/bilbo/{count+=1;print "Found another one. Now count is " count}' f1`. init count to 0. On records matching `/bilbo/` increment count by 1. Print the message.
* `-e` - execute commands. Use multiple awk commands. Eg: `awk -e 'BEGIN {IGNORECASE=1}' -e '/bilbo/{"Found him"}'`


#### String concatenation
* `print "hello" "world"` will output "helloworld". No space between.
* `print "hello","world"` will output "hello world". A space between by using a comma in print.

#### System commands
* You can execute system commands like so `awk '{system("ls "$1" -la")}' file.txt `.
* Let's break it down. We start by calling the system function on every record (line). We pass an argument built dynamically. "ls " followed by the first field (word) followed by " -l". If the field was "myfile.txt" the command would be "ls myfile.txt -la"
* note the spaces at the end in "ls ". If you don't put the space the command would look like "lsmyfile.txt -la" which won't work obviously. 


#### Writing dynamically to files
* If you find a certain pattern you might like to write that to a file (with some extra info maybe).
* `awk '/Bilbo/{print "I found him. First word is " $1 >> "appended.txt"}' file.txt `. It will append the print message (followed by first field(word)) to the file appended.txt
* Use '>' instead of '>' to overwrite the file (instead of appending)


#### System commands with stdin
* You can pipe with print into certain system commands. Here's an example `awk '{print "file.txt" | "xargs -n1 ls -l"}'`
* "file.txt" is piped into the command "xargs -n1 ls -l". Similar to `echo "file.txt" | xargs -n ls -l`
* The advantage is that you can pass "dynamic" arguments. Certain fields (words) for example.

#### Getline example
* `"date" | getline cur_date` - run "date", store into variable cur_date. This a simple use for getline.
* Here's a cooler one `awk '{"du "$0" |cut -f1" |getline cur_size;print "for file " $0 " size is " cur_size}' filenames.txt`
* Let's break it down. "du "$0" |cut -f" will look like "du myfile.txt | cut -f1". This linux command will output the size of the file in kb.
* We pass this value to the variable "cur_size". 
* We use a semicolon to separate commands. Next we print the value of the variable "cur_size"


#### $ - the positional variable
* $ is not a "normal" variable but rather a function triggered by the dolar sign (according to grymoire awk tut)
* `X=; print $X;` means `print $1`. 
* You can do more fancy stuff with this. `'{print $(NF-1)}' - NF is the number of fields (words) in the record (line). `$NF` would be the last field (word) (if there are 5 fields NF is 5. $NF will point to the 5th field (word))

#### Modify the positional variable
* You can modify a certain field (word) and print the record (line) containing that modified field.
* `echo "Meat's back on the menu" | awk '{$5="NO_MENUS_IN_ORC_LAND";print}'` will output `Meat's back on the NO_MENUS_IN_ORC_LAND`
* We assign a value to field number 5. Then we print the record (print with no arguments prints the current record, just like `$print $0`)


#### Selective .csv column print
* You have a .csv in the form:
```
city,area,population
LA,400,100
Miami,500,101,
Buenos Aires,800,102
```
* As you can see you have 3 columns. You would like to print the 1st and 3rd column only.
* This should do the trick `awk -F, '{print $1,$3}' cities.csv`
* We set a custom field (word) separator with `-F,`.
* Note the comma between the positional arguments. IF you remember `{print "a" "b"}` will output "ab". You need comma to separate by space, `{print "a","b"}`. 
* You can get fancy and skip the header row of the csv with `awk -F, '{if (NR>1) print $1,$3}' cities.csv `. If Number Recor is bigger than 1 (not the first) only then print. 

#### Custom field separator with OFS
* `{print "a", "b"}` will output "a b". When a comma is present awk uses the output field separator (OFS) which is a space by default.
* You can change OFS to something else, like `::` for example. `{OFS="::";print "a", "b"}` will output `a::b`


#### Mix with command line text
* Let's say you have a file produced by the `ls` command, like this:
```
drwxr-xr-x  2 root root        69632 Nov 13 19:21 .
drwxr-xr-x 16 root root         4096 Nov  9 07:35 ..
-rwxr-xr-x  1 root root        59888 Dec  5  2020 [
-rwxr-xr-x  1 root root        18456 Feb  7  2021 411toppm
-rwxr-xr-x  1 root root           39 Aug 15  2020 7z
-rwxr-xr-x  1 root root           40 Aug 15  2020 7za
-rwxr-xr-x  1 root root           40 Aug 15  2020 7zr
-rwxr-xr-x  1 root root        35344 Jul  1 00:42 aa-enabled
-rwxr-xr-x  1 root root        35344 Jul  1 00:42 aa-exec
```
* you want to print the name of the executable but only if it's smaller than 50 bytes. Use this `awk '{if ($5<50) print $9}' bin_10 `
* If the fifth field (containing size in bytes) is smaller than 50 print the 9th field (name of executable)

#### Math on text.
* This is one of the cool thigs about awk. You can take some text, perform all kinds of programming magic on it, spit it out nice and modified. Usually you need to write a lot of boilerplate if you're using a general scripting language like python. 
* Let's take the cities csv again:
```
city,area,population
LA,400,100
Miami,500,101,
Buenos Aires,800,102
```
* Check out this script.
```
#!/usr/bin/awk -f
BEGIN {
	total=0
	FS=","
}
{
	if (FNR>1) {
		real_pop=$3 * 1000
		total+=real_pop
		print "Real population of", $1, "is" ,real_pop
	}
}

END {
	
	print "Total Population:", total
}
```

* The output will be:
```
Real population of LA is 100000
Real population of Miami is 101000
Real population of Buenos Aires is 102000
Total Population: 303000
```
* By now you should understand what this does. We start by creating a variable and setting it to 0. We set the Field separator to a comma because we have a csv.
* If the File Number Record is bigger than 1 do stuff (note how we use FNR, not NR. That's because we want to skip the header of every file, not just the first file).
* We do some math, addition/multiplication. Finally we print the total. This works with multiple csv files. 
* With a bit of care we could cram it on 1/2 lines in the terminal. 


#### Fancy line numbers
* Look at this beaut: `awk '{print "(" NR ")" ,$0}' f1`
* It will print the line number in parenthesis followed by the actual line (from a file that doesn't have line numbering). Something like :
```
(1) line one
(2) line two
```
* Carefully study the spaces and commas from print. We know that by not using a comman strings will be concatenated without any separator in between. So `"(" NR ")"` will output something like `(9)`. We could even dispense with the spaces in the command (`"("NR")"`) but I've kept them because they make things clearer. Next we print the actual line. Note the comma. It means awk will put a space (OFS) between the parenthesised line number and the actual line.


#### Print words by their number 
* It's time to get fancy and change Field Separator and Record separator.
* By default RS is a newline. What happens if you change it into an empty string? 
* Vim  will load all the file in memory and treat it as a single record. 
* FS (Field Separator)remains the same. But since the whole file it's treated as a single record the positional variable will refere to word number in FILE (not in record/line).
* Sounds complicated? It's not. You have a file like this:
```
first second  third
fourth fifth sixth
seventh eight
```
* This is the awk script.
```
#!/usr/bin/awk -f
BEGIN { RS="" }
{
		print $1, $8
}
```
* It will output `first eight`. 
* Look at the script again. If RS would be a newline `print $1, $8` would print something like:
```
first 
fourth
seventh
```
* It prints the first field on each record but since records are lines it can't find the eight field (word). 
* But when the whole file is treated as a single record the positional variable ($) refers to field number in whole file. 
* You can put this on a one liner and use it daily: `awk 'BEGIN{RS=""}{print $1020}' file.txt `


#### Pass stdin to awk (and show nicely formatted size of files)
* `ls -lh | awk '{print $9,"has size of",$5}'`
* Generate text with `ls -lh` that looks somethin like:
```
-rwxr-xr-x  1 root root           39 Aug 15  2020 7z
-rwxr-xr-x  1 root root           40 Aug 15  2020 7za
-rwxr-xr-x  1 root root           40 Aug 15  2020 7zr
```
* pipe it to awk. Print field $9 (filename) and field $5 (size)



#### Pass both stdin and file to awk
* `echo "Coming from stdin" | awk "{print}" file.txt -`
* In the above command awk first processes file.txt then stdin (passed by using the final dash)


#### Check if text coming from stdin or file
* `{ if (FILENAME!="-") print $0 }` it's the script.
* When you pass it both stdin and files  `echo "Coming from stdin" | awk "{print}" file.txt -` awk won't process text from stdin.


#### Arrays intro
* `{myarr=["one","two","three"]}` - WON'T work, even though this is the syntax used by many languages to declare arays
* Use instead something like `{myarr[0]="one";myarr[1]="two"}` 
* Arrays are associative. You associate an index with a value. Like a dictionary.
```
#!/usr/bin/awk -f
{
		myarr["hobits"]="hobitses"
		print(myarr["hobits"])
}
```
* As you can see you can use strings and numbers as an index (as a key). In the end though array subscripts are always strings. 
* That is `{print myarr[5.1]}` will check for an '5.1' string index. 

#### Store lines in array
```
#!/usr/bin/awk -f
/bilbo/ {
		myarr[NR]=$0
}
END{
		for (i in myarr){
					print "subscript is",i
							print myarr[i]
								}
}
```
* For each record (line) that matches `/bilbo/` store the record(line) in myarr, using the current NR (Number Record - or current line count) as a subscript/index

* At the end enumerate over indexes in my arr (NOT over elements) with a 'for..in' loop. 
* It will print something like:
```
subscript is 3
a story by frodo and bilbo
subscript is 13
bilbo again and frodo
```
* Note how this array is not contigous. It is sparse. The indexes between 0-3 and 3-13 don't exist and are not assigned automatically. Kinda make sense since awk uses strings and NOT integers as indexes/subscripts.


#### Delete array elems
* `{delete myarr[1]}` will delete element at index '1' (where '1' is a string). 
* `{delete myarr}` - will delete ALL elements of the array


#### Array index concatenation
```
#!/usr/bin/awk -f
BEGIN{
	arr[1,2]="one"
	arr["abc","bcd"]="two"
	arr["abc",1]="three"
	arr["foo" "bar"]="four"
	arr["bar" "foo"]="five"
	for (i in arr){
		print "at index",i,"value is",arr[i]
	}
}
```
* Will output:
```
at index abc1 value is three
at index abcbcd value is two
at index foobar value is four
at index barfoo value is five
at index 12 value is one
```
* Awk treats integers as strings when used as array indexes. Then it concatenates them if you pass multiple values, either separated by space, comma or not separated at all. There is no space between these concatenated values.

* Another thing to note is that the 'for..in' loop doesn't enumerate elems in the order they were added. 


#### Ordered array indexes
```
#!/usr/bin/awk -f
BEGIN{
	i=0
	arr[""]=0
}
/bilbo/{
	arr[i++]=$0
}
END{
	for (j=0;j<i;j++){
		print "at index",j,"value is",arr[j]
	}
}
```
* Will output:
```
at index 0 value is a story by frodo and bilbo with ring
at index 1 value is bilbo again and frodo and orcs
```
* We start by declaring a variable 'i' and assign a value zero. Then we initialize an empty array (using the empty string to declare a dummy zero value). We could skip the array initialization step and the output would be the same - BUT the code is clearer this way.
* For every record that matches `/bilbo/` store the line. Use current value of 'i' as index and increment it afterwards. 
* At the end use a for loop to enumerate. 'i' now has a value of the array size. We use the new variable 'j' to enumerate and check against 'i' which is array size.

#### The power of printf
`{printf("%d is nice but %.2f is better",1,2)}`
* will output:
`1 is nice but 2.00 is better`
* printf is "formatted" print (thus the extra f). It uses escape sequences and format specifiers that should be familiar to most programmers.
* `%d` is a decimal. `%f` is a float. `%.2f` - float with 2 digits. 


#### Selective file processing
* You can skip processing certain files using a combo of FILENAME and nextfile commadn
```
#!/usr/bin/awk -f
{
	if (FILENAME=="skip.txt" || FILENAME=="skip2.txt"){
		nextfile
	}
	print $0
}
```
* 'nextfile' will start processing the next file immediately.


#### Skip records (lines) based on a certain condition
* ` awk '{if (NF>4) next; print}' file.txt`
* If NF (Number of Fields) is bigger than 4 stop processing current input record and get the "next" one. Else print the record.
* This will in effect print lines that have 4 or less fields (words)

#### Some math funcs
```
{
	print "log",log($1),$2
	print "col",cos($1),$2
	print "sin",sin($1),$2
	print "rand",rand()
}
```
* Pretty self explanatory math funcs. rand() will print a rand float between 0 and 1.

#### Print some random integers (1000 rand ints between 0-100)
```
#!/usr/bin/awk -f
BEGIN {
for (i=0; i<1000; i++) printf("%d\n",rand()*100)
}
```
* We create random ints between 1 and 100 by multiplying the rand float (between 0-1) with 100 and cutting off the digits by using a `%d` (decimal) in printf, which treats any numbers as ints (an cuts the digits)

#### String funcs - index
```
#!/usr/bin/awk -f
{
	i=index($0,"bilbo")
	print $0
	if (i>0) print ">>>> INDEX IS",i
}
```
* the 'index()' func searches for the index  of the second string ("bilbo") in the first string (the current record/line). If none found it returns 0.

#### String funcs - length
* very basic but very much needed functionality - the length of a string.
* `awk '{print "[",$0,"]","has length of",length($0)}' file.txt` outputs something like:
```
[ Hello world world ] has length of 17
[ hello andback again Again ] has length of 25
[ a story by frodo and bilbo with ring ] has length of 36
```

#### String funcs - split
* Split a string into parts based on a separator (regex or string).
```
#!/usr/bin/awk -f
BEGIN {
	mystring="The best | leaf in the Shire, isn't it?"
	n=split(mystring,array," ")
	for (i in array) print array[i]
	print "TOTAL splits:",n
}
```
* will output:
```
The
best
leaf
in
the
Shire,
isn't
it?
TOTAL splits: 8
```
* First parameter is the string. The second parameter is an array in which to store the split parts of the string. We can pass an uninitialized variable directly. The last param is the separator. 
* The function returns the number of splits (in the 'n') variable. It returns the split string components in the array "array" passed as argument. 


#### String funcs - substr
```
#!/usr/bin/awk -f
BEGIN {
	mystring="lotr is cool"
	n=substr(mystring,1,3)
	print n
}

```
* will output:
`lot`
* substring returns a portion of the string. first arg is the string. second is the index from where to start cutting. The indexing begins from 1, NOT 0. So first char is at index 1. The final (optional) arg is the length of the cut. 
* Read it as: get 3 chars from the first char (including the first char). 
* If you don't pass the third arg (length) it will cut until the end of string.
* Use it in a oneliner to print the first X chars of every line ` awk '{print substr($0,1,10)}' file.txt`


#### String funcs - gensub
```
#!/usr/bin/awk -f
BEGIN {
	mystring="Run Halifax. Show us the meaning of haste."
	res=gensub(/[Hh]ali/,"Shadow","g",mystring)
	print res
}
```
* will output:
` Run Shadowfax. Show us the meaning of haste.`
* first arg is the regex to search for. Second is the replacement string. Third is a flag. It can be "g" for global or an integer. If it's "g" it replaces all occurences of regex in mystring. If it's for example 2 it will replace only the second occurence of regex. The last arg is the target string (where to do search & replace)
* gensub will return a new string, leaving the original intact. 
* If you look at it you'll notice that it's similar to sed's substitute: `s/[Hh]ali/Shadow/g`
* If the last (target) string not supplied, use $0 (the current record)



#### String func - gsub
* gsub is similar to gensub but has some diffs. It doesn't take the flag argument and replaces globally by default. It also performs search & replace directly on the target string. It returns the number of substitutions (whereas gensub() returns the modified string)
* This make it very useful for modifying records nicely and rapidely. 
* `awk '{gsub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO". 


#### String func - sub
* Just like gsub() but only replaces the FIRST occurence.
* `awk '{sub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO", just the FIRST occurence on the record


#### String func - match
* Returns 0 if a match is not found, otherwise the starting index for the match (index STARTING AT 1, not 0)
* Look at this:
```
#!/usr/bin/awk -f
{
	i=match($0,/bilbo/)
	if (i>0){
		res=substr($0,i,5)
		print res
	}
}

```
* It returns the index for `/bilbo/` in current record. If bigger than 0 then get a substring (from 'i' to 'i'+5)


#### String func - tolower
* `echo "My Precious!" | awk '{print tolower($0)}'` 
* Will output `my precious!`

#### String func - toupper
* `echo "My Precious!" | awk '{print toupper($0)}'` 
* Will output `MY PRECIOUS!`

#### String func - asort
* Sorting an array of numbers or string might come in hand.
```
#!/usr/bin/awk -f
BEGIN {
	for (i=0;i<5;i++){
		arr[i]=rand()*100
		print arr[i]
	}
	print ">> SORTED"
	asort(arr)
	for (i in arr)print arr[i]
}

```
* will output:
```
92.4046
59.3909
30.6394
57.8941
74.0133
>> SORTED
30.6394
57.8941
59.3909
74.0133
92.4046
```

#### Time func - strftime
* ` awk 'BEGIN {print strftime("%a %b %e %H:%M:%S %Z %Y")}' will output `Sun Nov 14 15:09:56 EET 2021`
* Here's the format specifiers (taken from grymoire.com). (The man has some format specifiers but it's not this complete)
```
%a	The locale's abbreviated weekday name
%A	The locale's full weekday name
%b	The locale's abbreviated month name
%B	The locale's full month name
%c	The locale's "appropriate" date and time representation
%d	The day of the month as a decimal number (01--31)
%H	The hour (24-hour clock) as a decimal number (00--23)
%I	The hour (12-hour clock) as a decimal number (01--12)
%j	The day of the year as a decimal number (001--366)
%m	The month as a decimal number (01--12)
%M	The minute as a decimal number (00--59)
%p	The locale's equivalent of the AM/PM
%S	The second as a decimal number (00--61).
%U	The week number of the year (Sunday is first day of week)
%w	The weekday as a decimal number (0--6). Sunday is day 0
%W	The week number of the year (Monday is first day of week)
%x	The locale's "appropriate" date representation
%X	The locale's "appropriate" time representation
%y	The year without century as a decimal number (00--99)
%Y	The year with century as a decimal number
%Z	The time zone name or abbreviation
%%	A literal %.
```


#### Extract the inverse of a regex match
* When you use match() func and get an actual match you'll modify RSTART and RLENGTH. These are 2 built in awk vars that signify the start and the length of the match.
* You can use these vars to extract the inverse of a regex match. This might come handy in certain advanced scenarios. 
```
#!/usr/bin/awk -f
{
	reg="[Bb]ilbo"
	if (match($0,reg)){
		bef=substr($0,1,RSTART-1)
		aft=substr($0,RSTART+RLENGTH)
		pat=substr($0,RSTART,RLENGTH)
		print bef,"|",pat,"|",aft
	}
	else print $0
}

```
* This will output something like:
```
Hello world world
hello andback again Again
a story by frodo and  | bilbo |  with ring with bilbo
 | Bilbo |  baggins baggins baggins
```
* We start by checking for a match. If a match exists commands inside if will run (since it will return a value greater than 0).
* 'bef' is the substring before the match. We cut it from position beginning (string indexing starts at 1) up to the start of the match (to RSTART).
* 'aft' is the substring after the match. We cut it from the end of the match up to the end. Note how we use "RSTART+RLENGTH" to calculate the first char AFTER the end of the match. This it's a bit confusing with indexing starting from 1. If you're used to 0 indexing you would want to add a 1 ("RSTART+RLENGTH+1") but it's not needed here because indexing starts at 1.
* Note the comments starting with `#`  

#### Put all lines on one line
* Check out this wacky oneliner `awk '{a=a $0}END{print a}' file.txt`
* It will concatenate all lines on a single line (without any separators between lines). 
* Here's how it works. We start by declaring a variable a. Simply using it will make it uninitialized the first time awk encounters the variable name. Uninitialized variables have a value of "". You can use whatever var name you like, I used "a" because it's short and simple.
* On every record (line) add to "a" previous value of "a" + the current record (line). Awk concatenates values simply by putting them one after another. The line above would even work even without any spaces - eg `{a=a$0}` but I kept the space for clarity.
* After all input records are done processing print the final value of "a". "a" now contains all lines concatenated together.
* Feel free to add some separators between lines for clarity, like: `awk '{a=a"|"$0}END{print a}' file.txt`



#### User declared funcs
```

#!/usr/bin/awk -f

# Declare custom func outside
function throw_ring(who){
	if (who=="gollum"){
		return 0
	}
	else if (who=="frodo"){
		return 1
	}
}

# On all records matching /ring/
/ring/{
# Find gollum or frodo
if (match($0,"gollum")){
	was_thrown=throw_ring("gollum")
	#use ternary if/else. If was_thrown is true (or bigger than 0) return "THROWN". Else return "NOT THROWN"
	print "the  ring throw status is ", was_thrown?"THROWN":"NOT THROWN"
}

if (match($0,"frodo")){
	was_thrown=throw_ring("frodo")
	print "the  ring throw status is ", was_thrown?"THROWN":"NOT THROWN"
}

}

```
* This is a rather lange script but simple when you break it into pieces.
* Start by declaring a custom "throw_ring" function.
* on records that match `/ring/` exec the following operations:
* check if that record (which contains `/ring/`) contains "gollum". If yes get "thrown_status" value. 
* Use the ternary operator to return either "THROWN" or "NOT_THROWN". 
* Do the same for "frodo"
* The output will look like: `the  ring throw status is  THROWN`

#### More about conditional patterns
* Conditional patterns are powerful and succint. We can use them to check for certain conditions before executing any commands inside curly braces.
* `NF>4{print}` is equivalent to `{if(NF>4)print}`. As you can see the first one is clearer and shorter. It uses a conditional pattern. If true, exec commands inside curly braces.
* The second runs for every record and inside curly braces use a conditional 'if'. 
* We can use fairly advanced conditional patterns, using parenthesis and &&, || and even functions. 
* `awk 'match($0,/bilbo/){print}` - if we find `/bilbo/` inside current record print. Obviously we could've dispensed with the match and simply used a regular pattern. 
* But the power comes from combination with other tests. `awk '(NF>6)&& match($0,/bilbo/) {print}' file.txt`. If Number of Fields is bigger than 6 AND we have `/bilbo/` inside current record, print.
* We can replace match with the match operator - `~`. Like this: `NF>6 && $0~/bilbo {print}`. If Number of Fields bigger than 6 and we have a match of `/bilbo/` inside current record, print.


#### Advanced conditional patterns
* Here's a mind blowing one: `awk 'NF==3,/bilbo/{print}' f1`. The comma signifies a range (as for pattern range). But we use a conditional pattern as the start of the range and a regex as the end of the range. Pretty powerful stuff. 
* The line above will print before the first record where Number of Fields is 3 and up to (and including) the last record containing `/bilbo/`
* Here's a simpler but highly useful oneliner `awk 'NF!=0{print} file.txt`. If Number of Fields different than 0, print. It will skip printing empty lines.
* This one will make you oogle. `awk 'rand()>0.5{print}' file.txt`. It will print about half lines from 'file.txt', depending on the result of 'rand()'.


#### Print the first Nth lines of every file
* `awk 'FNR<=5{print}' f1 f2 f3`
* The line above will print the first 5 lines of every file passed to awk (without any separation between them). It uses a conditional pattern. If File Number Record smaller or equal to 5, print. 


#### Print until you encounter this pattern. Then move to next file.
* `awk '{if ($0~/bilbo/) nextfile;print}' f1 f2`.
* Print records (lines) from current file until you encounter `/bilbo/` in current record (line). Then move to the next file (skipping the rest of the current file).
* Do the same for the rest of the files.
* It will NOT print the line containing `/bilbo/`. 


#### Print every Nth line of file
* `awk '{getline;print}' file.txt` - print every second line of the file. 
* Why? For every record start with `getline`. This command sets '$0' from the next input record - it basically reads the next line into '$0'. Print then prints the newly updated '$0'
* `awk '{getline;getline;print}' file.txt` - print every third line of the file. We consume two lines then print the third one. And so on. Add more `getline` to increase `Nth` printing.




