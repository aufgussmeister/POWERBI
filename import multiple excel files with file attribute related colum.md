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



## Code

let
    Source = Folder.Files("C:\pathToFolder\ExcelBilancio"),
    AnnoMese_AddCol = Table.AddColumn(Source, "AnnoMese", each Text.BeforeDelimiter([Name],".")),
    AnnoMese_ChangeType = Table.TransformColumnTypes(AnnoMese_AddCol,{{"AnnoMese", Int64.Type}}),
    #"Removed Other Columns" = Table.SelectColumns(AnnoMese_ChangeType,{"Content", "AnnoMese"}),
    TableFromBinary_AddCol = Table.AddColumn(#"Removed Other Columns", "table", each Excel.Workbook([Content])),
    ExpandDataFromTable = Table.ExpandTableColumn(TableFromBinary_AddCol, "table", {"Data"}, {"table.Data"}),
    PromoteTableHeaders_AddCol = Table.AddColumn(ExpandDataFromTable, "promotedTableHeader", each Table.PromoteHeaders([table.Data]) as table),
    #"Removed Other Columns1" = Table.SelectColumns(PromoteTableHeaders_AddCol,{"AnnoMese", "promotedTableHeader"}),
    #"Expanded promotedTableHeader" = Table.ExpandTableColumn(#"Removed Other Columns1", "promotedTableHeader", {"c_tot_dare", "c_tot_avere", "c_tot_dare_prec", "c_tot_avere_prec", "c_tot_saldo_eser_prec", "c_tot_cc_dare", "c_tot_cc_avere", "c_tot_cc_dare_prec", "c_tot_cc_avere_prec", "c_tot_cc_saldo_eser_prec", "num_conto", "cod_conto", "descrizione_conto", "tipo_conto_2", "tipo_conto", "tipo_anag", "indetr_ires", "indetr_irap", "flg_conto_imposta", "c_liv_1", "c_liv_2", "c_liv_3", "c_liv_4", "c_liv_5", "c_descr_liv_1", "c_descr_liv_2", "c_descr_liv_3", "c_descr_liv_4", "c_descr_liv_5", "c_cod_cc", "c_des_cc", "c_flg_extra"}, {"promotedTableHeader.c_tot_dare", "promotedTableHeader.c_tot_avere", "promotedTableHeader.c_tot_dare_prec", "promotedTableHeader.c_tot_avere_prec", "promotedTableHeader.c_tot_saldo_eser_prec", "promotedTableHeader.c_tot_cc_dare", "promotedTableHeader.c_tot_cc_avere", "promotedTableHeader.c_tot_cc_dare_prec", "promotedTableHeader.c_tot_cc_avere_prec", "promotedTableHeader.c_tot_cc_saldo_eser_prec", "promotedTableHeader.num_conto", "promotedTableHeader.cod_conto", "promotedTableHeader.descrizione_conto", "promotedTableHeader.tipo_conto_2", "promotedTableHeader.tipo_conto", "promotedTableHeader.tipo_anag", "promotedTableHeader.indetr_ires", "promotedTableHeader.indetr_irap", "promotedTableHeader.flg_conto_imposta", "promotedTableHeader.c_liv_1", "promotedTableHeader.c_liv_2", "promotedTableHeader.c_liv_3", "promotedTableHeader.c_liv_4", "promotedTableHeader.c_liv_5", "promotedTableHeader.c_descr_liv_1", "promotedTableHeader.c_descr_liv_2", "promotedTableHeader.c_descr_liv_3", "promotedTableHeader.c_descr_liv_4", "promotedTableHeader.c_descr_liv_5", "promotedTableHeader.c_cod_cc", "promotedTableHeader.c_des_cc", "promotedTableHeader.c_flg_extra"}),
    #"Rename Headers" = Table.TransformColumnNames(#"Expanded promotedTableHeader", each if Text.Contains(_,"promotedTableHeader.") then Text.AfterDelimiter(_,".") else _)
in
    #"Rename Headers"
    
