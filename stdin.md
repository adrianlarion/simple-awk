
### System commands with stdin
* You can pipe with print into certain system commands. Here's an example `awk '{print "file.txt" | "xargs -n1 ls -l"}'`
* "file.txt" is piped into the command "xargs -n1 ls -l". Similar to `echo "file.txt" | xargs -n ls -l`
* The advantage is that you can pass "dynamic" arguments. Certain fields (words) for example.
* The output looks like:
```
-rw-rw-r-- 1 me me 0 Nov 14 17:40 file.txt
```
