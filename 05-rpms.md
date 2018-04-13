
How To Clear - Creating and mixing custom RPMs
=========================================

## What you'll learn in this chapter

* Modifying a Clear Linux kernel RPM
* Creating a new RPM from an upstream release
* Adding the custom RPM to the update content
* Deploying the change to the target system

## Concepts

Creating RPM files is a relatively simple task where source code and 
build instructions are used to generate a binary archive with some 
metadata. The RPM format has been in use for a very long time and is an 
efficient method to convey both content and metadata in a single file 
format.

Clear Linux OS uses the RPM file format for intermediate storage. This 
allows developers to solve partial problems and not have to rebuild an 
entire OS altogether. This increases speed while still providing all 
the tools and data needed to prevent breaking dependencies.

RPM files are created as the output of `rpmbuild` which takes a `spec` 
file as instructions, together with the source code, patches, and 
miscellaneous files that may be needed during the build. For this 
purpose the developer doesn't really work with RPM files, instead they 
work with these `spec` files and miscellaneous files that are the input 
to the `rpmbuild` phase.

This is what we call a **package** in our terminology. These packages 
live all in their own individual git tree and they are published for 
users to see, review and reuse at the following github URL:

    [https://github.com/clearlinux-pkgs]

Working with `spec` files is ... tedious work. A linux distribution 
typically contains thousands of them and they mostly repeat metadata 
that can automatically be generated or discovered, so we really don't 
want to spend too much time on the metadata aspect. The CLear Linux OS 
team also has created a bunch of tooling that will make dealing and 
creating these `spec` files easier and largely automated, so that 
developers can focus on the content instead.

We'll use the `common` tooling to bypass some of the hurdles that make 
packaging more difficult and get to borrow most of the upstream content 
for free. This allows us to modify targeted components for the purpose 
of our mix quickly.

## `common`

A repository at github specifically exists to deal with the creation 
and maintainance of `spec` files. It needs to be setup once and we have 
a handy shell script to do this. You can find it either on the 
`https://github.com/clearlinux/common/` repository but it's also in the 
`files` folder in the training repository.

```
~ $ how-to-clear/files/common/user-setup.sh
```

This command creates a folder called `clearlinux`. Note we are 
executing it in the `~` folder, but you can set it up and move it in 
its entirety to a new location without issues. Note we assume you 
cloned the training git project at this location, too.

Inside the `clearlinux` workspace you'll find a `Makefile` and the 
`projects` and `packages` folders. For now, you won't need to deal with 
the `projects` folder other than knowing that this is where `autospec` 
is stored and the `common` project lives, and these two are the heart 
of the tooling that deals with making packages for Clear Linux OS.

```
~ $ cd clearlinux
```

### `packages`

Under the `packages` folder we will store the folders that contain the 
`spec` and other files and from there we will create RPM files as 
needed for our training and testing.

The common tooling will allow us to reuse existing Clear Linux OS 
packages from upstream, and if we do, these will end up in here. If we 
end up making our own custom RPM files, we don't necessarily need to 
put them here at all, but we'll setup the workspace so that `mixer` can 
use the generated RPM files easily and so it will be easier to do this 
all in a consistent way, but in the end the `mixer` tooling doesn't 
care where the RPM files come from and how they are generated, and they 
can even be copied in if you want to do that, and bypass actually 
building them.

We can quickly borrow some of the upstream packages if we desire. This 
makes for an excellent starting point and shows off the tooling that we 
have.

```
~/clearlinux $ make clone_curl
~/clearlinux $ cd packages/curl
```

Since `curl` is already present in Clear Linux OS, we can just start 
using that packages and make modifications to it. This allows us to 
bypass most of the hurdles of packaging, and gives us a starting point 
that is as close to what Clear Linux OS uses as possible.

```
~/clearlinux/packages/curl $ make build
```

We can make a quick change to the configuration and rebuild to actually 
make a change. Edit the `configure` file and add the following option 
on a line somewhere:

```
--disable-rtsp
```

Then use autospec to recreate the package:

```
~/clearlinux/packages/curl $ make autospec
```

