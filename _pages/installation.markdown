---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

permalink: /installation/
layout: home
title: Installing TRIRIGA Assistant
description: Steps for requesting, provisioning and installing IBM TRIRIGA Assistant for your IBM TRIRIGA solution.

sidebar:
  nav: "docs"
---

## [GET THE CODE!](https://github.com/IBM/tririga-assistant)

## Estimated time

This installation should take 1-2 hours to complete, not including the time for IBM to provision the TRIRIGA Assistant skill.

## Steps

### Part 1 - Import OSLC resources, create assistant user and test

#### A) IMPORT THE OM PACKAGE

1.	Create new Object Migration import package selecting the `tri-assistant-*.zip` file provided in the om-package folder.
2.	Validate and Import the OM package.

#### B) SET UP THE ASSISTANT USER.

In order to allow the assistant user to create location reservations and service requests on behalf of other users, we have to create the user with the proper groups and licenses.

1.  Create a user with user with the following:

    Groups:
    - TRIRIGA Request Central
    - TRIRIGA Request Central - Fundamentals
    - TRIRIGA Request Central - Reserve
    - TRIRIGA Request Central - Reserve - Fundamentals
    - WAS Reserve OSLC

    License:
    - IBM Facilities and Real Estate Management on Cloud Self Service, or
    - IBM TRIRIGA Request Central, or
    - IBM TRIRIGA Workplace Reservation Manager

2.	Provide a primary location for the assistant account.  This is needed at the moment for Service Request creation to work.
3.  Provide an organization to the assistant account.  This is also needed at the moment for Service Request creation to work.
4.	Make note of the user name and password because it will needed in the provisioning step below.

#### C) TEST THE OSLC ENDPOINTS.

Execute a quick `curl` command to validate OSLC endpoints used during provisioning.  Follow these steps to make sure you have the necessary info and objects in place.

1.  You'll need the base64 encoded representation of the usernmame and password for the assistant account created earlier.  The `base64` unix command will generate this string for you given the username and password.
   `echo "username:password" | base64`
2.  Execute the following `curl` command and replace TRIRIGA_URL and ENCODED_USERNAME_AND_PASSWORD with your info.
    `curl --location --request GET 'TRIRIGA_URL/oslc/spq/ibmWAProvisionQC?oslc.select=*' --header 'Authorization: Basic ENCODED_USERNAME_AND_PASSWORD' --header 'User-Agent: PostmanRuntime/7.23.0' --insecure`

The response should have some `triParentBuildingTX` values returned.  If an error occurs and you can't solve the problem on your own, contact your IBM representative.

Also, optionally, if you understand Postman and would like to test all the OSLC calls, then you can test by using the Postman collection provided in the postman directory.  You will need to change the payload to have 'location/building/space' you have defined in your TRIRIGA instance. A successful test of the OSLC APIs when there are no OSLC errors.

### Part 2 - Submit the Provisioning Request

#### D) COMPLETE THE PROVISIONING FORM

The OM package imported contains a form that will execute a process of gathering data for the Assistant and providing that data to IBM.  The form asks for the user name and password for the account created in an earlier step.  This is needed so the IBM's TRIRIGA Assistant services can make an OSLC call back into your TRIRIGA instance and gather building and room names.

1. From the TRIRIGA Main Page, click on My Reports > System Reports, and filter on the Business Object column for `ibmAssistantProvisionOrder`.
2. Execute the `TRIRIGA Assistant Provision Record Query`.
3. Click on the only record found.  Don't add a record, just modify the existing record that was provided in the OM package.
2. Enter the URL for your TRIRIGA instance. Note: The instance must be accessible without a VPN and through HTTPS. Include the complete path, starting with `https://`.
3. Enter the user name and password for the assistant account you created earlier.
4. Enter your name and email address so we can contact you when the assistant services have been provisioned for your instance.
5. Click Submit.


#### E) WAIT FOR INTEGRATION ID FROM IBM

Once the information has been successfully received by the IBM TRIRIGA Assistant services, you will be contacted through email at the email address provided on the form.  Once you receive the Integration ID, you may proceed with the rest of the steps.  If you do not receive a response from IBM in a week, please contact your IBM representative.


### Part 3 - Add Assistant UI to Workplace Services apps

#### F) EDIT THE WORKPLACE SERVICES APP TO ADD CHAT UI ACCESS.

