OHHTTPStubs
===========

[![Version](http://cocoapod-badges.herokuapp.com/v/OHHTTPStubs/badge.png)](http://cocoadocs.org/docsets/OHHTTPStubs)
[![Platform](http://cocoapod-badges.herokuapp.com/p/OHHTTPStubs/badge.png)](http://cocoadocs.org/docsets/OHHTTPStubs)
[![Build Status](https://travis-ci.org/AliSoftware/OHHTTPStubs.png?branch=master)](https://travis-ci.org/AliSoftware/OHHTTPStubs)

`OHHTTPStubs` is a library designed to stub your network requests very easily. It can help you:

* test your apps with **fake network data** (stubbed from file) and **simulate slow networks**, to check your application behavior in bad network conditions
* write **Unit Tests** that use fake network data from your fixtures.

It works with `NSURLConnection`, new iOS7/OSX.9's `NSURLSession`, `AFNetworking` (both 1.x and 2.x), or any networking framework that use Cocoa's URL Loading System.

[![Donate](http://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=TRTU3UEWEHV92 "Donate")

----

# Documentation & Usage Examples

`OHHTTPStubs` headers are fully documented using Appledoc-like / Headerdoc-like comments in the header files. You can also [read the **online documentation** here](http://cocoadocs.org/docsets/OHHTTPStubs)
[![Version](http://cocoapod-badges.herokuapp.com/v/OHHTTPStubs/badge.png)](http://cocoadocs.org/docsets/OHHTTPStubs)

> Unfortunately macro documentation does not appear in the documentation generated by appledoc, so don't hesitate to take a look into `OHHTTPStubsResponse.h`, `OHHTTPStubsResponse+JSON.h` and `OHHTTPStubsResponse+HTTPMessage.h` too.

### Basic usage example

    [OHHTTPStubs stubRequestsPassingTest:^BOOL(NSURLRequest *request) {
        return [request.URL.host isEqualToString:@"mywebservice.com"];
    } withStubResponse:^OHHTTPStubsResponse*(NSURLRequest *request) {
        // Stub it with our "wsresponse.json" stub file
        NSString* fixture = OHPathForFileInBundle(@"wsresponse.json",nil);
        return [OHHTTPStubsResponse responseWithFileAtPath:fixture
                  statusCode:200 headers:@{@"Content-Type":@"text/json"}];
    }];

### [More examples](https://github.com/AliSoftware/OHHTTPStubs/wiki/Usage-Examples)
    
For a lot more examples, see the dedicated "[Usage Examples](https://github.com/AliSoftware/OHHTTPStubs/wiki/Usage-Examples)" wiki page.

### [Help Topics](https://github.com/AliSoftware/OHHTTPStubs/wiki)

The wiki also contain [some articles that can help you get started](https://github.com/AliSoftware/OHHTTPStubs/wiki) with (and troubleshoot if needed) `OHHTTPStubs`.

# Compatibility

`OHHTTPStubs` is compatible with **iOS 5.0+** and **OSX 10.7+**.

`OHHTTPStubs` also works with iOS7's and OSX 10.9's `NSURLSession` mechanism.

# Special Considerations

## Using OHHTTPStubs in your Unit Tests

`OHHTTPStubs` is ideal to write Unit Tests that normally would perform network requests. But if you use it in your Unit Tests, don't forget to:

* remove any stubs you installed after each test — to avoid those stubs to still be installed when executing the next Test Case — by calling `[OHHTTPStubs removeAllStubs]` in you `tearDown` method. [see this wiki page for more info](https://github.com/AliSoftware/OHHTTPStubs/wiki/Remove-stubs-after-each-test)
* be sure to wait until the request has received its response before doing your assertions and letting the test case to finish (like for any asynchronous test). [see this wiki page for more info](https://github.com/AliSoftware/OHHTTPStubs/wiki/OHHTTPStubs-and-asynchronous-tests)


## Automatic loading

In general, `OHHTTPStubs` is automatically enabled by default, both for:

* requests made using `NSURLConnection` or `[NSURLSession sharedSession]`;
* requests made using a `NSURLSession` created using a `[NSURLSessionConfiguration defaultSessionConfiguration]` or `[NSURLSessionConfiguration ephemeralSessionConfiguration]` configuration (using `[NSURLSession sessionWithConfiguration:…]`-like methods).

If you need to disable (and re-enable) `OHHTTPStubs` globally or per session, you can use:

* `[OHHTTPStubs setEnabled:]` for `NSURLConnection`/`[NSURLSession sharedSession]`-based requests
* `[OHHTTPStubs setEnabled:forSessionConfiguration:]` for requests sent on a session created using `[NSURLSession sessionWithConfiguration:...]`. Note that you have to call this before creating the `NSURLSession` as the `NSURLSessionConfiguration` is deep-copied on the creation of the `NSURLSession` instance and cannot be modified afterwards.

_In practice, there is no need to ever explicitly call `setEnabled:` or `setEnabled:forSessionConfiguration:` using `YES`, as this is the default._

## Known limitations

* `OHHTTPStubs` **can't work on background sessions** (sessions created using `[NSURLSessionConfiguration backgroundSessionConfiguration]`) because background sessions don't allow the use of custom `NSURLProtocols` and are handled by the iOS Operating System itself.
* `OHHTTPStubs` don't simulate data upload. The `NSURLProtocolClient` `@protocol` does not provide a way to signal the delegate that data has been **sent** (only that some has been loaded), so any data in the `HTTPBody` or `HTTPBodyStream` of an `NSURLRequest`, or data provided to `-[NSURLSession uploadTaskWithRequest:fromData:];` will be ignored, and more importantly, the `-URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:` delegate method will never be called when you stub the request using `OHHTTPStubs`.

_As far as I know, there's nothing we can do about those two limitations. Please let me know if you know a solution that would make that possible anyway._


# Installing in your projects

**The recommanded way to use `OHHTTPStubs` is via [CocoaPods](http://cocoapods.org/)**.
Simply add `pod 'OHHTTPStubs'` to your `Podfile` then run `pod install` and you are ready to use it.

_Note: `OHHTTPStubs` requires iOS5 minimum._

> **Warning: Be careful anyway to include `OHHTTPStubs` only in your test targets, or only use it in `#if DEBUG` portions, if you don't want your stubs to still be included in your release for the AppStore!**

In case you don't want to use CocoaPods (but you should!!!), the `OHHTTPStubs` project is provided as a Xcode project that generates a static library, so simply add its xcodeproj to your workspace and link your app against the `libOHHTTPStubs.a` library. See [here](https://github.com/AliSoftware/OHHTTPStubs/wiki/Detailed-Integration-Instruction) for detailed instructions.


---

### About OHHTTPStubs own Unit Tests

If you want to be able to run `OHHTTPStubs`' Unit Tests, be sure you cloned the [`AFNetworking`](https://github.com/AFNetworking/AFNetworking/) submodule (by using the `--recursive` option when cloning your repo, or using `git submodule init` and `git submodule update`) as it is used by some of `OHHTTPStubs` unit tests.

This submodule is only useful for `OHHTTPStubs`' own Unit Tests, that are testing its usage with `AFNetworking`: **you don't need the submodule to use `OHHTTPStubs`** and `OHHTTPStubs` has no dependency on `AFNetworking` itself.

**Every contribution to add more unit tests is welcome!**



# License and Credits

This project and library has been created by Olivier Halligon (@aligatr on Twitter) and is under the MIT License.

It has been inspired by [this article from InfiniteLoop.dk](http://www.infinite-loop.dk/blog/2011/09/using-nsurlprotocol-for-injecting-test-data/).
I would also like to thank to @kcharwood for its contribution, and everyone who contributed to this project on GitHub.

If you want to support the development of this library, feel free to [![Donate](http://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=TRTU3UEWEHV92 "Donate"). Thanks to all contributors so far!