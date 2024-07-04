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
  - ```
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


## 

