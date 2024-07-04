# PDF
Ins and outs of writing Portable Document Files from scratch

## Basic Structure

- Header
  - %PDF-1.0 -> %PDF-1.7
  - Include on 2-line: %âãÏÓ, for transfer of text over FTP or other legacy file transfer programs. Doesn't have to be that but bytes w/ character code higher than 127.
 
- Body
  - Open an object with `PostScript 1 0 obj << ... >> endobj`
- Cross-reference table
- Trailer
