## Introduction

                

This page lists common issues encountered during LISP program migration and their corresponding solutions. If this page does not fully resolve your issue or if you find that ZWCAD lacks certain functions or features, please contact [zdn@zwcad.com](mailto:zdn@zwcad.com) for assistance.

                
---

                

## Q1: Can LISP programs developed in AutoCAD be used in ZWCAD?

                

Yes. For user convenience, most LISP programs developed for AutoCAD can run directly in ZWCAD.

                
---

                

## Q2: How does ZWCAD differ from AutoCAD in loading LISP programs?

                

The methods are largely the same as AutoCAD: use the APPLOAD command, drag and drop files into the drawing area, or load via the load function.

                
---

                

## Q3: Why does loading a FAS/VLX file fail or result in a long error message after successful loading?

                

This is due to differences in the Unicode implementation between ZWCAD and AutoCAD, which prevent the program from running when LISPSYS=1. To resolve this, set the system variable LISPSYS to 0, restart ZWCAD, and reload the program.

                
---

                

## Q4: Why does the DCL dialog fail to appear after a command is executed?

                

1. Missing base DCL files: Add base.dcl and primitives.dcl from the ZWCAD installation directory to the support search path.
                    2. Undefined controls in the DCL file: Check the DCL file against the DCL reference documentation.

                
---

                

## Q5: Why does the console report "Error: undefined function - ACET-*" during loading or execution?

                

This indicates missing ACET functions. Replace them with ZWCAD-supported functions that provide similar functionality.

                
---

                

## Q6: Why does a command called via the LISP command function behave unexpectedly?

                

Command behavior may differ between CAD platforms. Refer to ZWCAD's official documentation for command specifications and modify the source code accordingly.

                
---

                

## Q7: Why does the console report "Error: ZWCAD variable setting rejected:"?

                

The setvar function failed due to:1. Out-of-range variable values2. Incorrect data type3. Missing system variablesVerify the variable name and value in the setvar function.

                
---

                

## Q8: Why does the console report "Error: misplaced right/left paren" during loading?

                

This is caused by mismatched parentheses in the LISP code. Check the source code for syntax errors.

                
---

                

## Q9: Why does the console report "Error: quit / exit abort" during loading or execution?

                

Possible causes:
                    1. The quit or exit function was called unexpectedly. Check the source code logic.
                    2. Internal LISP error. Contact support for assistance.

                
---

                

## Q10: Why does the console report "Error: undefined function - nil" during loading or execution?

                

Possible causes:
                    1. Missing function definition. Check if the undefined function is custom or built-in. For custom functions, verify naming conventions.
                    2. Internal LISP error. Contact support for assistance.