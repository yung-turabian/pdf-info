# PDF
Ins and outs of writing Portable Document Files from scratch

## Example (Incomplete, need to write XRef table)

```PostScript
%PDF-1.0
%âãÏÓ
1 0 obj % Page Tree
<< /Kids [2 0 R 3 0 R]  /Type /Pages /Count 3 >>
endobj
4 0 obj % Content stream for page 1
<< >>
stream
1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page One) Tj ET
endstream
endobj
2 0 obj % Page one
<<
  /Rotate 0
  /Parent 1 0 R
  /Resources
    << /Font << /F0 << /BaseFont /ComicSans /Subtype /Type1 /Type /Font >> >> >>
  /MediaBox [0.000000 0.000000 595.275590551 841.88976378]
  /Type /Page
  /Contents [4 0 R]
>>
endobj
5 0 obj % Document catalog
<< /PageLayout /TwoColumnLeft /Pages 1 0 R /Type /Catalog >>
endobj
6 0 obj % Page 3
<<
/Rotate 0
/Parent 3 0 R
/Resources
<< /Font << /F0 << /BaseFont /ComicSans /Subtype /Type1 /Type /Font >> >> >>
/MediaBox [0.000000 0.000000 595.275590551 841.88976378]
/Type /Page
/Contents [7 0 R]
>>
endobj
3 0 obj % Intermediate page tree node, linking to pages two and three
<< /Parent 1 0 R /Kids [8 0 R 6 0 R] /Count 2 /Type /Pages >>
endobj
8 0 obj % Page 2
<<
/Rotate 270
/Parent 3 0 R
/Resources
<< /Font << /F0 << /BaseFont /Times-Italic /Subtype /Type1 /Type /Font >> >> >>
/MediaBox [0.000000 0.000000 595.275590551 841.88976378]
/Type /Page
/Contents [9 0 R]
>>
endobj
9 0 obj % Content stream for Page 2
<< >>
stream
q 1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page Two) Tj ET Q
1. 0.000000 0.000000 1. 50. 750 cm BT /F0 16 Tf ((Rotated by 270 degrees)) Tj ET
endstream
endobj
7 0 obj % Content stream for Page 3
<< >>
stream
1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page Three) Tj ET
endstream
endobj
10 0 obj % Document information dictionary
<<
/Title (Example)
/Author (yung-turabian)
/Producer (Manually Created)
/ModDate (D:2024)
/CreationDate (D:2024)
>>
endobj xref
0 11
trailer % Trailer dictionary
<<
/Info 10 0 R
/Root 5 0 R
/Size 11
/ID [<xo84a9zefryhsg3b8nta4xz0> <bd1cxyw0n8ga28t1xznt1yuq>]
>>
startxref
0
%%EOF
```

## Basic Structure

### Header
  - %PDF-1.0 -> %PDF-1.7
  - Include on 2-line: %âãÏÓ, for transfer of text over FTP or other legacy file transfer programs. Doesn't have to be that but bytes w/ character code higher than 127.
 
### Body
  - Open an object with `1 0 obj << ... >> endobj`
- Cross-reference table
  - Lists byte offsets for each object, is what allows dynamic loading

    ```PostScript
    0 6                  % Six entries in table, starting at 0
    0000000000 65535 f   % Special entry
    0000000015 00000 n   % Object 1 is at byte offset 15
    0000000074 00000 n   % Object 2 is at byte offset 74
    0000000192 00000 n   %A nd more...
    0000000291 00000 n
    0000000409 00000 n   % Object 5 is at byte offset 409
    ```
### Trailer

#### Examples

```PostScript
trailer              % begin of Trailer
<<
/Root 5 0 R          % Points to catalog
/Size 6              % Number of objects
>>
startxref
459                  % Byte offset of xref table
%%EOF                % End-of-file marker
```

```PostScript
trailer
<<
  /Root 126 0 R
  /Size 200 0 R
  /Info 198 0 R
  /ID [<isyx0rh79lpn9q9ud9qzai3y> <xn3ucvxpqg2rt5s2bkrsqstd>]
>>
```

#### Details

- /Size (required) : `Integer` total number of entries in xref table (almost always number of objects plus 1)
- /Root (required) : `Indirect reference to dictionary` indirect reference to *catalog*
- /Info : `Indirect reference to dictionary` indirect reference to *information dictionary*
- /ID : `Array of 2 strings` unique identity. First string if decidied when the file is created, the second is modified by system when modifying the file

### Information Dictionary

### Examples
```PostScript
<<
  /ModDate (D:
  /CreationDate (D:
  /Title (blahblah)
  /Creator (XXX)
  /Producer (XXX)
  /Author (yung-turabian)
>>
```

#### Details
Metadata:
- /Title : Document title, string
- /Subject : Subject of document, string
- /Keywords : keywords associated with document, no structure of note, string
- /Author : Name of author, string
- /CreationDate : Date the document was created, date string
- /ModDate : Date of last modification, date string
- /Creator : Name of program that created this document, string
- /Producer : Name of program which converted file to PDF, string

### Catalog

