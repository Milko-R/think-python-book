# 12: Tuples

A **tuple** is essentially an ***immutable*** list. Tuples contain a sequence of values of any type, indexed by integer.

Tuples are declared as a comma-separated list of values. It is common to enclose tuples in parentheses for clarity. Empty tuples can be declared with the built-in `tuple()` function:

```python
t = tuple()
t = 'a', 'b', ['c', 'd']
t = ('a', 'b', ['c', 'd'])

# a tuple of one element must have a trailing comma
t = 'a',
```

Tuples can be operated upon similarly to lists:

```python
t = ('a', 'b', ['c', 'd'])
t[0]	# 'a'
t[1:3]	# ('b', 'c')
```

## `12.2` Tuple Assignment

A nifty trick to swap two values without using a temporary storage variable is to use **tuple assignment**:

```python
# swap values of a and b
a, b = b, a
```

Tuple assignment can also be used on other types of sequences:

```python
email = 'mrbeans@dogmail.com'
username, domain = email.split('@')
username	# 'mrbeans'
domain		# 'dogmail.com'

full_name = 'Frank Reynolds'
first, last = full_name.split(' ')
first		# 'Frank'

```

## `12.3` Tuples as return values

Tuples can be used to return a bundle of multiple values. As an example, the built-in function `divmod()` takes two integers and returns a *tuple* containing the quotient and the remainder:

```python
v = divmod(13, 4)
v	# (3, 1)

# with tuple assignment
quotient, remainder = divmod(13, 4)
quotient	# 3
remainder	# 1
```

## `12.4` Variable-length argument tuples

A function parameter prepended with **`*`** will **gather** multiple arguments into a tuple:

```python
def example(arg1, *multiple_args):
    print(arg1)
    print(multiple_args)
    
example('cheese', 'crackers', 'chianti', 'prosciutto')
# cheese
# ('crackers', 'chianti', 'prosciutto')
```

It is customary *though not mandatory* to name your gathering parameter `*args`.

The compliment of *gather* is **scatter**, which passes a single tuple to a function as multiple arguments:

```python
t = 8, 2	# tuple of two values
divmod(t)
# TypeError: divmod expected 2 arguments, got 1

divmod(*t)	# passes each element of tuple as its own arg
# (4, 0)
```

```python
def sum_all(*args):
    return sum(args)

ints = 1, 2, 3, 4, 5
sum_all(*ints)	# 15
```

## `12.5`

**`zips`** is a built-in function that takes two sequences and interweaves them into pairs as tuples:

```python
string = 'xyz'
t = [1, 2, 3]
zip(string, t)	# <zip object at 0x7fa36b830a08>

for pair in zip(string, t):
    print(pair)  
# ('x', 1)
# ('y', 2)
# ('z', 3)

```

## `12.6`

Dictionaries have a method called **`items()`** which returns a `dict_items` object, which is an *iterator* containing a list of tuples, where each tuple is a key-value pair from the dictionary:

```python
d = dict(a=0, b=1, c=2)
print(d.items())
# dict_items([('a', 0), ('b', 1), ('c', 2)])

# iterate through key-value pairs:
for key, value in d.items():
    print(key, value)
    
# a 0
# b 1
# c 2

```



Lists, dictionaries, and tuples are examples of **data structures**.

---

## `12.10` Exercises

### `Exercise 12.1`

> Write a function called `most_frequent()` that takes a string and prints the letters in decreasing order of frequency. Find text samples from several different languages and see how letter frequency varies between languages. Compare your results with the tables at [[Wikipedia link]](http://en.wikipedia.org/wiki/Letter_frequencies).

```python
# exercises/12.1.py

def most_frequent(string):
    """
    takes a string
    prints the letters of that string in descending order
    of frequency
    returns nothing
    """
    # format string: remove spaces, make lower
    formatted = string.replace(' ', '').lower()

    # create dict of letters and frequency
    # based on `histogram()` from chapter 11
    hist = dict()
    for char in formatted:
        hist[char] = hist.get(char, 0) + 1

    # reverse keys and values with tuple assignment
    # hist.items() == list of each char and freq as tuples
    freq_list = list()
    for (char, freq) in hist.items():
        freq_list.append((freq, char))

    # sort frequency list
    # `reverse` sorts in descending order
    freq_list.sort(reverse=True)

    # create list of characters by frequency
    char_freq = list()
    for (freq, char) in freq_list:
        char_freq.append(char)

    print(char_freq)

```

### `Exercise 12.2`

> More Anagrams:

1. > Write a program that reads a word list from a file and prints all the sets of words that are anagrams.
   >
   > example output:
   >
   > ```python
   > ['deltas','desalt','lasted','salted','slated','staled']
   > ['retainers','ternaries']
   > ['generating','greatening']
   > ['resmelts','smelters','termless']
   > ```

   ```python
   # exercises/12.2.1.py
   
   fin = open("exercises/words.txt")
   
   
   def word_to_tuple(word):
       """
       takes a word
       returns a tuple of each letter in that word
       sorted alphabetically
       """
       # since strings are sequences of letters
       # `sorted` will automatically convert a string
       # to a list, then sort it
       word = tuple(sorted(word))
       return word
   
   
   def print_anagrams(anagrams):
       """
       takes a dictionary of lists of words keyed
       to tuples of the letters in those words
       prints any list of words with more than one word in it.
       """
       for letter_set in anagrams:
           words = anagrams.get(letter_set)
           if len(words) > 1:
               print(words)
   
   
   def anagrams(word_list):
       """
       takes a list of words
       returns a dictionary containing lists of words
       keyed to tuples of the letters in those words
       """
       output = dict()
   
       for word in word_list:
           word = word.strip()
           letters = word_to_tuple(word)
           # add letters as key to output dict
           # if not present already
           output[letters] = output.get(letters, [])
           # append word to list at key
           output[letters].append(word)
   
       return output
   
   
   print_anagrams(anagrams(fin))
   
   ```

2. > Modify the previous program so that it prints the longest list of anagrams first, followed by the second longest, and so on.

   ```python
   # exercises/12.2.2.py
   
   # ...
   
   def print_anagrams(anagrams):
       
       # ...
       
       sorted_words = list()
   
       for letter_set in anagrams:
           words = anagrams.get(letter_set)
           if len(words) > 1:
               sorted_words.append(words)
   
       # sort by length in descending order
       # see: https://docs.python.org/3.7/library/functions.html#sorted
       for set in sorted(sorted_words, key=len, reverse=True):
           print(set)
           
   # ...
   ```

3. > In Scrabble, a "bingo" is when you play all seven tiles in your rack, along with a letter on the board, to form an eight-letter word. What collection of 8 letters forms the most possible bingos?

   ```python
   # exercises/12.2.3.py
   
   # ...
   
   def print_anagrams(anagrams):
       # ...
       for letter_set in anagrams:
           # ...
           if len(words) > 1 and len(letter_set) == 8:
               # ...
   
   # ...
   
   """
   ANSWER:
   	letters = ('a', 'e', 'g', 'i', 'n', 'r', 's', 't')
   	words = ['angriest', 'astringe', 'ganister', 'gantries', 'granites', 'ingrates', 'rangiest']
   """
   ```

   