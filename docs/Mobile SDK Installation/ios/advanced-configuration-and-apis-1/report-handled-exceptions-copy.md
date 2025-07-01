---
title: Report Handled Exceptions (COPY)
deprecated: false
hidden: false
metadata:
  robots: index
---
> ❗️
>
> Handled exception reporting is available **only for  iOS, Android and Flutter apps**
>
> Flutter SDK version must be 2.5.0 or above to support handled exceptions.

Non-fatal or caught exceptions are those that are manually handled by developers that do not result in application flow breaking. Once your application reaches a code block that raises an exception, you can catch the exception and send it to UXCam using the reportExceptionEvent API.

You can send handled exceptions to UXCam to:

* See how non-fatal exceptions impact your app performance and users' experience.
* Prevent the propagation of errors.
* Find the most common reasons and types of exceptions.
* Watch the recordings and send additional details for a deeper analysis.

In addition to the handled exception, you can send additional key-value properties to attach more details about the exception.

> 🚧
>
> e.g. if you have an e-commerce app and the user entered an invalid number when selecting quantity, you can send the properties bug=invalid number.

![](https://files.readme.io/c9f192f-Screen_Shot_2021-06-17_at_12.24.18_PM.png "Screen Shot 2021-06-17 at 12.24.18 PM.png")

### API Implementation

Use the following API to send handled exceptions in UXCam.

```swift iOS
-(void)reportExceptionEvent:(NSException*)exception 
properties(NSDictionary<NSString*,id>*)properties;

//Example
@try
	{
		// Exception crash
		NSArray* junk = @[@0,@1,@2];
		NSLog(@"Array 3 = %@", junk[3]);
	}
	@catch (NSException *exception)
	{
		NSDictionary *map = @{@"bug_sample_param_1": @"bug_sample_value_1", @"bug_sample_param_2": @"bug_sample_value_2"};
		[UXCam reportExceptionEvent:exception properties: map];
	}
```

> 📘
>
> The API parameters are:\
> **exception**: Exception object.
> **properties**: (Optional) A Dictionary of properties to associate with this exception. Only number and string property types are supported.