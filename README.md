# Akana API HOOK
![Image of Akana] 
(https://www.akana.com/img/formerlyLOGO8.png) 
[Akana.com](http://akana.com)

## Google-Drive-API-Hook
API Hook that takes a string and inserts it to a new file created in a folder called "Leads" in Google Drive
## Google Drive API 
### About the API
- Access, edit, and save your Google App's files to Google Drive.
- API Documentation: [Google Drive API docs] (https://developers.google.com/drive/v2/reference/)

### Pre-Reqs
- you must install the pso extensions custom polices:
    + unzip the com.soa.pso.openapi.extensions_7.2.2.zip (available in this repository) into the <Policy Manager Home>/sm70 directory. This will result in files placed in the sm70/lib/pso.opeapi.extensions_7.2.2 subdirectory
    + restart both PM and ND(s)
    + Using the SOA Admin Console, install the following features in each PM container:
        * SOA Professional Services OpenAPI Extensions
        * SOA Professional Services OpenAPI Extensions UI
    + Using the SOA Admin Console, install the following features in each ND container:
        * SOA Professional Services OpenAPI Extensions
- you must install the com.soa.pso.policy.JWTToken_1.0.0.zip nto the <Policy Manager Home>/sm70 directory. This will result in files placed in the sm70/lib/pso.policy.JWTToken_1.0.0 subdirectory
    + restart ND(s) only
    +  Using the SOA Admin Console, on each ND:
        * select the Insert JWT as HTTP Header Policy Handler feature  
        * press the "install" button
        * follow the install wizard instructions and restart the ND
- This will only work for [Google Apps for work] (https://www.google.com/intx/en_au/work/apps/business/?utm_source=google&utm_medium=cpc&utm_campaign=japac-smb-apps-bkws-au-en&utm_content=gafb&utm_term=google%20apps&gclid=CPGWm82o5cMCFU06vAod9kcAOA&gclsrc=ds). You, or your organisation, must have a subscription to this service. The reason for this is that this is the only service that Google provides 2-legged OAuth to (via a service account). Google uses only 3-legged OAuth with its free and open Google Docs. 3-legged OAuth is unsuitable to server to server integration.
- Register the application in the [Google Developers Console] (https://console.developers.google.com/). Creating an account if you have not already registered.
- once the Project is defined, double click on it to be taken the the Project details page. 
- Click on the "Credentials" left hand menu item
- When the Credentials Portlet is displaid, click on the "Create new Client ID" button. then select the "Service Acccount" for the CLient ID type. (note: you must be the Google Apps sdministrator to do this).
- once this is done you should have a new section in the Credentials Portlet for the Service Account.
- Copy the clientId of the Service Account
- login to the  [Google Apps for work Admin Console] (https://admin.google.com) select the security icon, scroll to the botom of the admin page and select the "Show more" link, select the "Advanced Settings" link that appears, then the "Manage API client access" link.
- in the "Client Name" field paste the Service Account ClientId you coppied, then copy this list of API scopes into the Scopes field and click the "Authorize" button:
    https://spreadsheets.google.com/feeds,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/drive.appdata,https://www.googleapis.com/auth/drive.apps.readonly,https://www.googleapis.com/auth/drive.file,https://www.googleapis.com/auth/drive.metadata.readonly,https://www.googleapis.com/auth/drive.readonly

### Getting Started Instructions
#### Download and Import
- Download GoogleDriveAPIHook.zip
- Download the migrations.properties file, and edit it to replace the <replace this with your key> text with the "Container Key" of the ND or ND cluster in your target PM.
    - the container key is found by going to the "Deatils Tab" of the ND cluster, or ND defined in the Policy Manager Console, then looking at the " Container Overview" tab on that page, and copying the "Container Key:" value. ![container key screenshot](https://github.com/pogo61/Google-Sheets-API-Integration/blob/master/Screen%20Shot%202015-03-18%20at%2011.24.45%20am.png "ND Container Key")
- Login to PolicyManager  example: http://localhost:9900
- Select the root "Registry" organisation and click on the "Import Package" from the Actions navigation window on the right side of the screen
  - click on button to browse for the GoogleDriveAPIHook.zip archive file 
  - make sure select the migrations.properties file 
  - click Okay to start the importation of the hook.
- this will create a "Google Drive API Hook" Organisation with the requisite artefacts needed to run the API.

#### Verify Import
- Expand the services folder in the Google Sheets API Hook you imported and find Google_Drive_API_Hook VS

#### Activate Anonymous Contract
- Expand the contracts folder in the Google Drive API Hook you imported and find the "Anonymous" contract under the "Provided Contracts" folder
- click on the "Activate Contract" workflow activity in the righ-hand Activities portlet
- ensure that the status changes to "Workflow Is Completed"

#### Configure Security
- Go to Google Drive API Hook -> Policies -> Operational Policies -> Insert JWT into Downstream request policy
    - Click "modify" in the XML Policy Tab. An XML Policy Content editor dialog will be displayed.
    - change the value of the tns:fileLocation element to be the location and name of the file containing the private key of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/)). Note that the location must be absolute, not relative.
    - change the value of the tns:serviceAccountEmail element to be the email address of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/).
    - change the value of the tns:userAccountEmail element to be the email address of the person who's spreadsheets the API is going to use (this must be a user in the Google Apps for work domain that the administrator has created).
    - save the changes
    - click on the "Activate Policy" workflow activity in the righ-hand Activities portlet
    - ensure that the status changes to "State: Active"
- Go to Google Sheets API Hook -> Policies -> Operational Policies ->    GetAuthToken policy
    - click on the "Activate Policy" workflow activity in the righ-hand Activities portlet
    - ensure that the status changes to "State: Active"


#### Verify Connectivity
- Using  curl http://<ND host URL>/google_drive/helloworld
- The correct response should be something like (this gets the metadata for your google drive):
    {
    "additionalRoleInfo": [
        {
            "roleSets": [
                {
                    "additionalRoles": [
                        "commenter"
                    ],
                    "primaryRole": "reader"
                }
            ],
            "type": "application/vnd.google-apps.drawing"
        },
        {
            "roleSets": [
                {
                    "additionalRoles": [
                        "commenter"
                    ],
                    "primaryRole": "reader"
                }
            ],
            "type": "application/vnd.google-apps.document"
        },
        {
            "roleSets": [
                {
                    "additionalRoles": [
                        "commenter"
                    ],
                    "primaryRole": "reader"
                }
            ],
            "type": "application/vnd.google-apps.presentation"
        },
        {
            "roleSets": [
                {
                    "additionalRoles": [
                        "commenter"
                    ],
                    "primaryRole": "reader"
                }
            ],
            "type": "*"
        },
        {
            "roleSets": [
                {
                    "additionalRoles": [
                        "commenter"
                    ],
                    "primaryRole": "reader"
                }
            ],
            "type": "application/vnd.google-apps.spreadsheet"
        },
        {
            "roleSets": [],
            "type": "application/vnd.google-apps.*"
        }
    ],
    "domainSharingPolicy": "allowedWithWarning",
    "etag": "\"tlWfk03TbBHKoqyGbIYE6wA918U/F4fORwPECwGSOraEh0zn2nXtufU\"",
    "exportFormats": [
        {
            "source": "application/vnd.google-apps.drawing",
            "targets": [
                "image/svg+xml",
                "image/jpeg",
                "image/png",
                "application/pdf"
            ]
        },
        {
            "source": "application/vnd.google-apps.presentation",
            "targets": [
                "application/pdf",
                "text/plain",
                "application/vnd.openxmlformats-officedocument.presentationml.presentation"
            ]
        },
        {
            "source": "application/vnd.google-apps.document",
            "targets": [
                "application/vnd.oasis.opendocument.text",
                "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
                "application/rtf",
                "text/html",
                "application/pdf",
                "text/plain"
            ]
        },
        {
            "source": "application/vnd.google-apps.spreadsheet",
            "targets": [
                "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
                "application/x-vnd.oasis.opendocument.spreadsheet",
                "text/csv",
                "application/pdf"
            ]
        },
        {
            "source": "application/vnd.google-apps.form",
            "targets": [
                "application/zip"
            ]
        },
        {
            "source": "application/vnd.google-apps.script",
            "targets": [
                "application/vnd.google-apps.script+json"
            ]
        }
    ],
    "features": [
        {
            "featureName": "ocr"
        },
        {
            "featureName": "translation",
            "featureRate": 2
        }
    ],
    "folderColorPalette": [
        "#ac725e",
        "#d06b64",
        "#f83a22",
        "#fa573c",
        "#ff7537",
        "#ffad46",
        "#fad165",
        "#fbe983",
        "#b3dc6c",
        "#7bd148",
        "#16a765",
        "#42d692",
        "#92e1c0",
        "#9fe1e7",
        "#9fc6e7",
        "#4986e7",
        "#9a9cff",
        "#b99aff",
        "#a47ae2",
        "#cd74e6",
        "#f691b2",
        "#cca6ac",
        "#cabdbf",
        "#8f8f8f"
    ],
    "importFormats": [
        {
            "source": "application/vnd.sun.xml.writer",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "image/pjpeg",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.ms-excel",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "text/csv",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "image/x-png",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/x-vnd.oasis.opendocument.text",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.presentationml.template",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.presentationml.presentation",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "application/vnd.oasis.opendocument.presentation",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/vnd.google-apps.script+json",
            "targets": [
                "application/vnd.google-apps.script"
            ]
        },
        {
            "source": "application/vnd.ms-powerpoint",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/vnd.ms-powerpoint.template.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.wordprocessingml.template",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "image/png",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.oasis.opendocument.spreadsheet",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "application/vnd.ms-powerpoint.slideshow.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "text/rtf",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.ms-word.template.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "image/gif",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/msword",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "text/html",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.oasis.opendocument.text",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.spreadsheetml.template",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "text/richtext",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/x-vnd.oasis.opendocument.spreadsheet",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "image/x-bmp",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "image/jpeg",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.ms-powerpoint.presentation.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "text/plain",
            "targets": [
                "application/vnd.google-apps.document",
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "application/vnd.ms-word.document.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "text/tab-separated-values",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "application/x-vnd.oasis.opendocument.presentation",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "image/bmp",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.google-apps.script+text/plain",
            "targets": [
                "application/vnd.google-apps.script"
            ]
        },
        {
            "source": "image/jpg",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/json",
            "targets": [
                "application/vnd.google-apps.script"
            ]
        },
        {
            "source": "application/rtf",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/vnd.openxmlformats-officedocument.presentationml.slideshow",
            "targets": [
                "application/vnd.google-apps.presentation"
            ]
        },
        {
            "source": "application/pdf",
            "targets": [
                "application/vnd.google-apps.document"
            ]
        },
        {
            "source": "application/x-msmetafile",
            "targets": [
                "application/vnd.google-apps.drawing"
            ]
        },
        {
            "source": "application/vnd.ms-excel.template.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        },
        {
            "source": "application/vnd.ms-excel.sheet.macroEnabled.12",
            "targets": [
                "application/vnd.google-apps.spreadsheet"
            ]
        }
    ],
    "isCurrentAppInstalled": false,
    "kind": "drive#about",
    "languageCode": "en-US",
    "largestChangeId": "17293",
    "maxUploadSizes": [
        {
            "size": "10485760",
            "type": "application/vnd.google-apps.document"
        },
        {
            "size": "104857600",
            "type": "application/vnd.google-apps.spreadsheet"
        },
        {
            "size": "104857600",
            "type": "application/vnd.google-apps.presentation"
        },
        {
            "size": "2097152",
            "type": "application/vnd.google-apps.drawing"
        },
        {
            "size": "5242880000000",
            "type": "application/pdf"
        },
        {
            "size": "5242880000000",
            "type": "*"
        }
    ],
    "name": "Paul Pogonoski",
    "permissionId": "03308875192974461129",
    "quotaBytesByService": [
        {
            "bytesUsed": "0",
            "serviceName": "DRIVE"
        },
        {
            "bytesUsed": "4620383988",
            "serviceName": "GMAIL"
        },
        {
            "bytesUsed": "0",
            "serviceName": "PHOTOS"
        }
    ],
    "quotaBytesTotal": "16106127360",
    "quotaBytesUsed": "1145328",
    "quotaBytesUsedAggregate": "4621529316",
    "quotaBytesUsedInTrash": "0",
    "quotaType": "LIMITED",
    "rootFolderId": "0AAPqj8BjYBX-Uk9PVA",
    "selfLink": "https://www.googleapis.com/drive/v2/about",
    "user": {
        "displayName": "Paul Pogonoski",
        "emailAddress": "paulpog@japarasolutions.com",
        "isAuthenticatedUser": true,
        "kind": "drive#user",
        "permissionId": "03308875192974461129",
        "picture": {
            "url": "https://lh5.googleusercontent.com/-09NG1fk2b-I/AAAAAAAAAAI/AAAAAAAAAD8/Oz8arKfe-AI/s64/photo.jpg"
        }
    }
}

### How Hello World Works
#### An Akana Integration Primer
The Google_Drive_API_Hook API is a "Virtual Service". That is, its interface is not that of a real service implementation. It can be a proxy to a "real" implementation, or it can be an aggregate (a combination) of a number of "real" implementations. In Policy Manager a "real" implementation is called a "Physical Service".
Apart from offering a different interface to the Physical Service, a Virtual Service offers the ability to attach Policies for security, logging, QoS, and a number of other non-functional capabilities.
Virtual Services also have the ability to have Custom Process and Scripts run before the Physical Service is called. Here is where a lot of the magic of Integration occurs.

#### Hello World
To create the helloworld operation in the Google_Drive_API_Hook VS (Virtual Service) the Google Drive API RAML was copied and the following was added to the copied RAML to create the Google Sheets API Hook RAML:  
    /helloworld:  
      &nbsp;get:  
        &nbsp;&nbsp;description: "returns all spreadsheets on your google drive"  
        &nbsp;&nbsp;&nbsp;responses:  
          &nbsp;&nbsp;&nbsp;&nbsp;200:  
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;body:  
              &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;application/atom+xml:  

Then a VS was created by using the RAML as the definition source.
Then all the Operations in the VS were mapped to the same operations in the Google Drive API PS (Physical Service), except the "helloworld" operation, which is mapped to the GET /about operation.

Go to the Google_Sheets_API_Hook VS -> Operations Tab -> GET /hellowworld operation -> Process tab you'll see this image:
![Helloworld process] 
(https://github.com/pogo61/Google-Drive-API-Hook/blob/master/Google%20Drive.png)

Double click on the Script activity and the invoke activity to see how these work to make the Hello World operation call successful.


### Create Your Own Integration with the Google Sheets API
The Hello World operation is one simple way of integrating or extending your API's.
Take a look at the [Google Sheets API Integration](https://github.com/pogo61/Google-Sheets-API-Integration). This will give you a deeper inderstanding of the richness of our gateway product in integrating to API's

### Modify and Build
In the event you need to change the API Hook.   Here are the instructions to do so. 

### License
Put a link to an open source license