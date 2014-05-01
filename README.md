<!--dox @defgroup NimbusKitCoreDataCoding NimbusKit Core Data Coding -->
<div id="github" feature="CoreDataCoding"></div>
![](https://github.com/NimbusKit/coredatacoding/raw/master/docs/gfx/banner.gif "CoreDataCoding")

Core Data Coding transforms NSManagedObjects to/from NSCoding-conformant objects.

Where is the Source?
====================

This library is part of NimbusKit+, a new collection of specialized, well-documented libraries that complement the NimbusKit ecosystem of libraries. All of these new libraries can be found on the [Featherless Software Design Etsy](https://www.etsy.com/shop/thefeatherless).

[etsy.com/shop/thefeatherless](https://www.etsy.com/shop/thefeatherless)

Full documentation may be found in the [NimbusKit docs](http://v2.nimbuskit.info/NimbusKitCoreDataCoding.html).

Adding it to your Project
=========================

Drag all of the files from the `src` directory into your project and then import the library header.

```objc
#import "CoreDataCoding.h"
```

Running the Unit Tests
----------------------

This library uses Cocoapods to manage its dependencies, so you will have to run

```sh
pod install
```

from the root directory in order to install the necessary pods. It should then be a matter of opening the CoreDataCoding workspace and running the unit tests.

Examples
========

CDCEncoder and CDCDecoder are the workhorses of this library. The simplest use looks like so:

```objc
id<NSCoding> codableObject = CDCEncode(entity);

// Save codable to disk, send it over the network, whatever you like!

NSManagedObject* recreatedEntity = CDCDecode(codableObject, moc);
```

Saving Entities to Disk
-----------------------

With Core Data Coding it's trivial to store an entity on disk.

```objc
id<NSCoding> codableObject = CDCEncode(entity);
NSData* data = [NSJSONSerialization dataWithJSONObject:codableObject options:0 error:nil];
[data writeToFile:@"somePath" atomically:YES];
```

This is particularly useful when syncing core data objects via file-based services like Dropbox. It also allows your users to access their data in a platform-agnostic format of your choosing, rather than having all of their data locked up in a Core Data database.

> Note: by default relationships are not encoded. Read the CDCEncoder docs for more details about how to encode relationships.

Reading Entities from Disk
--------------------------

Once you've saved an entity to disk it's trivial to then read it back in to your Core Data database.

```objc
NSData* data = [NSData dataWithContentsOfFile:@"someFile"];
id codableObject = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];
NSManagedObject* entity = CDCDecode(codableObject);
```

Uniquing
--------

With zero configuration the CDCDecoder will create new managed objects from the codable objects it is provided with. If you are maintaining uniqueness in your model for any entities then this is likely not behavior that you want. Read the docs for CDCDecoder for more details about declaring uniqueness on your entities.

Requirements
============

Must be compiled with the iOS 6 SDK or above. You must link to the Core Data frameworks.

Version History
===============

2.0.0 on May 1, 2014
-----

Bi-directional transforms between `id<NSCoding>` and `NSManagedObject*`.

- New classes, CDCEncoder and CDCDecoder to simplify the configuration of the en/de-coders.
- Plenty of documentation now available at http://v2.nimbuskit.info/.

1.0.0 on April 19, 2014
-----

Initial release.

- Includes CCDCodableObjectForManagedObject with `codable` and `representativeAttribute` configuration.

Credits
=======

Core Data Coding was built by [Jeff Verkoeyen](http://jeffverkoeyen.com/) ([featherless](http://twitter.com/)).

License
=======

Core Data Coding is licensed under the [NimbusKit+ Commercial Development license](http://nimbuskit.com/pluslicense).