1.	Once you have received your Integration ID, open the Workplace Service view by going to Tools > Web View Designer > triWorkplaceServices.
2.	Copy the value in `Development Filename` and paste it as the value in the `Production Filename`. (Note: This is done for testing purposes and can be reversed after testing passes. A link to instructions on how to do "vulcanization" is below.)
3.	In the View Files section, click on `/trilazy-imports.html`.
4.	Click on the Download View File icon.
5.  Edit the `trilazy-importshtml` file and add the following below the last `<link>` tag line:

    ```html
    <link rel="import" href="../ibmTriAssistant/ibmTriAssistant.html">
    ```

6.  Upload the changes by clicking on the Upload view file icon.
7.  Click `Save & Close` button in upper right corner.

8.	In the View Files section, click on /triview-workplace-services-dev.html
9.	Click on the Download View File icon.
10.	Edit the `triview-workplace-services-devhtml` file and at the bottom of the template section, paste the following lines of code above the `</template>` so the following HTML is between the `<template>` and `</template>` lines:
**Make sure to replace `PASTE_THE_INTEGRATION_ID_HERE` with the Integration ID provided.**

    ```html
    <ibm-TriAssistant integration-id="PASTE_THE_INTEGRATION_ID_HERE" region="us-south" model-and-view="ibmTriAssistant" instance-id="-1" online="[[online]]"> </ibm-TriAssistant>
    ```
11. Upload the changes by clicking on the Upload view file icon.
12. Click `Save & Close` button in upper right corner.

#### G) EDIT THE ROOM RESERVATION AND SERVICE REQUEST VIEWS.

From the "Web View Designer", repeat the same steps directly above for the other views:
    - triRoomReservation View (set the `Production Filename`, edit files `trilazy-imports.html` and `triview-room-reservation-dev.html`).
    - triServiceRequest View (set the `Production Filename`, edit files `trilazy-imports.html` and `triview-service-request-dev.html`).

#### H) (OPTIONAL) VULCANIZE THE VIEWS.

If you feel that your workplace service apps are loading much slower after the edits, then you can "vulcanize" the apps [following these instructions](https://www.ibm.com/developerworks/community/wikis/home?lang=en#!/wiki/IBM TRIRIGA1/page/How to vulcanize your UX application).  If you do this, make sure you undo the change like F2 that sets the `Production Filename` to the `Development Filename`.


### Part 4 - Configure permissions for users

#### I) ALLOW ASSISTANT USER TO CREATE RESERVATIONS ON BEHALF OF OTHER USERS.

1.	If assistant will be used to make room reservations, the `TRIRIGAWEB.properties` should have the `SHOW_PREFERENCES_LINK` env var set to `Y`

    - (note you will need to restart your WebSphere server if you have to change this value).

2.	For each user that plans to use the assistant to create location reservations, they must do the following:
    - Click the Welcome, {name} in the main UI home page
    - Click the Preferences tab
    - Click the Reservation Delegates tab
    - In the Reservation Delegates section, click the Find button
    - Click the checkbox for the `triassistant` user and click OK

#### J) MODIFY OR CREATE NEW SECURITY GROUP

The OM package imported contains a new model for the UX apps. Non-admin users need to be given proper access to this model.  To accomplish this, you can either create a new security group or modify an existing.  The steps below modify the `TRIRIGA Request Central - Fundamentals` security group to allow users, that have this group, to read, update, create and delete the `ibmTriAssistant` model.

1.  From the TRIRIGA Main UI, go to `Tools > Adminstration > Security Manager`.
2.  Click on the `TRIRIGA Request Central - Fundamentals` security group.
3.  In the window appears, click on the `Access` tab.
4.  Scroll down and expand the `Models` root and select `ibmTriAssistant`.
5.  In the "Model Access" panel on the right, select `Read,Update,Create and Delete`.
6.  Click Save & Close.

#### K) TEST THE WORKPLACE SERVICES APPS.

It's time to test with the Assistant Chat UI available from the Workplace Services app.  Make sure the user you are using has a primary location set, isn't the system or assistant user, and has the security group modified or created in step L.  If all edits were done correctly, you should see a chat icon appear at the bottom right of the Workplace Services apps.  If you know a room name, then try out the service request functionality by typing "the ________ room has a broken chair" and if you have reserve functionality, try "book a room".

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/installation/screenshot.png)
