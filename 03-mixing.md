
How To Clear - Mixing content and creating updates
============================================

## What you'll learn in this chapter

* Initializing a new content stream
* Version numbering
* Format version numbering
* Working with the upstream Clear Linux OS
* Creating, modifying and modifying bundles
* Creating the update content
* Creating an update

## Concepts

The `swupd` software delivery mechanism treats everything as an update. 
It does this by querying metadata from an update server and calculating 
what it needs to do and which content. The format of this data and
metadata is very simple, and easy to understand and reproduce, but
`swupd` needs a lot of it.

As the update content describes every file in the OS, it is a large 
database that needs to be kept synchronized on the client system. 
Maintaining these metadata lists is expensive, and the system is 
designed to do all the hard work on the server side, so that clients 
only need to work on small subsets of the data to perform the needed 
operations to update or install components.

`swupd` uses separate files for content and for metadata. The content 
is often shared between versions and these duplicates are not stored on 
the server or the client. The content is also compressed on the server, 
and each piece of content is identified by a hash value that is unique 
for both the content of the data, and the properties of the inode on 
the file system. In this way, if a file changes permissions, xattr tags 
or ownership, this is considered a normal update from one content unit 
to a new content unit.

The metadata is similarly reused between different versions of the OS. 
This means that if a bundle doesn't change between two versions, the 
`swupd` client will reuse the older version. In the metadata the latest 
version that a manifest metadata file is recorded. This allows clients 
to reconstruct a full new view of the work that needs to be done while 
maximizing reuse of the already known metadata.

### Metadata


### Content 
