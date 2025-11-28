# Project Movie Dashboard
## Table of Content
[Problem Statement](#problem-statement)
[Data Sourse](#data-sourse)
[Tools](#tools)
[Dashboard](#dashboard)
[M Code](#m-code)
[Recomendations](#recomendations)
###  Problem Statement
Netflix wants to better understand which movie they should produce next, including the most suitable actors and directors. We have a dataset containing movie budgets, box office performance, actors, directors, and genres. Your task is to build an Excel dashboard that provides insights into this dataset. The dashboard should help identify:
- The best-performing actors
- The top movies based on box office metrics
- Director performance
- Genre trends
- Seasonal patterns in movie performance
- Any additional insights that can guide future production decisions
  
The final dashboard should be clear, interactive, and visually compelling, enabling Netflix to make data-driven decisions.
###  Data Sourse
Movie Data : The primary dataset used for this analysis is the "Movie Data Homework.xlsx" file, containing detailed information about each movie's performance (box office and budget), actors, directors and genres. 
You can download the original datasource here: [Movie Dataset Excel file](https://github.com/user-attachments/files/23812159/Movies_Data_Homework.xlsx)


###  Tools
1. Power Query - I used Power Query for Data Cleaning
2. Excel - I used Excel for Data Analysis
3. Pivot Tables - for Creating the dashboard and Visualizations

###  Data Cleaning
- Data loading and inspection.
- Handling errors, missing values.
- Data cleaning and formatting. The excel file after the data cleaning & preparation process can be downloaded here - [Movies Dashbord](https://github.com/user-attachments/files/23812101/MOVIE.DATA.DASHBOARD.xlsx)

### Dashboard
<img width="1165" height="783" alt="Screenshot 2025-11-27 at 5 14 38 PM" src="https://github.com/user-attachments/assets/8d049843-46e5-4384-afca-b44b0612fbdf" />


### M Code
```
let
    Source = Excel.Workbook(File.Contents("D:\РАБОТА\Data Analys\Movies_Data_Homework.xlsx"), null, true),
    #"Movie Data_Sheet" = Source{[Item="Movie Data",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(#"Movie Data_Sheet", [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Movie Title", type text}, {"Release Date", type date}, {"Wikipedia URL", type text}, {"Genre_First_ID", Int64.Type}, {"Genre_Second_ID", Int64.Type}, {"Director_First_ID", Int64.Type}, {"Cast_First_ID", Int64.Type}, {"Cast_Second_ID", Int64.Type}, {"Cast_Third_ID", Int64.Type}, {"Cast_Fourth_ID", Int64.Type}, {"Cast_Fifth_ID", Int64.Type}, {"Budget ($)", Int64.Type}, {"Box Office Revenue ($)", type number}, {"Column14", type any}, {"Column15", type any}, {"Column16", type any}, {"Column17", type any}, {"Column18", type any}, {"Column19", type any}, {"Column20", type any}, {"Column21", type any}}),
    #"Merged Queries" = Table.NestedJoin(#"Changed Type", {"Genre_First_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
    #"Expanded Genres" = Table.ExpandTableColumn(#"Merged Queries", "Genres", {"Genre"}, {"Genres.Genre"}),
    #"Removed Columns" = Table.RemoveColumns(#"Expanded Genres",{"Column14", "Column15", "Column16", "Column17", "Column18", "Column19", "Column20", "Column21"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Columns",{{"Genres.Genre", "Genres"}}),
    #"Reordered Columns" = Table.ReorderColumns(#"Renamed Columns",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres", "Genre_Second_ID", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries1" = Table.NestedJoin(#"Reordered Columns", {"Genre_Second_ID"}, Genres, {"ID"}, "Genres.1", JoinKind.LeftOuter),
    #"Expanded Genres.1" = Table.ExpandTableColumn(#"Merged Queries1", "Genres.1", {"Genre"}, {"Genres.1.Genre"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Expanded Genres.1",{{"Genres.1.Genre", "Genres.1"}}),
    #"Reordered Columns1" = Table.ReorderColumns(#"Renamed Columns1",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres", "Genre_Second_ID", "Genres.1", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries2" = Table.NestedJoin(#"Reordered Columns1", {"Director_First_ID"}, Directors, {"ID"}, "Directors", JoinKind.LeftOuter),
    #"Expanded Directors" = Table.ExpandTableColumn(#"Merged Queries2", "Directors", {"Director"}, {"Directors.Director"}),
    #"Renamed Columns2" = Table.RenameColumns(#"Expanded Directors",{{"Directors.Director", "Director"}}),
    #"Reordered Columns2" = Table.ReorderColumns(#"Renamed Columns2",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres", "Genre_Second_ID", "Genres.1", "Director_First_ID", "Director", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries3" = Table.NestedJoin(#"Reordered Columns2", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors" = Table.ExpandTableColumn(#"Merged Queries3", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns3" = Table.RenameColumns(#"Expanded Actors",{{"Actors.Actor", "Actors"}}),
    #"Reordered Columns3" = Table.ReorderColumns(#"Renamed Columns3",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres", "Genre_Second_ID", "Genres.1", "Director_First_ID", "Director", "Cast_First_ID", "Actors", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries4" = Table.NestedJoin(#"Reordered Columns3", {"Cast_Second_ID"}, Actors, {"ID"}, "Actors.1", JoinKind.LeftOuter),
    #"Expanded Actors.1" = Table.ExpandTableColumn(#"Merged Queries4", "Actors.1", {"Actor"}, {"Actors.1.Actor"}),
    #"Renamed Columns4" = Table.RenameColumns(#"Expanded Actors.1",{{"Actors.1.Actor", "Actors_Second_cast"}}),
    #"Reordered Columns4" = Table.ReorderColumns(#"Renamed Columns4",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres", "Genre_Second_ID", "Genres.1", "Director_First_ID", "Director", "Cast_First_ID", "Actors", "Cast_Second_ID", "Actors_Second_cast", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Added Custom" = Table.AddColumn(#"Reordered Columns4", "ROI", each ([#"Box Office Revenue ($)"]-[#"Budget ($)"])/[#"Budget ($)"]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"ROI", Percentage.Type}})
in
    #"Changed Type1"
```
### Recomendations
Top 5 genres are Action, Comedy, etc. I would recommend Netflix to produce a movie with one of these genres as they brought in more in box office revenie based on the data from 2012 to 2016
