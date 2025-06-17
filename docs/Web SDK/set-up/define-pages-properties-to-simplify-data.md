---
title: Define Pages & Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
### Overview

Defining pages helps you simplify your analytics by grouping similar URLs under consistent and meaningful names. This ensures cleaner data, easier reporting, and better insights across web and mobile platforms.

**With page definitions, you can:**

* Group related URLs under a single page or screen name (e.g., “Product Page”).
* Extract and store dynamic values (like product name or category) as properties.
* Standardise naming conventions for pages and screens.
* Tag pages using URL fragments or parameters.

<br />

### Benefits of Defining Pages

* **Cleaner, More Actionable Data**\
  Eliminate clutter by treating dynamic values (e.g., IDs) as properties rather than separate pages.
* **Standardized Reporting**\
  Aggregate similar pages (e.g., all product pages) under one name for clearer, more concise analytics.
* **Improved Filtering and Segmentation**\
  Filter sessions by saved properties like product category or name to discover specific insights.
* **Consistent Cross-Platform Tracking**\
  Align naming across web and mobile to maintain structured analytics.
* **Smarter Alerts and Insights**\
  Grouping pages enables more accurate anomaly detection and performance monitoring.

<br />

> 👍 **Tip**
>
> Always define your pages if your URLs contain dynamic path parameters or fragments.

> 📘 **Note**
>
> If screen names are not manually defined, UXCam defaults to displaying the URL path as the screen name.

<br />

## **Accessing the Page Definition Section**

Without any dev dependency, with simple and intuitive UI, immediate changes and rule testing capabilities.

1. Clicking on the web app you created from the left side bar and opening the 'App setting'
2. Navigating to the 'Page definition' tab

<Image align="center" src="https://files.readme.io/c700417c7780aa316cd061043db4146dafc14ef012aa638c986f6bd5e626dbbf-Page-Defined.gif" />

<br />

<br />

## **Grouping Similar Content (Dynamic IDs in URLs)**

To ensure consistent tracking and analysis, it’s important to group URLs with dynamic path parameters—like user or product IDs—under a single, logical page name. This helps avoid treating every variation as a separate page and simplifies data reporting.

<br />

1. **Add a Representative URL**\
   Add a URL that reflects the structure of the pages you want to group

> Example: On Spotify, each artist has a unique ID represented in the URL. Even though the artist ID changes, these are all versions of the same **Artist Page**, and should not be considered separate pages.
>
> * /artists/1HY2Jd0NmPuamShAr6KMms).

<br />

2. **Configure the Dynamic Segment**

* Define the part of the dynamic path that varies and name it for later filtering and segmentation.
* To name dynamic URL part, clicking on it and give it a name
* It is also possible to use a wildcard (\*) instead to define consistent path without a name

> Example:
>
> * Album - Part of the URL path that is static
> * Artist ID - Part of the URL path that is dynamic (1HY2Jd0NmPuamShAr6KMms)
> * Assigning a placeholder name (e.g., `artist-id`) to capture it as a property for later filtering and segmentation purposes.

<br />

3. **Define the Page Name**

> Example: Artist Page

<br />

4. **Review and Test the Rule**\
   Validate the rule by previewing it with multiple URL variations to ensure accurate grouping before saving.

<Image align="center" src="https://files.readme.io/b0d59cf8b623ff685a5a420795fa1eddbc3ee7aee8e88313d09c452ef77bd490-Screenshot2025-03-04at08_07_35.png" />

<br />

## **Tagging Fragments as distinct pages**

URL fragments (the part after # in a URL) can be used to represent different views or states within a single page. By defining them as distinct pages in UXCam, you can improve the granularity of your session data and better segment user behaviour.

1. Enter the URL that represents the page structure you want to define.
2. Select the checkbox to ensure the Fragment is extracted from the URL
3. Define the Page Name
4. Confirm the pattern and test it with different URL variations before saving

<Image align="center" src="https://files.readme.io/85435ee8f436f4dbce978c491667d4a810dad88d3923d4934d0618d11d196ed1-Screenshot2025-03-04at08_15_47.png" />

<br />

## **Renaming Pages for Cleaner Reports**

To make your reports more readable and consistent, you can rename long or complex URLs with short, descriptive page names. This helps streamline analytics and aligns your data with internal naming conventions.

1. **Enter a URL** that represents the page you want to customise.
2. **Assign a clear, concise name** that reflects the page’s purpose (e.g., “Join Page” for a sign-up URL).

> Example: '[https://www.spotify.com/join/](https://www.spotify.com/join/)' renamed to -> **Join page**

<Image align="center" src="https://files.readme.io/2963d13b538a678b55a8fb52b91e3ce8014e40e7675ff99e47a17a0d98015743-Screenshot2B2025-03-042Bat2B08_11_01.png" />

<br />

## **Defining pages name with dynamic path and fragments**

Dynamic URLs with path parameters or fragments can clutter your reports and make it harder to analyse user behaviour. With UXCam’s Page Definition feature, you can group similar URLs, extract useful data, and assign clean, readable page or screen names - all in one step. This ensures your analytics remain consistent, easy to interpret, and aligned with your naming conventions.

1. **Enter a Sample URL** - Use a URL that reflects the structure of the page you want to define.
2. **Enable Fragment Matching** - Select the option indicating that your URL contains a fragment (e.g., #item-choice).
3. **Identify the Dynamic Segment** - Select the changing part of the path (e.g., `b12`) and optionally store it as a property (e.g., `RestaurantName`) for later filtering.
4. **Assign a Clear Page Name** - Enter a meaningful name that describes the screen (e.g., "Restaurant Choose Items").
5. **Review and Test** - Validate your rule against different URL variations to ensure consistent tagging.

<Image align="center" src="https://files.readme.io/59f2a79f3cb28eec613fe37424467694e93d248a99f9d8d7a878915220709964-Screenshot_2025-03-04_at_08.18.23.png" />

<br />

## **Assigning multiple rules**

When creating rules, you can assign the same 'page name' to multiple rules, these will be processed and displayed by UXCam as a single screen.

This can also be useful in case your URLs change but you want to keep your data consistent.

E.g. if you used to have `domain.com/products/product-name` defined as Products Page and now you have `domain.com/category/products/product-name `you can name it as Products Page and archive your previous rule.

💡 If you have dynamic URLs that share the same URL structure e.g. `domain.com/{dynamic-category-id} `vs `domain.com/{dynamic-product-id}.` Read the guides below:

[Resolving conflicting Page Definition rules for similar URL structures with dynamic values](https://help.uxcam.com/en/articles/11145060-resolving-conflicting-page-definition-rules-for-similar-url-structures-with-dynamic-values)

To learn more about Page definition, rule hierarchy and rule management please visit our **[Help Center.](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_bc701cd1b1)**

<br />

***

<br />

## How to use Page Definition for different use cases

* [Grouping Similar Content (Dynamic IDs in URLs)](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_11e7c1795b)
* [Tagging Fragments as distinct pages](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_924d5533fc)
* [Renaming pages for cleaner reports](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_3bf133bcdf)