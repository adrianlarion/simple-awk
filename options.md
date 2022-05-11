
### Options
* You can pass options to awk. Here are some useful ones:
* `-f` read awk source file. `awk -f source.awk file.txt`. You put all awk code in the source.awk file. NOTE - don't put the shebang (`#!/usr/bin/awk -f`)
* `-F` - field separator. Use to define "words". For example if you have a .csv you could make the separator a comma. Like this: `awk -F, '{print $2} file.txt ' - will print the second "word" separated by comma.
* `-v` assign a variable. Eg: `awk -v count=0 '/bilbo/{count+=1;print "Found another one. Now count is " count}' f1`. init count to 0. On records matching `/bilbo/` increment count by 1. Print the message.
* `-e` - execute commands. Use multiple awk commands. Eg: `awk -e 'BEGIN {IGNORECASE=1}' -e '/bilbo/{"Found him"}'`