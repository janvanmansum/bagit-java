# BagIt Library (BIL)
[![Master Branch Build Status](https://travis-ci.org/LibraryOfCongress/bagit-java.svg?branch=master)](https://travis-ci.org/LibraryOfCongress/bagit-java)

[![Coverage Status](https://coveralls.io/repos/github/LibraryOfCongress/bagit-java/badge.svg?branch=master)](https://coveralls.io/github/LibraryOfCongress/bagit-java?branch=master)

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/gov.loc/bagit/badge.svg)](https://maven-badges.herokuapp.com/maven-central/gov.loc/bagit)

[![License](https://img.shields.io/badge/License-Public--Domain-blue.svg)](https://github.com/LibraryOfCongress/bagit-java/blob/master/LICENSE.txt)

[![javadoc.io](https://img.shields.io/badge/javadoc.io-Latest-blue.svg)](https://javadocio-badges.herokuapp.com/gov.loc/bagit)

## Description
The BAGIT LIBRARY is a software library intended to support the creation, 
manipulation, and validation of bags. Its current version is 0.97. It is version aware with the earliest
supported version being 0.93.

## Requirements
* Java 8
* gradle (for development only)

## Major differences between version 5 and 4.*
##### Commandline
We no longer support a command line interface for the java version of bagit. If you would like a command line interface for bagging, verifying, and other actions please check out our [bagit-python implementation](https://github.com/LibraryOfCongress/bagit-python) or the [ruby based implementation](https://github.com/tipr/bagit) 
##### Serialization
We no longer support directly serializing a bag. But if that is something you require there are plenty of great libraries that offer this capability
##### Fetching
We no longer support fetching. This is due to the various protocalls that could be involved. Again, if this is something you need, there are much better java libraries out there that you can use to fill this functionality.

### Examples of using the new bagit-java library
The "new" bagit interface is very intuitive, but here are some easy to follow examples. Instead of returning messages like in the old interface, now it throws errors so you don't have to parse messages to understand what happened.

##### Create a bag from a folder using version 0.97
```java
Path folder = Paths.get("FolderYouWantToBag");
StandardSupportedAlgorithms algorithm = StandardSupportedAlgorithms.MD5;
boolean includeHiddenFiles = false;
Bag bag = BagCreator.bagInPlace(folder, algorithm, includeHiddenFiles);
```
##### Read an existing bag (version 0.93 and higher)
```java
Path rootDir = Paths.get("RootDirectoryOfExistingBag");
BagReader reader = new BagReader();
Bag bag = reader.read(rootDir);
```
##### Write a Bag object to disk
```java
Path outputDir = Paths.get("WhereYouWantToWriteTheBagTo");
BagWriter.write(bag, outputDir); //where bag is a Bag object
```
##### Verify Complete
```java
boolean ignoreHiddenFiles = true;
BagVerifier verifier = new BagVerifier();
verifier.isComplete(bag, ignoreHiddenFiles);
```
##### Verify Valid
```java
boolean ignoreHiddenFiles = true;
BagVerifier verifier = new BagVerifier();
verifier.isValid(bag, ignoreHiddenFiles);
```
##### Quickly verify by payload-oxum
```java
boolean ignoreHiddenFiles = true;
BagVerifier verifier = new BagVerifier();

if(verifier.canQuickVerify(bag)){
  verifier.quicklyVerify(bag, ignoreHiddenFiles);
}
```
##### Add other checksum algorithms
You only need to implement 2 interfaces
```java
public class MyNewSupportedAlgorithm implements SupportedAlgorithm {
  @Override
  public String getMessageDigestName() {
    return "SHA3-256";
  }
  @Override
  public String getBagitName() {
    return "sha3256";
  }
}

public class MyNewNameMapping implements BagitAlgorithmNameToSupportedAlgorithmMapping {
  @Override
  public SupportedAlgorithm getMessageDigestName(String bagitAlgorithmName) {
    if("sha3256".equals(bagitAlgorithmName)){
      return new MyNewSupportedAlgorithm();
    }
    
    return StandardSupportedAlgorithms.valueOf(bagitAlgorithmName.toUpperCase());
  }
}
```
and then add the implemented BagitAlgorithmNameToSupportedAlgorithmMapping class to your BagReader or bagVerifier object before using their methods

### Examples of how to implement features that have been removed
See the [examples](src/test/java/gov/loc/repository/bagit/examples) directory for examples of how to implement features that have been removed yourself. 

## Developing Bagit-Java
Bagit-Java uses [Gradle](https://gradle.org/) for its build system. Check out the great [documentation](https://docs.gradle.org/current/userguide/userguide_single.html) to learn more.
##### Running tests and code quality checks
Inside the bagit-java root directory, run `gradle check`.
##### Uploading to maven central
1. Follow their guides
  1. http://central.sonatype.org/pages/releasing-the-deployment.html
  2. https://issues.sonatype.org/secure/Dashboard.jspa
2. Once you have access, to create an office release and upload it you should specify the version by running `gradle -Pversion=<VERSION> uploadArchives` 
  1. *Don't forget to tag the repository!* 

### Note if using with Eclipse
Simply run `gradle eclipse` and it will automatically create a eclipse project for you that you can import.

### Roadmap
* Further refine reading and writing of bags version 0.93-0.97
* Integrate new proposed specification we are calling "dot bagit"
* Fix bugs/issues reported with new library (on going)

### Support
1. The Digital Curation Google Group (https://groups.google.com/d/forum/digital-curation) is an open discussion list that reaches many of the contributors to and users of this open-source project
2. If you have found a bug please create a new issue on [the issues page](https://github.com/LibraryOfCongress/bagit-java/issues)
3. To contact a developer at the Library of Congress please email repo-dev@listserv.loc.gov
4. If you would like to contribute, please submit a [pull request](https://help.github.com/articles/creating-a-pull-request/)
