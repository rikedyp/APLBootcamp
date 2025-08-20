# Part 6
Error handling and debugging

## An Honest Mistake

Define the tradfn `Anagram` in your active workspace.

```apl
∇ r←Anagram b;Norm
  r←(Norm a)≡(Norm b)
∇
```

Then define the following dfn.

```apl
Norm←{⍵[⍋⍵]~' '}
```

The `Anagram` function has two bugs that prevent it from working correctly. Fix the bugs so that the following expressions work.

```
      'ELEVEN PLUS TWO' Anagram 'TWELVE PLUS ONE'
1
      'ELEVEN PLUS TWO' Anagram 'TEN PLUS THREE'
0
```

## Can We Fix It?
This scripted namespace defines a toy app to read a UTF-8 text file and convert its data to hexadecimal representation.

```APL
:Namespace app

    file←'/tmp/file.txt'

    ∇ Main
      Hex file
    ∇

    ∇ hex←Hex file;bytes;⎕TRAP
      bytes←'UTF-8'∘⎕UCS¨⊃⎕NGET file 1
      hex←↑{,⍉3↑(⎕D,⎕A)[16 16⊤⍵]}¨bytes
    ∇

:EndNamespace
```

The author of the function modifies it to exhibit certain error handling behaviours. Unfortunately, their code has bugs. Investigate the following scenarios and try to solve the issues.

1.  The author has set up error trapping. They are aware of a potential `FILE NAME ERROR`, but have also set up a global trap in case any unexpected errors occur.
	
    ```apl
	:Namespace app
	
	    file←'/tmp/file.txt'
	
	    ∇ Main;⎕TRAP
	      ⎕TRAP←0 'E' 'Report ⋄ →0'
	      Hex file
	    ∇
	
	    ∇ hex←Hex file;bytes
	      ⎕TRAP←22 'C' '→ERROR'
	      bytes←'UTF-8'∘⎕UCS¨⊃⎕NGET file 1
	      hex←↑{,⍉3↑(⎕D,⎕A)[16 16⊤⍵]}¨bytes
	      →0
	      
	     ERROR:
	      Report
	    ∇
	
	    ∇ Report
	      error←↑⎕DM
	      ⎕←'An error occurred. See app.error for more information.'
	    ∇
	
	:EndNamespace
    ```
	
	Unfortunately, the function suspends with an unexpected `VALUE ERROR`.
	
    ```
	VALUE ERROR: Undefined name: ERROR
	      →ERROR
	       ∧
    ```
	
	After modifying the code, the function should print to the session:
	
    ```
	      app.Main
	An error occurred. See app.error for more information.
    ```
	
	The variable `app.error` should be populated:
	
    ```
	      ⎕←app.error
	FILE NAME ERROR                        
	Hex[2] bytes←'UTF-8'∘⎕UCS¨⊃⎕NGET file 1
	                           ∧           
    ```
	

1.  Now that the file name error is handled, they want to test the application using a file. Paste the following into a text editor and save it somewhere. Update `app.file` to point to the correct location.
	
    ```
	sample text
    ```
	
	Running `app.Main` reveals either 1 or 2 more bugs:
	
	- Running the function now results in an `INDEX ERROR`.
	- The global trap did not catch the `INDEX ERROR`.
	
	Fix the remaining bugs. The application should successfully convert the file now:
	
    ```
	      app.Main
	73 61 6D 70 6C 65 20 74 65 78 74 
    ```
	

1.  Finally, the author decides it would be more useful if `app.error` contained more information about the error, and also that the `Report` function should display this directly in the session as well.
	
    ```apl
	:Namespace app
	
	    file←'/tmp/file.txt'
	
	    ∇ Main;⎕TRAP
	      ⎕TRAP←0 'E' 'Report {⍵(⍎⍵)}¨⎕NL¯2 ⋄ →0'
	      Hex file
	    ∇
	
	    ∇ hex←Hex file;bytes
	      ⎕TRAP←22 'C' '→ERROR'
	      bytes←'UTF-8'∘⎕UCS¨⊃⎕NGET file 1
	      hex←↑{,⍉3↑(⎕D,⎕A)[16 16⊤⍵]}¨bytes
	      →0
	      
	     ERROR:
	      Report⊂'file' file
	    ∇
	
	    ∇ Report names_values;error
	      error←⊂↑⎕DM
	      error,←⊂↑names_values
	      ⎕←'An error occurred. Error information in app.error:'
	      ⎕←error
	    ∇
	
	:EndNamespace
    ```

    1. Add the new reporting functionality to your fixed version of the app.
    
    1. Rewrite the app to use `:Trap` instead of `⎕TRAP`

## Indian Summer
[IndiaRainfall.csv](./assets/IndiaRainfall.csv) is a file of [comma separated values](https://simple.wikipedia.org/wiki/Comma-separated_values). It is adapted from [IndiaRainfallSource.csv](./assets/IndiaRainfallSource.csv) to remove incomplete records.

The [India Meteorological Department(IMD)](http://www.imd.gov.in/) has shared this dataset under [Govt. Open Data License - India](https://data.gov.in/government-open-data-license-india). It can be downloaded from the links above or from [the Kaggle data science website](https://www.kaggle.com/rajanand/rainfall-in-india).

The data contains the total measured monthly rain fall in millimeters for `30` regions in India from the years `1915` to `2015` inclusive.

1. Load the data into the workspace

	??? Hint
		Press <kbd>F1</kbd> or use the `]Help` user command to view the documentation for `⎕CSV`.

	!!! Hint "Bonus"
		Try reading **IndiaRainfallSource.csv** and removing the missing records for yourself. When data sets contain a very small amount of missing data, sometimes it is appropriate to estimate those values in a process called [imputation](https://en.wikipedia.org/wiki/Imputation_%28statistics%29). Often, it is best to just remove the records with missing fields.

1. What was the total rainfall in Punjab in 1995?
1. Which month in which region had the highest rainfall in 1995?
1. Use a least squares linear fit to estimate the total rainfall in all 30 regions in 1905

	??? Hint
		No one would expect you to derive an expression for the least squares linear fit. If you have done it, kudos to you. The expression `Mv(⊢⌹1,∘⍪⊣)Nv` from [APLcart](https://aplcart.info/?q=linear%20regression#) will compute coefficients of a least squares linear fit given a vector of X values `Mv` and a vector of Y values `Nv`.

1. Inspect the data in **IndiaRainfallSource.csv** to see how close the true values were to your estimates. What was the standard error?

	??? Hint
		If the error `e` is a vector of the differences between Y values predicted by the linear fit and the actual Y values
		
		$$e_i=Y_i^{\text{predicted}}-Y_i^{\text{actual}}$$
		
		then an estimate for the variance is given by
		
		$$s^2=\sum_{i=1}^n{{e_i^2}\over{n-2}}$$
		
		where the standard deviation (standard error) is $s$.