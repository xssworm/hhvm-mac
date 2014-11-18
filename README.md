# HHVM 3.3 for Mac

[HHVM page](http://hhvm.com) |
[Hacklang page](http://hacklang.org) |
[General group](https://www.facebook.com/groups/hhvm.general/) |
[Dev group](https://www.facebook.com/groups/hhvm.dev/?ref=br_tf) |
[Twitter](http://twitter.com/HipHopVM)

HHVM (aka the HipHop Virtual Machine) is an open-source virtual machine designed for executing programs written in [Hack](http://hacklang.org) and PHP. HHVM uses a just-in-time compilation approach to achieve superior performance while maintaining the flexibility that PHP developers are accustomed to. To date, HHVM (and its predecessor HPHPc before it) has realized over a 9x increase in web request throughput and over a 5x reduction in memory consumption for Facebook compared with the PHP 5.2 engine + APC.

HHVM should be used together with a FastCGI-based webserver like [nginx](https://github.com/facebook/hhvm/wiki/FastCGI#making-it-work-with-nginx) or [apache](https://github.com/facebook/hhvm/wiki/FastCGI#making-it-work-with-apache).


## FAQ

Our [FAQ](https://github.com/facebook/hhvm/wiki/FAQ) has answers to many common questions about HHVM, from [general questions](https://github.com/facebook/hhvm/wiki/FAQ#general) to questions geared towards those that want to [use](https://github.com/facebook/hhvm/wiki/FAQ#users) or [contribute](https://github.com/facebook/hhvm/wiki/FAQ#contributors) to HHVM.


## Installing

### CheckList
==========

- CMake >= 3.0.2
- Using `ranlib` from `/opt/local/bin/ranlib` or `/usr/bin/ranlib`
- GCC 4.8
- Boost 1.55+ and is compiled with GCC 4.8
- JeMalloc compiled with patch.
- OCaml 3/4 (hack requirement)



### 1. Install homebrew

```sh
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 2. Tap additional repositories

```sh
brew tap homebrew/versions
```

### 3. Install dependencies
```sh
brew install freetype gettext cmake git libtool mcrypt oniguruma re2c     \
             autoconf libelf readline automake mysql-connector-c pcre     \
             gd icu4c libmemcached pkg-config tbb imagemagick binutils    \
             curl imap-uw libxslt libevent sqlite homebrew/versions/gcc48 \
             openssl
brew install --build-from-source --cc=gcc-4.8 glog boost
```

Install some packages that are not (yet) part of homebrew:

```sh
brew install \
  https://gist.github.com/slbmeh/9059260/raw/1cbf0f6f5639b4a066395bedb702cdd6bd895d15/libdwarf.rb \
  https://gist.github.com/sgolemon/8fdc7e2afcd73a960b9c/raw/1211e21151ed3443dbc027e5383fb49e9eb1ab91/jemallocfb.rb
```

### 4. Get HHVM

```sh
git clone --recursive git://github.com/xssworm/hhvm-mac.git
```

### 5. Build HHVM

```sh
cd hhvm
cmake . \
    -DCMAKE_CXX_COMPILER=$(brew --prefix gcc48)/bin/g++-4.8 \
    -DCMAKE_C_COMPILER=$(brew --prefix gcc48)/bin/gcc-4.8 \
    -DCMAKE_ASM_COMPILER=$(brew --prefix gcc48)/bin/gcc-4.8 \
    -DLIBIBERTY_LIB=$(brew --prefix gcc48)/lib/x86_64/libiberty-4.8.a \
    -DCMAKE_INCLUDE_PATH="/usr/local/include:/usr/include" \
    -DCMAKE_LIBRARY_PATH="/usr/local/lib:/usr/lib" \
    -DLIBEVENT_LIB=$(brew --prefix libevent)/lib/libevent.dylib \
    -DLIBEVENT_INCLUDE_DIR=$(brew --prefix libevent)/include \
    -DICU_INCLUDE_DIR=$(brew --prefix icu4c)/include \
    -DICU_LIBRARY=$(brew --prefix icu4c)/lib/libicuuc.dylib \
    -DICU_I18N_LIBRARY=$(brew --prefix icu4c)/lib/libicui18n.dylib \
    -DICU_DATA_LIBRARY=$(brew --prefix icu4c)/lib/libicudata.dylib \
    -DREADLINE_INCLUDE_DIR=$(brew --prefix readline)/include \
    -DREADLINE_LIBRARY=$(brew --prefix readline)/lib/libreadline.dylib \
    -DCURL_INCLUDE_DIR=$(brew --prefix curl)/include \
    -DCURL_LIBRARY=$(brew --prefix curl)/lib/libcurl.dylib \
    -DBOOST_INCLUDEDIR=$(brew --prefix boost)/include \
    -DBOOST_LIBRARYDIR=$(brew --prefix boost)/lib \
    -DBoost_USE_STATIC_LIBS=ON \
    -DJEMALLOC_INCLUDE_DIR=$(brew --prefix jemallocfb)/include \
    -DJEMALLOC_LIB=$(brew --prefix jemallocfb)/lib/libjemalloc.dylib \
    -DLIBINTL_LIBRARIES=$(brew --prefix gettext)/lib/libintl.dylib \
    -DLIBINTL_INCLUDE_DIR=$(brew --prefix gettext)/include \
    -DLIBDWARF_LIBRARIES=$(brew --prefix libdwarf)/lib/libdwarf.3.dylib \
    -DLIBDWARF_INCLUDE_DIRS=$(brew --prefix libdwarf)/include \
    -DLIBMAGICKWAND_INCLUDE_DIRS=$(brew --prefix imagemagick)/include/ImageMagick-6 \
    -DLIBMAGICKWAND_LIBRARIES=$(brew --prefix imagemagick)/lib/libMagickWand-6.Q16.dylib \
    -DMYSQL_INCLUDE_DIR=$(brew --prefix mysql-connector-c)/include \
    -DMYSQL_LIB=$(brew --prefix mysql-connector-c)/lib \
    -DFREETYPE_INCLUDE_DIRS=$(brew --prefix freetype)/include/freetype2 \
    -DFREETYPE_LIBRARIES=$(brew --prefix freetype)/lib/libfreetype.dylib \
    -DLIBMEMCACHED_LIBRARY=$(brew --prefix libmemcached)/lib \
    -DLIBMEMCACHED_INCLUDE_DIR=$(brew --prefix libmemcached)/include \
    -DLIBELF_LIBRARIES=$(brew --prefix libelf)/lib/libelf.a \
    -DLIBELF_INCLUDE_DIRS=$(brew --prefix libelf)/include/libelf \
    -DLIBGLOG_LIBRARY=$(brew --prefix glog)/lib/libglog.dylib \
    -DLIBGLOG_INCLUDE_DIR=$(brew --prefix glog)/include \
    -DOPENSSL_SSL_LIBRARY=$(brew --prefix openssl)/lib/libssl.dylib \
    -DOPENSSL_INCLUDE_DIR=$(brew --prefix openssl)/include \
    -DOPENSSL_CRYPTO_LIBRARY=$(brew --prefix openssl)/lib/libcrypto.dylib \
    -DPCRE_LIBRARY=$(brew --prefix pcre)/lib \
    -DPCRE_INCLUDE_DIR=$(brew --prefix pcre)/include \
    -DTBB_INSTALL_DIR=$(brew --prefix tbb) \
    -DLIBSQLITE3_INCLUDE_DIR=$(brew --prefix sqlite)/include \
    -DLIBSQLITE3_LIBRARY=$(brew --prefix sqlite)/lib/libsqlite3.0.dylib
make -j4
sudo ln -s $(pwd)/hphp/hhvm/hhvm /usr/bin/hhvm
```

### 6. Test HHVM

```sh
hhvm --version
```



## Running

You can run standalone programs just by passing them to hhvm: `hhvm my_script.php`.

If you want to host a website:
* Install your favorite webserver
* Install our [package](https://github.com/facebook/hhvm/wiki/Prebuilt%20Packages%20for%20HHVM)
* Start your webserver
* Run `sudo /etc/init.d/hhvm start`
* Visit your site at http://.../index.php


## License

HHVM is licensed under the PHP and Zend licenses except as otherwise noted.

The Hack typechecker (`hphp/hack`) is licensed under the BSD license (`hphp/hack/LICENSE`) with an additional grant of patent rights (`hphp/hack/PATENTS`) except as otherwise noted.
