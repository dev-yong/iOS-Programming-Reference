# Data storage

## UserDefaults

`NSUserDefaults` as the name suggests (vaguely) should be used for storing preferences and app settings only. You should not be storing critical data and or user data into them

- Easy to store and retrieve data.
- Useful for storing default values with minimum fuzz.
- Not suitable for large data sets
- Performance hit when you try to store and load large amount of data All or nothing approach

## CoreData

CoreData is a full fledged persistent framework which supports large data transaction

- Reliable framework to interact and query against data
- Can be extremely fast when setup correctly (with relationships)
- Powerful capabilities

## NSKeyedArchiver

## SQLite

## Realm

## Reference

- https://stackoverflow.com/questions/47412289/userdefaults-or-core-data
- [UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults)
- [Core Data](https://developer.apple.com/documentation/coredata)
- [Core Data Programming Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/index.html)
- [NSKeyedArchiver](https://developer.apple.com/documentation/foundation/nskeyedarchiver)