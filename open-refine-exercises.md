# Exercises for Open Refine: 

## Exercise 1: Create your first Open Refine project (using provided data)
There are several options for getting your data set into OpenRefine. You can upload or import files in a variety of formats including:

* TSV (tab-separated values)
* CSV (comma-separated values)
* Excel
* JSON (javascript object notation)
* XML
* Google Spreadsheet

To import the data for the exercises below, run OpenRefine. *NOTE: If Open Refine does not open in a browser window, open your browser and type the address http://127.0.0.1:3333/ to take you to the Open Refine interface.*
* Locate the file called which you previously downloaded 'doaj-article-sample.csv' (your downloaded this yesterday!)
* Click 'Next'

The next screen gives you some options to ensure that the data gets imported into OpenRefine correctly. The options vary depending on the type of data you are importing.

In this case you need to:

* Set the 'Character encoding' to 'UTF-8'
* Ensure the first row is used to create the column headings
* Make sure OpenRefine doesn't try to automatically detect numbers and dates

Once you are happy click 'Create Project >>'

### Going Further
* Look at the other options on the Import screen - try changing some of these options and see how that changes the Preview and how the data appears after import.
* Do you have access to JSON or XML data? If so the first stage of the import process will prompt you to select a 'record path' - that is the parts of the file that will form the data rows in the OpenRefine project.

## Exercise 2: Which licences are used for articles in this file?
* Create a facet for the 'Licence' column
* What is the most common Licence in the file?
* How many articles in the file don't have a licence assigned?

## Exercise 3: Find all publications without a DOI
* Use the 'Facet by blank' function to find all publications in this data set without a DOI

## Exercise 4: Correct the Language values via a facet
* Create a Text facet on the Language column
* Notice that there is both 'EN' and 'English'
* Put the mouse over the 'English' value
* Click 'Edit'
* Type 'EN' and click 'Apply'
* See how the Language facet updates

## Exercise 5: Split author names into separate cells
If you look at the Author column you should be able to see that there are multiple names in each cell separated by the pipe symbol "|". To work with the author names effectively we need to split them into separate cells:

* Click the dropdown menu at the top of the Author column
* Choose 'Edit cells->Split multi-valued cells'
* In the prompt type the "\|" symbol and click 'OK'
    * Note that the rows are still numbered sequentially
* Click the 'Records' option to change to Records mode
    * Note how the numbering has changed - indicating that several rows are related to the same record

## Exercise 6: Use Clustering to clean up author data
* Choose 'Edit cells->Cluster and edit' from the author column (which should be split into individual values from the last exercise)
* Using the 'key collision' method with the 'fingerprint' Keying Function work through the clusters of values, merging them to a single value where appropriate
* Try changing the clustering method being used - which ones work well?

