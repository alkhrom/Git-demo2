# Intro to Unicode for Dummy Programmers
## Why Unicode?
Unicode has come as a solution to the problem of how to ensure consistent display of an increasing variety of symbols (e.g. alphabet letters from different languages not to mention  special characters, emoji, heiroglyphics and much more) when it became clear it does not fit into 8-bit in-memory representation.
> Of course, if the entire Globe had spoken English, and if emoji had never existed, and if ...and if ...,  there would have never been a need for anything else but the good old ASCII. 


## Some Philosophy Behind Unicode
The idea underlying the Unicode concept is to separate characters   as idealistic platonic entities from their in-memory representations by introducing the "magic numbers", or *code points*.
![enter image description here](https://lh3.googleusercontent.com/JQm9xEWsHYnNtK1qFN9TWw9xuqSFt5dZguJ9rW6qqHWUtI9rR_-VwZ5qd47kvqiiSXsvSV9khh3v)


Unicode in itself is nothing else than a vast lookup table to map each and every character in the Unicode character set to its own code point.
![enter image description here](https://lh3.googleusercontent.com/i4KF4qMjV5-Q2Hp-lNxEdfEzD49m9Wx-aQl0Ug8nGx1s1t2sWh4Fl1bVQtb8O8zDGHdeyI31V90R)
By convention, code points are commonly written in the format of U+XXXX~16~. For instance the code point for  the Cyrillic capital "Ж" is U+0416.
As soon as the code point for a character is found in the lookup table, *encoding* comes into action to build an in-memory representation for the code point according to the encoding scheme rules (there may be, and actually are, different encoding schemes, mostly due to historical reasons, e.g. UCS-2, UTF-32, etc.).
The entire Unicode space allows 1 114 112 code points ranging from 0 to 10FFFF~16~, of which only  128 237 are actually defined in v 9.0 of The Standard. The space can be structured by *planes*, of which the Plane 0 (Basic Multilingual Plane, BMP) accommodates characters for almost all modern languages, and a large number of symbols.
## How To Encode a Character in UTF-8 
UTF-8 is an undeniable winner of the race among Unicode encoding schemes when it comes to memory saving and  reverse compatibility with ASCII simply due to the encoding rule#1, which rules:
 - Use one byte for points in the range from 0 to 127 (decimal) like this:
	0x00000000 to 0x0000007F: 0xxxxxxx
> It is easy to see this is nothing else but the good old one-byte ASCII, so users within Latin alphabet domain would not even notice the difference.

As for the code points above 128 (dec), the UTF-8 rules to actually use as many bytes as needed (so it is a variable-width encoding) according to the scheme below:

0x00000080 — 0x000007FF: 110xxxxx 10xxxxxx
0x00000800 — 0x0000FFFF: 1110xxxx 10xxxxxx 10xxxxxx
Etc., up to 5 bytes (there is nothing to encode with 6 or more bytes).
where:
-  "110" is a a multi-byte mark
-  "10" is a "code continued" mark

Verbally, in more detail, e.g. for a code point above 128 (decimal) yet below 2048 (decimal), do:

1. Convert it into binary, which will take 11 bit. 
2. Split those bits into 5 on the left and 6 on the right. 
3. Start the first byte with a multi-byte mark of "**110**".  
4. Concatenate the left 5 bits to form the first byte. 
5. Start the second byte with a "code continued" mark of "**10**". 
6. Concatenate the right 6 bits to obtain the send byte. 
7. Concatenate the two bytes.

### Example
The code point for capital Cyrillic "Ж" is "1046" (decimal) or "0416" (hex) or "10000 010110" (binary), which brings us directly to UTF-8 encoding of "**110**10000 **10**010110" or D096 (hex).

### Code Implementation
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
  `Next`
  `End  Function`

`Function ToLong(intVal)  `
	`If intVal < 0  Then  `
		`ToLong = CLng(intVal) + &H1000  `
`Else  `
`ToLong = CLng(intVal) `
`End  If   `
`End  Function`

## How To Do The Same In UTF-16 
With UTF-16, simply write the code point of a character into two bytes "as is".
> UTF-16 is a fixed-width encoding.

For instance, the UTF-16 representation for the capital Cyrillic "Ж" is 00010110 00000100 in binary.


## UTF-8 versus UTF-16 Matrix



# Going Deeper Down The Unicode


## "High- and Low-endian" Versions of UTF-16


## Encoding Outside BMP

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMTEzMzk2MzAsLTQxODc0MDk0MiwtND
AxMzE1OTU3LDEyODcwMTMwMTgsMjg0NzEzMTYzLDIwOTExMDc3
NjAsNDcwMDg2NjUxLDIwNzg3ODgxLDI4MzQxODk1NCw5ODg5MD
k4OTgsLTE3Nzg3MDUwODAsMjAxOTAwMDg3OCwxMDgyMjQ4OTc3
LDczNzU1MDQ1OSwyMDM4NjE1NzY0LDgxODM5OTMxOCwtNzc4Nj
M5OTksOTI5OTY0MzM0LC0yMDE2Njg4ODg5LDE0MjIxMDc0OV19

-->
