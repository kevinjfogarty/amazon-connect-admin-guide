# Set up outbound caller ID<a name="queues-callerid"></a>

We recommend setting your outbound caller ID\. Not doing so may result in some PSTN carriers considering your outbound calls fraudulent activity, and they may drop them\. 

There are a few times when your outbound caller ID—your company name and number—will appear to contacts:
+ During customer callbacks\.
+ If an agent makes an outbound call\.
+ If an agent transfers a call, for example, to an external number\.

There are a few places where you can specify what your outbound caller ID:
+ In a queue\. You can specify both the outbound caller ID name and the phone number\. For instructions, see [Create a queue](create-queue.md)\.
+ In the **Call phone number** block in an outbound whisper contact flow\. You can use this block with the **Set contact attributes** block to set the callback number dynamically\. For example, you can display a certain caller ID number based on the customer's account type\.
+ In the [Transfer to phone number](transfer-to-phone-number.md) block\. 

## Use the Call phone number block to set caller ID<a name="using-call-number-block"></a>

Use the [Call phone number](call-phone-number.md) block in an outbound whisper flow to initiate an outbound call to a customer and, optionally, specify a custom caller ID number that is displayed to call recipients\. This is useful when you have multiple telephone numbers used to make outbound calls, but want to consistently display the same company phone number for the caller ID for calls made from your contact center\. You can also use the block to display a phone number for a specific line of business, or for displaying different numbers to customers based on their account type\.

There are two ways you can set up how caller ID works for outbound calls: 
+ Select any phone number from your instance\.
+ Or, use an attribute to set the number dynamically during the contact flow\. 

Only phone numbers that you've [claimed](claim-phone-number.md) or [ported to Amazon Connect](port-phone-number.md) can be used as your caller ID number\.

### About using dynamic caller ID<a name="using-dynamic-caller-id"></a>

If you use an attribute to set the caller ID number dynamically, the attribute can be one you define in the **Set contact attributes** block in the contact flow\. Or, it can be an external attribute returned from an AWS Lambda function\.

The value of the attribute must be a phone number from your instance in [ E\.164](https://www.itu.int/rec/T-REC-E.164/en) format\. 
+ If the number is not in E\.164 format, the number from the queue associated with the outbound whisper flow is used for the caller ID number\.
+ If no number is set for the outbound caller ID number for the queue, the call attempt will fail\.

### Use E\.164 format for international phone numbers<a name="international-calls-ccp"></a>

Amazon Connect requires phone numbers in [ E\.164](https://www.itu.int/rec/T-REC-E.164/en) format\. 

To express a US phone number in E\.164 format, add the '\+' prefix and the country code in front of the number\. For example, for a US number: 
+  \+1\-800\-555\-1212

In the UK and many other countries internationally, local dialing requires the addition of a 0 in front of the subscriber number\. However, to use E\.164 formatting, this 0 must be removed\. A number such as 020 718 xxxxx in the UK would be formatted as \+44 20 718 xxxxx\.

Phone numbers that are not formatted in E\.164 may work, but it depends on the phone or handset that is being used as well as the carrier from which the call is originated\.

When you place calls from the CCP using Amazon Connect the CCP provides the correct formatting for numbers automatically\.

### How caller ID works in **Call phone number** block<a name="call-number-block-how-it-works"></a>

Outbound whisper flows execute in Amazon Connect immediately after an agent accepts the call during direct dial and callback scenarios\. When the contact flow executes: 
+ The caller ID number is set if one is specified in the **Call phone number** block\.
+ If no caller ID is specified in the **Call phone number** block, the caller ID number defined for the queue is used when the call is placed
+ When there is an error with a call that is initiated by the **Call phone number** block, the call is disconnected and the agent is placed in ACW status\.

The **Call phone number** block is supported only in outbound whisper flow contact flows\. Only published contact flows can be selected as the outbound whisper flow for a queue\.

### Specify a custom caller ID number using a **Call phone number** block

1. In Amazon Connect choose **Routing**, **Contact flows**\.

1. Choose the down arrow next to **Create contact flow**, and then choose **Create outbound whisper flow**\.

1. Add a **Call phone number** block to the flow, and connect the **Entry point** block to it\.

   The **Call phone number** block must be placed before a **Play prompt** block if one is included in your contact flow\.

1. Select the **Call phone number** block, and then select **Caller ID number to display**\.

1. Do one of the following:
   + To use a number from your instance, choose **Select a number from your instance**, and then search for or select the number to use from the drop\-down\.
   + Choose **Use attribute** to use a contact attribute to provide the value for the caller ID number\. You can use either a **User Defined** attribute you create using a **Set contact attributes** block, or an **External** attribute returned from an AWS Lambda function\. The value of any attribute you use must be a phone number claimed for your instance and be in E\.164 format\. If the number used from an attribute is not in E\.164 format, the number set for the **Outbound caller ID number** for the queue is used\.

1. Add any additional blocks to complete your contact flow, and connect the **Success** branch of the **Call phone number** block to the next block in the flow\. Note that there is no error branch for the block\. If a call is not successfully initiated, the contact flow ends and the agent is placed in an **AfterContactWork** \(ACW\) state\.

## Why your caller ID might not appear correctly to customers<a name="why-callerid-name-might-not-appear-correctly"></a>

Amazon Connect presents Outbound Caller ID Name correctly via the Calling Line/Party Presentation service on outbound calls\. In testing, with all of our telephony providers, the Outbound Caller ID Name value comes back to us intact on all the carriers we use\. This service is not consistent because downstream carriers \(including mobile carriers\) often ignore the value we set in the Outbound Caller ID Name and CNAM is not regulated or enforced\. 

## How to avoid labels like "spam" and "telemarketer"<a name="enroll-in-CNAM-services"></a>

Amazon Connect has contracted with a leading provider of CNAM services for US numbers to provide Calling Name to the extent possible\. This enables outbound calls that show the enrolled Calling Line Identity \(CLI\) to generally avoid reputation\-sensitive labels like "spam" or "telemarketer\."

To enroll your numbers with this CNAM services provider, open a Support ticket\. Our Support team will gather the required information to enroll your numbers\. For instructions on how to access Support, see [Get administrative support for Amazon Connect](get-admin-support.md)\.

**Note**  
Only numbers in the 50 US states, Puerto Rico, and Virgin Islands are eligible\.