---
layout: sidebar
title: Extensions and the Add-on ID
permalink: /documentation/develop/extensions-and-the-add-on-id/
topic: Develop
tags: [webextensions]
contributors:
  [
    Rob--W,
    jsantell,
    mdnwebdocs-bot,
    charmander,
    freaktechnik,
    wbamberg,
    serv-inc,
    scheinercc,
    mconca,
    DamienCassou,
    andrewtruongmoz,
    andymckay-github,
    timdream,
    Timendum,
    willdurand,
  ]
last_updated_by: willdurand
date: 2022-05-17
---

<!-- Page Hero Banner -->

{% capture page_hero_banner_content %}

# Extensions and the add-on ID

Firefox add-ons contain a unique ID which is used to distinguish this add-on from any other Firefox add-on.

This article describes how add-on IDs are handled for extensions that are built with WebExtensions APIs.

Firefox add-ons contain a unique identifier which is used both inside Firefox itself and on the [addons.mozilla.org](https://addons.mozilla.org/) (AMO) website. For example, it's used by Firefox to check for updates to installed add-ons and to identify which objects (such as data stores) are controlled by this add-on.

{% endcapture %}
{% include modules/page-hero.liquid,
  content: page_hero_banner_content
%}

<!-- END: Page Hero Banner -->

<!-- Single Column Body Module -->

<section id="basic-workflow-with-no-add-on-id" class="module">
<aside class="module-aside table-of-contents">

{%- include contents.liquid -%}

</aside>
<article class="module-content grid-x grid-padding-x">
<div class="cell small-12">

## Basic workflow with no add-on ID (Manifest V2)

Extensions can explicitly set the add-on ID using the [`browser_specific_settings`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/manifest.json/browser_specific_settings) key in manifest.json. However, this key is usually optional for Manifest V2 extensions. If you don't set it, then you can usually develop, debug, publish, and update your extension without ever having to deal with an ID. One advantage of this is that Google Chrome does not recognize the `browser_specific_settings` key and will show a warning if you include it.

Note, though, that some WebExtension APIs use the add-on ID and expect it to be the same from one browser session to the next. If you use these APIs in Firefox, then you must set the ID explicitly using the [`browser_specific_settings`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/manifest.json/browser_specific_settings) key. See [When do you need an Add-on ID?](#when-do-you-need-an-add-on-id).

### Developing and debugging

If your manifest.json does not contain an ID then the extension will be assigned a randomly-generated temporary ID when you [install it in Firefox](/documentation/develop/temporary-installation-in-firefox/) through `about:debugging`. If you then reload the extension using the "Reload" button, the same ID will be used. If you then restart Firefox and load the add-on again, it will get a new ID.

If you turn the extension into an `.xpi` or `.zip` and install it through `about:addons`, it will not work. To have it work in this scenario, you will need to add in the [`browser_specific_settings`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/manifest.json/browser_specific_settings) key in `manifest.json`.

### Publishing

Once you have finished developing the extension, you can [package it and submit it to AMO for review and signing](/documentation/publish/signing-and-distribution-overview/). If the packaged extension you upload does not contain an ID, AMO will generate one for you. It's only at this point that the add-on will be assigned a permanent ID, which will be embedded in the signed packaged extension.

### Updating

Even after this point, though, you don't generally have to deal with the ID at all. You can continue to develop the add-on without an ID, and when you want to update, upload the new version by visiting the add-on's AMO page. Because you are uploading the add-on through that page, AMO knows that this is an update to this particular add-on, even though it doesn't contain an ID.

::: note
It's essential with this workflow that you update the add-on _manually using its page on AMO_, otherwise AMO will not understand that the submission is an update to an existing add-on, and will treat the update as a brand-new add-on.
:::

## What happens with Manifest V3?

All [Manifest V3 extensions](/documentation/develop/manifest-v3-migration-guide/) need an add-on ID in their manifest.json when submitted to AMO. Contrary to Manifest V2 extensions, AMO will not accept Manifest V3 extensions without an ID and it will not automatically embed this ID in the signed packaged extension.

</div>
</article>
</section>

<!-- END: Single Column Body Module -->

<!-- Single Column Body Module -->

<section id="when-do-you-need-an-add-on-id" class="module">
<article class="module-content grid-x grid-padding-x">
<div class="cell small-12">

## When do you need an add-on ID?

All [Manifest V3 extensions](/documentation/develop/manifest-v3-migration-guide/) need an add-on ID in their manifest.json when submitted to AMO.

For Manifest V2 extensions, you need an add-on ID when:

- If you want to install an unsigned add-on from its XPI file, rather than loading it temporarily using `about:debugging`.
- If you want to have a value other than a randomly generated ID upon [getting your extension signed](/documentation/publish/#get-your-extension-signed) for the first time.
- If you use [AMO's API](https://addons-server.readthedocs.io/en/latest/topics/api/signing.html) for uploading your add-on, rather than uploading it manually on its page, then you need to include the add-on's ID in the request.
- Some WebExtension APIs use the add-on ID and expect it to be the same from one browser session to the next. If you use these APIs, then you must set the ID explicitly using the [`browser_specific_settings`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/manifest.json/browser_specific_settings) key. This applies to the following APIs:
  - [`storage.managed`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/storage/managed "A storage.StorageArea object that represents the managed storage area. Items in managed storage are set by the domain administrator or other native applications installed on user's computer, and are read-only for the extension. Trying to modify this storage area results in an error.")
  - [`storage.sync`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/storage/sync 'Represents the sync storage area. Items in sync storage are synced by the browser, and are available across all instances of that browser that the user is logged into (e.g. via Firefox sync, or a Google account), across different devices.')
  - [`identity.getRedirectURL`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/identity/getRedirectURL 'Generates a URL that you can use as a redirect URL.')
  - [Native messaging](https://developer.mozilla.org/Add-ons/WebExtensions/Native_messaging)
  - [`pkcs11`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/pkcs11 'The pkcs11 API enables an extension to enumerate PKCS #11 security modules and to make them accessible to the browser as sources of keys and certificates.')
  - [`runtime.onMessageExternal`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onMessageExternal "This API can't be used in a content script.")
  - [`runtime.onConnectExternal`](https://developer.mozilla.org/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onConnectExternal 'Fired when an extension receives a connection request from a different extension.')
- Using the [`dictionaries` key in manifest.json](https://developer.mozilla.org/Mozilla/Add-ons/WebExtensions/manifest.json/dictionaries).

See [`browser_specific_settings` in manifest.json](https://developer.mozilla.org/Add-ons/WebExtensions/manifest.json/browser_specific_settings) for the syntax of setting the extension ID.

</div>
</article>
</section>

<!-- END: Single Column Body Module -->


