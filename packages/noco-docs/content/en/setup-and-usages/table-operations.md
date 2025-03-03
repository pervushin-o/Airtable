---
title: "Table Operations"
description: "Table Operations: Row, Column, Quick Import, Export & Import"
position: 510
category: "Product"
menuTitle: "Table Operations"
---

Once you have created a new NocoDB project you can open it, In the browser, the URL would be like `example.com/dashboard/#/nc/<project_id>`.

## Table

### Table Create

Now you can start creating new tables by simply clicking one of the following options.

- Click `Add new table` button
- Hover `Add new table` button in table menu, click three dots, use Quick Import to create
- Drag and drop CSV, JSON or Excel file to import

<img width="1505" alt="image" src="https://user-images.githubusercontent.com/35857179/194795025-afd81191-4743-435b-b802-88367d2663f9.png">

A modal will be popped up. Input the corresponding info and enable or disable default columns and click `Submit` button.

<img width="1510" alt="image" src="https://user-images.githubusercontent.com/35857179/194795048-aef59b23-ba3f-40ca-88bd-19ee512d7114.png">

Click Show more for advanced settings.

<alert>
Note: You can't disable the `id` column since NocoDB needs a primary column for every table. You can rename it after the creation.
</alert>

<img width="756" alt="image" src="https://user-images.githubusercontent.com/35857179/194795067-e9275cec-f375-45b4-80f9-098745d83e5c.png">

After the successful submission, the table will be created and open as a new tab.  

<img width="1504" alt="image" src="https://user-images.githubusercontent.com/35857179/194795081-f41ebd4d-7fa9-4f65-a66f-3d2375925106.png">

### Table Rename

Right click on Table name on left hand project-tree menu, select `Rename`

<img width="606" alt="image" src="https://user-images.githubusercontent.com/35857179/194795096-82b007fb-f57a-4141-938e-be502b1fb2cd.png">

In modal popup, enter new table name and click `Submit` button

<img width="1506" alt="image" src="https://user-images.githubusercontent.com/35857179/194795119-4aeb05e1-16d5-4b4f-bf6c-81752234d946.png">

### Table Delete

Right click on Table name on left hand project-tree menu, select `Delete`

<img width="641" alt="image" src="https://user-images.githubusercontent.com/35857179/194795140-4fe71896-0802-45dd-9c93-64e51925be57.png">

Click Yes to confirm the table deletion

<img width="1507" alt="image" src="https://user-images.githubusercontent.com/35857179/194795152-9bdbf8df-846e-42f3-89d0-c68bce022cc1.png">

## Column

### Column Add

Click the `+` icon on the right corner of the table.

<img width="352" alt="image" src="https://user-images.githubusercontent.com/35857179/189053971-a3d29b3b-1177-49fe-8178-8868528fe3e7.png">

After the click, it will show a menu and you can enter the column name and choose the column type.  (See [Column Types](./column-types) for the full list).

<img width="459" alt="image" src="https://user-images.githubusercontent.com/35857179/189073266-a0f19e2e-5dd2-4343-8c74-4ef709da272c.png">

You can also click `Show more` for additional menu options.

