Problem 1: Write an s-op that contains the indices in reverse order. For example:

'''
RASP 0.1
running example is: hello
>> reverse_indices = length - indices - 1;
     s-op: reverse_indices
 	 Example: reverse_indices("hello") = [4, 3, 2, 1, 0] (ints)
>> 
.. 
>> reverse_indices("hello");
	 =  [4, 3, 2, 1, 0] (ints) 
'''


Problem 2: Write a function that "rotates" the input text by the specified number of characters. For example:

'''
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
'''

Problem 3: Write a function that takes a sequence as input and "swaps everyletter with its neighbor". Specifically, for every even index i,positions iand i+1 will be swapped; if the length of the sequence is odd, then the last element should not move. For example:


'''
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
'''
