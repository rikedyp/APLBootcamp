# Bootcamp Exercises

## To do
- [ ] Put relevant constructs in or near problem descriptions?
    - Or a summary of constructs that can be used to solve these problems


## Problem set 3







1. Back to School
	1. Write a function to produce the multiplication table from `1` to `⍵`. 

		<pre><code class="language-APL">      MulTable 7</code></pre>
		<pre><code class="language-APL">1  2  3  4  5  6  7
		2  4  6  8 10 12 14
		3  6  9 12 15 18 21
		4  8 12 16 20 24 28
		5 10 15 20 25 30 35
		6 12 18 24 30 36 42
		7 14 21 28 35 42 49</code></pre>

	1. Write a function to produce the addition table from `0` to `⍵`.

		<pre><code class="language-APL">      AddTable 6</code></pre>
		<pre><code class="language-APL">0 1 2 3  4  5  6
		1 2 3 4  5  6  7
		2 3 4 5  6  7  8
		3 4 5 6  7  8  9
		4 5 6 7  8  9 10
		5 6 7 8  9 10 11
		6 7 8 9 10 11 12</code></pre>

1. Making the Grade

	<table id="gradeBoundaryTable" style="border-top: none;">
	<tbody>
	<tr>
	<td><strong>Score Range</strong></td>
	<td><code>0-64</code></td>
	<td><code>65-69</code></td>
	<td><code>70-79</code></td>
	<td><code>80-89</code></td>
	<td><code>90-100</code></td>
	</tr>
	<tr>
	<td><strong>Letter Grade</strong></td>
	<td>F</td>
	<td>D</td>
	<td>C</td>
	<td>B</td>
	<td>A</td>
	</tr>
	</tbody>
	</table>

    Write a function that, given an array of integer test scores in the inclusive range 0 to 100, returns a list of letter grades according to the table above.

	<pre><code class="language-APL">      Grade 0 10 75 78 85</code></pre>
	<pre><code class="language-APL">FFCCB</code></pre>

	???Example "Answer"
		Use an outer product to compare between lower bounds and the scores. The column-wise sum then tells us which "bin" each score belongs to:

		```APL
		Grade ← {'FDCBA'[+⌿0 65 70 80 90∘.≤⍵]}
		```

		You can use a different comparison if you choose to use upper bounds:

		```APL
		{'ABCDF'[+⌿64 69 79 89 100∘.≥⍵]}
		```

1. Analysing text

	1. Write a function test if there are any vowels `'aeiou'` in text vector `⍵`

		```APL
		      AnyVowels 'this text is made of characters'
		1
		      AnyVowels 'bgxkz'
		0
		```

	1. Write a function to count the number of vowels in its character vector argument `⍵`

		```APL
		      CountVowels 'this text is made of characters'
		```
		```
		9
		```
		---
		```APL
		      CountVowels 'we have twelve vowels in this sentence'
		```
		```
		12
		```

	1. Write a function to remove the vowels from its argument

		```APL
		      RemoveVowels 'this text is made of characters'
		ths txt s md f chrctrs
		```

	???Example "Answers"
		<ol type="a">
		<li>
		With two *or-reductions*, we ask "are there any `1`s in each row?" Then, "are there any `1`s in any of the rows?"
		
		```APL
		AnyVowels ← {∨/∨/'aeiou'∘.=⍵}
		```

		Or we can ravel the contents of the array into a vector to perform one big or-reduction across all elements:

		```APL
		AnyVowels ← {∨/,'aeiou'∘.=⍵}
		```

		</li>
		<li>

		Similar techniques can be used for counting the ones:

		```APL
		CountVowels ← {+/+/'aeiou'∘.=⍵}
		CountVowels ← {+/,'aeiou'∘.=⍵}
		```

		Because we are comparing a single vector, +⌿ and ∨⌿ both tell us if there is any vowel in that position:

		```APL
		CountVowels ← {+/+⌿'aeiou'∘.=⍵}
		CountVowels ← {+/∨⌿'aeiou'∘.=⍵}
		```

		</li>
		<li>
		To remove vowels, we must consider the columns of our outer product equality. We then keep elements which are not `~⍵` vowels.

		```APL
		RemoveVowels ← {⍵/⍨~∨⌿'aeiou'∘.=⍵}
		```

		Or rows if the arguments to our outer product are swapped:

		```APL
		RemoveVowels ← {⍵/⍨~∨/'aeiou'∘.=⍵}
		```

		Since we are compressing elements out of a vector, we can use either replicate `⍺/⍵` or replicate-first `⍺⌿⍵`. This is because a vector only has a single dimension, or axis, and that axis is both the first and the last.

		```APL
		RemoveVowels ← {⍵/⍨∨⌿'aeiou'∘.=⍵}
		RemoveVowels ← {⍵⌿⍨∨⌿'aeiou'∘.=⍵}
		```

		</li>

