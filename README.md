# Event Monitoring Plus
A lightweight collection of CRM Analytics dashboards for gaining a better understanding of Event Monitoring data.

## Installation Instructions

Two important points:
* Never click the 'Update Available' link in the Event Monitoring Plus app folder after instllation. This will remove Event Monitoring Plus. You ***can*** click 'Update Available' in the standard Event Monitoring Analytics app.
* The latest version of Event Monitoring Plus contains a recipe to process Apex Executions and Apex Unexpected Exceptions. ***You must run or schedule*** the ***'ApexUnexpectedExceptionsEnhancements'*** recipe in the Data Manager before viewing the 'Apex Exceptions' dashboard.


### Prerequisite: Setting Up The Log Ingestion process:

Easy Option: CRM Analytics Templated App

* This package relies on v59 or later of the CRM Analytics Templated App.
* (If the "Event Monitoring Analytics" CRM Analytics app is already running with the below-listed log types, check that the log types in "Create the analytics app" have been included. If so, proceed to "Deploying the new dashboards".)
* Select a sandbox or production org
* [Ensure Event Monitoring Analytics is enabled](https://help.salesforce.com/s/articleView?id=sf.bi_app_event_monitor_enable_select_PSL.htm&type=5)
* [Assign permissions to users](https://help.salesforce.com/s/articleView?id=bi_app_event_monitor_create_permsets.htm&type=5&language=en_US)
* [Create the analytics app](https://help.salesforce.com/s/articleView?language=en_US&type=5&id=sf.bi_app_admin_wave_create.htm)
    * *Hard Requirement*: **must** include following log types:
      * ApexExecution
      * ApexUnexpectedException
      * ApexCallout
      * ApexRestApi
      * ApexSoap
      * ApexTrigger
      * API
      * BulkApi
      * ConcurrentLongRunningApexLimit
      * LightningInteraction
      * LightningPageView
      * Login
      * Report
      * RESTApi
* At the end of the wizard (Step 7) you ***must*** check the box asking "Add only new event log file data to existing datasets?"

Harder Option: [Set up a custom process to push Event Logs into CRM Analytics](https://www.salesforcehacker.com/2015/01/simple-script-for-loading-event.html)

### Deploying the new dashboards and recipe

#### Easy Option: Automate Deployment via Heroku App

Use the button below to authenticate with the Heroku app.
**Make sure to type 'main' in the Branch/Tag/Commit field.**

<a href="https://githubsfdeploy.herokuapp.com?owner=seamusocionnaigh&amp;repo=eventmonitoringplus&amp;branch=main">
  <img alt="Deploy to Salesforce"
       src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/deploy.png">
</a>

#### Manual Deployment

[Installation guide](https://vimeo.com/789882418)

* It is recommended to use Salesforce DX or Workbench to perform the deployment, as per the video installation guide.
* This package assumes that an administrator has created only one version of the Event Monitoring Analytics app.
* Multiple instances of that app create multiple versions of the datasets with numbers appended to the name. (If you have gone through several iterations of creating the Event Monitoring Analytics templated app, you may end up with dataset names like "ApexExecutionWithUsers1" or "ApexExecutionWithUsers5" (if you've been unlucky enough to create it 5 times)).
* If there are multiple versions of the app, it is recommended to consult with all creatores, owners and stakeholders of each instance of the app to determine if there is overlap between each instance and find a path towards consolidating into one app.
* If there are multiple instances of the app and hence multiple instances, you will need to identify the API name of the correct datasets to use. (These should be datasets that are regularly updated, as part of whichever log ingestion process you've configured.)
* To identify the API name of a dataset, [follow these steps](https://help.salesforce.com/s/articleView?id=sf.bi_dataset_edit.htm&type=5) to reach the edit page and note the 'API Name' in the top-left.
* **If** your dataset names are different / have numbers as suffixes, download the package, edit the .wdash (Wave Dashboard) and .wdpr (Wave Recipe) files, and Find/Replace with the appropriate dataset API name from your environment (e.g. "LightningPageViewWithUsers" with "LightningPageViewWithUsers1"). (User_Activities.wdash uses the LightningInteractionWithUsers and LightningPageViewWithUsers datasets; Apex_Performance.wdash uses ApexExecutionWithUsers; Apex_Exceptions.wdash uses ApexUnexpectedException).
* If you are using the datasets from the default Event Monitoring analytics app (the *'~WithUsers'* datasets), make sure that you've scheduled your [Event Monitoring Analytics app's dataflow](https://help.salesforce.com/s/articleView?id=sf.bi_app_event_monitor_schedule_dataflow.htm&type=5).
* This is an SFDX version project. You can authenticate to an org and deploy as per the `manifest/package.xml` as explained [here](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_develop_any_org.htm). 
* You can also deploy the dashboards with these two other alternatives:
  * Metadata deployment with Ant, SFDX, etc. If you know how to do this, the `misc/mdapi/package.xml` and payload should suffice.
    * If you *don't* know how to do a metadata deployment, create a .zip of the `misc/mdapi/` directore (your zip file should not have any root folder - both `package.xml` and `wave` folder should be on the root of the zip file), [and then follow this guide](https://help.salesforce.com/s/articleView?id=000315117&type=1); note in Step 7 you *do have to* click the 'Single Package' checkbox.
  * You can also use the JSON files that are available into `misc/json/` and copy and past them from the folder direclty into a new, blank dashboard in CRM Analytics. This method is not preferred as you're not deploying the dashboard's extended metadata (XMD), and so you lose the rich, informative colour-coded metrics that were lovingly added to the dashboards. Only use this approach as a last resort or you just want an initial glance.


## Dashboards

### User Journeys:

[Video Guide](https://vimeo.com/789882598)

It is imperative that every business understands what parts of a Salesforce implementation are valuable to its users.  This dashboard gives a top-to-bottom view of what UI-level interactions users perform in Lightning, from apps, standard and custom objects, to components of a page layout, what they view and what they click. As a result, Salesforce sponsors, stakeholdes, owners, product owners, project managers, admins and developers have a clearer sense of what is valuable to users by virtue of their level of interaction with many of the customisations they have developed and improved and put in the hands of users.
This dashboard leverages the [LightingInteration event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_LightningInteractionWithUsers.htm); reading the documentation is strongly encouraged.

![User_Activities](https://user-images.githubusercontent.com/20658634/147172567-c1ff9dee-c955-4429-a163-c104363f9c8d.png)

### Apex Performance:

[Video Guide](https://vimeo.com/789882324)

A narrative of the raw ApexExecutionWithUsers dataset in order to help pin-point slow-performing code. Ideally for a Developer, Admin, QA, Release Manager and / or Product Owner to identify what code is consistently slow, what's started being slow, and what skillset is required to begin reviewing the code. Try and use this early-on in your development process, or at the very latest in a UAT full-copy sandbox, so you can spot trends in newly-problematic code and decide if a current release is about to introduce risk to the business in production.
This dashboard leverages the [ApexExecutionWithUsers event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_ApexExecutionWithUsers.htm); reading the documentation is strongly encouraged.

![Apex_Performance](https://user-images.githubusercontent.com/20658634/147172579-f79cc601-23c6-4e1e-83bd-4b352a377aed.png)

### Apex Exceptions

[Video Guide](https://vimeo.com/789882264)

When Apex starts to break, quick and decisive action is needed. This dashboard gives a clear view of what Apex classes and methods are generating unhandled exceptions, by volume, over time. Realistically, every datapoint on this dashboard is something for developers to review, consider, and fix or prevent. QAs, Release Managers and Product Owners should be reviewing this dashboard early in the development process, or at the very latest in a UAT full-copy sandbox. If things are beaking, this dashboard will help decide if a current release is about to introduce risk to the business in production by breaking business-critical processes.
This dashboard leverages the [ApexUnexpectedException event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_apexunexpectedexception.htm); reading the documentation is strongly encouraged.

![Apex_Exceptions](https://user-images.githubusercontent.com/20658634/147174402-a45c0868-872e-45d2-97bc-495a3359c994.png)

### Lightning Page Performance

[Video Guide](https://vimeo.com/789882474)

Helps you to identify the pages that are performing poorly in Lightning, by GEO, App, Object. Uses CRMA compare tables to calculate lost productivity due to poor page load time (EPT).
This dashboard leverages the [LightningPageViewWithUsers](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_LightningPageViewWithUsers.htm) and [LightingInteration event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_LightningInteractionWithUsers.htm); reading the documentation is strongly encouraged.

![image](https://user-images.githubusercontent.com/20658634/157493917-9955c525-0332-4c2a-8b67-0caea2508815.png)

### Experience Cloud Page Performance
A very similar dashboard to the Lightning Page Performance EPT dashboard, but with some filters and tweaks to help better understand and visualise which Experience Cloud pages have higher EPT.  This can be explored by many dimensions, such as the Site URL, user's location / geo, browser version, etc.  This has only been tested with Experience Cloud site templates; it has not been tested with Aura or LWR Buil-Your-Own sites.

![image](https://user-images.githubusercontent.com/20658634/191331061-3a32f043-bf29-423c-8852-dcb3d8f24ab1.png)

### Report Adoption

[Video Guide](https://vimeo.com/789882563)

A dashboard that looks at reports from adoption, performance and security points-of-view (the three pillars of Event Monitoring!).
Adoption: who's running existing reports as opposed to editing or building new ones (reports with no name).
Performance: the relationship between rows returned and database (DB) time.
Security: how may reports are being run in .csv, Excel or printable formats.

![image](https://user-images.githubusercontent.com/8489662/212783437-090034c7-5505-47a5-9534-7c570e560c7b.png)

### Org Performance Overview

This dashboard gives a complete performance overview of the business logic and API layers of your org; highlights the usual errors and hotspots that cause general platform and org-wide performance bottlenecks and failures.  Consider this as a general superset of some of the other performance-focused dashboards in this repo. Can be used as a startign point to spot general performance trends that can be investigated more thoroughly in other lenses or dashboards.

![image](https://user-images.githubusercontent.com/20658634/236536412-617a30bc-9a28-4fa4-ac4c-ce5d74a06607.png)

## Notes, Observations, Comments:

The Dashboards are in an app / folder called "Event Monitoring Plus" that uses the Event Monitoring licence type. Please do not click the 'Reconfigure' link at the top of the app page; it will overwrite and remove these dashboards.

As of January 2023, the dashboards now use the *'~WithUsers'* datasets. It is important that you've scheduled your [Event Monitoring Analytics app's dataflow](https://help.salesforce.com/s/articleView?id=sf.bi_app_event_monitor_schedule_dataflow.htm&type=5).

The app is deployed with a default security setting of 'Manager' for the Entire Organisation; anyone in the Entire Organisation that has access to CRM Analytics. Users will only be able to view the contents of the dashboards *if* they also have access to the datasets. Your datasets are protected within whichever app you've stored them in and so will remain as the main security control.
Related point: feel free to edit and save these dashboards into your existing Event Monitoring CRM Analytics app. Again, be aware of reconfiguring any templated apps, as it will delete these dashboards.