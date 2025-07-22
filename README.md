using System;
using Autodesk.Revit.UI;
using Autodesk.Revit.DB;
using Autodesk.Revit.UI.Selection;
using System.Collections.Generic;
using System.Linq;

namespace CreateSheets
{
    [Autodesk.Revit.Attributes.Transaction(Autodesk.Revit.Attributes.TransactionMode.Manual)]
    [Autodesk.Revit.DB.Macros.AddInId("B11E2C87-C01F-4018-A6F9-9F9E2FA4AE90")]
    public partial class ThisDocument
    {
        private void Module_Startup(object sender, EventArgs e)
        {

        }

        private void Module_Shutdown(object sender, EventArgs e)
        {

        }

        #region Revit Macros generated code
        private void InternalStartup()
        {
            this.Startup += new System.EventHandler(Module_Startup);
            this.Shutdown += new System.EventHandler(Module_Shutdown);
        }
        #endregion
        public void CreateSheets()
        {
            //define current document
            Document curDoc = this.Application.ActiveUIDocument.Document;
            
            //define starting sheet number
            int sheetNum = 101;
            
            //get all plans in current view
            FilteredElementCollector viewColl = new FilteredElementCollector(curDoc);
            viewColl.OfClass(typeof (ViewPlan));
            
            //get all title blocks in current model
            FilteredElementCollector tblockColl = new FilteredElementCollector(curDoc);
            tblockColl.OfCategory(BuiltInCategory.OST_TitleBlocks);
            
            //create transaction
            Transaction curTrans = new Transaction(curDoc, "Create Sheets");
            curTrans.Start();
                
            //loop through views
            foreach (ViewPlan curView in viewColl.OrderBy(zz => zz.Name))
            {
                if (curView.ViewType == ViewType.FloorPlan)
                {
                    if (curView.IsTemplate == false)
                    {
                        //get a floor plan - create the sheet
                        ViewSheet newSheet;
                        newSheet = ViewSheet.Create(curDoc, tblockColl.FirstElementId());
                        
                        //rename sheet to view name
                        newSheet.Name = curView.Name;
                        
                        //renumber sheet
                        newSheet.SheetNumber = "A" + sheetNum.ToString();
                        
                        //increment sheet number
                        sheetNum ++;
                        
                        //add view to sheet
                        XYZ insPoint = new XYZ(1.75,1.25,0);
                        Viewport curVP = Viewport.Create(curDoc, newSheet.Id, curView.Id, insPoint);
                    }
                }
            }
            
            //commit changes and close transaction
            curTrans.Commit();
            curTrans.Dispose();
        }
    }   
