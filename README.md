# AltServer-Linux

AltServer for AltStore, but on-device

## Usage

- 📦 Install IPA
  ```bash
  ./AltServer -u [UDID] -a [AppleID account] -p [AppleID password] [ipaPath.ipa]
  ```

- ➰ Running as AltServer Daemon 
  ```bash
  ./AltServer
  ```

- 🧪 Full Usage
  ```bash
  Usage:  AltServer-Linux options [ ipa-file ]
    -h  --help             Display this usage information.
    -u  --udid UDID        Device's UDID, only needed when installing IPA.
    -a  --appleID AppleID  Apple ID to sign the ipa, only needed when installing IPA.
    -p  --password passwd  Password of Apple ID, only needed when installing IPA.
    -d  --debug            Print debug output, can be used several times to increase debug level.

  The following environment var can be set for some special situation:
    - ALTSERVER_ANISETTE_SERVER: Set to custom anisette server URL
            if not set, the default one: https://armconverter.com/anisette/irGb3Quww8zrhgqnzmrx, is used
    - ALTSERVER_NO_SUBSCRIBE: (*unused*) Please enable this for usbmuxd server that do not correctly usbmuxd_listen interfaces
  ```

## Download

- [Github Release](https://github.com/NyaMisty/AltServer-Linux/releases)
- [altserver-linux-curve-git <sup>AUR</sup>](https://aur.archlinux.org/packages/altserver-linux-curve-git)
- Nightly Builds are available as [Workflow Artifacts](https://github.com/NyaMisty/AltServer-Linux/actions)

---

# Offline Anisette

Offline Anisette Data Generation (i.e. with Sideloadly) is supported.  
Run [alt_anisette_server](https://hub.docker.com/r/nyamisty/alt_anisette_server) and specify the custom server url through the environment variable `ALTSERVER_ANISETTE_SERVER`.

# Wi-Fi Refresh

[netmuxd](https://github.com/jkcoxson/netmuxd) supports network devices since versions `>0.1.1`.

To use netmuxd stop the original `usbmuxd` and run `netmuxd` before starting altserver.

> ~~If netmuxd does not work, please try using special env var `ALTSERVER_NO_SUBSCRIBE`. Enabling this would disable **auto-refresh when plugged-in** of USB devices~~

---

## Advanced: Build Instructions
> If you have issues with building you may want to checkout the build workflow

> **Note**  
> The following build instructions apply to archlinux only, but should be roughly the same on other distros.

- 🚧 Install Build Dependencies
  - Arch Linux:
    ```bash
    yay -S boost clang cmake ninja git libcorecrypto-git zlib python3 avahi
    ```
  - Ubuntu:
    ```bash
    sudo apt install clang cmake git zlib python3 avahi libavahi-compat-libdnssd-dev
    ```
    Then we have to install boost libraries. We can download it from [its website](https://www.boost.org/users/download/) and then build them like this:
    ```bash
    tar xvf boost_1_XX_Y.tar.gz
    sudo apt-get install build-essential g++ python3-dev autotools-dev libicu-dev libbz2-dev libboost-all-dev
    cd boost_1_XX_Y
    ./bootstrap.sh --prefix=/usr/
    sudo ./b2 install
    ```
	Replacing `1_XX_Y` for the exact version you have downloaded.

    In addition, we need to build and install corecrypto from Apple sources. Since the build is a bit broken, it needs some fixes which I already committed [here](https://github.com/altserver-for-android/corecrypto).
    You can use it as follows (the last "-j`nproc`" parameter is not necessary, but it speeds the build considerably):
    ```bash
    git clone https://github.com/altserver-for-android/corecrypto
    cd corecrypto
    mkdir build
    cd build
    CC=clang CXX=clang++ cmake ..
    sudo make install -j`nproc`
    ```

- 📦 Grab the source
  ```bash
  git clone --recursive https://github.com/NyaMisty/AltServer-Linux
  cd AltServer-Linux
  ````

- 🏗️ Build
  ```bash
  mkdir build && cd build
  cmake .. && cmake --build . --config Release
  ```