## Intro Me DADDY!

### Awk operates on records and fields. 
* A record is by default a line. 
* A field is a "word" by default. 
* You can change how a record/field is defined by changing the field separator var (FS) and the record separator (RS).

### Inputs
* You give awk some files on which to operate.
* Awk starts reading records (lines) from these files one by one.
*  It splits each record (line) into fields (words). 

### In your awk program
*  you have code that will look like this  `/bilbo/ {print $0}`. `/bilbo/` is a pattern. `{print $0}` is an action (inside the curly braces). 
* On each record (line) that matches pattern (`/bilbo/`) execute actions inside curly braces (`{print $0}`). 
* The special variable `$0` represents the whole record (line). `print` prints.
