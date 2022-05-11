### Writing dynamically to files
* If you find a certain pattern you might like to write that to a file (with some extra info maybe).
* `awk '/Bilbo/{print "I found him. First word is " $1 >> "appended.txt"}' file.txt `. It will append the print message (followed by first field(word)) to the file appended.txt
* Use '>' instead of '>' to overwrite the file (instead of appending)

