## Description

                

ZWCAD supports compiling and encrypting LSP-format programs using the COMPILE command to ensure the security of LISP source code while improving runtime performance. This page explains how to generate encrypted programs via the COMPILE command in ZWCAD, addressing developers' security concerns.

                
---

                

## Steps

                

Step 1

                

Launch ZWCAD and execute the COMPILE command. The dialog box shown below will appear.

                

                

Step 2

                

Click "Select File" to choose the LSP-format file to compile and encrypt. The button highlighted by the red rectangle in the image below is for selecting files. The image shows that "foo.lsp" has been selected.

                

                

Step 3

                

Click "Select Save Directory" to choose the output directory for the encrypted file. To save the encrypted file in the same directory as the source LSP file, check the "Use Source Directory" option.

                

                

Step 4

                

To modify the output filename, edit the "Output Filename" field. COMPILE supports generating encrypted LISP files in two formats: LSP and ZELX. Developers can choose the format based on their needs.

                

                

Step 5

                

Click "Compile" to generate the encrypted file.