
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
~/clearlinux $ make clone_dmidecode
~/clearlinux $ cd packages/dmidecode
```

Since `dmidecode` is already present in Clear Linux OS, we can just 
start using that packages and make modifications to it. This allows us 
to bypass most of the hurdles of packaging, and gives us a starting 
point that is as close to what Clear Linux OS uses as possible.

```
~/clearlinux/packages/dmidecode $ make bump
~/clearlinux/packages/dmidecode $ make build
```

We can make a quick change to this package, and change the revision to 
a new number that's higher than the Clear Linux OS version, and rebuild 
it. This way we can include it in our mix and know for sure that it's 
our version and not the version from Clear Linux OS' RPM repository 
instead.

We should end up with several new RPM files under `results/`. This 
brings us to the next phase: Adding `dmidecode` into our mix content 
and pushing it to our target device.

## Adding `dmidecode` to our mix

We need to maintain an RPM repository. An RPM repository is a 
combination of a few RPM files and some metadata that allows programs 
like `yum` and `dnf` to follow and include dependencies when we're 
asking it to use specific RPM files.

In the mixer folder, we've already created a location for RPM files 
when we used the `mixer init --local-rpms` command in an earlier 
chapter. We will be using this location to put our newly generated RPM 
files and thereby convey them to mixer so it can include them as 
needed.

The `make autospec` command creates RPM files under each package. We 
could copy these files manually over to the `local-rpms` folder in the 
`mix` folder structure, but we have some tools available to do this 
more efficiently.

Inside a package folder, there are two simple commands that you can 
execute that will automatically place RPM files of that package into an 
RPM repository for you. This repository will be created in the 
`~/clearlinux/repo` folder. Once we're done placing RPM files here, we 
can copy them over to the `mixer` location:

```
~/clearlinux/packages/dmidecode $ make repoadd
~/clearlinux/packages/dmidecode $ cd ~/mix
~/clearlinux/packages/dmidecode $ cp -v ~/clearlinux/repo/*.rpm local-rpms/
```

Next, we can include `dmidecode` in several ways to our update content. 
We can either create a new bundle, locally. We can modify an existing 
upstream bundle, or we can even include an upstream bundle that already 
has `dmidecode` present. For simplicity, we'll make a new local bundle:

```
~/mix $ mixer bundle add dmidecode
~/mix $ mixer bundle edit dmidecode
```

Add the `dmidecode` or `demidecode-bin` (your choice) RPM file name to
the bundle, and you're ready to deploy the change:

```
~/mix $ sudo mixer build all
```

After this, we can go to our target device and install the new bundle 
after we update, and use it:

```
~ # swupd update
~ # swupd bundle-add dmidecode
~ # dmidecode
```

