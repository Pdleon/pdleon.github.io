---
title: "Auto Attendant Toll Fraud Call"
date: 2017-03-17
tags: [Sonus, Auto Attendant]
excerpt: "Configuring and troubleshooting Toll Fraud calls through an Auto Attendant
---



## Problem

Customer  received an email from SIP provider advising of a possible fraud  occurrence. [Source Number] has alarmed for a growing volume of calls to  [+1264xxxxxxx], please verify that destination number is authorized for  business.



![img](../../../../../Users/dponcedeleon/Dropbox/Images/Fraud.jpg)

 

## Troubleshooting

- Search for the source number to identify the user/object assigned to that number. 
- Rely on the user activity report under Skype monitoring reports to retrieve more information on the number[s] called. 
  - We  can see that there is a large volume of calls (275) to this number  located in Anguilla which the business has no need to interact with.

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-23-2017 -0003 AutoAttendant.png)

 

When  we dig into the details of this call, the most important detail is the  “Referred by” field which will point you out the object that is placing  the calls from  and in our case it was an auto attendant hosted in O365  which its dial plan and voice policy allows to dial out 11 digit number  with the country code of North America +1

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-23-2017 -0002 AutoAttendant.png)




##  Solution

There  are multiple ways to address this issue, each solution should be  applied to whatever fits your environment best. In no particular order:

- - Restrict the auto attendant and create dialing rules. 
  - Create an outbound trunk configuration rule to manipulate all these area codes and terminate the call. 
  - Create  a dial plan and voice policy in Skype that allows/restricts numbers  according to the business requirements and apply it to the Exchange UM  object in Skype. 
  - Terminate  [ reject ] the call at the gateway level,  depending on your  environment, this could be done for the calling number [ source ] or  your called number [ destination ].
  - Create  an unassigned number range in Skype, include all the area codes to  block and send that to a recorded announcement. This solution adds  flexibility if for some reason a legit business call gets trapped in the  rule. It can be easily traced, diagnosed, and could politely warn the  caller  to stop such activities.

We  needed to put a plan in place and the easiest and fastest way to  address this was to terminate the call at the gateway level and then  figure it out another solution that fits the environment best.

 

Here is the list of countries that are part of this loop hole:

242     Bahamas
246     Barbados
264     Anguilla (split from 809)
268     Antigua and Barbuda
284     British Virgin Islands
340     US Virgin Islands: St Thomas, St John
345     Cayman Islands
441     Bermuda
473     Grenada
649     Turks and Caicos Islands
664     Montserrat
670     Northern Mariana Islands
671     Guam
758     St. Lucia
767     Dominica
784     St. Vincent and Grenada
787     Puerto Rico
809    Caribbean, Bermuda, Puerto Rico, Virgin Islands
868    Trinidad and Tobago
869    St. Kitts/Nevis
876    Jamaica
900    Pay_Per_Call Numbers
939    Puerto Rico
976    Pay_Per_Call Numbers

 

## Steps

- In our Sonus SBC 2000 we first create a new transformation table and we call it Block Outbound Calls. 
  - Add  a new rule, the input field type should be Called Address/Number and  add your regex condition that blocks all numbers destined to the area  codes listed above with any remaining 7 digits. 

**^(\+1(242|246|264|268|284|340|345|441|473|649|664|670|671|758|767|784|787|809|855|868|869|876|900|939|976)\d{7})**

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-22-2017 -0007 AutoAttendant.png)

 

- Add  this transformation rule to your call routing table that handles calls  from Skype to SIP provider and move it all the way to the top to be the  first rule processed in the route.
  - Number/Name Transformation Table : Block Outbound Calls 
  - Destination Type : Deny 
  - Deny Q.850 Cause Code : 21 Call Rejected

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-22-2017 -0000 AutoAttendant.png)

 

- Apply these rules to all the necessary gateways in your environment based on all your internal routing rules.
- Ready to test, start capturing from your LX tool.
  - Place  a call from your client to any number in the list [ search for hotel  numbers ] or call your auto attendant and dial the number when you get  to the option of dial by extension.
  - In your LX tool, do an advanced search and look for the number called, in our case we see a successful 603 decline code.

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-23-2017 -0006 AutoAttendant.png)

 

| Response | Code    | Definition    |
| -------- | ------- | ------------- |
| Q.850    | **21**  | Call Rejected |
| SIP      | **603** | Decline       |

If we look at that call, we get the proper response code.

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-23-2017 -0007 AutoAttendant.png)

 

We can also look into the log transcript and follow the call route

 

![img](../../../../../Users/dponcedeleon/Dropbox/Images/3-22-2017 -0004 AutoAttendant.png)






##  Notes

For  now, this solution addresses the issue but will be looking into  implementing other solutions and keep everything contained in the skype  environment, perhaps a dial plan and voice policy applied to the  exchange object is preferable for this specific scenario.

Stay  tuned for an update and feel free to suggest any other possible  solution if you have experienced the same in your environment.



## References

 

- [SIP Code Response @ Sonus](https://support.sonus.net/display/UXAPIDOC/SIP+Response+Codes+-+Reference)
- [Q.850 Code Response @ Sonus](https://support.sonus.net/display/UXAPIDOC/Q.850+Cause+Codes+-+Reference)
- [About Auto Attendant @ MS](https://technet.microsoft.com/en-us/library/cc526581.aspx) | [Unified Messaging @ MS TechNet](https://technet.microsoft.com/en-us/library/cc526590.aspx)

