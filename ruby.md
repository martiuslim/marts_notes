# Ruby

## String
```
poem = "My toast has flown from my hand
    And my toast has gone to the moon.
    But when I saw it on television,
    Planting our flag on Halley's comet,
    More still did I want to eat it."
```
To convert this paragraph of text into a list of lines, use `lines.to_a`. Now poem will become
```
["My toast has flown from my hand\", 
"And my toast has gone to the moon.\", 
"But when I saw it on television,\", 
"Planting our flag on Halley's comet,\", 
"More still did I want to eat it.\"]
```
To reverse use `.reverse` and to join the lines back into a paragraph use `.join`.  
Use `[]` to target and find things or even replace them if necessary e.g. `poem['toast'] = 'honeydew'`.  

## Hash (dictionary)
To create an empty hash `books = {}`  or `ratings = Hash.new(0)`.  
To add a book rating (key-value pair in the hash) use `books["Blockchain Revolution"] = :splendid`.  
Placing a `:` infront of a word makes it a symbol instead of a String -> cheaper in terms of computer memory.  

## Blocks 
Blocks are always attached to methods.  

To count the ratings for all the book reviews use `books.value.each { |rate| ratings[rate] += 1 }`. This pipes each unique value in books as keys in ratings and increments the count for each instance of the rating.

## Methods
Example method
```
def load_comics(path)
  comics = {}
  File.foreach(path) do |line|
    name, url = line.split(': ')
    comics[name] = url.strip
  end
  comics
end
```
- The method passes in the `path` variable as an argument.
- The method gets the `comics` variable just before the end of the method and returns it.
- `File.foreach` opens the file and hands each line of the file to the block.
- The `line` variable in between the `do...end` block took turns with each line in the file.
- The `split` method breaks each line into an array delimited by `:` and is stored in `name` and `url`.
- The `strip` method removes extra spaces around the `url`.

## Files
### Reading
Read file and print contents `print File.read("/comics.txt")`.  
### Copying
Copy file to another location `FileUtils.cp('/comics.txt', '/Home/comics.txt')`.  
### Writing
Open file in **append** mode indicated by the `a` parameter. This will open a code block and the prompt in irb will change to become `..` until the `end` keyword is input.
Example for appending data to a text file is as follows:
```
>  File.open("/Home/comics.txt", "a") do |f|
..     f << "Cat and Girl: http://catandgirl.com/"
.. end
```
### Time
Use `mtime` to see when the file was modified (returns a time object). 

## Directories
Listing out contents in root directory `Dir.entries "/"`.  
Listing out specific filetypes `Dir["/*.txt"]` for text files.  
