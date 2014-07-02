<!--dox @defgroup NimbusKitCoreDataCoding Core Data Coding -->
<div id="github" feature="CoreDataCoding"></div>
![](https://github.com/NimbusKit/coredatacoding/raw/master/docs/gfx/coredatacoding_banner.gif "")

Core Data Coding transforms Core Data entities to and from NSCoding-conformant objects.

Consider a simple Core Data model for a generic messenger app:

![](https://github.com/NimbusKit/coredatacoding/raw/master/docs/gfx/messengerApp.png "A generic messenger app")

In order to send messages between devices you could use Core Data Coding to encode a Message entity on one device and decode it on another, all with only a few lines of code.

```objc
// A Message entity fetched from the sending device's managed object context
NSManagedObject* message;

// On the sending device:
CDCCodableObject codableMessage = CDCEncode(message);

// Send codableMessage to the receiving device however you choose

// On the receiving device:
// Inject the message into the device's Core Data context
CDCDecode(codableMessage, managedObjectContext);
```

In the above example, the entities could be configured such that `codableMessage` would resemble the following:

```
{"dateSent":"2014-05-27T14:32:25",
 "uuid":1235,
 "text":"Working on the docs today.",
 "conversation":1,
 "byWho":2}
```

Core Data Coding makes it easy to configure what relationships and attributes of any Core Data entity are encoded. For more details, read the documentation on CDCEncoder and CDCDecoder.

Where is the Source?
====================

This library is part of NimbusKit+, a collection of specialized, well-documented libraries that complement the NimbusKit ecosystem of libraries. All of the NimbusKit+ libraries are available for purchase at [Featherless Software Design](https://www.etsy.com/shop/thefeatherless) on Etsy.

Full documentation is available at [v2.nimbuskit.info](http://v2.nimbuskit.info/NimbusKitCoreDataCoding.html).

Adding it to your Project
=========================

Drag CoreDataCoding.framework into your Xcode project. Ensure that the framework is added to your app target.

You can now import the CoreDataCoding header and start using its features.

```objc
#import <CoreDataCoding/CoreDataCoding.h>
```

Requirements
------------

Must be compiled with the iOS 6 SDK or above. You must link to the Core Data framework.

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
NSManagedObject* someEntity // fetched from a Core Data context
CDCCodableObject codableObject = CDCEncode(entity);

// Save codableObject to disk, send it over the network, whatever you like!

NSManagedObject* recreatedEntity = CDCDecode(codableObject, managedObjectContext);
```

Saving Entities to Disk
-----------------------

With Core Data Coding it's trivial to store an entity on disk.

```objc
CDCCodableObject codableObject = CDCEncode(entity);
NSData* data = [NSJSONSerialization dataWithJSONObject:codableObject options:0 error:nil];
[data writeToFile:@"somePath" atomically:YES];
```

This is particularly useful when syncing core data objects via file-based services like Dropbox. It also allows your users to access their data in a platform-agnostic format of your choosing, rather than having all of their data locked up in a Core Data database.

> Relationships are not encoded by default. Read the CDCEncoder docs for more details about how to encode relationships.

### JSON Encoding and Transformable Attributes

While it is possible to encode Core Data entities that have Transformable attributes, **JSON does not support Transormable attributes**. As per the NSJSONSerialization docs, only values of the following types can be transformed into JSON: `NSString`, `NSNumber`, `NSArray`, `NSDictionary`, or `NSNull`. Any attempt to turn an encoded Core Data entity with Transformable attributes will likely cause the JSON serializer to fail.

Reading Entities from Disk
--------------------------

Once you've saved an entity to disk it's trivial to then read it back in to your Core Data database.

```objc
NSData* data = [NSData dataWithContentsOfFile:@"someFile"];
id codableObject = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];
NSManagedObject* entity = CDCDecode(codableObject, managedObjectContext);
```

Version History
===============

2.1.0 on July 2, 2014
-----

Extensive documentation update, basic migration support, and general clarifications across the library.

- [new] @link NimbusKitCoreDataCodingArticles In-depth articles@endlink.
- [new] @link NimbusKitCoreDataCodingLeafExtractionAndInjection Leaf Extraction and Injection@endlink.
- Cleaner error handling in CDCDecoder and newly-documented @link CDCErrorCode CDCErrorCode@endlink values.
- Clearer configuration option names. See the chart below for migrating your model entity configurations to the new names.
- Rudimentary support for entity migration via CDCEncoder::codableObjectFromManagedObject:migrationManager: and CDCEncoder::forEntityName:setEntityMigrator:.
- [api] CDCEncoder and CDCDecoder now respectively generate and consume an opaque type, CDCCodableObject.
- [api] Deleted `useCommittedValues` from CDCEncoder.
- [api] Renamed `CDCDecoder::forEntityName:andKey:setKeyMapper:` to CDCDecoder::forEntityName:andKey:setValueMapper:.

#### Configuration Name Changes

| Old Key Name            | New Key Name            |
|-------------------------|-------------------------|
| `codable`               | `encodable`             |
| `codingTags`            | `requiredEncodingTags`  |
| `shallowCoding`         | `shallowEncoding`       |
| `uniquingRelationship`  | `primaryKey`. For uniquing within a parent context, also use `uniqueChild`. |

Learn more about @link NimbusKitCoreDataCodingConfigurationOptions configuration options@endlink.

2.0.2 on May 5, 2014
-----

Misc bug fixes.

- [bug] Codable tagged relationships will no longer be overwritten when an object is decoded that did not include that relationship due to lack of matching tags.
- [bug] If a codable object's primary key value is null then a new entity with no primary key will be created now.
- [crashfix] Nil relationships are now properly en/decoded (previously nil codable relationships were causing a crash).
- [bug] Managed object contexts created with NSConfinementConcurrencyType may now be used.

2.0.1 on May 2, 2014
-----

Minor internal cleanup.

- Replaced legacy CDCSetValueForKeyWithVerb with simpler equivalent.

2.0.0 on May 1, 2014
-----

Bi-directional transforms between `CDCCodableObject` and `NSManagedObject*`.

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
