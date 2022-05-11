### Selective .csv column print
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
* You can get fancy and skip the header row of the csv with `awk -F, '{if (NR>1) print $1,$3}' cities.csv `. If Number Record is bigger than 1 (not the first) only then print. 

### Custom field separator with OFS
* `{print "a", "b"}` will output "a b". When a comma is present awk uses the output field separator (OFS) which is a space by default.
* You can change OFS to something else, like `::` for example. `{OFS="::";print "a", "b"}` will output `a::b`


### Mix with command line text
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

### Math on text.
* This is one of the cool things about awk. You can take some text, perform all kinds of programming magic on it, spit it out nice and modified. Usually you need to write a lot of boilerplate if you're using a general scripting language like python. 
* Let's take the cities .csv again:
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


### Fancy line numbers
* Look at this beaut: `awk '{print "(" NR ")" ,$0}' f1`
* It will print the line number in parenthesis followed by the actual line (from a file that doesn't have line numbering). Something like :
```
(1) line one
(2) line two
```
* Carefully study the spaces and commas from print. We know that by not using a command strings will be concatenated without any separator in between. So `"(" NR ")"` will output something like `(9)`. We could even dispense with the spaces in the command (`"("NR")"`) but I've kept them because they make things clearer. Next we print the actual line. Note the comma. It means awk will put a space (OFS) between the parenthesised line number and the actual line.


### Print words by their number 
* It's time to get fancy and change Field Separator and Record separator.
* By default RS is a newline. What happens if you change it into an empty string? 
* Vim  will load all the file in memory and treat it as a single record. 
* FS (Field Separator)remains the same. But since the whole file it's treated as a single record the positional variable will refer to word number in FILE (not in record/line).
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


### Pass stdin to awk (and show nicely formatted size of files)
* `ls -lh | awk '{print $9,"has size of",$5}'`
* Generate text with `ls -lh` that looks something like:
```
-rwxr-xr-x  1 root root           39 Aug 15  2020 7z
-rwxr-xr-x  1 root root           40 Aug 15  2020 7za
-rwxr-xr-x  1 root root           40 Aug 15  2020 7zr
```
* pipe it to awk. Print field $9 (filename) and field $5 (size)



### Pass both stdin and file to awk
* `echo "Coming from stdin" | awk "{print}" file.txt -`
* In the above command awk first processes file.txt then stdin (passed by using the final dash)


### Check if text coming from stdin or file
* `{ if (FILENAME!="-") print $0 }` it's the script.
* When you pass it both stdin and files  `echo "Coming from stdin" | awk "{print}" file.txt -` awk won't process text from stdin.


### Arrays intro
* `{myarr=["one","two","three"]}` - WON'T work, even though this is the syntax used by many languages to declare arrays
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

### Store lines in array
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
* For each record (line) that matches `/bilbo/` store the record(line) in 'myarr', using the current NR (Number Record - or current line count) as a subscript/index

* At the end enumerate over indexes in my arr (NOT over elements) with a 'for..in' loop. 
* It will print something like:
```
subscript is 3
a story by frodo and bilbo
subscript is 13
bilbo again and frodo
```
* Note how this array is not contiguous. It is sparse. The indexes between 0-3 and 3-13 don't exist and are not assigned automatically. Kinda make sense since awk uses strings and NOT integers as indexes/subscripts.


### Delete array elems
* `{delete myarr[1]}` will delete element at index '1' (where '1' is a string). 
* `{delete myarr}` - will delete ALL elements of the array


### Array index concatenation
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


### Ordered array indexes
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

### The power of printf
`{printf("%d is nice but %.2f is better",1,2)}`
* will output:
`1 is nice but 2.00 is better`
* printf is "formatted" print (thus the extra f). It uses escape sequences and format specifiers that should be familiar to most programmers.
* `%d` is a decimal. `%f` is a float. `%.2f` - float with 2 digits. 
* Here are all the format specifiers (taken from awk man):
```
The printf Statement
       The  AWK  versions of the printf statement and sprintf() function (see
       below) accept the following conversion specification formats:

       %a, %A  A floating point number  of  the  form  [-]0xh.hhhhp+-dd  (C99
               hexadecimal floating point format).  For %A, uppercase letters
               are used instead of lowercase ones.

       %c      A single character.  If the argument used for %c  is  numeric,
               it  is treated as a character and printed.  Otherwise, the ar‐
               gument is assumed to be a string, and the only first character
               of that string is printed.

       %d, %i  A decimal number (the integer part).

       %e, %E  A  floating  point number of the form [-]d.dddddde[+-]dd.  The
               %E format uses E instead of e.

       %f, %F  A floating point number of the  form  [-]ddd.dddddd.   If  the
               system  library  supports it, %F is available as well. This is
               like %f, but uses capital letters for special “not  a  number”
               and “infinity” values. If %F is not available, gawk uses %f.

       %g, %G  Use %e or %f conversion, whichever is shorter, with nonsignif‐
               icant zeros suppressed.  The %G format uses %E instead of %e.

       %o      An unsigned octal number (also an integer).

       %u      An unsigned decimal number (again, an integer).

       %s      A character string.

       %x, %X  An unsigned hexadecimal number (an integer).   The  %X  format
               uses ABCDEF instead of abcdef.

       %%      A single % character; no argument is converted.

       Optional,  additional parameters may lie between the % and the control
       letter:

       count$ Use the count'th argument at  this  point  in  the  formatting.
              This is called a positional specifier and is intended primarily
              for use in translated versions of format strings,  not  in  the
              original text of an AWK program.  It is a gawk extension.

       -      The expression should be left-justified within its field.

       space  For  numeric  conversions, prefix positive values with a space,
              and negative values with a minus sign.

       +      The plus sign, used before the width modifier (see below), says
              to  always  supply  a sign for numeric conversions, even if the
              data to be formatted is positive.  The +  overrides  the  space
              modifier.

  #      Use  an  “alternate form” for certain control letters.  For %o,
              supply a leading zero.  For %x, and %X, supply a leading 0x  or
              0X for a nonzero result.  For %e, %E, %f and %F, the result al‐
              ways contains a decimal point.  For %g, and %G, trailing  zeros
              are not removed from the result.

       0      A  leading  0  (zero)  acts  as  a flag, indicating that output
              should be padded with zeroes instead of spaces.   This  applies
              only  to the numeric output formats.  This flag only has an ef‐
              fect when the field  width  is  wider  than  the  value  to  be
              printed.

       '      A  single quote character instructs gawk to insert the locale's
              thousands-separator character into decimal numbers, and to also
              use  the  locale's  decimal point character with floating point
              formats.  This requires correct locale support in the C library
              and in the definition of the current locale.

       width  The  field  should  be padded to this width.  The field is nor‐
              mally padded with spaces.  With the 0 flag, it is  padded  with
              zeroes.

       .prec  A  number  that  specifies  the precision to use when printing.
              For the %e, %E, %f and %F, formats, this specifies  the  number
              of  digits  you want printed to the right of the decimal point.
              For the %g, and %G formats, it specifies the maximum number  of
              significant  digits.   For  the %d, %i, %o, %u, %x, and %X for‐
              mats, it specifies the minimum number of digits to print.   For
              the  %s  format,  it specifies the maximum number of characters
              from the string that should be printed.
```


### Selective file processing
* You can skip processing certain files using a combo of FILENAME and nextfile command
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


### Skip records (lines) based on a certain condition
* ` awk '{if (NF>4) next; print}' file.txt`
* If NF (Number of Fields) is bigger than 4 stop processing current input record and get the "next" one. Else print the record.
* This will in effect print lines that have 4 or less fields (words)

### Some math funcs
```
{
	print "log",log($1),$2
	print "col",cos($1),$2
	print "sin",sin($1),$2
	print "rand",rand()
}
```
* Pretty self explanatory math funcs. rand() will print a rand float between 0 and 1.

### Print some random integers (1000 rand ints between 0-100)
```
#!/usr/bin/awk -f
BEGIN {
for (i=0; i<1000; i++) printf("%d\n",rand()*100)
}
```
* We create random ints between 1 and 100 by multiplying the rand float (between 0-1) with 100 and cutting off the digits by using a `%d` (decimal) in printf, which treats any numbers as ints (an cuts the digits)

### String funcs - index
```
#!/usr/bin/awk -f
{
	i=index($0,"bilbo")
	print $0
	if (i>0) print ">>>> INDEX IS",i
}
```
* the 'index()' func searches for the index  of the second string ("bilbo") in the first string (the current record/line). If none found it returns 0.

### String funcs - length
* very basic but very much needed functionality - the length of a string.
* `awk '{print "[",$0,"]","has length of",length($0)}' file.txt` outputs something like:
```
[ Hello world world ] has length of 17
[ hello andback again Again ] has length of 25
[ a story by frodo and bilbo with ring ] has length of 36
```

### String funcs - split
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


### String funcs - substr
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
* substring returns a portion of the string. First arg is the string. Second is the index from where to start cutting. The indexing begins from 1, NOT 0. So first char is at index 1. The final (optional) arg is the length of the cut. 
* Read it as: get 3 chars from the first char (including the first char). 
* If you don't pass the third arg (length) it will cut until the end of string.
* Use it in a one-liner to print the first X chars of every line ` awk '{print substr($0,1,10)}' file.txt`


### String funcs - gensub
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
* first arg is the regex to search for. Second is the replacement string. Third is a flag. It can be "g" for global or an integer. If it's "g" it replaces all occurrences of regex in mystring. If it's for example 2 it will replace only the second occurrence of regex. The last arg is the target string (where to do search & replace)
* gensub will return a new string, leaving the original intact. 
* If you look at it you'll notice that it's similar to sed's substitute: `s/[Hh]ali/Shadow/g`
* If the last (target) string not supplied, use $0 (the current record)



### String func - gsub
* gsub is similar to gensub but has some diffs. It doesn't take the flag argument and replaces globally by default. It also performs search & replace directly on the target string. It returns the number of substitutions (whereas gensub() returns the modified string)
* This make it very useful for modifying records nicely and rapidly. 
* `awk '{gsub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO". 


### String func - sub
* Just like gsub() but only replaces the FIRST occurrence.
* `awk '{sub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO", just the FIRST occurrence on the record


### String func - match
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


### String func - tolower
* `echo "My Precious!" | awk '{print tolower($0)}'` 
* Will output `my precious!`

### String func - toupper
* `echo "My Precious!" | awk '{print toupper($0)}'` 
* Will output `MY PRECIOUS!`

### String func - asort
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

### Time func - strftime
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


### Extract the inverse of a regex match
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

### Put all lines on one line
* Check out this wacky oneliner `awk '{a=a $0}END{print a}' file.txt`
* It will concatenate all lines on a single line (without any separators between lines). 
* Here's how it works. We start by declaring a variable a. Simply using it will make it uninitialized the first time awk encounters the variable name. Uninitialized variables have a value of "". You can use whatever var name you like, I used "a" because it's short and simple.
* On every record (line) add to "a" previous value of "a" + the current record (line). Awk concatenates values simply by putting them one after another. The line above would even work even without any spaces - eg `{a=a$0}` but I kept the space for clarity.
* After all input records are done processing print the final value of "a". "a" now contains all lines concatenated together.
* Feel free to add some separators between lines for clarity, like: `awk '{a=a"|"$0}END{print a}' file.txt`



### User declared funcs
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

### More about conditional patterns
* Conditional patterns are powerful and succint. We can use them to check for certain conditions before executing any commands inside curly braces.
* `NF>4{print}` is equivalent to `{if(NF>4)print}`. As you can see the first one is clearer and shorter. It uses a conditional pattern. If true, exec commands inside curly braces.
* The second runs for every record and inside curly braces use a conditional 'if'. 
* We can use fairly advanced conditional patterns, using parenthesis and &&, || and even functions. 
* `awk 'match($0,/bilbo/){print}` - if we find `/bilbo/` inside current record print. Obviously we could've dispensed with the match and simply used a regular pattern. 
* But the power comes from combination with other tests. `awk '(NF>6)&& match($0,/bilbo/) {print}' file.txt`. If Number of Fields is bigger than 6 AND we have `/bilbo/` inside current record, print.
* We can replace match with the match operator - `~`. Like this: `NF>6 && $0~/bilbo {print}`. If Number of Fields bigger than 6 and we have a match of `/bilbo/` inside current record, print.


### Advanced conditional patterns
* Here's a mind blowing one: `awk 'NF==3,/bilbo/{print}' f1`. The comma signifies a range (as for pattern range). But we use a conditional pattern as the start of the range and a regex as the end of the range. Pretty powerful stuff. 
* The line above will print before the first record where Number of Fields is 3 and up to (and including) the last record containing `/bilbo/`
* Here's a simpler but highly useful oneliner `awk 'NF!=0{print} file.txt`. If Number of Fields different than 0, print. It will skip printing empty lines.
* This one will make you oogle. `awk 'rand()>0.5{print}' file.txt`. It will print about half lines from 'file.txt', depending on the result of 'rand()'.