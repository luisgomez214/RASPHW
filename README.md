Problem 1: Write an s-op that contains the indices in reverse order. For example:

```
running example is: hello
>> reverse_indices = length - indices - 1;
     s-op: reverse_indices
 	 Example: reverse_indices("hello") = [4, 3, 2, 1, 0] (ints)
>> 
.. 
>> reverse_indices("hello");
	 =  [4, 3, 2, 1, 0] (ints) 
```


Problem 2: Write a function that "rotates" the input text by the specified number of characters. For example:

```
>> def rotate(tokens, n) {
..      seq_length = round(1 / aggregate(full_s, indicator(indices == 0)));
..      shifted_indices = (indices - n + seq_length) % seq_length;
..      rotated = aggregate(select(indices, shifted_indices, ==), tokens);
..      return rotated;
..      }
     console function: rotate(tokens, n)
>> 
.. 
>> rotate(tokens, 0)("hello");
	 =  [h, e, l, l, o] (strings)
>> rotate(tokens, 1)("hello");
	 =  [o, h, e, l, l] (strings)
>> rotate(tokens, 2)("hello");
	 =  [l, o, h, e, l] (strings)
```

Problem 3: Write a function that takes a sequence as input and "swaps everyletter with its neighbor". Specifically, for every even index i,positions iand i+1 will be swapped; if the length of the sequence is odd, then the last element should not move. For example:


```
>> def swap(seq){
..        return aggregate(select(indices, indices+1, ==), tokens, "X") if indices%2==0 else aggregate(select(indices, indices-1, ==), tokens, "X");
..        }
     console function: swap(seq)
>>  
..  def _swaplast(seq){
..        return aggregate(select(indices, indices, ==), tokens, "X") if indices==length-1 and length%2==1 else swap(seq);
     }
..        console function: _swaplast(seq)
>> 
.. 
>> _swaplast(tokens)("hello");
	 =  [e, h, l, l, o] (strings)
>> _swaplast(tokens)("ababab");
	 =  [b, a, b, a, b, a] (strings)
```


Problem 4: Write a function that returns the maximum value in the sequence repeated for every position. For example:
```
>> sorting = sort(tokens, tokens);
     s-op: sorting
 	 Example: sorting("hello") = [e, h, l, l, o] (strings)
>> lasttoken = length - 1;
     s-op: lasttoken
 	 Example: lasttoken("hello") = [4]*5 (ints)
>> def maxseq(seq) {
..        return load_from_location(sorting, lasttoken);
..        }
     console function: maxseq(seq)
>> maxseq(tokens)("ababcabab");
	 =  [c]*9 (strings)
```



Problem 6: Write a function that performs sequence reversal "autogeneratively". That is, it will take a sequence as input, and the sequence will contain a special token $ that marks the "end of the prompt". The text before the $ should be unchanged, and the text after the $ should be the text before the $ reversed (this text represents the model's response to the prompt). The code should be robuse to the case when the length of text after $ is not the same as the length of text before $. For example:

>> set example "hello$     "
>> dollar_position = select_from_first(tokens, "$");
     selector: dollar_position
 	 Example:
 			     h e l l o $          
 			 h |           1          
 			 e |           1          
 			 l |           1          
 			 l |           1          
 			 o |           1          
 			 $ |           1          
 			   |           1          
 			   |           1          
 			   |           1          
 			   |           1          
 			   |           1          
>>  dollar_indices = aggregate(dollar_position, indices);
     s-op: dollar_indices
 	 Example: dollar_indices("hello$     ") = [5]*11 (ints)
>> def get_second_part(seq) {
..        reversed_part = aggregate(select(indices, dollar_indices + dollar_indices - indices, ==), seq, " ");
..        return reversed_part;
..   }
     console function: get_second_part(seq)
>> def reverse_sequence(seq) {
..        return seq if indices <= dollar_indices else get_second_part(seq);
..   }
     console function: reverse_sequence(seq)
>> reverse_sequence(tokens)("hello$     ");
	 =  [h, e, l, l, o, $, o, l, l, e, h] (strings)
>> reverse_sequence(tokens)("hello$ ");
	 =  [h, e, l, l, o, $, o] (strings)
>> reverse_sequence(tokens)("hello$X");
	 =  [h, e, l, l, o, $, o] (strings)
>> reverse_sequence(tokens)("hello$XXXXXXXXXX");
	 =  [h, e, l, l, o, $, o, l, l, e, h,  ,  ,  ,  ,  ] (strings)

Problem 6: Write a function that counts the number of times a certain token appears in the input sequence. For example:
```
>>  def howmany(seq, atom){
..        return selector_width(select(seq, atom, ==));
..        }
     console function: howmany(seq, atom)
>> 
.. 
>> howmany(tokens, "a")("hello");
	 =  [0]*5 (ints)
>>  howmany(tokens, "h")("hello");
	 =  [1]*5 (ints)
>> howmany(tokens, "l")("hello");
	 =  [2]*5 (ints)
```


Problem 7: Write a function that counts the number of times a certain token has appeared in the input sequence so far. For example:

```
>> mask_ag = select(indices, indices, <=);
     selector: mask_ag
 	 Example:
 			     h e l l o
 			 h | 1        
 			 e | 1 1      
 			 l | 1 1 1    
 			 l | 1 1 1 1  
 			 o | 1 1 1 1 1
>> 
.. 
>>  def howmany(seq, atom){
..         count = round((indices + 1) * aggregate(mask_ag, indicator(seq == atom)));
..         return aggregate(select(indices, indices, ==), count, "");
..      }
     console function: howmany(seq, atom)
>> 
.. 
>> howmany(tokens, "a")("hello");
	 =  [0]*5 (ints)
>> howmany(tokens, "h")("hello");
	 =  [1]*5 (ints)
>>  howmany(tokens, "e")("hello");
	 =  [0, 1, 1, 1, 1] (ints)
>> howmany(tokens, "l")("hello");
	 =  [0, 0, 1, 2, 2] (ints)
```

Problem 8: Write a function that returns the letter (tokens) in the indices that are odd. 

```
selected_double_indices = select(indices, indices*2, ==);
output_double = aggregate(selected_double_indices, tokens, ".");


>> output_double("hello");
	 =  [h, l, o, ., .] (strings)

>> output_double("oxnxlxyxpxrxixnxtxixnxgxoxdxdx");
	 =  [o, n, l, y, p, r, i, n, t, i, n, g, o, d, d, ., ., ., ., ., ., ., ., ., ., ., ., ., ., .] (strings)
```
