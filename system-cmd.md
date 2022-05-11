### System commands
* You can execute system commands like so `awk '{system("ls "$1" -la")}' file.txt `.
* Let's break it down. We start by calling the system function on every record (line). We pass an argument built dynamically. "ls " followed by the first field (word) followed by " -l". If the field was "myfile.txt" the command would be "ls myfile.txt -la"
* note the spaces at the end in "ls ". If you don't put the space the command would look like "lsmyfile.txt -la" which won't work obviously. 
* It will output something like:
```
-rw-rw-r-- 1 me me 0 Nov 14 17:40 f1
-rw-rw-r-- 1 me me 59 Nov 14 17:41 f2
-rw-rw-r-- 1 me me 20 Nov 12 15:42 col1
```
* Basically we run `ls` on the first word of every file passed to awk.