#### Details
- /Type (required) : must be /Catalog, name
- /Pages (required) : root node of page tree, indirect reference to dictionary
- /PageLabels : A number tree giving the page labels, more complicated number like i,ii,iii, number tree
- /Names : the name dictionary, contains name trees, mapping to entities to drop the need to use object numbers to reference dirctly, dictionary
- /Dests : `dictionary` dictionary maps names to destinations, a dest being a hyperlink
- /ViewerPreferences : `dictionary` allows for flags to specify certain behavior
- /PageLayout : `name` Specify the page layout.
  - /SinglePage (default), /OneColumn, /TwoColumnLeft, /TwoColumRight, /TwoPageLeft, /TwoPageRight
- /PageMode : `name` specify page mode to be used by viewers
  - /UseNone (default), /UseOutlines, /UseThumbs, /Fullscreen, /UseOC, /UseAttachments
- /Outlines : `indirect reference to dictionary` outline dictionary, i.e. bookmarks
- /Metadata : `indirect reference to dictionary` document's XMP metadata

### Pages and Page Trees

```PostScript
1 0 obj % The root
<< /Type /Pages /Kids [2 0 R 3 0 R 4 0 R] /Count 7 >>
endobj
2 0 obj % Intermediate node
<< /Type /Pages /Kids [5 0 R] /Parent 1 0 R /Count 3 >>
endobj
3 0 obj % Intermediate node
<< /Type /Pages /Kids [6 0 R] /Parent 1 0 R /Count 3 >>
endobj
4 0 obj % Page 1
<< /Type /Page /Parent 1 0 R /MediaBox [0 0 500 500] /Resources << >> >>
endobj
5 0 obj % Page 2
<< /Type /Page /Parent 2 0 R /MediaBox [0 0 500 500] /Resources << >> >>
endobj
6 0 obj % Page 3
<< /Type /Page /Parent 3 0 R /MediaBox [0 0 500 500] /Resources << >> >>
endobj
```

```PostScript
/MediaBox [0 0 600 900] % rectangle type; x,y coords, lower-left to upper-right
```

#### Details

Pages are linked with a page tree, it is good practice to create a balanced tree. Nodes with no children are the pages.

For a page...
- /Type (required) : `name` must be /Page
- /Parent (required) : `indirect reference to dictionary` parent node of this node
- /Resources : `dictionary` page's resources (fonts, images, etc.), if empty resources are inheirted from parent node
- /Contents : `indirect reference to stream or array of streams` graphical content of the page, if empty the page is empty
- /Rotate : `integer` viewing rotation of page in degress, multiple of 90
- /MediaBox (required) : `rectangle` page's media box, page size most often. If missing inherited from parent node in page tree
- /CropBox : `rectangle` defines visible region of page, is missing same space as media box

For a root or intermediate page node...
- /Type (required) : `name` must be /Pages
- /Kids (required) : `array of indirect references` immediate child page-tree nodes
- /Count (requirec) : `integer` number of page nodes that are children
- /Parent : `indirect reference to page tree node` reference to parent of this node, if not present this is now the root node

## Lexicon

- 3 kinds of characters, regular, whitespace and delimiters
  - Delimiters: ( ) < > [ ] { } / %

### Object Types...

#### Basics

1. Integers and real numbers, `42 and 3.1415`
2. Strings, `(Hello World)`, must be enclosed in brackets.
    - Can escape both ( ) & \ by using \, like in (\(Hello) -> "Hello"
    - Basic escape sequences work too, `\n \r \t \b \f \ddd`
    a. Text strings are strings outside the actual textual content (bookmark names, etc.), encoded using either PDFDocEncoding or Unicode
    b. Date strings, the PDF date format is (D:YYYYMMDDHHmmSSOHH'mm')
      - 2024 .. 06 .. 04 .. 15 .. 14 .. 03 .. - .. 04' .. 00'
      - (D:2024) is also valid
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

     ```PostScript
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

       ```PostScript
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

  ```PostScript
  << /Resources 10 0 R
     /Contents [4 0 R] >>  % Both work!
  ```
  
### Linearized PDF

PDF 1.2 introduced rules for order of objects and **hint tables**. Useful for HTTP or web viewing of PDFs for faster access.

- A linearization dictionary will appear at the top of the file

```PostScript
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

- `pdfopt` comes with [GhostScript👻](https://www.ghostscript.com/) will linearlize a file.

```bash
$ pdfopt in.pdf out.pdf
```

### Order PDF file is read

1. Reads header and version number to confirm this is, in fact, a PDF
2. EOF is now by found by reversing the stream to the end of the file. Trailer dictionary is now read, the byte offset of the xref table is read.
3. The xref table is now read, revealing locations of all objects in the file.
4. All objects can now be parsed, or can wait until needed, on demand.
5. Extracting date: pages, graphics, and metadata.

A PDF object as a data structure could look like, for example:

```PostScript
<< /Kids [2 0 R] /Count 1 /Type /Pages >>
```

```Python
dict = {
  "/Kids": [2],
  "/Count": 1,
  "/Type": "/Pages"
}
```

A structure would need bools, ints, reals(floats), string, names(strings), array(output as "[1 1 1 1]"), dictionary(string, pdfobject), stream(pdfobject, bytes), indirect(a reference as in [2 0 R], just the 2).

### 



