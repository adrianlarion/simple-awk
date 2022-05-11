### $ - the positional variable
* $ is not a "normal" variable but rather a function triggered by the dolar sign (according to grymoire awk tut)
* `X=; print $X;` means `print $1`. 
* You can do more fancy stuff with this. `'{print $(NF-1)}' - NF is the number of fields (words) in the record (line). `$NF` would be the last field (word) (if there are 5 fields NF is 5. $NF will point to the 5th field (word))

### Modify the positional variable
* You can modify a certain field (word) and print the record (line) containing that modified field.
* `echo "Meat's back on the menu" | awk '{$5="NO_MENUS_IN_ORC_LAND";print}'` will output `Meat's back on the NO_MENUS_IN_ORC_LAND`
* We assign a value to field number 5. Then we print the record (print with no arguments prints the current record, just like `$print $0`)
