# Intro to Unicode for Dummy Programmers
## Why Unicode?
Unicode has come as a solution to the problem of how to ensure consistent display of an increasing variety of symbols (e.g. alphabet letters from different languages not to mention  special characters, emoji, heiroglyphics and much more) when it became clear it does not fit into 8-bit in-memory representation.
> Of course, if the entire Globe had spoken English, and if emoji had never existed, and if ...and if ...,  there would have never been a need for anything else but the good old ASCII. 

Unicode character set numbers over 128 000 symbols by now, far exceeding the ASCII limit of 256. The set covers almost all known alphabets, math and note signs, newly invented emoji, etc., yet the domain keeps on growing. Unicode is exactly what allows modern software display all this correctly in a uniform manner all across the globe.
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

> It is easy to see this is nothing else but the good old one-byte ASCII, so legacy software that knows nothing about Unicode would not even notice the difference.

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
The code snippet below gives an example of what the code of a UTF-8 encoder may be:

	Function EncodeUTF8(s)
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
				utfc = Chr(&hEF) & Chr(&hBF) & Chr(&hBD)
			End  If
			EncodeUTF8 = EncodeUTF8 + utfc
		Next
	End  Function
	Function ToLong(intVal)
		If intVal < 0  Then
			ToLong = CLng(intVal) + &H10000
		Else
			ToLong = CLng(intVal)
		End  If
	End  Function

## How To Do The Same In UTF-16 
With UTF-16, simply write the code point of a character into two bytes "as is". For instance, the UTF-16 representation for the capital Cyrillic "Ж" is 00010110 00000100 in binary. However, this only works with code points below FFFF~16~. Encoding of a bigger code point require more than two bytes, so UTF-16 is a variable-width encoding too, but the minimum code unit size is 2 bytes instead of 1 for UTF-8.

The code snippet below gives an illustration to a possible UTF-encoding implementation:

	Procedure WriteUTF16Char(Code: UInt32)
    If (Code < $10000) Then
        WriteWord(LoWord(Code))
    Else
        Code = Code - $10000
        Var Lo10: Word = LoWord(Code And $3FF)
        Var Hi10: Word = LoWord(Code Shr 10)
        WriteWord($D800 Or Hi10)
        WriteWord($DC00 Or Lo10)
    End If
	End Procedure


## UTF-8 versus UTF-16 
There is pretty much to say why UTF-8 seems, and actually is, more preferable to UTF-16 whenever it comes to quick efficient communications in most European languages, including, but bot limited to:

 - A UTF-8 file that contains only ASCII  characters is identical to an ASCII file. Legacy programs can generally handle UTF-8 encoded files, even if they contain non-ASCII characters. For instance, the C   `printf`  function can print a UTF-8 string, as it only looks for the ASCII '%' character to define a formatting string, and prints all other bytes unchanged, thus non-ASCII characters will be output unchanged. 
 - UTF-8 encoded text files are less in size (hence, take
   less time to  transmit) with almost all languages except (not
   necessarily) for Asian hieroglyphics. 
   
 - UTF-16 seems much less robust to loss of data, specifically of odd number of bytes; should this happen, it garbles all the following text (whereas with UTF-8 it causes a loss of only one symbol).

On the other hand, UTF-16 historically comes from USC-2 (a fixed-width two-byte encoding scheme, outdated now), which in its turn was an extension on ASCII approach onto two bytes instead of one. At that time UTF-8 didn't even exist, so contemporary software inventions, still popular today, such as [Win32 API](https://ru.wikipedia.org/wiki/Windows_API), [NTFS](https://ru.wikipedia.org/wiki/NTFS) and [FAT](https://ru.wikipedia.org/wiki/FAT) use UTF-16LE.

# Digging Deeper Into Unicode


## "High- and Low-endian" Versions of UTF-16


## Encoding In- and Outside BMP
Right after ASCII had been extended onto fixed-width two-byte UCS-2 to cover 2<sup>16</sup>=65 536 symbols , IT people were as naive as to think it would be enough forever. However, it appeared pretty soon it wouldn't, so one more restructuring of the Unicode character set was undertaken to shape it as we know it today, which is like described below.

### The BMP

The former USC-2 character set is now referred to as the Basic Multilinguial  Plane (BMP).

It numbers 2<sup>16</sup> code points ranging from U+0000 to U+FFFF, of which:
    - E000 - F8FF  are reserved for private use (private use area).
    - D800  - DBFF and DC00 - DFFF  are the so-called  *high surrogates* and *low surrogates*, respectively, as explained below.

A BMB character takes one (the ASCII subset) or two code units to encode with UTF-8, and one unit - with UTF-16.

As of Unicode 12.1, the BMP comprises the [163 blocks of characters](https://en.wikipedia.org/wiki/Plane_%28Unicode%29#Basic_Multilingual_Plane) including, yet far not limited to, all European alphabets, basic mathematics notations, etc.

### Surrogates for Encoding Outside BMP

Code points from U+010000 to U+10FFFF (the Unicode top limit) obviously take more than 16 bits to encode - this is exactly what *surrogate pairs*, or simply *surrogates* are for. There are 2<sup>20</sup> such points totally.

With UTF-8 the encoding scheme works as described to output 3, 4 or 5 code units.

With UTF-16 two code units become needed (16 bits, or 2 bytes, each), of which one comes as a high surrogate, the other as a low one. Each surrogate is 10 bits long, which produces 2<sup>10</sup> 2<sup>10</sup>=2<sup>20</sup> pair combinations strictly equal to the total number of code points to encode.
In more detail, for a code point above U+10000 do the following to encode it in UTF-16:
Subtract 10000<sub>16</sub> from it to obtain a 20-bit number.
Take a high surrogate to encode the first 10 bits and a low one for the remaining  
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NTc0Njc2ODQsODA0NjM4MzA0LDUxNT
Q0OTkxMSwtMTE4ODUzMjY3Miw3MDE0NjExNzAsMTQ5MzM1NzEw
OCwxMDM0ODcxMDksLTgzMjgwNTQ4MiwyMDc1NzkxNDQ5LDEwNj
c3MDU0ODIsMjAxOTAxNjI2MSwtNzczMjAzNTc5LDM5MjM3NDIy
OSw5ODgxODc4MTksOTA5MTM5NzA4LC03MjE4OTI4MTksLTg1Nj
gxOTA5NSwtMTI5NTcyOTQyNiwtMjExMTMzOTYzMCwtNDE4NzQw
OTQyXX0=
-->