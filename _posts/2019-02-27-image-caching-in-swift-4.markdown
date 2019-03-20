---
layout: post
title: Image Caching in Swift 4
date: 2019-02-27 09:14 +0800
categories: ios swift 4  basic
---

Image caching is an interesting and a very important concept to understand, especially for mobile developers. In many mobile apps, we deal with a lot of images and assets, and we are also required to show all these images to the user as quickly as possible. Just imagine a social media app like Facebook, where a quick scroll down and already multiple images are expected to be shown to the user. Most of the time, these images are also to be fetched from a certain API or database - there's absolutely no way these images can be loaded in a blink of an eye if we were to constantly be fetching them from the network to be displayed!

### What is Image Caching

And so image caching comes to the rescue! This is Wikipedia's description of what caching is:

> Cache is a hardware or software component that stores data so future requests for that data can be served faster; the data stored in a cache might be the result of an earlier computation, or the duplicate of data stored elsewhere

In the case of mobile apps, images can be cached either in the device's memory or the disk. Each has it's pros and cons - in general, caching to memory yields faster retrieval times, but is not persistent, while caching to the disk allows the cached data to be persistent even after the application process has been killed. 

Often times, we'll be using a two-tier caching mechanism, where we cache images/data in both the memory and disk cache to reap the benefits of both (speed and persistence). We will then only retrieve new data from the network if the data requested is not available in both locations.

In this post, I'll introduce some ways we can implement an image caching system in your iOS app.

### `NSCache`

`NSCache` holds the image in memory and is mostly used for optimal performace in terms of speed. Usage of `NSCache`, however, takes up memory (RAM) and that can result in the application throwing back memory warnings at you, but not worry as Apple's `NSCache` implementation will take care of this on its own:

> The NSCache class incorporates various auto-eviction policies, which ensure that a cache doesn’t use too much of the system’s memory. If memory is needed by other applications, these policies remove some items from the cache, minimizing its memory footprint.

Here's how you can use an `NSCache` for image caching as an extension from `UIImageView`:

```swift
var imageCache = NSCache<AnyObject, AnyObject>()

extension UIImageView {

    func loadImage(urlString: String) {
        
        if let cacheImage = imageCache.object(forKey: urlString as AnyObject) as? UIImage {
            self.image = cacheImage
            return
        }
        
        guard let url = URL(string: urlString) else { return }
        
        URLSession.shared.dataTask(with: url) { (data, response, error) in
            if let error = error {
                print("Couldn't download image: ", error)
                return
            }
            
            guard let data = data else { return }
            let image = UIImage(data: data)
            imageCache.setObject(image, forKey: urlString as AnyObject)
            
            DispatchQueue.main.async {
                self.image = image
            }
        }.resume()

    }
}
```

### `NSURLCache`

Apple also provides a cache called `NSURLCache` that does all the caching required internally for you and there is no need for you to manually manage the caching of data. Internally, `NSURLCache` uses both in-memory and on-disk image caching, and it makes its decisions based on the size of the data. All you need to do is to initialise this cache and to set it as a default for your application, and then use `NSURLSession` to download your images/data.

```swift
// You should initialise the cache once your application launches.
func application(application: UIApplication!, didFinishLaunchingWithOptions launchOptions: NSDictionary!) -> Bool {
    let URLCache = NSURLCache(memoryCapacity: 4 * 1024 * 1024, diskCapacity: 20 * 1024 * 1024, diskPath: nil)
    NSURLCache.setSharedURLCache(URLCache)

    return true
}
```

### Third Party Alternatives

Other than doing it natively in Swift, there are also numerous third party libraries out there that have already done all the dirty work for you and you will be able to easily use their abstracted methods to perform any sort of data caching you might have to do. Some of the more popular libraries include:

- [SDWebImage](https://github.com/SDWebImage/SDWebImage)
- [Alamofire](https://github.com/Alamofire/Alamofire)
- [AFNetworking](https://github.com/AFNetworking/AFNetworking)
- [JMImageCache](https://github.com/jakemarsh/JMImageCache)
