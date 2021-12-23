# Event Monitoring Plus
A lightweight collection of Tableau CRM dashboards for gaining a better understanding of Event Monitoring data.

## Installation Instructions

### Prerequisite: Setting Up The Log Ingestion process:

Easy Option: Tableau CRM Templated App

* (If the "Event Monitoring Analytics" Tableau CRM app is already running with the below-listed log types, you can skip to Package Deployment)
* Select a sandbox or production org
* [Ensure Event Monitoring Analytics is enabled](https://help.salesforce.com/s/articleView?id=sf.bi_app_event_monitor_enable_select_PSL.htm&type=5)
* [Assign permissions to users](https://help.salesforce.com/s/articleView?id=bi_app_event_monitor_create_permsets.htm&type=5&language=en_US)
* [Create the analytics app](https://help.salesforce.com/s/articleView?language=en_US&type=5&id=sf.bi_app_admin_wave_create.htm)
    * *Hard Requirement*: **must** include following log types:  LightningInteraction, LightningPageview, ApexExecution, ApexUnexpectedException)

Harder Option: [Set up a custom process to push Event Logs into Tableau CRM](https://www.salesforcehacker.com/2015/01/simple-script-for-loading-event.html)

### Deploying the new dashboards

* Identify the API name of the above-mention datasets in Tableau CRM that you wanted to analyse.
* These should be datasets that are regularly updated, as part of whichever log ingestion process you've configured above.
* To identify the API name of a dataset, [follow these steps](https://help.salesforce.com/s/articleView?id=sf.bi_dataset_edit.htm&type=5) to reach the edit page and note the 'API Name' in the top-left.
* This package assumes that the dataset names are the first or only iteration of the log ingestion process, and hence uses simple dataet names, like, for example "ApexExecution" for the ApexExecution log file. (If you have gone through several iterations of creating the Tableau CRM templated app, you may end up with dataset names like "ApexExecution1" or "ApexExecution5" (if you've been unlucky enough to create it 5 times)).
* **If** your dataset names are different / have numbers as suffixes, download the package, edit the .wdash (Wave Dashboard) files, and Find/Replace with the appropriate dataset API name from your environment (e.g. "LightningPageView" with "LightningPageView1"). (User_Activities.wdash uses the LightningInteraction and LightningPageView datasets; Apex_Performance.wdash uses ApexExecution; Apex_Exceptions.wdash uses ApexUnexpectedException).
* There are two options to deploy the dashboards:
  - Metadata deployment with Ant, SFDX, etc. If you know how to do this, the package.xml and payload should suffice.
    - If you *don't* know how to do a metadata deployment, create a .zip of the *package.xml* file and the *wave* directory together, [and then follow this guide](https://help.salesforce.com/s/articleView?id=000315117&type=1); note in Step 7 you *do have to* click the 'Single Package' checkbox.
  - Copying and pasting the dashboard JSON from the *json* folder direclty into a new, blank dashboard in Tableau CRM. This method is not preferred as you're not deploying the dashboard's extended metadata (XMD), and so you lose the rich, informative colour-coded metrics that were lovingly added to the dashboards. Only use this approach as a last resort or you just want an initial glance.


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
![Apex_Exceptions](https://user-images.githubusercontent.com/20658634/147172587-f2fa43ad-cdf7-44e7-b0e9-e12084cc7a44.png)

## Notes, Observations, Comments:

The Dashboards are in an app / folder called "Event Monitoring Plus" that uses the Event Monitoring licence type. Please do not click the 'Reconfigure' link at the top of the app page; it will overwrite and remove these dashboards.

If you're used to working with Event Monitoring data in Tableau CRM, you might be used to working with the *'~WithUsers'* datasets. We have opted not to use these for two reasons: augmenting the event logs with user names is a Tableau CRM function. This package focuses on highlighting the valuable insights you can gain from event logs without too much Tableau CRM magic. As much as we love Tableau CRM (*it really is just the best thing ever*), users have a choice of using any analytical or log processing tool to gain these insights. So for that reason we've stuck with the original logs. (Although, there is *a bit* of sourcery in the Apex Exceptions dashboard, which does some if-else (or *case statement*) logic on the EXCEPTION_MESSAGE and STACK_TRACE columns, but nothing you can't do in Python). The second point is, some people forget to schedule the dataflow and the *'~WithUsers'* datasets can be out of date, so we're just focusing on the logs that are most likely to be up-to-date.

The app is deployed with a default security setting of 'Manager' for the Entire Organisation; anyone in the Entire Organisation that has access to Tableau CRM. Users will only be able to view the contents of the dashboards *if* they also have access to the datasets. Your datasets are protected within whichever app you've stored them in and so will remain as the main security control.
Related point: feel free to edit and save these dashboards into your existing Event Monitoring Tableau CRM app. Again, be aware of reconfiguring any templated apps, as it will delete these dashboards.

This is on Github for the momement as it's the easiest way for Event Monitoring customers to get some additional insights from the Event Logs without being overly-reliant on additional Tableau CRM features. If we were to follow Tableau CRM best practices of app creation, packaging, etc, each customer would need to purchase a full Tableau CRM licence. There is a much-more robust Tableau CRM version of these dashboards in the works, so watch this space. Or what the AppExchange, where it may end up.
