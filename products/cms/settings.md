# Settings

## Logs <a href="#logs" id="logs"></a>

#### The Audit Log functionality is a configurable feature in MapsIndoors, and if not enabled on your Solution, it can be on request. <a href="#logs" id="logs"></a>

The Audit Log feature can be used to access the Audit log on MapsIndoors data objects, i.e. you can find or inspect the change history.

The Log functionality is found in the Logs tab under Settings in the CMS.

The Log can be filtered on date, ID, User and Data object types. When pressing Download, a comma separated file (csv) will be stored on your computer's hard drive.

The log will be in CSV format, which can be opened by a spreadsheet program eg. Excel. Each log entry represents a modification in the data. In short, each entry tells at what time who did what to which object.

The headers represented are: Time, User, Action, ObjectType, ObjectId and ObjectData

* **Time:** Tells which user did the change (The email representing the user logged in to the system)
* **Action:** Tells what happened:
  * If data was added, the 'Action' will be set to 'Created'
  * If data was changed, the 'Action' will be set to 'Changed'
  * If data was deleted, the 'Action' will be set to 'Deleted'
* **ObjectType:** Tells what type of data was modified (eg. ‘building', ‘location', ‘user', ‘graphdata' ... )
* **ObjectId:** Is a unique ID that represents the given data - Building, Location or whatever it is.
* **ObjectData:** Is a JSON formatted representation of the actual data stored in the MapsIndoors system. To see what changed you can compare this data to the previous change.

Examples of use cases could be:

* **How to do I get Network history?** Filter the ObjectType to ‘graphdata' to see these entries. To find a particular user history, filter ‘User' to their email as well.
* **How to do I get Categories history?** Filter the ObjectType to ‘category' to see these entries. To find a particular data history, filter ‘User' to their email as well.
* **How to do I get Location Type/type template/visibility history?** Filter the ObjectType to ‘locationtype' to see these entries. To find a particular data history, filter ‘User' to their email as well. The type template is covered in the "LocationTypeField” section of the data. The visibility is covered in the "displayrule” section of the data
* **How to find user login activity?** When a user logs in, the corresponding user object will be changed too (the ‘last login field will be updated'). Filter the ObjectType to ‘user' to see login entries. To find a particular user login history, filter ‘User' to their email as well.

***

## Users

Allows Admins to control who has access, and what roles they have, as well as create new users.

A list appears with all users for the Solution. You can also search for an email via the search box in the upper right.

**Editing a user:** Click on the pencil to the left of the email. The user can be deleted, changed role or added to a Solution. Click Save.

**New user:** Click on new user bottom at top. Enter email, choose Editor or Administrator, and assign the appropriate Solution. Click Create.
