# Intro to Unicode for Dummy Programmers

## Why Unicode?

Of course, had the entire Globe spoken English, there would have never been a need for anything else but the good old ASCII. However, as the Internet party

## Some Philosophy Behind Unicode

![enter image description here](https://lh3.googleusercontent.com/driTrANKja0TwszTgvDmmmLzRCryfCI5TGAj11gkI_PfxbNG3JVqszY282WWRLYdndv4AZL68Q9Q)


### Code Points And Code Units

### Scripts and Planes

## UTF-8 Encoding Algorithm

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



<!--stackedit_data:
eyJoaXN0b3J5IjpbODE4Mzk5MzE4LC03Nzg2Mzk5OSw5Mjk5Nj
QzMzQsLTIwMTY2ODg4ODksMTQyMjEwNzQ5LDk4ODgyNTk2NCwt
MTU4ODc5NjY2XX0=
-->