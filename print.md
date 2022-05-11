
### Print the first Nth lines of every file
* `awk 'FNR<=5{print}' f1 f2 f3`
* The line above will print the first 5 lines of every file passed to awk (without any separation between them). It uses a conditional pattern. If File Number Record smaller or equal to 5, print. 


### Print until you encounter this pattern. Then move to next file.
* `awk '{if ($0~/bilbo/) nextfile;print}' f1 f2`.
* Print records (lines) from current file until you encounter `/bilbo/` in current record (line). Then move to the next file (skipping the rest of the current file).
* Do the same for the rest of the files.
* It will NOT print the line containing `/bilbo/`. 


### Print every Nth line of file
* `awk '{getline;print}' file.txt` - print every second line of the file. 
* Why? For every record start with `getline`. This command sets '$0' from the next input record - it basically reads the next line into '$0'. Print then prints the newly updated '$0'
* `awk '{getline;getline;print}' file.txt` - print every third line of the file. We consume two lines then print the third one. And so on. Add more `getline` to increase `Nth` printing.