# Intro to Unicode for Dummy Programmers

## Why Unicode?

Of course, had the entire Globe spoken English, there would have never been a need for anything else but the good old ASCII. However, as the Internet party

## Some Philosophy Behind Unicode

![enter image description here](https://lh3.googleusercontent.com/driTrANKja0TwszTgvDmmmLzRCryfCI5TGAj11gkI_PfxbNG3JVqszY282WWRLYdndv4AZL68Q9Q)


### Code Points And Code Units

### Scripts and Planes

## UTF-8 Encoding Algorithm and Possible Implementation
UTF-8 is a variable-width in-memory representation of Unicode code points. Use one byte for points in the range from 0 to 127 (decimal), two - between 128 and 2047, three - between 2048 and 65 


0x00000000 — 0x0000007F: 0xxxxxxx
0x00000080 — 0x000007FF: 110xxxxx 10xxxxxx
0x00000800 — 0x0000FFFF: 1110xxxx 10xxxxxx 10xxxxxx
0x00010000 — 0x001FFFFF: 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx

- If  a code point is below 128 (decimal), write to a UTF-8 byte "as is".
**Example:** 

- If a code point is above 128 (decimal) yet below 2048 (decimal), do:
	1. Convert it into binary, which will take 11 bit. 
	2. Split those bits into 5 on the left and 6 on the right. 
	3. Start the first byte with a multi-byte mark of "**110**".  
	4. Concatenate the left 5 bits to form the first byte. 
	5. Start the second byte with a "code continued" mark of "**10**". 
	6. Concatenate the right 6 bits to obtain the send byte. 
	7. Concatenate the two bytes.

**Example:**
The code point for capital Cyrillic "Ж" is "1046" (decimal) or "0416" (hex) or "10000 010110" (binary), which brings us directly to UTF-8 encoding of "**110**10000 **10**010110" or D096 (hex).

### Implementation
The code snippet below shows an example of what UTF-8 encoding implementation might look like.
    Function EncodeUTF8(s) Dim i, c, utfc, b1, b2, b3 For i=1  to  Len(s) c = ToLong(AscW(Mid(s,i,1))) If c < 128  Then utfc = chr( c) ElseIf c < 2048  Then b1 = c Mod &h40 b2 = (c - b1) / &h40 utfc = chr(&hC0 + b2) & chr(&h80 + b1) ElseIf c < 65536  And (c < 55296  Or c > 57343) Then b1 = c Mod &h40 b2 = ((c - b1) / &h40) Mod &h40 b3 = (c - b1 - (&h40 * b2)) / &h1000 utfc = chr(&hE0 + b3) & chr(&h80 + b2) & chr(&h80 + b1) Else  ' Младший или старший суррогат UTF-16 utfc = Chr(&hEF) & Chr(&hBF) & Chr(&hBD) End  If EncodeUTF8 = EncodeUTF8 + utfc Next  End  Function  Function ToLong(intVal) If intVal < 0  Then ToLong = CLng(intVal) + &H10000 Else ToLong = CLng(intVal) End  If  End  Function

## UTF-16 Encoding Algorithm and Implementation




<!--stackedit_data:
eyJoaXN0b3J5IjpbOTIwNTcwNjkzLDgxODM5OTMxOCwtNzc4Nj
M5OTksOTI5OTY0MzM0LC0yMDE2Njg4ODg5LDE0MjIxMDc0OSw5
ODg4MjU5NjQsLTE1ODg3OTY2Nl19
-->