![Screenshot 2023-03-03 at 8 13 07 PM](https://user-images.githubusercontent.com/86527202/222749857-0e793db2-a5d2-4b54-8d23-2a0cbbec8f5d.png)
<!-- <img width="445" alt="image" src="https://user-images.githubusercontent.com/35857179/189075678-d18b799f-df13-4f78-a5a5-813e8d3277ae.png"> -->

Click `Save` button to create the new column. 

<img width="1509" alt="image" src="https://user-images.githubusercontent.com/35857179/194795274-08483315-5538-4685-8c08-261a9c2dfe14.png">

### Column Edit

To edit column properties, click the down arrow, select `Edit` from the menu.  
  
<img width="230" alt="image" src="https://user-images.githubusercontent.com/35857179/189077129-dfb7a815-3fc7-41ea-b72c-e57f3c30a7f4.png"> 
  
You will be able to edit column name & associated datatype using pop-up modal.  You can also click `Show more` for additional menu options.
  
<img width="497" alt="image" src="https://user-images.githubusercontent.com/35857179/189077270-7acdc818-3747-4307-93fb-e970cb7936f9.png">

Prior to v0.104.3, Advanced menu by default displayed developer specific database configuration options. To avoid unintended tweaks from user, these are now hidden under an easter egg menu. To enable, double click on `show all`/`hide all` button in column edit modal.

![Screenshot 2023-03-06 at 10 45 26 AM](https://user-images.githubusercontent.com/86527202/223024810-85dac1c6-87ef-4193-90cb-3a05be8ccc1d.png)


### Column Delete

To delete a column, click the down arrow, select `Delete` from the menu.  

<img width="256" alt="image" src="https://user-images.githubusercontent.com/35857179/189077566-c9376e4e-9ee8-4ffa-b437-1240894a30cd.png">

Click `Yes` to confirm the column deletion. 

<img width="1507" alt="image" src="https://user-images.githubusercontent.com/35857179/194795311-c2a5587e-d92f-4b88-a8a3-e20ac13c694b.png">

## Row

For adding new values to the table we need new rows, new rows can be added in two methods.

### Row Add (Using Form)

- Click the `+` icon in the toolbar of the table tab.  
  <img width="1038" alt="image" src="https://user-images.githubusercontent.com/35857179/189079143-8f3e3dd6-9b62-4fb0-9a78-a57545026d11.png">
- Then you can enter the values and click `Save row`.  
  <img width="1506" alt="image" src="https://user-images.githubusercontent.com/35857179/194795353-2d90316f-a5e4-41af-8931-20b3c6ed08dc.png">
- After saving it will be there on your table.  
  <img width="620" alt="image" src="https://user-images.githubusercontent.com/35857179/194795402-d7c26ced-a009-43d9-a4a4-e3c2653225f0.png">

### Row Add (Using Table Row at bottom of page)

- Click the bottom row of the table `+ Add new row`.
  <img width="545" alt="image" src="https://user-images.githubusercontent.com/35857179/189079815-9a7ea5e3-4eb7-452e-99a8-78c271f2ad1f.png">
- A new empty row will be created
  <img width="567" alt="image" src="https://user-images.githubusercontent.com/35857179/189080009-3aeb70b4-92b0-4702-acb9-e5e52e31855e.png">

### Row Add (Pressing Enter Key from Previous Row)

When you finish editing a cell and press Enter, the cell in the next row with the same column will be highlighted.

![image](https://user-images.githubusercontent.com/35857179/203271676-bab64ca4-e0e4-4deb-9a62-609a97158911.png)

### Row Edit

You can start editing by any of the following methods  
  - Double click on cell to edit  
  - Click on cell and start typing (this way it will clear the previous content)  
  - Click on cell and press enter to start editing
- And it will automatically save on blur event or if inactive.  

### Row Delete

Right-click on anywhere in the row and then from the context menu select `Delete Row` option.  

Bulk delete is also possible by selecting multiple rows by using the checkbox in first column and then `Delete Selected Rows` options from the right click context menu.

<img width="568" alt="image" src="https://user-images.githubusercontent.com/35857179/189081764-9f13c286-e02a-40d0-93ea-4b1362d96827.png">

## Quick Import

You can use Quick Import when you have data from external sources such as Airtable, CSV file or Microsoft Excel to an existing project by either 

- Hover `Add new table` button in table menu, click three dots, use Quick Import to create
- Drag and drop CSV, JSON or Excel file to import

<img width="1505" alt="image" src="https://user-images.githubusercontent.com/35857179/194795025-afd81191-4743-435b-b802-88367d2663f9.png">

### Import Airtable into an Existing Project

- See <a href="./import-airtable-to-sql-database-within-a-minute-for-free">here</a>

### Import CSV data into an Existing Project

- Hover `Add new table` button in table menu, click three dots, and click `CSV file`
- Drag & drop or select files (at most 5 files) to upload or specify CSV file URL, and Click Import
  - **Auto-Select Field Types**: If it is checked, column types will be detected. Otherwise, it will default to `SingleLineText`.
  - **Use First Row as Headers**: If it is checked, the first row will be treated as header row.
  - **Import Data**: If it is checked, all data will be imported. Otherwise, only table will be created.
  ![image](https://user-images.githubusercontent.com/35857179/197454479-1ed18dce-1d0b-4ee3-88b3-9b6a132dea2a.png)
- You can revise the table name by double clicking it, column name and column type. By default, the first column will be chosen as <a href="./display-value" target="_blank">Display Value</a> and cannot be deleted.
  ![image](https://user-images.githubusercontent.com/35857179/197454633-5b30323e-2b13-4c55-843a-948c093d373e.png)
- Click `Import` to start importing process. The table will be created and the data will be imported.
  ![image](https://user-images.githubusercontent.com/35857179/197455547-2d93df5e-a7f0-4c88-af53-990067625967.png)

### Import Excel data into an Existing Project

- Hover `Add new table` button in table menu, click three dots, and click `Microsoft Excel`
- Drag & drop or select file (at most 1 file) to upload or specify Excel file URL and Click Import.
  - **Auto-Select Field Types**: If it is checked, column types will be detected. Otherwise, it will default to `SingleLineText`.
  - **Use First Row as Headers**: If it is checked, the first row will be treated as header row.
  - **Import Data**: If it is checked, all data will be imported. Otherwise, only table will be created.
  ![image](https://user-images.githubusercontent.com/35857179/197455788-8dd8a7d1-38f3-48c3-a05e-6ab0cf25045c.png)
- You can revise the table name, column name and column type. By default, the first column will be chosen as <a href="./display-value" target="_blank">Display Value</a> and cannot be deleted.
  <alert>
  Note: If your Excel file contains multiple sheets, each sheet will be stored in a separate table.
  </alert>

  <img width="1449" alt="image" src="https://user-images.githubusercontent.com/35857179/194795771-77963196-8e10-4f45-b605-eb1089d6bc9b.png">
- Click `Import` to start importing process. The table(s) will be created and the data will be imported to the corresponding table(s).
  <img width="1508" alt="image" src="https://user-images.githubusercontent.com/35857179/194795789-80366467-9778-464b-bce0-a5c0dfe97522.png">

## Export Data

You can export your data from a table as a CSV file by clicking the down arrow next to Table name and hover on `Download`. Currently only CSV and XLSX formats are supported for export.

<img width="660" alt="image" src="https://user-images.githubusercontent.com/35857179/194795866-a2db2a9b-d8e3-43f2-aec5-085e1932a0a5.png">

## Import Data

You can import your data in CSV format to a table by clicking the down arrow next to Table name and hover on `Upload`. Currently only CSV format is supported for upload.

<img width="668" alt="image" src="https://user-images.githubusercontent.com/35857179/194795880-60bf2003-0bef-45cd-aafa-1b97adb75d42.png">

