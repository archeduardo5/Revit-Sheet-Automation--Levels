🧩 CreateSheets - Macro for Autodesk Revit
This project contains a macro written in C# for Autodesk Revit that automates the creation of sheets from floor plan views.

🚀 What does this macro do?
The CreateSheets macro performs the following actions:

Finds all views of type Floor Plan in the current Revit model.

Ignores views that are templates (IsTemplate == true).

Uses the first available Title Block type in the model.

Creates a new sheet (ViewSheet) for each floor plan view.

Assigns:

Sheet name based on the view name.

Sheet number starting from A101, A102, etc.

Places the view on the sheet at a fixed position.

public void CreateSheets()
{
    Document curDoc = this.Application.ActiveUIDocument.Document;
    int sheetNum = 101;

    var viewColl = new FilteredElementCollector(curDoc).OfClass(typeof(ViewPlan));
    var tblockColl = new FilteredElementCollector(curDoc).OfCategory(BuiltInCategory.OST_TitleBlocks);

    Transaction curTrans = new Transaction(curDoc, "Create Sheets");
    curTrans.Start();

    foreach (ViewPlan curView in viewColl.OrderBy(v => v.Name))
    {
        if (curView.ViewType == ViewType.FloorPlan && !curView.IsTemplate)
        {
            ViewSheet newSheet = ViewSheet.Create(curDoc, tblockColl.FirstElementId());
            newSheet.Name = curView.Name;
            newSheet.SheetNumber = "A" + sheetNum++;
            XYZ insPoint = new XYZ(1.75, 1.25, 0);
            Viewport.Create(curDoc, newSheet.Id, curView.Id, insPoint);
        }
    }

    curTrans.Commit();
}




<img width="1200" height="1200" alt="image" src="https://github.com/user-attachments/assets/28b424f4-7cf5-46ac-9efe-5ffd7435a684" />

