Folly: Facebook Open-source Library
-----------------------------------

### What is `folly`?

Folly (acronymed loosely after Facebook Open Source Library) is a
library of C++11 components designed with practicality and efficiency
in mind. **Folly contains a variety of core library components used extensively
at Facebook**. In particular, it's often a dependency of Facebook's other
open source C++ efforts and place where those projects can share code.

It complements (as opposed to competing against) offerings
such as Boost and of course `std`. In fact, we embark on defining our
own component only when something we need is either not available, or
does not meet the needed performance profile. We endeavor to remove
things from folly if or when `std` or Boost obsoletes them.

Performance concerns permeate much of Folly, sometimes leading to
designs that are more idiosyncratic than they would otherwise be (see
e.g. `PackedSyncPtr.h`, `SmallLocks.h`). Good performance at large
scale is a unifying theme in all of Folly.

### Logical Design

Folly is a collection of relatively independent components, some as
simple as a few symbols. There is no restriction on internal
dependencies, meaning that a given folly module may use any other
folly components.

All symbols are defined in the top-level namespace `folly`, except of
course macros. Macro names are ALL_UPPERCASE and should be prefixed
with `FOLLY_`. Namespace `folly` defines other internal namespaces
such as `internal` or `detail`. User code should not depend on symbols
in those namespaces.

Folly has an `experimental` directory as well. This designation connotes
primarily that we feel the API may change heavily over time. This code,
typically, is still in heavy use and is well tested.

### Physical Design

At the top level Folly uses the classic "stuttering" scheme
`folly/folly` used by Boost and others. The first directory serves as
an installation root of the library (with possible versioning a la
`folly-1.0/`), and the second is to distinguish the library when
including files, e.g. `#include <folly/FBString.h>`.

The directory structure is flat (mimicking the namespace structure),
i.e. we don't have an elaborate directory hierarchy (it is possible
this will change in future versions). The subdirectory `experimental`
contains files that are used inside folly and possibly at Facebook but
not considered stable enough for client use. Your code should not use
files in `folly/experimental` lest it may break when you update Folly.

The `folly/folly/test` subdirectory includes the unittests for all
components, usually named `ComponentXyzTest.cpp` for each
`ComponentXyz.*`. The `folly/folly/docs` directory contains
documentation.

### What's in it?

Because of folly's fairly flat structure, the best way to see what's in it
is to look at the headers in [top level `folly/` directory](https://github.com/facebook/folly/tree/master/folly). You can also
check the [`docs` folder](folly/docs) for documentation, starting with the
[overview](folly/docs/Overview.md).

Folly is published on Github at https://github.com/facebook/folly

### Build Notes

#### Dependencies

folly requires gcc 4.9+ and a version of boost compiled with C++14 support.

Please download googletest from
https://github.com/google/googletest/archive/release-1.8.0.tar.gz and unpack it into the
folly/test subdirectory as `gtest`:

    (cd folly/test && \
     rm -rf gtest && \
     wget https://github.com/google/googletest/archive/release-1.8.0.tar.gz && \
     tar zxf release-1.8.0.tar.gz && \
     rm -f release-1.8.0.tar.gz && \
     mv googletest-release-1.8.0 gtest)

#### Linking non-default boost libraries

If you have boost installed in a non-default location, you need to be sure that
the linker and configure scripts know where to find boost.  This means making
sure that the `LIBRARY_PATH` environment variable contains `<BOOST_ROOT>/lib`,
as well as including the path explicitly when running
`./configure`:

```
export LIBRARY_PATH=$BOOST_ROOT/lib:$LIBRARY_PATH
./configure --with-boost=$BOOST_ROOT/lib
```

#### Ubuntu 16.04 LTS

The following packages are required (feel free to cut and paste the apt-get
command below):

```
sudo apt-get install \
    g++ \
    automake \
    autoconf \
    autoconf-archive \
    libtool \
    libboost-all-dev \
    libevent-dev \
    libdouble-conversion-dev \
    libgoogle-glog-dev \
    libgflags-dev \
    libiberty-dev \
    liblz4-dev \
    liblzma-dev \
    libsnappy-dev \
    make \
    zlib1g-dev \
    binutils-dev \
    libjemalloc-dev \
    libssl-dev \
    pkg-config
```

If advanced debugging functionality is required

```
sudo apt-get install \
    libunwind8-dev \
    libelf-dev \
    libdwarf-dev
```

In the folly directory, run:
```
  autoreconf -ivf
  ./configure
  make
  make check
  sudo make install
```

#### OS X (Homebrew)

folly is available as a Formula and releases may be built via `brew install folly`.

You may also use `folly/build/bootstrap-osx-homebrew.sh` to build against `master`:

```
  cd folly
  ./build/bootstrap-osx-homebrew.sh
```

#### OS X (MacPorts)

Install the required packages from MacPorts:

```
  sudo port install \
    autoconf \
    automake \
    boost \
    gflags \
    git \
    google-glog \
    libevent \
    libtool \
    lz4 \
    lzma \
    scons \
    snappy \
    zlib
```

Download and install double-conversion:

```
  git clone https://github.com/google/double-conversion.git
  cd double-conversion
  cmake -DBUILD_SHARED_LIBS=ON .
  make
  sudo make install
```

Download and install folly with the parameters listed below:

```
  git clone https://github.com/facebook/folly.git
  cd folly/folly
  autoreconf -ivf
  ./configure CPPFLAGS="-I/opt/local/include" LDFLAGS="-L/opt/local/lib"
  make
  sudo make install
```

#### Windows (Vcpkg)

folly is available in [Vcpkg](https://github.com/Microsoft/vcpkg#vcpkg) and releases may be built via `vcpkg install folly:x64-windows`.

You may also use `vcpkg install folly:x64-windows --head` to build against `master`.

#### Other Linux distributions

- double-conversion (https://github.com/google/double-conversion)

  Download and build double-conversion.
  You may need to tell configure where to find it.

  [double-conversion/] `ln -s src double-conversion`

  [folly/] `./configure LDFLAGS=-L$DOUBLE_CONVERSION_HOME/ CPPFLAGS=-I$DOUBLE_CONVERSION_HOME/`

  [folly/] `LD_LIBRARY_PATH=$DOUBLE_CONVERSION_HOME/ make`

- additional platform specific dependencies:

  Fedora 21 64-bit
    - gcc
    - gcc-c++
    - autoconf
    - autoconf-archive
    - automake
    - boost-devel
    - libtool
    - lz4-devel
    - lzma-devel
    - snappy-devel
    - zlib-devel
    - glog-devel
    - gflags-devel
    - scons
    - double-conversion-devel
    - openssl-devel
    - libevent-devel

  Optional
    - libdwarf-dev
    - libelf-dev
    - libunwind8-dev
