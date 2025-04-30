---
title: Flutter Occlusion Feature
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
> 🚧 Important
>
> Please keep in mind that this feature is only available in versions 2.3.0+ of the UXCam Flutter SDK

## What is it and what does it do?

In Flutter, there has always been some limitations for occluding specific areas and widgets using the UXCam SDK. With this new feature this is part of the past. This is a new occlusion feature that will let you hide specific widgets ( Column, ListView, etc…) from the video recording. 

## How does it work

Now you might be wondering, how does it work? Well, it's pretty simple. Decide which is the widget that you want to occlude and pass it as a child to our **OccludeWrapper** container. 

## Things to note

It’s pretty important to mention that our SDK is the one in charge of occluding these elements, so the occlusion settings will happen in the device before the session gets uploaded to the UXCam servers. 

## Examples

What do you need to do in order to implement this new feature? 

1. **First of all (IMPORTANT), import the library that contains this new wrapper**

<pre><code class="language-java">import 'package:flutter_uxcam/flutter_uxcam.dart';</code></pre>

2. **Decide which is the widget that you would like to occlude and wrap it with an occlusion container (OccludeWrapper Widget).**

<pre><code class="language-java">const OccludeWrapper(     
   child:
   // Here goes your widget
),</code></pre>

Example on how to get a Text widget occluded  

<pre><code class="language-java">//Example  
const OccludeWrapper(  
   child: Text(  
       'Sensitive data that will be occluded by the Wrapper',  
    ),  
),  
</code></pre>

3. **Now you are all set, you should start getting video recordings with your widgets being occluded properly.**

In case you have any questions, please feel free to favourite support team at [team@uxcam.com](mailto:team@uxcam.com).