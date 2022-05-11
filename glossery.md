### Note about lines/records fields/words
* Usually you'll want to keep working with records which are "lines". 


* That is - strings delimited by newlines. 
* I'll also be referring to records as lines to make things simpler. 

* You can change how awk interprets records.
*  Then records will be other things which are not "lines" 
*  (like strings separated by commas for example).  


* The same goes for fields. Fields are strings separated by space by default.
*  You can change this of course to have the separator be a comma or something else.


* Because I just told you you'll know that a record is a line by default. 
* But you could change it to something else (by changing the RS - the Record Separator). 
* You also know that a field is a word (separated by space) by default - but you could change it by changing the FS - Field Separator. 
