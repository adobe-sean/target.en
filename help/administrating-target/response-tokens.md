---
keywords: response tokens;tokens;plugins;plug-ins;at.js;response
description: Learn how to use response tokens in Adobe [!DNL Target] output-specific information to use in debugging and integrating with 3rd-party tools (such as Session Replay technology).
title: What are Response Tokens and How Do I Use Them?
feature: Administration & Configuration
role: Administrator
exl-id: d0c1e914-3172-466d-9721-fe0690abd30b
---
# Response tokens

Response tokens let you automatically output information specific to [!DNL Adobe Target] to your brand's web page. This information can include details about the activity, offer, experience, user profile, geo information, and more. These details provide extra response data to share with internal or 3rd-party tools (such as Session Replay technology) or to use for debugging.

Response tokens let you choose which variables (in key value pairs) to use and then enable them to be sent as part of a [!DNL Target] response. You enable a variable using the switch and the variable is sent with [!DNL Target] responses, which can be validated in network calls. Response tokens also work in [!UICONTROL Preview] mode.

A key difference between plug-ins and response tokens is that plug-ins deliver JavaScript to the page that executes upon delivery. Response tokens, however, deliver an object that can then be read and acted upon using event listeners. The response token approach is safer and allows for easier development and maintenance of 3rd-party integrations.

>[!NOTE]
>
>Response tokens are available with the [!DNL Adobe Experience Platform Web SDK] version 2.5.0 or later (release scheduled for June 1, 2021) and with at.js version 1.1 or later.

