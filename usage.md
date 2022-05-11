### Programming usage
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
>> Check if we have the string "samwise" inside the record (line). Index is a built in function. It takes two strings. If the second string is contained within the first it will return a value bigger than 0. If the second string is not present in the first return 0.
>> If index() returns a value bigger than 0 ("samwise" was found inside the current record (line)) do the following:
>>> increase hobitses by 1
>>> print a message
* After processing all the records print a message with value of our custom variable "hobitses".