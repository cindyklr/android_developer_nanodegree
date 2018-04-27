# Lesson 3 - Librairies

## When to use Librairies

Creating a content provider is no easy feat, and it has many components. Many developers have struggled with this, so naturally, someone created a library to simplify this process. Check out [Schematic](https://github.com/SimonVT/schematic) for an example of such a library.

Loading images over the network is a challenge since it depends heavily on network availability and consumes a lot of battery. There are many libraries that help with this, including robust features such as caching, configured timeouts and more. See [Picasso](http://square.github.io/picasso/) for an example of such a library.

Barcode scanning is a feature that is available in many different applications, so that should give you a hint that there are libraries that help with that. The Google Mobile Vision library includes API's for scanning barcodes.

Complex logging needs can also be addressed using Libraries. The default logger falls short if you need to modify what kind of things you need displayed in the debugger depending on what version of your app is Running. [Timber](https://github.com/JakeWharton/timber) includes this, and many more features.

## Finding Librairies

Finding a library that fits your needs starts where all the rest of your questions have been answered so far: that's right, [stackoverflow](https://stackoverflow.com/).

Libraries are useful for solving problems that appear over and over again in different applications. If you search Stack Overflow, often the accepted solution to the problem you are trying to solve will be a third party library that does exactly what you're looking for.

I'd also like to mention two more resources for finding libraries: The first one is [Android Arsenal](https://android-arsenal.com/), which is a large library of libraries (aha) that just about anyone can add to. There are literally thousands of libraries on here, of highly varied quality. The second resource is this [Github](https://github.com/codepath/android_guides/wiki/Must-Have-libraries) page, which lists some of the most commonly used android libraries.

### But Wait!

A word of caution: just because there is a library that seems like a perfect fit for you need, do not be too hasty in adding it to you application. Using a library that is not well maintained or documented comes with a host of other issues and is likely to cause more of a headache than it cures. The next video will be devoted to figuring out whether a library is a good candidate for your application.
64K Method Limit

Another consideration in deciding whether or not to use a library is to be aware of how it will affect the size of your app. When your app, including the libraries it uses, reaches a certain size, you encounter build errors that indicate your app has reached a limit of the Android app build architecture ([64K Method Limit](https://developer.android.com/studio/build/multidex)). Libraries are commonly used and without a doubt efficient, but do be aware of theses considerations. 

## Choose a Librairy

For more information on licenses and which licenses to use, check out this [link](https://choosealicense.com/)