| Target SDK | Suggested actions |
|--- |--- |
|[Adobe Experience Platform Web SDK](/help/c-implementing-target/c-implementing-target-for-client-side-web/aep-web-sdk.md)|Ensure that you are using Platform Web SDK version 2.5.0 or later. For information about downloading the latest version of Platform Web SDK, see [Install the SDK](https://experienceleague.adobe.com/docs/experience-platform/edge/fundamentals/installing-the-sdk.html) in the *Platform Web SDK overview* guide. For information about new functionality in each version of the Platform Web SDK, see [Release notes](https://experienceleague.adobe.com/docs/experience-platform/edge/release-notes.html) in the *Platform Web SDK overview* guide.|
|[at.js](/help/c-implementing-target/c-implementing-target-for-client-side-web/c-how-atjs-works/how-atjs-works.md)|Ensure that you are using at.js version 1.1 or later. For information about downloading the latest version of at.js, see [Download at.js](/help/c-implementing-target/c-implementing-target-for-client-side-web/how-to-deployatjs/implementing-target-without-a-tag-manager.md). For information about new functionality in each version of at.js, see [at.js Version Details](/help/c-implementing-target/c-implementing-target-for-client-side-web/target-atjs-versions.md).<br>Customers using at.js are encouraged to use response tokens and move away from plugins. Some plugins that rely on internal methods that exist in mbox.js, but not in at.js, are delivered but fail. For more information, see [at.js Limitations](/help/c-implementing-target/c-implementing-target-for-client-side-web/t-mbox-download/c-target-atjs-implementation/target-atjs-limitations.md).|

## Using response tokens {#section_A9E141DDCBA84308926E68D05FD2AC62}

1. Ensure that you are using Platform Web SDK version 2.5.0 (or later) or at.js version 1.1 (or later).

   For more information:
   
   * **Platform Web SDK**: See [Install the SDK](https://experienceleague.adobe.com/docs/experience-platform/edge/fundamentals/installing-the-sdk.html) in the *Platform Web SDK overview* guide.
   * **at.js**: See [Download at.js](/help/c-implementing-target/c-implementing-target-for-client-side-web/how-to-deployatjs/implementing-target-without-a-tag-manager.md#concept_1E1F958F9CCC4E35AD97581EFAF659E2). 

1. In [!DNL Target], click **[!UICONTROL Administration]** > **[!UICONTROL Response Tokens]**.

   ![](assets/response_tokens-new.png)

1. Activate the desired response tokens, such as `activity.id` and `option.id`.

   The following parameters are available by default:

   | Type | Parameter | Notes |
   |--- |--- |--- |
   |Built-in Profiles|`profile.activeActivities`|Returns an array of the `activityIds` the visitor is qualified for. It increments as users are qualified. For example, on a page with two [!DNL Target] requests delivering two different activities, the second request includes both activities.|
   ||`profile.isFirstSession`|Returns "true" or "false."|
   ||`profile.isNewSession`|Returns "true" or "false."|
   ||`profile.daysSinceLastVisit`|Returns the number of days since the visitor's last visit.|
   ||`profile.tntId`|Returns the visitor's tntID|
   ||`profile.marketingCloudVisitorId`|Returns the visitor's Experience Cloud Visitor ID.|
   ||`profile.thirdPartyId`|Returns the visitor's third-party ID.|
   ||`profile.categoryAffinity`|Returns the visitor's favorite category.|
   ||`profile.categoryAffinities`|Returns an array of the visitor's top 5 categories as strings.|
   |Activity|`activity.name`<br>`activity.id`<br>`experience.name`<br>`experience.id`<br>`option.name`<br>`option.id`|Details of the current activity. Note that "option" equals "offer."|
   |Geo|`geo.country`<br>`geo.state`<br>`geo.city`<br>`geo.zip`<br>`geo.dma`<br>`geo.domainName`<br>`geo.ispName`<br>`geo.connectionSpeed`<br>`geo.mobileCarrier`|See [Geo](/help/c-target/c-audiences/c-target-rules/geo.md) for more information about using geo targeting in activities.|
   |Traffic Allocation Method<br>(Applies to [!UICONTROL Auto-Target] and [!UICONTROL Automated Personalization] activities only.)|`experience.trafficAllocationId`|Returns 0 if a visitor received an experience from being in “control” traffic and 1 if a visitor received an experience from the “targeted” traffic distribution.|
   ||`experience.trafficAllocationType`|Return “control” or “targeted."|

   User profile attributes and Customer Attributes also display in the list.

   >[!NOTE]
   >
   >Parameters with special characters do not display in the list. Only alphanumeric characters and underscores are supported.

1. (Conditional) To use a profile parameter as a response token, but the parameter has not been passed through a [!DNL Target] request and, thus, has not loaded into the [!DNL Target] UI, you can use the [!UICONTROL Add Response Token] button to add the profile to the UI.

   Click **[!UICONTROL Add Response Token]**, provide the token name, then click **[!UICONTROL Activate]**.

   ![](assets/response_token_create.png)

1. Create an activity.

## Listen for responses and read response tokens

The process you use to listen for [!DNL Target] responses and read response tokens differs depending on whether you have a [!DNL Platform Web SDK] or at.js implementation.

### ![Adobe Experience Platform Web SDK badge](/help/assets/platform.png) [!DNL Platform Web SDK] using the Handle object class

Use the Handle object class, which has a meta data object and a data object to listen for [!DNL Target] responses and read the response tokens.

The following code sample adds a [!DNL Platform Web SDK] custom event handler directly to the HTML page:

```html
<html>

<head>
 ...
 <script src="alloy.js"></script>
 <script>
  {
   "requestId": "4d0a7cfd-952c-408c-b3b8-438edc38250a",
   "handle": [{
    "type": "personalization:decisions",
    "payload": [{
     "id": "....",
     "scope": "__view__",
     "scopeDetails": {
      "decisionProvider": "TGT",
      "activity": {
       "id": "..."
      },
      "experience": {
       "id": "...."
      }
     },
     "items": [{
      "id": "123",
      "schema": "https://ns.adobe.com/personalization/dom-action",
      "meta": {
       "activity.id": "...",
       "activity.name": "...",
       "profile.foo": "...",
       "profile.bar": "..."
      },
      "data": {
       "id": "123",
       "type": "setHtml",
       "selector": "#foo",
       "prehidingSelector": "#foo",
       "content": "<div>Hello world</div>"
      }
     }]
    }]
   }]
  }
  });
 </script>
</head>

<body>
 ...
</body>

</html>
```

|Object|Information|
| --- | --- |
|Type – Personalization.decision|[!DNL Target] and Offer Decisioning data is passed here.|
|DecisionProvider – TGT|TGT-[!DNL Target]. [!DNL Target] provides the response token metadata and values to the page.|
|Meta|Meta data that is passed to the page.|
|Data|Values of the meta data passed to the page.|

### ![at.js badge](/help/assets/atjs.png) at.js using custom events

Use [at.js custom events](/help/c-implementing-target/c-implementing-target-for-client-side-web/atjs-custom-events.md) to listen for the [!DNL Target] response and read the response tokens.

The following code sample adds an [!DNL at.js] custom event handler directly to the HTML page:

```html
<html> 
  <head> 
    .... 
    <script src="at.js"></script> 
    <script> 
      document.addEventListener(adobe.target.event.REQUEST_SUCCEEDED, function(e) { 
        console.log("Request succeeded", e.detail); 
      }); 
    </script> 
  <head> 
  <body> 
  ... 
  </body> 
</html>
```

## Response token FAQs {#section_3DD5F32C668246289CDF9B4CDE1F536D}

**Which role is required to activate or deactivate response tokens?**

Response tokens can be activated or deactivated only by users with the [!DNL Target] [!UICONTROL Administrator] role.

**What happens if I am running [!DNL Platform Web SDK] 2.5.0 (or earlier)?

You do not have access to response tokens.

**What happens if I am running at.js 1.0 (or earlier)?**

You see the response tokens, but at.js cannot use them.

**Can I have both [!DNL Target Classic] plugins and response tokens active at the same time?**

Plugins and response tokens are available in parallel; however, plugins will be deprecated in the future.

**Are response tokens delivered through all [!DNL Target] responses or only through [!DNL Target] responses delivering an activity?**

Response tokens are delivered only through [!DNL Target] responses delivering an activity.

**My [!DNL Target Classic] plugin included JavaScript. How do I replicate its functionality using response tokens?**

When migrating to response tokens, this type of JavaScript must be kept in your codebase or tag management solution. You can trigger this code using [!DNL Platform Web SDK] or [!DNL at.js] custom events and pass the response token values to your JavaScript functions.

**Why does my profile/Customer Attributes parameter not display in the response tokens list?**

[!DNL Target] normally refreshes parameters every 15 minutes. This refresh depends on user action and data is refreshed only when you view the response tokens page. If your parameters do not display in the response token list, [!DNL Target] has not yet refreshed the data.

Also, if your parameter contains anything but non-alphanumeric characters or any symbol other than underscores, the parameter does not appear in the list. Currently, only alphanumeric and underscore characters are supported.

**Does the response token still deliver content if it uses a deleted profile script or a profile parameter?**

Response tokens extract information from user profiles and then deliver that information. If you delete a profile script or parameter, that does not mean that the information has been removed from the user profiles. The user profiles still have data corresponding to the profile script. The response token continues delivering the content. For users that do not have that information saved in their profiles, or for new visitors, that token is not delivered because the data is not present in their profiles.

[!DNL Target] does not toggle off the token automatically. If you delete a profile script and no longer want the token to be delivered, you must toggle off the token yourself.

**I renamed my profile script, but why is the token using that script still active with the old name?**

As mentioned above, response tokens work on the profile information saved for users. Even though you renamed your profile script, users who have visited your website have the old profile script value saved in their profiles. The token continues to pick up the old value that is already saved in the user profiles. If you now want to deliver content on the new name, you must toggle off the previous token and toggle on the new token.

**If my attributes have changed, when are they be removed from the list?**

[!DNL Target] performs a refresh of attributes at regular intervals. Any attribute that is not toggled on is removed during the next refresh. However, if you have an attribute that was toggled on and has been removed, that script is not removed from the attribute list until you toggle it off. As an example, you removed a profile script that was used as a token. [!DNL Target] removes only the toggled-off attributes from the list when they are deleted or renamed.

## Send data to Google Analytics

The following sections describe how to send [!DNL Target] data to Google Analytics:

### ![AEP badge](/help/assets/platform.png) Sending data to Google Analytics via Platform Web SDK

Google Analytics can be sent data via Platform Web SDK version 2.5.0 (or later) by adding the following code in the HTML page:

(Code to come)

### ![at.js badge](/help/assets/atjs.png) Sending data to Google Analytics via at.js {#section_04AA830826D94D4EBEC741B7C4F86156}

Google Analytics can be sent data via at.js by adding the following code in the HTML page:

```javascript
<script type="text/javascript"> 
  (function(i, s, o, g, r, a, m) { 
    i['GoogleAnalyticsObject'] = r; 
    i[r] = i[r] || function() { 
      (i[r].q = i[r].q || []).push(arguments) 
    }, i[r].l = 1 * new Date(); 
    a = s.createElement(o), 
      m = s.getElementsByTagName(o)[0]; 
    a.async = 1; 
    a.src = g; 
    m.parentNode.insertBefore(a, m) 
  })(window, document, 'script', 'https://www.google-analytics.com/analytics.js', 'ga'); 
  ga('create', 'Google Client Id', 'auto'); 
</script> 
 
<script type="text/javascript"> 
  document.addEventListener(adobe.target.event.REQUEST_SUCCEEDED, function(e) { 
    var tokens = e.detail.responseTokens; 
 
    if (isEmpty(tokens)) { 
      return; 
    } 
 
    var activityNames = []; 
    var experienceNames = []; 
    var uniqueTokens = distinct(tokens); 
 
    uniqueTokens.forEach(function(token) { 
      activityNames.push(token["activity.name"]); 
      experienceNames.push(token["experience.name"]); 
    }); 
 
    ga('send', 'event', { 
      eventCategory: "target", 
      eventAction: experienceNames, 
      eventLabel: activityNames 
    }); 
  }); 
 
  function isEmpty(val) { 
    return (val === undefined || val == null || val.length <= 0) ? true : false; 
  } 
 
  function key(obj) { 
     return Object.keys(obj) 
    .map(function(k) { return k + "" + obj[k]; }) 
    .join(""); 
  } 
 
  function distinct(arr) { 
    var result = arr.reduce(function(acc, e) { 
      acc[key(e)] = e; 
      return acc; 
    }, {}); 
   
    return Object.keys(result) 
    .map(function(k) { return result[k]; }); 
  } 
</script>
```

## Debugging

The following sections provide information about debugging response tokens:

### ![at.js badge](/help/assets/atjs.png) Google Analytics and debugging

The following code lets you debug using Google Analytics:

```javascript
<script type="text/javascript"> 
  (function(i, s, o, g, r, a, m) { 
    i['GoogleAnalyticsObject'] = r; 
    i[r] = i[r] || function() { 
      (i[r].q = i[r].q || []).push(arguments) 
    }, i[r].l = 1 * new Date(); 
    a = s.createElement(o), 
      m = s.getElementsByTagName(o)[0]; 
    a.async = 1; 
    a.src = g; 
    m.parentNode.insertBefore(a, m) 
  })(window, document, 'script', 'https://www.google-analytics.com/analytics.js', 'ga'); 
  ga('create', 'Google Client Id', 'auto'); 
</script> 
 
<script type="text/javascript"> 
  document.addEventListener(adobe.target.event.REQUEST_SUCCEEDED, function(e) { 
    var tokens = e.detail.responseTokens; 
 
    if (isEmpty(tokens)) { 
      return; 
    } 
 
    var activityNames = []; 
    var experienceNames = []; 
    var uniqueTokens = distinct(tokens); 
 
    uniqueTokens.forEach(function(token) { 
      activityNames.push(token["activity.name"]); 
      experienceNames.push(token["experience.name"]); 
    }); 
 
    ga('send', 'event', { 
      eventCategory: "target", 
      eventAction: experienceNames, 
      eventLabel: activityNames 
    }); 
  }); 
 
  function isEmpty(val) { 
    return (val === undefined || val == null || val.length <= 0) ? true : false; 
  } 
 
  function key(obj) { 
     return Object.keys(obj) 
    .map(function(k) { return k + "" + obj[k]; }) 
    .join(""); 
  } 
 
  function distinct(arr) { 
    var result = arr.reduce(function(acc, e) { 
      acc[key(e)] = e; 
      return acc; 
    }, {}); 
   
    return Object.keys(result) 
    .map(function(k) { return result[k]; }); 
  } 
```

### Debugging using the equivalent of the ttMeta plugin

The equivalent of the ttMeta plugin for debugging purposes can be created by adding following code to HTML page:

```javascript
<script type="text/javascript" > 
  document.addEventListener(adobe.target.event.REQUEST_SUCCEEDED, function (e) { 
    window.ttMETA= typeof(window.ttMETA)!="undefined" ? window.ttMETA : []; 
 
    var tokens=e.detail.responseTokens; 
 
    if (isEmpty(tokens)) { 
      return; 
    } 
     
    var uniqueTokens = distinct(tokens); 
 
    uniqueTokens.forEach(function(token) { 
      window.ttMETA.push({ 
        'CampaignName': token["activity.name"], 
        'CampaignId' : token["activity.id"], 
        'RecipeName': token["experience.name"], 
        'RecipeId': token["experience.id"], 
        'OfferId': token["option.id"], 
        'OfferName': token["option.name"], 
        'MboxName': e.detail.mbox}); 
      console.log(ttMETA); 
    }); 
  }); 
 
  function isEmpty(val){ 
    return (val === undefined || val == null || val.length <= 0) ? true : false; 
  } 
 
  function key(obj) { 
     return Object.keys(obj) 
    .map(function(k) { return k + "" + obj[k]; }) 
    .join(""); 
  } 
 
  function distinct(arr) { 
    var result = arr.reduce(function(acc, e) { 
      acc[key(e)] = e; 
      return acc; 
    }, {}); 
   
    return Object.keys(result) 
    .map(function(k) { return result[k]; }); 
  } 
</script>
```

## ![at.js](/help/assets/atjs.png) Training Video: Response Tokens and at.js Custom Events {#section_3AA0A6C8DBD94A528337A2525E3E05D5}

The following video explains how to use response tokens and at.js custom events to share profile information from [!DNL Target] to 3rd-party systems.

>[!NOTE]
>
>The [!DNL Target] [!UICONTROL Administration] menu UI (formerly [!UICONTROL Setup]) has been redesigned to provide improved performance, reduce the maintenance time required when releasing new features, and to improve the user experience across the product. The information in the following video is correct; however, options are in slightly different locations.

>[!VIDEO](https://video.tv.adobe.com/v/23253/)
