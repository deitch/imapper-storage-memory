# imapper Storage Memory Plugin

This module provides a storage plugin for imapper where all of the folders and messages are stored in memory. That means that when you stop the process, they are lost *entirely*. This primarily is useful for testing.

## Usage
Install with `npm install`:

````bash
npm install imapper-storage-memory
````

and then require it:

````javascript
var storage = require('imapper-storage-memory'),
server = imapper({
	storage: storage
});
````

## Loading and Modifying Data
The memory storage plugin by default loads an "INBOX" with no messages, and a "" namespace with no folders. This leaves it clean for you to load data as you wish.

All IMAP commands executed will perform on the in-memory data. You can reset the data at any time by calling `reset()`:

````javascript
var storage = require('imapper-storage-memory'),
server = imapper({
	storage: storage
});
storage.reset();
````


To load specific data, you call the `load()` command with the desired data:

````javascript
var storage = require('imapper-storage-memory'),
server = imapper({
	storage: storage
});
storage.load(data);
````

The `data` provided is a javascript object. Each key is the name of a namespace. Under each namespace you can create folders. "INBOX" is special, in that it is both a namespace and a folder. 

### Namespaces
Namespaces are the root level of the data. Folders are contained only within namespaces, except for the "INBOX" namespace, which is both namespace and folder.

Namespaces have the following properties:

* `type`: What type of namespace this is, can be one of "personal", "shared", "users". Default is "personal".
* `separator`: What the separator between levels of folders is. Default is '/'. For example, if folder5 is a child of folder2, and the IMAP user will call them 'folder2/folder5', then the separator is '/'. '/' is common for mail folders, while '.' is common for newsgroups.
* `folders`: The folders in this namespace.


### Folders

Each folder is an object which has the following allowed properties as keys:

* `folders`: Object. Further subfolders in this folder or namespace.
* `messages`: Array. Message objects in this folder.
* `subscribed`: boolean. If the user subscribed to this folder using the IMAP `SUBSCRIBE` command. Default is `false`, except for INBOX, where the default is `true`.

Note that folders can be nested several levels deep, for example:

````json
{"":{
    "folders": {
      "level1": {
        "folders": {
          "level2": {
            "folders": {
              "level3": {
                "folders": {
                  "level4": {
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
````


### Messages

Each message in a folder's message array is an object, consisting of the following keys:

* `raw`: The raw text of the messages, in its entirety.
* `internaldate`: The internal date of the message, as stored by the IMAP server, in `DD-MMM-YYYY` format per RFC3501.
* `flags`: A string (for a single flag) or an array of strings (for multiple flags) stored on this message.


