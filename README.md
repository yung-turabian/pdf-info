# PDF
Ins and outs of writing Portable Document Files from scratch

## Basic Structure

- Header
  - %PDF-1.0 -> %PDF-1.7
  - Include on 2-line: %âãÏÓ, for transfer of text over FTP or other legacy file transfer programs. Doesn't have to be that but bytes w/ character code higher than 127.
 
- Body
  - Open an object with `1 0 obj << ... >> endobj`
- Cross-reference table
  - Lists byte offsets for each object, is what allows dynamic loading
  - ```PostScript
    0 6                  % Six entries in table, starting at 0
    0000000000 65535 f   % Special entry
    0000000015 00000 n   % Object 1 is at byte offset 15
    0000000074 00000 n   % Object 2 is at byte offset 74
    0000000192 00000 n   %A nd more...
    0000000291 00000 n
    0000000409 00000 n   % Object 5 is at byte offset 409
    ```
- Trailer
  - ```PostScript
    trailer              % begin of Trailer
    <<
    /Root 5 0 R          % Points to catalog
    /Size 6              % Number of objects
    >>
    startxref
    459                  % Byte offset of xref table
    %%EOF                % End-of-file marker
    ```


## Lexicon

- 3 kinds of characters, regular, whitespace and delimiters
  - Delimiters: ( ) < > [ ] { } / %

### Object Types...

#### Basics

1. Integers and real numbers, `42 and 3.1415`
2. Strings, `(Hello World)`, must be enclosed in brackets.
    - Can escape both ( ) & \ by using \, like in (\(Hello) -> "Hello"
    - Basic escape sequences work too, `\n \r \t \b \f \ddd`
4. Names, `/Name`, keys for dictionares and denoted with a /
    - / by itself even counts as a name!
    - No spaces allowed, but use #20 and it will appear that way if needed
6. Booleans, `true` or `false`
7. Null object, `null`

#### Compound

1. Arrays, `[1 2]` or `[/Buckle /My [/Shoe]]` -- the second example contains an array within the array
    - Must all be of same type
3. Dictionaries, `<< /Contents 1 0 R /Resources 2 0 R >>`
4. Streams, hold binary data and work with a dictionary defining attributes of data, used to store fonts and images and other writables.
    - ```
      8 0 obj
      <<
      /Length 65
      >>
      stream
      1. 0. 0. 1. 50. 700. cm % 65 bytes of data of graphics stream
      BT
        /FO 36. Tf
        (Hello, World!) Tj
      ET
      endstream
      endobj

      ```
   - Streams are most always compressed
     - `/FlateDecode`
     - ```
       796 0 obj
       <</Length 275 /Filter /FlateDecode>>
       stream
       HTKO0÷ü
       endstream
       endobj
       ```
     - Can use multiple methods like for JPEG compression with:
         `/Filter [/ASCII85Decode /DCTDecode]`

#### Linking

- Indirect reference, forms a link from one object to another, `1 0 R`
    - Tha example is object number 1, generation umber 0, R is the indirect refernece keyword
- ```
  << /Resources 10 0 R
     /Contents [4 0 R] >>  % Both work!
  ```
  
### Linearized PDF

PDF 1.2 introduced rules for order of objects and **hint tables**. Useful for HTTP or web viewing of PDFs for faster access.

A linearization dictionary will appear at the top of the file
```GhostScript
%PDF-1.4
%âãÏÓ
1 0 obj
<< /E 200967
/H [ 667 140 ]
/L 201431
/Linearized 1
/N 1
/O 7
/T 201230
>>
endobj
```