## Exercise 7: Correct Publisher data
* Create a text facet on the Publisher column
* Note that in the values there are two that look identical - why does this value appear twice?
* On the publisher column use the dropdown menu to select 'Edit cells->Common transforms->Trim leading and trailing whitespace'
* Look at the publisher facet now - has it changed? (if it hasn't changed try clicking the Refresh option to make sure it updates)

## Exercise 8: Put titles into Title Case
* Facet by publisher
* Select "Akshantala Enterprises" and "Society of Pharmaceutical Technocrats"
    * To select multiple values in the facet use the 'Include' link that appears to the right of the facet
* See that the Titles for these are all in uppercase
* Click the dropdown menu on the Title column
* Choose 'Edit cells->Transform...'
* In the Expression box type ```value.toTitlecase()```
* In the Preview note that you can see what the affect of running this will be
* Click 'OK'

## Exercise 9: Reformat the Date
* Make sure you remove all Facets and Filters
* On the Date column use the dropdown menu to select 'Edit cells->Common transforms->To date'
* Note how the values are now displayed in green and follow a standard convention for their display format (ISO8601) - this indicates they are now stored as date data types in OpenRefine. We can now carry out functions that are specific to Dates
* On the Date column dropdown select 'Edit column->Add column based on this column'. Using this function you can create a new column, while preserving the old column
* In the 'New column name' type "Formatted Date"
* In the 'Expression' box type the GREL expression ```value.toString("dd MMMM yyyy")```

## Exercise 10: Reverse author names
In this exercise we are going to use both the Boolean and Array data types.
If you look at the Authors column, you can see that most of the author names are written in the natural order. However, a few have been reversed to put the family name first.

We can do a crude test for reversed author names by looking for those that contain a comma:

* Make sure you have already split the author names into individual cells using 'Edit cells->Split multi-valued cells' (you should have done this in exercise 5)
* On the Authors column, use the dropdown menu and select 'Facet->Custom text facet...'
    * The Custom text facet function allows you to write GREL functions to create a facet
* In the Expression box type ```value.contains(",")```
* Click 'OK'
* Since the 'contains' function outputs a Boolean value, you should see a facet that contains 'false' and 'true'. These represent the outcome of the expression, i.e. true = values containing a comma; false = values not containing a comma
* In this facet select 'true' to narrow down to the author names that contain a comma

Now we have narrowed down to the lines with a comma in a name, we can use the 'match' function. The match function allows you to use regular expressions, and output the capture groups as an array, which you can then manipulate.

* On the Authors column use the dropdown menu and select 'Edit cells->Transform'
* In the Expression box type ```value.match(/(.*),(.*)/)```  The "/",  means you are using a regular expression inside a GREL expression. The parentheses indicate you are going to match a group of characters. The ".*" expression will match any character from 0, 1 or more times. So here we are matching any number of characters, a comma, and another set of any number of characters.
* See how this creates an array with two members in each row in the Preview column

To get the author name in the natural order you can reverse the array and join it back together with a space to create the string you need:

* In the Expression box, add to the existing expression until it reads ```value.match(/(.*),(.*)/).reverse().join(" ")```
* In the Preview view you should be able see this has reversed the array, and joined it back into a string
* Click 'OK'

## Exercise 11: Retrieving journal details from CrossRef via ISSN
Because retrieving data from external URLs takes time, this exercise targets a single line in the data. In reality you would want to run this over many rows (and probably go and do something else while it ran)

* Select a single row from the data set which contains an ISSN by:
    * Clicking the star icon for the relevant row in the first column
    * Facet by Star
    * Choose the single row
* In the ISSN column use the dropdown menu to choose 'Edit column->Add column by fetching URLs'
* Give the column a name e.g. "Journal details"
* In the expression box you need to write some GREL where the output of the expression is a URL which can be used to retrieve data (the format of the data could be HTML, XML, JSON, or some other text format)

In this case we are going to use the CrossRef api (read more about the CrossRef service at [http://www.crossref.org](http://www.crossref.org), read more about the API we are going to use at [https://github.com/CrossRef/rest-api-doc/blob/master/rest_api.md)](https://github.com/CrossRef/rest-api-doc/blob/master/rest_api.md))

The syntax for requesting journal information from CrossRef is ```http://api.crossref.org/journals/{ISSN}``` where {ISSN} is replaced with the ISSN of the journal

* In the expression box type the GREL ```"http://api.crossref.org/journals/"+value```
* Click 'OK'

You should see a message at the top on the OpenRefine screen indicating it is fetching some data, and how far it has got. Wait for this to complete. Fetching data for a single row should take only ten seconds or so, but fetching data for all rows will take longer. You can speed this up by modifying the "Throttle Delay" setting in the 'Add column by fetching URLs' dialog which controls the delay between each URL request made by OpenRefine. This is defaulted to a rather large 5000 milliseconds (5 seconds).

At this point you should have a new cell containing a long text string in a format called 'JSON' (this stands for JavaScript Object Notation, although very rarely spelt out in full).

OpenRefine has a function for extracting data from JSON (sometimes referred to as 'parsing' the JSON). The 'parseJson' function is explained in more detail at [https://github.com/OpenRefine/OpenRefine/wiki/GREL-Other-Functions](https://github.com/OpenRefine/OpenRefine/wiki/GREL-Other-Functions).

* In the new column you've just added use the dropdown menu to access 'Edit column->Add column based on this column'
* Add a name for the new column e.g. "Journal Title"
* In the Expression box type the GREL ```value.parseJson().message.title```
* You should see in the Preview the Journal title displays

The reason for using 'Add column based on this column' is simply that this allows you to retain the full JSON and extract further data from it if you need to. If you only wanted the title and did not need any other information from the JSON you could use 'Edit cells->Transform...' with the same GREL expression.

## Exercise 12: Reconcile Publisher names with VIAF IDs
In this exercise you are going to use the VIAF Reconciliation service written by [Jeff Chiu](https://twitter.com/absolutelyjeff). Jeff offers two ways of using the reconciliation service - either via a public service he runs at [http://refine.codefork.com/](http://refine.codefork.com/), or by installing and running the service locally using the instructions at [https://github.com/codeforkjeff/refine_viaf](https://github.com/codeforkjeff/refine_viaf).

If you are going to do a lot of reconciliation, please install and run your own local reconciliation service - the instructions at [https://github.com/codeforkjeff/refine_viaf](https://github.com/codeforkjeff/refine_viaf) make this reasonably straightforward.

Once you have chosen which service you are going to use:

* In the Publisher column use the dropdown menu to choose 'Reconcile->Start Reconciling'
* If this is the first time you've used this particular reconciliation service, you'll need to add the details of the service now
    * Click 'Add Standard Service...' and in the dialogue that appears enter:
        * "http://refine.codefork.com/reconcile/viaf" for Jeff's public service
        * "http://localhost:8080/reconcile/viaf" if you are running the service locally
* You should now see a heading in the list on the left hand side of the Reconciliation dialogue called "VIAF Reconciliation Service"
* Click on this to choose to use this reconciliation service
* In the middle box in the reconciliation dialogue you may get asked what type of 'entity' you want to reconcile to - that is, what type of thing are you looking for. The list will vary depending on what reconciliation service you are using.
    * In this case choose "Corporate Name" (it seems like the VIAF Reconciliation Service is slightly intelligent about this and will only offer options that are relevant)
* In the box on the righthand side of the reconciliation dialogue you can choose if other columns are used to help the reconciliation service make a match - however it is sometimes hard to tell what use (if any) the reconciliation service makes of these additional columns
* At the bottom of the reconciliation dialogue there is the option to "Auto-match candidates with high confidence". This can be a time saver, but in this case you are going to uncheck it, so you can see the results before a match is made
* Now click 'Start Reconciling'

Reconciliation is an operation that can take a little time if you have many values to look up. However, in this case there are only 6 publishers to check, so it should work quite quickly.

Once the reconciliation has completed two Facets should be created automatically:
* Publisher: Judgement
* Publisher: best candidate's score

These are two of several specific reconciliation facets and actions that you can get from the 'Reconcile' menu (from the column drop down menu).

* Close the 'Publisher: best candidate's score' facet, but leave the 'Publisher: Judgement' facet open

If you look at the Publisher column, you should see some cells have found one or more matches - the potential matches are show in a list in each cell. Next to each potential match there is a 'tick' and a 'double tick'. To accept a reconciliation match you can use the 'tick' options in cells. The 'tick' accepts the match for the single cell, the 'double tick' accepts the match for all identical cells.

* Create a text facet on the Publisher column
* Choose 'International Union of Crystallography'

In the Publisher column you should be able to see the various potential matches. Clicking on a match will take you to the VIAF page for that entity.

* Click a 'double tick' in one of the Publisher column cells for the option "International Union of Crystallography"
* This will accept this as a match for all cells - you should see the other options all disappear
* Check the 'Publisher: Judgment' facet. This should now show that 858 items are 'matched' (if this does not update, try refreshing the facets)

We could do these one by one, but if we are confident with matches, there is an option to accept all:

* Remove all filters/facets from the project so all rows display
* In the Publisher column use the dropdown menu to choose 'Reconcile->Actions->Match each cell to its best candidate'

There are two things that reconciliation can do for you. Firstly it gets a standard form of the name or label for the entity. Secondly it gets an ID for the entity - in this case a VIAF id. This is hidden in the default view, but can be extracted:

* In the Publisher column use the dropdown menu to choose 'Edit column->Add column based on this column...'
* Give the column the name 'VIAF ID'
* In the GREL expression box type ```cell.recon.match.id```
* This will create a new column that contains the VIAF ID for the matched entity
