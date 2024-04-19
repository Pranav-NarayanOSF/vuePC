Steps to do
Create a cloudpage which will be the index for the customer which has the following code

%%[ 
   SET @ENV_ROUTE = "view" 
   Output(ContentBlockByKey("pc-routing.ssjs")) 
]%%
Create the following folder structure for the preference structure in Content Builder

Open Screenshot 2023-07-27 at 1.29.08 pm.png
Screenshot 2023-07-27 at 1.29.08 pm.png
On the pc-index.html copy the following code



<!doctype html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link rel="icon" href="%%=v(@ENV_FAV_ICON)=%%">
    <title>%%=v(@APP_NAME)=%%</title>
    %%=v("<")=%%script defer="defer" src="data:text/javascript;base64,%%=Base64Encode(ContentBlockByKey("pc-vendor.js"))=%%"></script>
    %%=v("<")=%%script defer="defer" src="data:text/javascript;base64,%%=Base64Encode(ContentBlockByKey("pc-app.js"))=%%"></script>
    <style>%%=ContentBlockByKey("pc-vendor.css")=%%</style> 
    <style>%%=ContentBlockByKey("pc-app.css")=%%</style>
  </head>
  <style>
    body{
      max-width : 100%;
    }
  </style>
  <body>
    <noscript><strong>We're sorry but %%=v(@ENV_APP_NAME)=%% doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript>
    <div id="app"></div>
  </body>
</html>
Copy all the server ssjs files from the GitHub Repository into the server folder files on Content Builder

Create a JSON resource in the Preference Centre collection with the following code

Configure the Preference Centre - ENV file with your associated details

MID - The parent BU

JWT_SECRET - Create a symmetric key in every BU that you are going to implement the link of the preference center

PROCESS_ENDPOINT - The end point of the JSON resource created in step 5

CP_PC_VIEW - The CloudPage ID of the CP created in step 1

Configure the Preference Centre - Metadata file with all the labels and configurable items on the page

Make changes to the Preference Centre - Process File to the get and save function based on the required logic

 



%%[ 
   SET @ENV_ROUTE = "view" 
   Output(ContentBlockByKey("pc-routing.ssjs")) 
]%%
Upon first load of the preference centre, on the console, you will see the following statement

Open Screenshot 2023-07-27 at 1.39.35 pm.png
Screenshot 2023-07-27 at 1.39.35 pm.png
 

Clone the repository into your local machine

In the app folder of your local repository, ensure there are two files with names

.env.local

.env.production

If they are not there , create them using VSCode by right clicking on app and create file.

You should see the files like in this structur

If you had to create the files in step 12, then do the following in the .env.local file, else skip

Copy the AUTH_JWT from the console on step 10 to VUE_APP_AUTH_JWT

Copy the DATA from the console on step 10 to VUE_APP_DATA

Copy the ENV_PROCESS_ENDPOINT from the console on step 10 to VUE_APP_ENV_PROCESS_ENDPOINT

Copy SRC from the console on step 10 to VUE_APP_METADATA

Open Screenshot 2023-07-27 at 1.43.14 pm.png
Screenshot 2023-07-27 at 1.43.14 pm.png
If you had to create the files in step 12, then do the following in the .env.production file, else skip



VUE_APP_AUTH_JWT=%%=v(@AUTH_JWT)=%%
VUE_APP_DATA=%%=v(@DATA)=%%
VUE_APP_ENV_PROCESS_ENDPOINT=%%=v(@ENV_PROCESS_ENDPOINT)=%%
VUE_APP_IS_PRODUCTION="true"
VUE_APP_METADATA=%%=v(@SRC)=%%
Open terminal and run npm install to install dependencies

Run the command npm run serve to run the Preference Center locally and keep making changes

After making changes , run the command npm run build

This will result in a dist folder getting created under the app. 

Open Screenshot 2023-07-27 at 1.47.55 pm.png
Screenshot 2023-07-27 at 1.47.55 pm.png
 Copy the chunk-vendor**.js to the PreferenceApp-VendorJS

Copy the app.****.js to the PreferenceApp-AppJS

Copy the chunk-vendor***.css to the PreferenceApp-VendorCSS

Copy the chunk-vendor***.js to the PreferenceApp-VendorJS

Create a test link using the preference builder code in the repository to generate the URL and if all is good, you will have a preference center ready to use ! :slight_smile: 