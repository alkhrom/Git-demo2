# Intro to Unicode for Dummy Programmers

## Why Unicode?

Of course, had the entire Globe spoken English, there would have never been a need for anything else but the good old ASCII. However, as the Internet party

## Some Philosophy Behind Unicode

![enter image description here](https://lh3.googleusercontent.com/driTrANKja0TwszTgvDmmmLzRCryfCI5TGAj11gkI_PfxbNG3JVqszY282WWRLYdndv4AZL68Q9Q)


### Code Points And Code Units

### Scripts and Planes

## UTF-8 Encoding Algorithm

 1. If  a code point is below 128 (decimal), write to a UTF-8 byte "as is".

Example: 

 1. If a code point is above 128 (decimal) yet below 2048 (decimal), do:
Convert it into binary, which will take 11 bit.
Split those bits into 5 on the left and 6 on the right.
Start the first byte with a multi-byte mark of "110".
Concatenate the left 5 bits to form the first byte.
Start the second byte with a "code continued" mark of "10"
Concatenate the right 6 bits to obtain the send byte.
Concatenate the two bytes.
**Example:**
The code point for capital Cyrillic "Ж" is "1046" (decimal) or "0416" (hex) or "10000 010110" (binary), which brings us directly to UTF-8 encoding of "**110**10000 **10**010110" or D096 (hex).



<!--stackedit_data:
eyJoaXN0b3J5IjpbOTI5OTY0MzM0LC0yMDE2Njg4ODg5LDE0Mj
IxMDc0OSw5ODg4MjU5NjQsLTE1ODg3OTY2Nl19
-->