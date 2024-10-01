# Variables, Datatypes and Operators.

In here we learn about topics variables, datatypes and operators. Let's look into one by one.

### Variables
Varibles are mostly like a containers where you store your values so that you can access them easily.  
Consider you have a data like your name. Now when you need to show your name on the screen how can you access it. Normally we know data are stored in your memory with some address to it. But it's difficult to remember the address. So instead we have something called variables to access those values. So once you create a variable it links with memory address and store the data you give to that variable and will show it to you when ever we need with the help of that variable name.

Now let's create a variable and store your name in it.
```ruby
name = "Chetan"
```
If you see my above code, I've created a variable named `name`. In ruby you can specify any variable just by giving a name like I did above and then I'm assigning a value to my variable `name` which is "Chetan".  
Okay, now I've added a variable with name. What if i want to store some numbers to it. You can actually store numbers too.
```ruby
age = 24
```
Just like we did with the name, we are creating a variable named `age` and we are storing a number inside of it.  
So, creating a variable is that simple. Now you get a doubt on what are the different things we can store in a variable. That's a good question. To know that we need to know something called datatypes.

### Datatypes
There are different types in data. Default data types ruby has are
- Numbers
- Strings
- Boolean
- Symbols
- Arrays
- Hashes

What the heck are these. Okay, let me go through one by one.

##### Numbers
Any variable that deals with numbers like 1, 2, 3, 4.5, 0.3 etc.
In numbers we have 2 types, Integers and Flaoting values.

###### Integers
These are integer numbers goes from negative values to positive values without decimal values in it.
`... -982, -981, ..., 0, ..., 10922, 10923, ...`
As you can see above, those values are example for integer values.
```ruby
value1 = 22
value2 = -512
```

###### Floating values
These are numbers that contains negative values to positive values and also consists of decimal values too in it.

` ..., -123.3223, ..., 0.0, ..., 1235.32431, ...`
As you can see above, those values are example for floating values.
```ruby
value1 = 123.123
value2 = -123.232
```

##### Strings
When you want to store a text in a variable, then they are called strings. It's just simple as that.

```ruby
value = "This is a string variable"
value2 = 'Another string'
```
As you can see in above code, the text should be inside the double quotes. You can also use single quotes to specify a string.
String has more features inside it.

###### How to get sub-string from a string?
Normally string is set of characters. If there is a string "This is a string", here 'T' is a character, 'h' is a character. You can access the charactes in a string seprately something like below
```ruby
value = "This is a string"
```

| T | h | i | s |   | i | s |   | a |   | s | t | r | i | n | g |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |

Normaly this is how a string in indexed. It starts from 0 and goes on.  
Now consider you need to get the character present in index 10.
```ruby
value[10] # This will output character 's'
```
In the same way you can access the characters in a string. Now what if you want to access the set of characters from a string. Like you need to get the sub-string "string" from the main string "This is a string".  
For that you can do something like this.
```ruby
value[10,6] # This will output "string"
```
Hmm, How this works?  
`value` variable contains main string right. Now you are using square brackets and inside that you specify from which index it should start and how many characters it should take from the index you specified.  
So in above example 10 is your starting index and 6 is no of characters it should take from index 10.

You can also get a sub-string from a string like this.
```ruby
value[10..15] # This will output "string"
```
ooh, but what does it do?
This will also get you the sub-string but in a different way. Now inside the square bracket you need to specify start index number followered by two dots and followed by ending index value. So, as you want to get "string" from he main string. 's' index is 10 and 'g' index in 15 so you specify `value[10..15]` to get the sub-string "string".

hah, what happens if you specify negative index number for sub-string?
```ruby
value[-1] # This will output "g"
```
Now it goes from backword of the string. So here it prints the last character of the string.

(Note: You will learn more about string concatination in future. You need to know someother topics to understand better. It will be mentioned in the section)
More detail topics on string. Look into these.
[String Concatination](./string_concatination.md)
[String interpolation](./string_interpolation.md)

#### Boolean
Boolean datatype contains just 'yes' or 'no' values.
```ruby
value = true
value2 = false
```
It's just straight forward. 

#### Symbols
These are something special in ruby. You can say symbol is more like a string but it dosen't have character seperation and stuff and storing in memory is different.  
To be precise, strings can be changed, so every time a string is used, Ruby has to store it in memory even if an existing string with the same value already exists. Symbols, on the other hand, are stored in memory only once, making them faster in certain situations.  
Consider you need a string that dosen't change without space then you can use symbols. It looks something like this.

```ruby
value = :some_value # When you print value it will give you some_value as output.
```

You will see using symbols more in future and will be able to get it more on how it works.


#### Arrays
