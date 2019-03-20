---
layout: post
title: iOS Local Notifications in Swift 4
date: 2019-02-10 19:54 +0800
categories: ios swift 4 local notifications basic
---

Notifications are a way to notify your users that something new is available for them, or when something requires their action in the application. Notifications can come in two forms, one being remote/push notifications and the other being local notifications. 

Push notifications are the notifications we are mostly familiar with, such as a notification for a new message, or a notification for a new friend request on Facebook. These notifications are usually intiated by servers and the backend services of your application to push some data onto your user's devices via Apple's push notification service.

Local notifications are notifications that only live on the user's device, and it usually scheduled based on a certain pre-determined cycle, for instance every week, every hour, etc. Local notifications can also be scheduled based on the device's geolocation.

The thing about notifications in iOS is that for developers to access the Apple push notification service, the developer would have to be part of the paid developer program. Local notifications on the other hand, can be scheduled even with a free developer account. In this post, we will be talking about local notifications using Swift 4.

### Setting up local notifications in your app

To start off, you should ask for permission from the user to show him/her notifications from your app.

```swift
import UserNotifications

let options: UNAuthorizationOptions = [.alert, .sound, .badge] // You can customise this

UNUserNotificationCenter.current().requestAuthorization(options: options) {
  (access, error) in
    if !access {
      print("Error: \(error)")
    }
}
```

This step should be done as early as possible in the app lifecycle - a good place to make this authorisation request is in `AppDelegate`'s `didFinishLaunchingWithOptions` function.

### Opening a Local Notification Request

After requesting for permission from the user to schedule notifications, you should create a local notification request, which is of type `UNNotificationRequest`. To initialise a `UNNotificationRequest`, you'll need the following:

#### UNNotificationIdentifier

This is just your usual string identifiers.

#### UNNotificationContent

This is the content that the user will see when the notification pops up on their screen.

```swift
let content = UNMutableNotificationContent()
content.title = "Notification Title"
content.body = "Notification Body"
content.sound = UNNotificationSound.default()
```

#### UNNotifcationTrigger

This is the trigger that defines when the notification will be shown to the user. It can be customised either based on a set amount of time, the device's calendar, or by the device's geolocation.

```swift
// Time interval: notification will trigger every 60 secs, on repeat
let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 60, repeats: true)

// Calendar date: notification will trigger at the next 1200hrs
var dateComponents = DateComponents()
dateComponents.calendar = Calendar.current
dateComponents.hour = 12
dateComponents.minute = 00
let trigger = UNCalendarNotificationTrigger(dateMatching: dateComponents, repeats: false)

// Location: notification will trigger when device enters given `CLRegion`
let trigger = UNLocationNotificationTrigger(triggerWithRegion: region, repeats: false)
```

#### UNNotificationRequest

Finally, we are ready to put out the request to the notification system.

```swift
let request = UNNotificationRequest(identifier: identifier, content: content, trigger: trigger)
UNUserNotificationCenter.current().add(request, withCompletionHandler: { (error) in 
    if error != nil {
        print(error)
    }
})
```

### Conclusion

And there you have it! With just these snippets of code, you will be able to schedule your own local notifications for you application and get them to be presented to your user based on the pre-defined triggers. For more information you can visit [Apple's official docs](https://developer.apple.com/documentation/usernotifications/scheduling_a_notification_locally_from_your_app) on user notifications.


