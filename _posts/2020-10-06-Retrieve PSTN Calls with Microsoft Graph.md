---
title: "Microsoft Teams - Retrieve PSTN Call Records with Microsoft Graph"
tags: [MS Graph]
excerpt: "Connect to Microsoft Graph and use Powershell to retrieve PSTN and Direct Routing Call Records"
---

> All credit goes to this blog | script on Github [leeford-Get-TeamsPSTNCallRecords](https://github.com/leeford/Get-TeamsPSTNCallRecords)

Now, lets put this in practice and find out how it works



### From the Microsoft official Documentation 

## Call Record Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](https://docs.microsoft.com/en-us/graph/permissions-reference).

| Permission type                        | Permissions (from least to most privileged) |
| :------------------------------------- | :------------------------------------------ |
| Delegated (work or school account)     | Not supported.                              |
| Delegated (personal Microsoft account) | Not supported.                              |
| Application                            | CallRecords.Read.All                        |

## Application permissions vs Delegated permissions

This is important to understand because when using the graph explorer you may encounter consent permissions warnings|errors to execute the query as the *CallRecords.Read.All* is specific to an application and not user scope.

- **Application permissions** allow an application in Azure Active Directory to act as it’s own entity, rather than on behalf of a specific user.
- **Delegated permissions** allow an application in Azure Active Directory to perform actions on behalf of a particular user.



#### Application permissions

| Permission             | Display String        | Description                                                  | Admin Consent Required |
| :--------------------- | :-------------------- | :----------------------------------------------------------- | :--------------------- |
| *CallRecords.Read.All* | Read all call records | Allows the app to read call records for all calls and online meetings without a signed-in user. | Yes                    |

### Remarks

The *CallRecords.Read.All* permission grants an **application** privileged access to [callRecords](https://docs.microsoft.com/en-us/graph/api/resources/callrecords-callrecord) for every call and online meeting within your organization, including calls to and from external phone numbers. This includes potentially sensitive details about who participated in the call, as well as technical information pertaining to these calls and meetings that can be used for network troubleshooting, such as IP addresses, device details, and other network information.

### Example usage

#### Application

- *CallRecords.Read.All*: Retrieve a call record (`GET /v1.0/communications/callRecords/{id}`).
- *CallRecords.Read.All*: Subscribe to new call records (`POST /v1.0/subscriptions`).
- *CallRecords.Read.All*: Retrieve direct routing call records within the specified time range (`GET /v1.0/communications/callRecords/microsoft.graph.callRecords.getDirectRoutingCalls(fromDateTime={start date and time),toDateTime={end date and time))`)



Microsoft provides the ability to test and validate your query through graph explorer but since this is an application specific resource/permission, we get a 403 requesting for consent of an application or in this case not supported



![](../assets/images/2020-10-06-Graph%20Explorer%20-CallRecords.png)





Back to the original blog/source:

- Create permissions for an application in Azure | App Registration | +New Registration -> Name it however you want it
- Copy the application ID and Directory ID



![](../assets/images/Azure-NewAppRegistration.png)



- Create a new client secret and copy your string

  

  ![](../assets/images/Azure-AppCertificateandSecrets.png)



- Back to Azure Active Directory | Enterprise applications | %Your App Name% | Permissions 
  - Make sure that you have the necessary permissions for either your domain or the user to run this application. In this case I am consenting permissions to all users in my organization for test purposes. You can review  and modify the permissions for your application.

    
  
  ![](../assets/images/Azure-AppPermissions.png)



- Back to the github script link below, click on Raw and copy the script to your preferred editor and modify your applicationid, directory id and client secret string as illustrated on his blog.

  [Get-TeamsPSTNCallRecords]: https://github.com/leeford/Get-TeamsPSTNCallRecords/blob/master/Get-TeamsPSTNCallRecords.ps1

  

![](../assets/images/Github-RawScript.png)



![](../assets/images/GetPSTNAdaptedScript.png)



- Let's run and test the script - currently running on Powershell 7.0.3 and also ran in Powershell 5.1 with success

![](../assets/images/GetPSTNRunningScript.png)





- Everyone has a preference on the format but I find it's easy to read and follow a json file. Here is a sample of the output:

![](../assets/images/OutputJson.png)



- Final thoughts:
  - Thank you to Lee for this script , great work 
  - Where do we go from here ? Since Microsoft only stores information up to 1 year, perhaps there is a need to move this data to another repository whether is a Sharepoint or Microsoft list, an external/internal database or simply leave it on a json,csv file for archiving purposes. 
  - What do we do with all this data, we can utilize Power Bi to display a nice graph or trend within your environment but also this could serve to your helpdesk or admin to troubleshoot PSTN calls.