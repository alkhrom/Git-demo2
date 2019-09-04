# Intro to Unicode for Dummy Programmers

## Why Unicode?

Unicode has come as a solution to the problem of how to ensure consistent display of the vast variety of symbols, e.g. alphabet letters from different languages not to mention  special characters, 
> Of course, if the entire Globe spoken English, and if emoji had never existed, and if ...and if ...,  there would have never been a need for anything else but the good old ASCII. 


## Some Philosophy Behind Unicode

![enter image description here](https://lh3.googleusercontent.com/driTrANKja0TwszTgvDmmmLzRCryfCI5TGAj11gkI_PfxbNG3JVqszY282WWRLYdndv4AZL68Q9Q)


### Code Points And Code Units

### Scripts and Planes

## How To Encode a Character in UTF-8 
UTF-8 is a variable-width in-memory representation of Unicode code points. Use one byte for points in the range from 0 to 127 (decimal), two - between 128 and 2047, three - between 2048 and 65 535, etc., according to the scheme below:
0x00000000 — 0x0000007F: 0xxxxxxx
0x00000080 — 0x000007FF: 110xxxxx 10xxxxxx
0x00000800 — 0x0000FFFF: 1110xxxx 10xxxxxx 10xxxxxx
0x00010000 — 0x001FFFFF: 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
where:
-  "110" is a a multi-byte mark.
-  "10" is a "code continued" mark



- If  a code point is below 128 (decimal), write to a UTF-8 byte as a binary after the starting "0".
- If a code point is above 128 (decimal) yet below 2048 (decimal), do:
	1. Convert it into binary, which will take 11 bit. 
	2. Split those bits into 5 on the left and 6 on the right. 
	3. Start the first byte with a multi-byte mark of "**110**".  
	4. Concatenate the left 5 bits to form the first byte. 
	5. Start the second byte with a "code continued" mark of "**10**". 
	6. Concatenate the right 6 bits to obtain the send byte. 
	7. Concatenate the two bytes.


The code point for capital Cyrillic "Ж" is "1046" (decimal) or "0416" (hex) or "10000 010110" (binary), which brings us directly to UTF-8 encoding of "**110**10000 **10**010110" or D096 (hex).

### Implementation
The code snippet below shows an example of what UTF-8 encoding implementation might look like.
`Function EncodeUTF8(s)`
    
    Dim i, c, utfc, b1, b2, b3 
    
    For i=1  to  Len(s) 
	    c = ToLong(AscW(Mid(s,i,1))) 
    If c < 128  Then 
		    utfc = chr( c) 
    ElseIf c < 2048  Then 
		    b1 = c Mod &h40 
		    b2 = (c - b1) / &h40 
		    utfc = chr(&hC0 + b2) & chr(&h80 + b1) 
    ElseIf c < 65536  And (c < 55296  Or c > 57343) Then 
		    b1 = c Mod &h40 
		    b2 = ((c - b1) / &h40) Mod &h40 
		    b3 = (c - b1 - (&h40 * b2)) / &h1000 
		    utfc = chr(&hE0 + b3) & chr(&h80 + b2) & chr(&h80 + b1) 
    Else  
		    ' Младший или старший суррогат UTF-16 
		    utfc = Chr(&hEF) & Chr(&hBF) & Chr(&hBD) 
    End  If 
    EncodeUTF8 = EncodeUTF8 + utfc 
  `Next``End  Function`

Function ToLong(intVal) 
    If intVal < 0  Then 
	    ToLong = CLng(intVal) + &H10000 
    Else 
	    ToLong = CLng(intVal) 
    End  If  
End  Function`

## How To Do The Same In UTF-16 

Символы Unicode до FFFF~16~  включительно (исключая диапазон для суррогатов) записываются как есть 16-битным словом.

Символы же в диапазоне 1000016..10FFFF16  (больше 16 бит) кодируются по следующей схеме:

-   Из кода символа вычитается 1000016. В результате получится значение от нуля до FFFFF16, которое помещается в разрядную сетку 20 бит.

-   Старшие 10 бит (число в диапазоне 000016..03FF16) суммируются с D80016, и результат идёт в ведущее (первое) слово, которое входит в диапазон  D80016..DBFF16.

-   Младшие 10 бит (тоже число в диапазоне 000016..03FF16) суммируются с DC0016, и результат идёт в последующее (второе) слово, которое входит в диапазон  DC0016..DFFF16.

## UTF-8 versus UTF-16 Matrix

<!--stackedit_data:
eyJoaXN0b3J5IjpbNjMxODMyNTUwLC0xNzc4NzA1MDgwLDIwMT
kwMDA4NzgsMTA4MjI0ODk3Nyw3Mzc1NTA0NTksMjAzODYxNTc2
NCw4MTgzOTkzMTgsLTc3ODYzOTk5LDkyOTk2NDMzNCwtMjAxNj
Y4ODg4OSwxNDIyMTA3NDksOTg4ODI1OTY0LC0xNTg4Nzk2NjZd
fQ==
-->