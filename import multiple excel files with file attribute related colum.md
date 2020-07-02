# Import multiple excel files
The goal is to import multiple fils and add a column to indentify from wich file the row come from.
The file Name is YearMonth.xls (202007.xls)
### Import Data From Folder
First step is to import data from folder: 
  ' Get Data -> more... -> Folder '
After that
## Transform Data
* Choose Columns [Content, Name]
* Create a New Colum for YearMonth
  New Column 'YearMont=Text.BeforeDelimiter([Name],".")'
* Change YearMonth Type to number
* Choose only the columns needed [Content, YearMonth]
* Extract from the Binary content the table
    New Column 'Table=Excel.Workboo([Content])'
* Expand the data
    Table -> Expand -> (select only) Data
* Transform the first row in header
    Custom Column 'PromoteHeaders = Table.PromoteHeaders([Table.Data)]'
* Expand data to obtain a unique dataset with YearMonth column added
    PromoteHedares -> Expand -> (select) rows
* Remove unused columns ( Removed Other Columns2)
* Rename the header
    '#"Rename Columns" = Table.TransformColumnNames(#"Removed Other Columns2", each if Text.Contains( _ ,"PromoteHeaders.") then Text.AfterDelimiter( _ ,".") else _ )'
