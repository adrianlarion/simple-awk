### Getline example
* `"date" | getline cur_date` - run "date", store into variable cur_date. This a simple use for getline.
* Here's a cooler one `awk '{"du "$0" |cut -f1" |getline cur_size;print "for file " $0 " size is " cur_size}' filenames.txt`
* Let's break it down. "du "$0" |cut -f" will look like "du myfile.txt | cut -f1". This linux command will output the size of the file in kb.
* We pass this value to the variable "cur_size". 
* We use a semicolon to separate commands. Next we print the value of the variable "cur_size"
