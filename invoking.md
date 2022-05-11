### How to call awk
* You call it like `awk '{print $0}' file1 file2`
* In the examples below I sometimes show the text inside '', not the full blown bash command.
* If there are multiple files you have to use an awk script. Make a new file and put inside it:
```
#!/usr/bin/awk -f
BEGIN {print "BEGINNING"}
/Gollum/ {print "I like it raw and riggling"}
```
* Name it something like `myscript.awk` and make it executable with `chmod +x myscript.awk`
* Call it with `./myscript.awk file1 file2`