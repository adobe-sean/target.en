---
keywords: mbox.js faq;mbox.js frequently asked questions;document.write;tt.omtrdc.net;parser blocking
description: Learn about the legacy mbox.js implementation of Adobe Target. Migrate to the Adobe Experience Platform Web SDK (AEP Web SDK) or to the latest version of at.js.
title: What Are Some Frequently Asked Questions about [!DNL Target] mbox.js?
feature: at.js
role: Developer
exl-id: 0e207896-d45b-45f9-8556-6532fda72a45
---
# mbox.js Frequently Asked Questions

Answers to frequently asked questions about mbox.js.

>[!IMPORTANT]
>
>**mbox.js end-of-life**: As of March 31, 2021, [!DNL Adobe Target] no longer supports the mbox.js library. Post March 31, 2021, all calls made from mbox.js will gracefully fail and impact your pages that have [!DNL Target] activities running by serving default content.
>
>We recommend that all customers migrate to the most recent version of the new [!DNL Adobe Experience Platform Web SDK] or the at.js JavaScript library before this date to avoid any potential issues with your sites. For more information, see [Overview: implement Target for client-side web](/help/c-implementing-target/c-implementing-target-for-client-side-web/implement-target-for-client-side-web.md).

## What is the impact of mbox.js on page-load times? {#section_90B3B94FE0BF4B369577FCB97B67F089}

For more information, see [Benefits of at.js](/help/c-implementing-target/c-implementing-target-for-client-side-web/t-mbox-download/c-target-atjs-implementation/target-atjs-implementation.md#benefits).

## Why do I get "Parser-blocking" warning messages in Google Chrome when using mbox.js and document.write? {#section_355A3A5BF02F42EEB8271C96EF41590A}

This console message displays when using Chrome in many scenarios in which the `document.write` function is used within the mbox.js file. This is a warning message and should not affect your activity setup process.

The best way to prevent this situation is to [migrate your Target implementation to the at.js JavaScript library](/help/c-implementing-target/c-implementing-target-for-client-side-web/t-mbox-download/c-target-atjs-implementation/target-migrate-atjs.md#task_DE55DCE9AC2F49728395665DE1B1E6EA), which doesn't use the `document.write` function. Using at.js provides many advantages versus using mbox.js. For more information, see [at.js Frequently Asked Questions](/help/c-implementing-target/c-implementing-target-for-client-side-web/c-target-atjs-faq/target-atjs-faq.md#concept_D6EFE8D84A06476DB5ABD494D7E8C769).

## Why are my mboxes not firing on my web pages? {#section_4BA5DA424B734324AAB51E4588FA50F5}

Target customers sometimes use cloud-based instances with [!DNL Target] for testing or simple proof-of-concept purposes. These domains, and many others, are part of the [Public Suffix List](https://publicsuffix.org/list/public_suffix_list.dat).

Modern browsers won't save cookies if you are using these domains unless you customize the `cookieDomain` setting using targetGlobalSettings(). For more information, see [Using Cloud-Based Instances with Target](/help/c-implementing-target/c-implementing-target-for-client-side-web/c-target-debugging-atjs/targeting-using-cloud-based-instances.md#concept_A2077766948F4EA081CE592D8998F566).

## What is the domain tt.omtrdc.net that [!DNL Target] server calls go to? {#section_999C29940E8B4CAD8A957A6B1D440317}

[!DNL tt.omtrdc.net] is the domain name for Adobe's EDGE network, used to receive all server calls for Target.

## Why don't at.js and mbox.js use HttpOnly and Secure cookie flags? {#section_74527E3B41B54B0A83F217C3E664ED1F}

HttpOnly can be set only via server-side code. Target cookies, such as mbox, are created and saved via JavaScript code, so Target can't use the HttpOnly cookie flag.

Secure can be set via JavaScript only when the page has been loaded via HTTPS. If the page initially loads via HTTP, JavaScript can't set this flag. In addition, if the Secure flag is used, the cookie will be available only on HTTPS pages.

To ensure that Target can properly track users, and because the cookies are generated client-side, Target doesn't use either of these flags.
