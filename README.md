# CryptoShark

Self-optimizing cross-platform code tracer based on dynamic recompilation,
powered by Frida and Capstone. Works at the machine code level, no source
code needed. Tags threads based on which APIs they use, showing you in
real-time what functions have been called, allowing you to study them by
carefully injecting logging and other side-effecty code.

## Screencast

[![ScreenShot](http://img.youtube.com/vi/hzDsxtcRavY/0.jpg)](https://www.youtube.com/watch?v=hzDsxtcRavY)

## Binaries

Get them at: https://github.com/frida/cryptoshark/releases

## Building for local development

### Building agent.js

This is the blob of JavaScript that CryptoShark injects into target processes.

#### Fetch build-time dependencies

    $ cd agent
    $ npm install

This will also build `agent.js`.

#### Build agent.js

    $ cd agent
    $ npm run build

#### Watch while developing

    $ cd agent
    $ npm run watch

Which will monitor the TypeScript source code and incrementally compile
`agent.js`. Note that the agent is included as a resource, so remember
to hit “Build” in Qt Creator.

### Building the application

- Install the latest [Qt 5.x](https://www.qt.io/download-open-source).
  Debian users can install the following packages: `qt5-qmake qt5-default
  libqt5qml5 libqt5quick5 libqt5quickwidgets5 qml-module-qtquick-dialogs`

- Grab the latest frida-qml binaries from
  [here](https://github.com/frida/frida/releases). Extract the tarball in your
  Qt installation's `qml` directory – on macOS it might be something like:
  `~/Qt/5.15.0/clang_64/qml/`.
  (We only provide macOS binaries for now. On other platforms you will have to
  build frida-core and frida-qml yourself.)

- Open `CryptoShark.pro` with Qt Creator, select the `Release` configuration
  and hit `Run`.

## Building a portable Windows binary

> FIXME: This section is outdated.

In order to build a portable binary we will need a static build of Qt and
frida-qml. This is not recommended for development due to the prolonged linking
times, but it is very useful for generating a portable CryptoShark binary
without any external dependencies.

### Prerequisites

* MS Visual Studio 2013
* Windows SDK 8.1
* Git
* Perl
* Python
* nasm

Review all the paths in `tools\env.bat` to make sure everything matches your
system. Now, run it to enter the environment, which is required for the next
steps.

### Building OpenSSL

- Download the latest openssl tarball and extract it next to the CryptoShark repo.

- Change to that directory and run: `..\CryptoShark\tools\01-build-openssl.bat`.

### Building Qt

- Get the qt5 repo: `git clone git://gitorious.org/qt/qt5.git qt5`.

- Switch to the 5.3 branch:

```
cd qt5
git checkout 5.3
```

- Get the source code: `perl init-repository --no-webkit`.

- Change working directory to `qt5\qtbase` and run `..\..\CryptoShark\tools\02-build-qt.bat`.

- Change working directory to `qt5\qtdeclarative` and run `qmake` followed by `nmake`.

- Change to `qt5\qtquickcontrols` and run `qmake` followed by `nmake`.

### Building frida-qml

- Build `frida.sln` for `Release|Win32` as described [here](http://www.frida.re/docs/building/).

- Edit `frida-qml.pro` and change `win32:installPath` to point to
  your `qt5\qtbase\qml\Frida`.

- Change to `frida\frida-qml` and run `qmake` followed by `nmake install`.

### Building CryptoShark

- Run `npm install` followed by `gulp build`.

- Run `tools\generate-qml-imports-qrc.py`.

- Run `qmake` followed by `nmake`.

- A fresh new portable binary is now at: `release\cryptoshark.exe`.
