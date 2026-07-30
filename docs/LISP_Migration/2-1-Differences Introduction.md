## COM Object Identifier Differences

                

COM object identifiers vary across CAD platforms. Developers must replace these identifiers during migration. Creating COM objects with identifiers from other CAD platforms in ZWCAD may not work as expected. For example, running "(vlax-create-object "AutoCAD.AcCmColor.24")" in AutoCAD creates an AcCmColor COM object, but the same code returns nil in ZWCAD. Developers should replace "AutoCAD.AcCmColor.24" with "ZWCAD.ZcCmColor.*", where * represents ZWCAD's annual version number. For example, in ZWCAD 2026, use "(vlax-create-object "ZWCAD.ZcCmColor.2026")" to create a ZcCmColor COM object successfully.

                

The table below shows differences in COM object identifiers between various CAD platforms and ZWCAD:

                

                    

                        AutoCAD
                        BricsCAD
                        ZWCAD
                    
                    

                         | AutoCAD.Application.*
                         | BricscadApp.AcadApplication.*
                         | ZWCAD.Application.*
                    
                    

                         | AutoCAD.Drawing.*
                         | BricscadApp.AcadDocument.*
                         | ZWCAD.Drawing.*
                    
                    

                         | AutoCAD.AcadLayerStateManager.*
                         | BricscadApp.AcadLayerStateManager.*
                         | ZWCAD.ZcadLayerStateManager.*
                    
                    

                         | AutoCAD.AcCmColor.*
                         | BricscadDb.AcadAcCmColor.*
                         | ZWCAD.ZcCmColor.*
                    
                    
                

                
                
![](../images/note.gif)

                Note:
                The * symbol in each table entry represents version numbers of CAD platforms. For specific version naming rules, refer to the registry key: Computer\HKEY_CLASSES_ROOT.

                
---

                

## Filename Differences

                

CAD-related directories often contain files (e.g., linetypes, menus, DCL dialogs). Developers may use data from these files for specific functionalities. Filenames differ across CAD platforms. During migration, modify relative filenames to match those in ZWCAD's supported search paths, as shown below:

                

                    

                        AutoCAD
                        BricsCAD
                        ZWCAD
                    
                    

                         | ACAD.lin
                         | -
                         | ZWCAD.lin
                    
                    

                         | ACADiso.lin
                         | iso.lin
                         | ZWCADiso.lin
                    

                    

                         | ACAD.pat
                         | -
                         | ZWCAD.pat
                    
                    

                         | ACADiso.pat
                         | iso.pat
                         | ZWCADiso.pat
                    
                    

                         | ACAD.cuix
                         | iso.pat
                         | ZWCAD.cuix
                    
                    
                

                
![](../images/note.gif)

                Note:
                Not all ZWCAD files have equivalents in other CAD platforms. If your program uses such files, contact zdn@zwcad.com for assistance.

                
---

                

## System Variable Differences

                
                    Some system variables in ZWCAD have different values compared to other CAD platforms. For example, running "(getvar "ROAMABLEROOTPREFIX")" in AutoCAD returns "C:\\Users\\localuser\\AppData\\Roaming\\Autodesk\\AutoCAD