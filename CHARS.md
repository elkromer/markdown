---
tags:
	- Character
	- Encodings
	- Charset
---

# Characters, Character Sets, Encodings


In the olden days, the only characters that mattered were good ol' unaccented English letters. We had a code for them called [ASCII](http://www.robelle.com/library/smugbook/ascii.html) which was able to represent every character using a number between 32 and 127 (only 7 bits needed). Everybody figured numbers 128-255 were up for grabs and developed their own code pages for the last 128 numbers. **This got solidified in the ANSI standard!** 
> Greek users used 737. Isralis used 862. National versions of MS-DOS had dozens of these code pages installed on them. This presented issues when trying to install more than one code page on an older machine.

If a letter's shape changes at the end is it a different letter? Hebrew says yes, Arabic says no. Is the German `ß` its own letter or a fancy way of writing `ss`?

Figuring out what the letter is can cause controversy.

## Unicode

A brave effort to create a single **character set** for every writing system on the planet. 

### Background

*Ah yeah, Unicode is simply a 16-bit code where each character takes 16 bits and therefore there are 65,535 possible characters!* **Wrong.** Unicode has a different way of thinking about characters.

Until now, a letter maps to some bits which you store on disk `A -> 0100 0001`. In Unicode, a letter maps to something called a **code point** and how that code point is represented on disk is a whole nuther story.

### General Idea

Every platonic letter in every alphabet is assigned a magic number written like this `U+0639` called a code point. There is no real limit on the number of letters that Unicode can define and they have already gone past 65,535. 

| Word | Code Points |
| ----------- | ----------- |
| Hello | `U+0048 U+0065 U+006C U+006C U+006F` |

We haven't even talked about how to store the character in memory yet. 

## Encodings

A character encoding is a system that pairs each character in a supported character set with some value that represents that character. 
> For example, Morse code is a character encoding that pairs each character in the Roman alphabet with a pattern of dots and dashes that are suitable for transmission over telegraph lines. 

As you can see, the Unicode character set needs a Unicode character encoding scheme to represent it in memory.

### UTF-16 and UCS-2

The first way that was devised to store Unicode was **everything gets two bytes** (also called `UCS2` or `UTF-16`). 

| Word | UTF-16 BE Mode | UTF-16 LE Mode |
| ----------- | ----------- | ----------- |
| Hello | `00 48 00 65 00 6C 00 6C 00 6F` | `48 00 65 00 6C 00 6C 00 6F 00` |

>1776 is a “big endian” number because the “biggest” (most significant) digit is stored in the leftmost position. The big end of 1776 is on the left.

For a while it seemed like this would work, except programmers kept complaining about all the zeros. Doubling the storage for strings was an inconvenience to them.

### UTF-8

A system for storing your string of Unicode code points using 8-bit bytes. Every code point from 0-127 is stored in a single byte. Only code points 128 and above are stored using 2, 3, 4, 5, and 6 bytes. 
>This has the unique side-effect that English text looks exactly the same in UTF-8 as it did in ASCII

### UTF-7

Guarantees the high-bit will always be zero.

### UCS-4

Each code point is stored in 4 bytes.

### Older schemes

Those unicode code points can be encoded in OEM Greek, Hebrew ANSI, ASCII, or any of the several hundred encodings invented so far. There is one catch:

If there's no equivalent for the Unicode code point you're trying to represent in the encoding you're trying to represent it in, you get a `?` or a `�`. **There are several hundred traditional encodings which can only store SOME unicode code points correctly and change all other code points into question marks.**

## There ain't no such thing as Plain Text

It does not make sense to have a string without knowing what encoding it uses. If you have a string in memory, in a file, or in an email message, you have to know what encoding it is in or you cannot interpret it or display it to users correctly.