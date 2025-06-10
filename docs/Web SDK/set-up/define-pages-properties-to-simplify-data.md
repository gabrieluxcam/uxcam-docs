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
Page definition allows you to define your pages/screens by setting up matching rules for URLs to:

* Group similar pages under a single name. (e.g. Product page)
* Define and save properties based on URL data (e.g. Product name, product category) 
* Create clean and consistent Page/Screen names 
* Tag pages/screens based on Fragments

Without any dev dependency, with simple and intuitive UI, immediate changes and rule testing capabilities.

<Image align="center" src="https://files.readme.io/c700417c7780aa316cd061043db4146dafc14ef012aa638c986f6bd5e626dbbf-Page-Defined.gif" />

### Why should you define your pages/screens?

**Cleaner & More Actionable Data**: Reduce URL clutter by storing dynamic values (e.g., product ID, user ID) as properties instead of separate pages.

* **Standardized Reporting**: Group similar pages (e.g., Product Page, Category Page) under a common name for clearer analytics.
* **Enhanced Filtering & Segmentation**: Use saved properties like product name or category to refine searches and uncover specific insights.
* **Seamless Cross-Platform Tracking**: Maintain consistency between web and mobile analytics with structured, unified page nameSmarter Alerts & Insights – Grouped pages enable more accurate anomaly detection and performance monitoring.
* **Smarter Alerts & Insights** – Grouped pages enable more accurate anomaly detection and performance monitoring.

> 👍 **Tip**: We highly recommend that you always define your pages if you have Dynamic path parameters or fragments on your URLs.

> 📘 **Note**: If screen names have not been defined, UXCam will display the URL path as ‘screen name.

## How to use Page Definition for different use cases

* [Grouping Similar Content (Dynamic IDs in URLs)](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_11e7c1795b)
* [Tagging Fragments as distinct pages](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_924d5533fc)
* [Renaming pages for cleaner reports](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_3bf133bcdf)

1. ## **Grouping Similar Content (Dynamic IDs in URLs)**
   <br />

For example, on **Spotify**, each artist has a unique ID in the URL:

* /artist/**5K79FLRUCSysQnVESLcTdb**
* /artist/**1HY2Jd0NmPuamShAr6KMms** 

Even though the artist ID changes, these are all versions of the same **Artist Page**, and should not be considered separate pages. 

To group URLs that include dynamic path parameters under a single page, follow these steps:

1. Choose a URL that follows the structure of the pages you want to group.
2. Select the Dynamic Part
   1. Identify the changing section (e.g. artist ID: 1HY2Jd0NmPuamShAr6KMms).
   2. Decide whether to name it and save it as a **property** (e.g. \{artist-Id} so you can filter later) or ignore it (replace it with a wildcard \* ) 
3. Define the Page Name (e.g. Artist Page) 
4. Review & Test the Rule
   1. Confirm the pattern and test it with different URL variations.

<Image align="center" src="https://files.readme.io/b0d59cf8b623ff685a5a420795fa1eddbc3ee7aee8e88313d09c452ef77bd490-Screenshot2025-03-04at08_07_35.png" />

2. ## Tagging Fragments as distinct pages
   <br />

* Enter the URL that represents the page structure you want to define.
* Select the checkbox to ensure the Fragment is extracted from the URL
* Define the Page Name
* Confirm the pattern and test it with different URL variations.

<Image align="center" src="https://files.readme.io/85435ee8f436f4dbce978c491667d4a810dad88d3923d4934d0618d11d196ed1-Screenshot2025-03-04at08_15_47.png" />

3. ## Renaming Pages for Cleaner Reports

If you want to replace long URLs with short, customized page names for clearer reporting, follow these steps:

1. **Enter a URL** that follows the structure of the page you want to define.
2. **Define a clear page name** that aligns with your naming conventions.

> In the example below, you can see [https://www.spotify.com/join/](https://www.spotify.com/join/) was renamed -> Join page

<Image align="center" src="https://files.readme.io/2963d13b538a678b55a8fb52b91e3ce8014e40e7675ff99e47a17a0d98015743-Screenshot2B2025-03-042Bat2B08_11_01.png" />

If your URLs contain **fragments**, and **dynamic path parameters**, and you also want a clean page name, you can solve them all at once using Page Definition.

Follow these steps:

1. Enter a URL that follows the structure of the page you want to define. 
2. Select my URL contains fragments
3. Select the dynamic part  (e.g. \{RestaurantName}  and optionally save it as a property.
4. Define a meaningful name for your screen/page  (e.g. “Restaurant Choose items”).
5. Review and test the rule to ensure it applies correctly.

<Image align="center" src="https://files.readme.io/59f2a79f3cb28eec613fe37424467694e93d248a99f9d8d7a878915220709964-Screenshot_2025-03-04_at_08.18.23.png" />

When creating Rules, you can assign the same Page/Screen name to multiple rules, these will be processed and displayed by UXCam as a single screen. 

This can also be useful in case your URLs change but you want to keep your data consistent. 

E.g. if you used to have `domain.com/products/product-name` defined as Products Page and now you have `domain.com/category/products/product-name `you can name it as Products Page and archive your previous rule.

💡 If you have dynamic URLs that share the same URL structure e.g. `domain.com/{dynamic-category-id} `vs `domain.com/{dynamic-product-id}.` Read the guides below:

[Resolving conflicting Page Definition rules for ismilar URL structures with dynamic values](https://help.uxcam.com/en/articles/11145060-resolving-conflicting-page-definition-rules-for-similar-url-structures-with-dynamic-values)

To learn more about Page definition, rule hierarchy and rule management please visit our **[Help Center.](https://help.uxcam.com/en/articles/10652702-define-pages-properties-to-simplify-data#h_bc701cd1b1)**