1. Matching shapes
	1. 
		Write a function to add a vector `⍵` to each row of a matrix `⍺`:

		```APL
		      (3 2⍴1 100) AddRows 1 9
		2 109
		2 109
		2 109
		      (5 3⍴1 10 100 1000) AddRows 5 10 15
		6   20  115
		1005   11   25
		105 1010   16
		15  110 1015
		6   20  115
		```

	1. Write a function to add a vector to each row of a matrix, regardless of the order in which they are supplied:

		```APL
		      1 9 AddRows 3 2⍴1 100
		2 109
		2 109
		2 109
		      (2 2⍴1 9 11 18) AddRows 9 1
		10 10
		20 19
		```

	???Example "Answers"
		<ol type="a">
		<li>
		Reshape recycles elements. We can use this to duplicate rows until we have the correct shape to allow `+` to map between elements for us:
		```APL
		AddRows ← {⍺+(⍴⍺)⍴⍵}
		```
		</li>
		<li>
		Finding the maximum shape is a more general solution:
		```APL
		AddRows ← {s←(⍴⍺)⌈⍴⍵ ⋄ (s⍴⍺)+s⍴⍵}
		```

		This way of applying functions between arrays of different shapes is very common. As with many things in this course, eventually we will discover more elegant methods. Here is an example of using [the rank operator](./cells-and-axes.md#the-rank-operator):

		```APL
		AddRows ← +⍤1
		```
		</li>
		</ol>

1. These are the heights of some students in 3 classes.
	```APL
	student ← 10 7⍴'Kane   Jonah  JessicaPadma  Katie  CharlieAmil   David  Zara   Filipa '
	class ← 'CBACCCBBAB'
	height ← 167 177 171 176 178 164 177 177 173 160
	```

	Use APL to:

	1. Find the height of the tallest student
	1. Find the name of the tallest student
	1. Find the class to which the tallest student belongs  
	1. Find the average height of students in class `B`
	
	???Example "Answers"
		<ol type="a">
		<li>
		```APL
		      ⌈/height
		178
		```
		</li>
		<li>
		```APL
		      (height=⌈/height)⌿student
		Katie
		```
		</li>
		<li>
		```APL
		      (height=⌈/height)⌿class
		C
		```

		You might have tried to use indexing and gotten an error:

		```APL
		RANK ERROR
				student[⍸height=⌈/height]
						∧
		```	

		There is [additional syntax](./selecting-from-arrays.md#square-bracket-indexing) in order to select from matrices and higher rank arrays.

		</li>
		<li>
		We can use either compress or indexing to select from the `height` vector:
		```APL
		      Mean ← {(+/⍵)÷≢⍵}
		      Mean (class='B')/height
		172.75
		      Mean height[⍸class='B']
		172.75
		```
		</li>
		</ol>

1. Optimus Prime

	A prime number is a positive whole number greater than $1$ which can be divided only by itself and $1$ with no remainder.

	Write a dfn which returns all of the prime numbers between `1` and `⍵`.

	```APL
	      Primes 10
	```
	```
	2 3 5 7
	```
	---
	```
	      Primes 30
	```
	```
	2 3 5 7 11 13 17 19 23 29
	```

	???Example "Answer"
		```APL
		Primes ← {⍸2=+⌿0=∘.|⍨⍳⍵}
		```

		An alternative coding uses the multiplication table:

		```APL
		Primes ← {i~∘.×⍨i←1↓⍳⍵}
		```

		Of course, the outer product `∘.F` indicates that the number of calculations to compute both of these solutions increases with the square of the input size. We say they have a computational complexity "*of order n squared*" or $O(n^2)$ in [big-O notation](https://en.wikipedia.org/wiki/Big_O_notation). This is a very inefficient way to find prime numbers.
		To see discussions around more efficient ways to compute prime numbers in APL, see [the dfns page on prime numbers](https://dfns.dyalog.com/n_pco.htm).

## Problem Set 4
1. Try to work out the shapes of the results of the following expressions by hand, without executing them.
	1. `'APL IS COOL'`
	1. `¯1 0 1 ∘.× 1 2 3 4 5` 
	1. `1 2 3 4∘.+¯1 0 1∘.×1 10`
	1. `+/⍳4`
