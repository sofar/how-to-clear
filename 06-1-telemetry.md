
How To Clear - Clear Linux Telemetry
================================

## What you'll learn in this chapter

* Ground rules of telemetry, privacy, opt-in
* Basics of Clear Linux OS Telemetry
* Creating a custom telemetry event
* Backend collection concepts


## Concepts

## How to start

First, we'll need to enable telemetry on the target device. For this, 
we have to add the telemetrics bundle to our mix, and make it available 
to clients.

```
~/mix $ mixer bundle add telemetrics
Adding bundle "telemetrics" from upstream bundles
~/mix $ sudo mixer build all
```

We can obviously modify the image to add telemetry by default as a 
bundle, but we'd lose our existing system, so we'll just add it on our 
target device manually for now:

```
~ # swupd update
~ # swupd bundle-add telemetrics
```

