# Event Monitoring Plus
A lightweight collection of CRM Analytics dashboards for gaining a better understanding of Event Monitoring data.

## Installation Instructions

### Prerequisite: Setting Up The Log Ingestion process:

Easy Option: CRM Analytics Templated App

* (If the "Event Monitoring Analytics" CRM Analytics app is already running with the below-listed log types, you can skip to Package Deployment)
* Select a sandbox or production org
* [Ensure Event Monitoring Analytics is enabled](https://help.salesforce.com/s/articleView?id=sf.bi_app_event_monitor_enable_select_PSL.htm&type=5)
* [Assign permissions to users](https://help.salesforce.com/s/articleView?id=bi_app_event_monitor_create_permsets.htm&type=5&language=en_US)
* [Create the analytics app](https://help.salesforce.com/s/articleView?language=en_US&type=5&id=sf.bi_app_admin_wave_create.htm)
    * *Hard Requirement*: **must** include following log types:  LightningInteraction, LightningPageview, ApexExecution, ApexUnexpectedException)

Harder Option: [Set up a custom process to push Event Logs into CRM Analytics](https://www.salesforcehacker.com/2015/01/simple-script-for-loading-event.html)

### Deploying the new dashboards

* Identify the API name of the above-mention datasets in CRM Analytics that you wanted to analyse.
* These should be datasets that are regularly updated, as part of whichever log ingestion process you've configured above.
* To identify the API name of a dataset, [follow these steps](https://help.salesforce.com/s/articleView?id=sf.bi_dataset_edit.htm&type=5) to reach the edit page and note the 'API Name' in the top-left.
* This package assumes that the dataset names are the first or only iteration of the log ingestion process, and hence uses simple dataet names, like, for example "ApexExecution" for the ApexExecution log file. (If you have gone through several iterations of creating the CRM Analytics templated app, you may end up with dataset names like "ApexExecution1" or "ApexExecution5" (if you've been unlucky enough to create it 5 times)).
* **If** your dataset names are different / have numbers as suffixes, download the package, edit the .wdash (Wave Dashboard) files, and Find/Replace with the appropriate dataset API name from your environment (e.g. "LightningPageView" with "LightningPageView1"). (User_Activities.wdash uses the LightningInteraction and LightningPageView datasets; Apex_Performance.wdash uses ApexExecution; Apex_Exceptions.wdash uses ApexUnexpectedException).
* This is a SFDX version of the original repo. So you just have to auth an org and deploy as per the `manifest/package.xml` as explained [here](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_develop_any_org.htm). 
* You can also use the JSON files that are available into `misc/evmplusJSON/` and copy and past them from the folder direclty into a new, blank dashboard in CRM Analytics. This method is not preferred as you're not deploying the dashboard's extended metadata (XMD), and so you lose the rich, informative colour-coded metrics that were lovingly added to the dashboards. Only use this approach as a last resort or you just want an initial glance.


## Dashboards

### User Journeys:
It is imperative that every business understands what parts of a Salesforce implementation are valuable to its users.  This dashboard gives a top-to-bottom view of what UI-level interactions users perform in Lightning, from apps, standard and custom objects, to components of a page layout, what they view and what they click. As a result, Salesforce sponsors, stakeholdes, owners, product owners, project managers, admins and developers have a clearer sense of what is valuable to users by virtue of their level of interaction with many of the customisations they have developed and improved and put in the hands of users.
This dashboard leverages the [LightingInteration event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_lightninginteraction.htm); reading the documentation is strongly encouraged.

![User_Activities](https://user-images.githubusercontent.com/20658634/147172567-c1ff9dee-c955-4429-a163-c104363f9c8d.png)

### Apex Performance:
A narrative of the raw ApexExecution dataset in order to help pin-point slow-performing code. Ideally for a Developer, Admin, QA, Release Manager and / or Product Owner to identify what code is consistently slow, what's started being slow, and what skillset is required to begin reviewing the code. Try and use this early-on in your development process, or at the very latest in a UAT full-copy sandbox, so you can spot trends in newly-problematic code and decide if a current release is about to introduce risk to the business in production.
This dashboard leverages the [ApexExecution event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_apexexecution.htm); reading the documentation is strongly encouraged.

![Apex_Performance](https://user-images.githubusercontent.com/20658634/147172579-f79cc601-23c6-4e1e-83bd-4b352a377aed.png)

### Apex Exceptions
When Apex starts to break, quick and decisive action is needed. This dashboard gives a clear view of what Apex classes and methods are generating unhandled exceptions, by volume, over time. Realistically, every datapoint on this dashboard is something for developers to review, consider, and fix or prevent. QAs, Release Managers and Product Owners should be reviewing this dashboard early in the development process, or at the very latest in a UAT full-copy sandbox. If things are beaking, this dashboard will help decide if a current release is about to introduce risk to the business in production by breaking business-critical processes.
This dashboard leverages the [ApexUnexpectedException event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_apexunexpectedexception.htm); reading the documentation is strongly encouraged.

![Apex_Exceptions](https://user-images.githubusercontent.com/20658634/147174402-a45c0868-872e-45d2-97bc-495a3359c994.png)

### Lightning Page Performance
Helps you to identify the pages that are performing poorly in Lightning, by GEO, App, Object. Uses CRMA compare tables to calculate lost productivity due to poor page load time (EPT).
This dashboard leverages the [LightningPageView](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_lightningpageview.htm) and [LightingInteration event log](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_objects_eventlogfile_lightninginteraction.htm); reading the documentation is strongly encouraged.

![image](https://user-images.githubusercontent.com/20658634/157493917-9955c525-0332-4c2a-8b67-0caea2508815.png)

### Experience Cloud Page Performance
A very similar dashboard to the Lightning Page Performance EPT dashboard, but with some filters and tweaks to help better understand and visualise which Experience Cloud pages have higher EPT.  This can be explored by many dimensions, such as the Site URL, user's location / geo, browser version, etc.  This has only been tested with Experience Cloud site templates; it has not been tested with Aura or LWR Buil-Your-Own sites.

![image](https://user-images.githubusercontent.com/20658634/191331061-3a32f043-bf29-423c-8852-dcb3d8f24ab1.png)

## Notes, Observations, Comments:

The Dashboards are in an app / folder called "Event Monitoring Plus" that uses the Event Monitoring licence type. Please do not click the 'Reconfigure' link at the top of the app page; it will overwrite and remove these dashboards.

If you're used to working with Event Monitoring data in CRM Analytics, you might be used to working with the *'~WithUsers'* datasets. We have opted not to use these for two reasons: augmenting the event logs with user names is a CRM Analytics function. This package focuses on highlighting the valuable insights you can gain from event logs without too much CRM Analytics magic. As much as we love CRM Analytics (*it really is just the best thing ever*), users have a choice of using any analytical or log processing tool to gain these insights. So for that reason we've stuck with the original logs. (Although, there is *a bit* of sourcery in the Apex Exceptions dashboard, which does some if-else (or *case statement*) logic on the EXCEPTION_MESSAGE and STACK_TRACE columns, but nothing you can't do in Python). The second point is, some people forget to schedule the dataflow and the *'~WithUsers'* datasets can be out of date, so we're just focusing on the logs that are most likely to be up-to-date.

The app is deployed with a default security setting of 'Manager' for the Entire Organisation; anyone in the Entire Organisation that has access to CRM Analytics. Users will only be able to view the contents of the dashboards *if* they also have access to the datasets. Your datasets are protected within whichever app you've stored them in and so will remain as the main security control.
Related point: feel free to edit and save these dashboards into your existing Event Monitoring CRM Analytics app. Again, be aware of reconfiguring any templated apps, as it will delete these dashboards.