# Google Sheets to Python
***
## Overview
***
Trying to get data out of a Google Sheet into a Python program should be straightforward but, due to requirements of authentication from Google Sheets, this requires a bit of finagling to manage. Here's the step-by-step instructions to get this working in your Python project.

## Google Project and API Setup
***
First, we need to create and configure the Google Cloud project and Sheet that we'll be interacting with:
  1. Navigate to [Google API Manager](https://console.cloud.google.com/).

  2. Create a new project. If you are a new Google Cloud user, the screen will resemble the following:
    ![Figure 1 - New Google Cloud User Project](images/01_NewGoogleCloudUserProject.png?raw=true "Figure 1 - New Google Cloud User Project")
  If you are an existing Google Cloud user, it may look more like the following:
    ![Figure 2 - Existing Google Cloud User Project](images/100_ExistingGoogleCloudUserProject.png?raw=true "Figure 2 - Existing Google Cloud User Project")
  followed by:
    ![Figure 3 - Existing Google Cloud User Project](images/100_ExistingGoogleCloudUserProject_b.png?raw=true "Figure 3 - Existing Google Cloud User Project")

  3. Name your project.
    ![Figure 4 - Name Your Project](images/02_NewGoogleCloudUserProjectName.png?raw=true "Figure 4 - Name Your Project")
  If you are an existing Google Cloud user, it may look more like the following:
    ![Figure 5 - Existing User: Name Your Project](images/101_ExistingGoogleCloudUserProjectName.png?raw=true "Figure 5 - Existing User: Name Your Project")

  4. Next, enable APIs and Services:
    ![Figure 6 - Enable APIs and Services](images/03_NewGoogleCloudUserProjectEnableAPI.png?raw=true "Figure 6 - Enable APIs and Services")
  The API Library page loads:
    ![Figure 7 - Welcome to the API Library](images/04_NewGoogleCloudUserProjectSearchAPI.png?raw=true "Figure 7 - Welcome to the API Library")

  5. On the resulting page, search for 'Google Drive API':
    ![Figure 8 - Search for Google Drive API](images/05_NewGoogleCloudUserProjectSearchAPI2.png?raw=true "Figure 8 - Search for Google Drive API")

  6. Select 'Google Drive API' and click the 'Enable' button on the resulting page:
    ![Figure 9 - Enable Google Drive API](images/06_NewGoogleCloudUserProjectSheetsAPIEnable2.png?raw=true "Figure 9 - Enable Google Drive API")

  7. Return to the API Library page and search for 'Google Sheets API':
    ![Figure 10 - Search for Google Sheets API](images/05_NewGoogleCloudUserProjectSheetsAPI.png?raw=true "Figure 10 - Search for Google Sheets API")

  8. Select 'Google Sheets API' and click the 'Enable' button on the resulting page:
    ![Figure 11 - Enable Google Sheets API](images/06_NewGoogleCloudUserProjectSheetsAPIEnable.png?raw=true "Figure 11 - Enable Google Sheets API")

## Google Drive API Credentials
***
  1. Now we'll create credentials via the Google Drive API. Tap the 'Manage' button (where the 'Enable' button had been previously). On the page that loads, click the 'Create Credentials' button:
    ![Figure 12 - Create Google Drive API Credentials](images/07_NewGoogleCloudUserProjectCredentials.png?raw=true "Figure 12 - Create Google Drive API Credentials")

  2. Specify that we're using the 'Google Drive API':
    ![Figure 13 - Specify 'Google Drive API' for Credentials](images/08_NewGoogleCloudUserProjectCredentialsAPI.png?raw=true "Figure 13 - Specify 'Google Drive API' for Credentials")

  3. Select 'Web server (e.g. node.js, Tomcat) to specify from where we will be calling the API.
  Select 'Application data' as the data we will be accessing.
  Select 'No, I'm not using them' about the Engines.
    ![Figure 14 - Specify 'Google Drive API' for Credentials](images/08_NewGoogleCloudUserProjectCredentialsAPI_2.png?raw=true "Figure 14 - Specify 'Google Drive API' for Credentials")

  4. Click the 'What credentials do I need?' button. The Add credentials to your project page loads. In the 'Role' dropdown, select 'Editor>Editor>Edit access to all resouces.':
    ![Figure 15 - Specify 'Editor' Role for Credentials](images/09_NewGoogleCloudUserProjectCredentialsDetails.png?raw=true "Figure 15 - Specify 'Editor' Role for Credentials")

  5. Specify a 'Service account name', and a 'Service account ID'. Ensure 'Key type' is set to 'JSON':
    ![Figure 16 - Specify Name, ID, and Key Type for Credentials](images/09_NewGoogleCloudUserProjectCredentialsDetails_2.png?raw=true "Figure 16 - Specify Name, ID, and Key Type for Credentials")

  6. Click the 'Continue' button and bask in the glory of your new service account and key. The private key should be downloaded to your computer automatically:
    ![Figure 17 - Private Key.JSON Creation Complete](images/10_NewGoogleCloudUserProjectCredentialsCreated.png?raw=true "Figure 17 - Private Key.JSON Creation Complete")

  7. It is highly recommended that you do NOT add this JSON file directly to any project repo. If you need to share the credentials with others, find a secure password management tool that you all have access to and store it in there. Include instructions in your project's README.md about where it can be found and where the file should be located in reference to your project (your home directory is one option). Below is an example README.md inclusion that you can copy and tailor to your needs:

      #### Google Sheet Credentials

      This code requires access to a Google Sheet or which you will need to specify credentials.

      Cert contents can be found in (your password manager of choice):
      - Folder = (Folder Name, if applicable for your password manager)
      - Secure Note Name = (Unique identifier within password manager)

      Copy the contents and create a file named (yournameofchoice)Credentials.json in your Home directory (or preferred location of choice that is NOT in the project repo):

      ```bash
      ~/(yournameofchoice)Credentials.json
      ```

## Google Sheet Configuration

If you haven't already made or selected a Google Sheet for which you are wanting to grant access via code, now would be the time to get that set up. Once you've done so, you'll click the 'Share' button to 'Share with others'. 

Open the credentials JSON file we just created and copy the 'client-email' value. Paste it in the 'Share with others' modal:
  ![Figure 18 - Share With Others](images/11_NewGoogleCloudUserProjectCredentialsShared.png?raw=true "Figure 18 - Share With Others")




## Python Code
***
1. Install the supporting libraries:
    - gspread
    - oauth2client

    ```bash
    $ pip install gspread oauth2client
    ```

2. Add the following code to your project replacing the following:
    - References in comments to a password manager
    - References in comments and code to the name and location of the Credentials.JSON file
    - The name of your Google Sheet document

    ```bash
    # This code requires access to a Google Sheet for which you will need to
    # specify credentials.

    # Go to (your password manager of choice):
    #   Folder = (Folder Name, if applicable for your password manager)
    #   Secure Note Name = (Unique identifier within password manager)

    # Copy the contents and create a file ~/(yournameofchoice)Credentials.json

    import json
    import os.path

    # Google Sheets library
    import gspread
    # Service client credentials from oauth2client
    from oauth2client.service_account import ServiceAccountCredentials

    def gSheetReader():
        # Define scope
        scope = ['https://spreadsheets.google.com/feeds',
                 'https://www.googleapis.com/auth/drive']
        # Define credentials using content of <your-file>.json plus defined scope
        # Create JSON keyfile local to project; add text to point to your
        # password manager: (Directions to data in password manager)
        creds = ServiceAccountCredentials.from_json_keyfile_name(
            os.path.expanduser('~/(yournameofchoice)Credentials.json'), scope)
        # Define gspread authorization using defined credentials
        client = gspread.authorize(creds)

        # Access the first Sheet in our Google Sheet
        sheet = client.open('YourSheetNameHere').sheet1
    ```


### But Wait, There's More!
***
If you tack the below onto the above code snippet, you can isolate your response to populated columns and return it all in a list. This uses `row_values()` and `col_values()`:

```bash
# This gets us the populated col count based on the first row having the
# maximum number of columns populated
nonEmptyColNum = len(sheet.row_values(1))
result = []
# Add + 1 to the 2nd range() param to get the contents of the last column
# too
for i in range(1, nonEmptyColNum + 1):
# Add all values for each column to a list and concatenate all lists into
# 'result'
     result = result + [sheet.col_values(i)]

# Return the requested value(s)
return result
```

You can call the above as follows:

```bash
# Obtain data segments from an external Google Sheet
# We are expecting a list of lists; each column's values should consist of
# a single list
myData= list(gSheetReader())
```

We can get all records with `get_all_record()`:

```bash
#Access all records in the Sheet
result = sheet.get_all_record()
```

We can get the value in a particular cell with `cell()`:

```bash
#Access a single cell's value in the Sheet
result = sheet.cell(5,2)
```

To update the value in a particular use `update_cell()`:

```bash
#Update a single cell's value in the Sheet
result = sheet.update_cell(5,2,'my value')
```

For more examples and documentation on gspread, check out [the official gspread documentation](https://gspread.readthedocs.io/en/latest/user-guide.html#).