# Rust Industrial I/O for Linux

This is a Rust library crate for using the Linux Industrial I/O (IIO) subsytem, primarily used for the input and output of analog data from a Linux system. See the [IIO Wiki](https://wiki.analog.com/software/linux/docs/iio/iio).

The current version is a wrapper around the user-space C library, [libiio](https://github.com/analogdevicesinc/libiio).  Subsequent versions may access the interface the kernel ABI directly.

## Pre-release notes

This is an early, pre-release verion of the crate. The API is under active development and may change repeatedly. It is not recommended to use this for production applications... yet.

This initial development work wrappers a _specific_ version (v0.15) of _libiio_. It assumes that the library is installed on the target system.

## Testing the Crate

A great thing about the user-space IIO libraries is that, if you're developing on a fairly recent Linux host, you can start experimenting without having to do development on a board. You can run the IIO server daemon on an embedded board, and then use this crate to communicate with it over a network connection. When your application is working, you can then compile it for the target board and test it natively.

Several maker boards can be used to try out the Industrial I/O subsystem pretty easily. The BeagleBone Black and Green have the on-board AM335X A/D. The IIO library for it supports individual and buffered sampling, though without external trigger support.  The [Debian 9.5 IoT](https://beagleboard.org/latest-images) distribution for the board has IIO compiled into the kernel which can be used out of the box - although the user-space library should be upgraded.

## Installing the C Library

Install _libiio_ v0.15 on the target board. If you're developing on a Linux host, install the same version of the library there so that you can do some development on the host,

### Check the version on the target

If you're using a BeagleBone Black, an old library may have shipped with the distro. Install the latest distribution for the board. (This was tested with _Debian 9.5 2018-10-07 4GB SD IoT_).

Log onto the board and check the version:

```
$ iiod --version
0.15
```

If this is less than 0.15, remove the Debian packages and install from sources.

First, get rid of the existing library and utilities:

```
$ sudo apt-get purge libiio-utils libiio0
```

### Build from sources

Then install the pre-requisites for the build:

```
$ sudo apt-get install cmake flex bison libxml2-dev
```

And then download the library sources and build:

```
$ cd /tmp
$ wget https://github.com/analogdevicesinc/libiio/archive/v0.15.tar.gz
$ tar -xf v0.15.tar.gz 
$ cd libiio-0.15/
$ mkdir build ; cd build
$ cmake .. && make && sudo make install
```

Then check that the version installed properly:

```
$ iiod --version
0.15
```

## Build the Rust Crate

This is a fairly standard Rust wrapper project around a C library. It contains an unfafe _"-sys"_ sub-crate to wrap the C library API, and a higher-level, safe, Rust library in the main crate. To build them:

```
$ cargo build
```

There are also a number of example applications. They can all be built with:

```
$ cargo build --examples
```
