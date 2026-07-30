## Introduction

                

ZWCAD supports LISP language for developers to perform secondary development. Developers can use LISP functions to implement complex functionalities and directly load LISP programs developed on other CAD platforms. However, compatibility issues may arise due to platform differences. This section introduces the steps to migrate LISP programs from other CAD platforms to ZWCAD, avoiding compatibility issues and improving migration efficiency.

                
---

                

## Procedure

                

Step 1

                

Take an LSP file as an example. Open the LSP file with an editor and check the code for differences mentioned in the [Differences Introduction](2-1-Differences%20Introduction.md) between other platforms and ZWCAD. If differences are found, adjust the code accordingly.

                

Example 1:

                

For instance, using arxload to load an ARX file in AutoCAD:

                

(arxload "test.arx")

                

In ZWCAD, the code needs to be adjusted to:

                

(zrxload "test.zrx")

                

test.zrx is the ZWCAD extension binary file compiled using ZRX SDK.

                

Example 2:

                

For example, using the following code in AutoCAD to get the current CAD version number:

                

(atof (getvar "ACADVER"))

                

In ZWCAD, the code needs to be adjusted to:

                

(atof (getvar "VERNUM"))

                

ZWCAD uses the VERNUM system variable to retrieve the CAD version number, release date, and other information.

                
                

Step 2 (Optional)

                

Encrypt the LSP file into a secured LSP or ZELX format by referring to [LISP Encryption](LISP%20Encryption.md). ZELX format is recommended for higher security and execution efficiency compared to LSP encryption.

                

Step 3

                

Load the modified LSP program (from Step 1) or the encrypted program (from Step 2) in ZWCAD by following the [LISP Loading Methods](LISP%20Load%20Methods.md). Test whether the program loads and functions correctly.