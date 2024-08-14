# Datatable As Related List

This example is in response to a [question I posted on Stack Exchange](https://salesforce.stackexchange.com/questions/418808/update-records-updated-in-flexcard-datatable/418928#418928) with regards to using Datatable events to update related records.

The repository for the question can be found [here](https://github.com/sfadriaan/Stack-Exchange-Questions/tree/main/418808)

So it seems the `update` event will not work for the scenario where you want to use the datatable element to edit records which then gets updated in the database afwards. 

I created a flexcard that basically showcases what each datatable event does:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/datatable-as-related-list/images/datatable-event-examples.gif)

You can find the metadata for the above Flexcard under `force-app/main/default/omniUiCard/DatatableEventExamples`.

A summary of each event that I tested:

* `rowclick` captures the row column values with `{action.result.FIELD_NAME}`. You can click anywhere on the row and it will return that row's information. In the GIF you can see how the `recordId` gets updated for "Clicked On RecordId"
* `selectrow` returns the last selected row's details and each field's value can be accessed with `{action.result.FIELD_NAME}`.
* `update` fires when the data in the datatable changes. It does not return the values of the row that was updated, or the updated `{records}` merge field

An interesting thing I picked up is that the `{records}` list does not get updated when you update a cell value in the datatable. 

~~Also, I added in the event listener for `rowclick` to write the row values to `Session.FIELD_NAME`, but when I update a value and press the checkmark for row level edit (or click out of the cell I just updated for cell level edit), the updated value does not get written to its designated place.~~

**UPDATE:** I am now able to write the value of `{action.result.FIELD_NAME}` to a JSON property e.g. `Session.FIELD_NAME`.

This lets me assume that you actually cannot commit any changes made in the datatable to the database with the datatable alone.

As such I used the `rowclick` event to write the `recordId` to `Session.Id` and then launch a flyout Omniscript. 
The OS uses the `recordId` to get the relevant fields for that `recordId` and displays it for editing by the user. 
The user can then make the changes, save the record and the flexcard gets reloaded to display the newest changes:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/datatable-as-related-list/images/datatable-as-related-list.gif)

My conclusion is thus:

* **Flexcards** are used to display data and enables a user to take certain actions (such as click on a record to edit it, or press a button to launch an Omniscript).
* **Omniscripts** are used to guide the user through a process which can be capturing/updating information.