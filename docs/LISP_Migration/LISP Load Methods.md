## Description

                    

ZWCAD supports loading LISP programs through multiple methods to meet different user requirements. All formats of LISP programs can be loaded and run using these methods. This section uses the test program "foo.lsp" as an example to introduce these loading methods.

                    
---

                    

Loading via APPLOAD Command

                    

Step 1

                    

Launch ZWCAD, execute the APPLOAD command, and the dialog box shown below will appear. Click "Add" to select and add the LISP program to the list.

                    

                    

                    

Step 2

                    

Select the added program in the list and click the "Load" button to complete the loading.

                    

                    

You can select the LISP program and click "Add to Startup Group" to automatically load the program every time ZWCAD starts or a drawing is opened, avoiding repeated manual loading.

                    

                    

To remove a LISP program from the Startup Group, click "Startup Group", select the program, click "Delete" -> "OK". This prevents automatic loading in future sessions.

                    

                    
---

                    

Drag to Drawing Area to Load

                    

Launch ZWCAD, navigate to the system folder, press and hold the left mouse button on the LISP program, then drag it into the drawing area to load.

                    

                    
---

                    

Loading via LISP Functions

                    

Use the LISP function examples listed in the table below to load LISP applications.

                    

                        

                            Function Name
                            Example
                            Description
                        
                        

                             | load
                             | (load &lt;filename&gt;)
                             | 
                                Loads the LISP program into the current document. &lt;filename&gt; is the absolute or relative path of the LISP program, e.g., "d:/foo.lsp" or "foo.lsp". For relative paths, ensure the preceding path is added to the supported search paths.
                            
                        
                        

                             | vl-load-all
                             | (vl-load-all &lt;filename&gt;)
                             | 
                                Loads the LISP program into all open documents. &lt;filename&gt; is the absolute or relative path of the LISP program. For relative paths, ensure the preceding path is added to the supported search paths.
                            
                        
                        

                             | autoload
                             | (autoload &lt;filename&gt; &lt;command-list&gt;)
                             | Automatically loads the LISP program specified by &lt;filename&gt; when commands in &lt;command-list&gt; are executed.
                        
                    

                    
---

                    
![](../images/note.gif)

                    Note:
                    When using the above methods to load migrated LISP programs, errors may occur, causing loading failures or function interruptions. If such issues arise, refer to Differences Introduction to adjust the LISP program.