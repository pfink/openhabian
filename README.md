﻿As an **openHABian end user**, please check out the official openHAB
documentation:  
-   <https://www.openhab.org/docs/installation/openhabian.html>

# openHABian - Hassle-free openHAB Setup
[![build](https://travis-ci.org/openhab/openhabian.svg?branch=master)](https://travis-ci.com/github/openhab/openhabian)
![shellcheck](https://github.com/openhab/openhabian/workflows/shellcheck/badge.svg?branch=master)

Setting up a fully working Linux system with all needed packages and useful
tooling is a boring, lengthy albeit challenging task. Fortunately,

***A home automation enthusiast doesn't have to be a Linux enthusiast!***

openHABian is here to provide a **self-configuring** Linux system setup to meet
the needs of every openHAB user, in two flavours:

* a **SD-card image pre-configured with openHAB** for all *Raspberry Pi* models
* as a set of scripts that sets up openHAB and tools on any Debian based system

## openHAB versions 2 and 3
openHABian was made to provide a seamless User eXperience with openHAB.
Now that openHAB 3 is released to the public, we have incorporated changes to run
on and migrate to openHAB 3 from within openHABian.

## Hardware recommendation
Let's put this first: our current recommendation is to get a RPi 4 with 2 or 4 GB,
a 3 A power supply and a 16 GB SD card.
Also get another 32 GB or larger SD card and a USB card reader to make use of the
["auto backup" feature](docs/openhabian.md#Auto-Backup).
***
ATTENTION:<br>
Avoid getting the 8 GB model of RPi 4. 8 GB are a waste of money and it has issues,
you must [disable ZRAM](https://github.com/openhab/openhabian/blob/master/docs/openhabian.md#disable-zram) or use the 64bit image (untested).
***
## Hardware and OS support
As of openHABian version 1.6 and later, all Raspberry Pi models are supported as
hardware. Anything x86 based may work or not. Anything else ARM based such as ODroids,
OrangePis and the like may work or not. NAS servers such as QNAP and Synology
boxes will not work. Support for PINEA64 was dropped in this current release.<br>
We strongly recommend that users choose Raspberry Pi 2, 3 or 4 systems to have
1 GB of RAM or more. RPi 1 and 0/0W only have a single CPU core and 512 MB.
This can be sufficient to run a smallish openHAB setup, but it will
not be enough to run a full-blown system with many bindings and memory consuming
openHABian features/components such as ZRAM, InfluxDB or Grafana.
We do not actively prohibit installation on any hardware, including unsupported
systems, but we might skip or deny to install specific extensions such as those
memory hungry applications named above.

Supporting hardware means testing every single patch and every release. There
are simply too many combinations of SBCs, peripherals and OS flavors that
maintainers do not have available, or, even if they did, the time to spend on
the testing efforts that is required to make openHABian a reliable system.
Let's make sure you understand the implications of these statements: it means
that to run on hardware other than RPi 2/3/4 or (bare metal i.e. not virtualized)
x86 may work but this is **not** supported.

It may work to install and run openHABian on unsupported hardware. If it does
not work, you are welcome to find out what's missing and contribute it back to
the community with a Pull Request. It is sometimes simple things like a naming
string. We'll be happy to include that in openHABian so you can use your box
with openHABian unless there's a valid reason to change or remove it.
However, that does not make your box a "supported" one as we don't have it
available for our further development and testing. So there remains a risk that
future openHABian releases will fail to work on your SBC because we changed a
thing that broke support for your HW - unintentionally so however inevitable.

For ARM hardware that we don't support, you can try any of the [fake hardware parameters](openhabian.md/#fake-hardware-mode)
to 'simulate' RPi hardware and Raspi OS. If that still doesn't work for
you, give [Ubuntu](https://ubuntu.com/download/iot) or [ARMbian](https://www.armbian.com/) a try. 

Going beyond what the RPi image provides, as a manually installed set of
scripts, we support running openHABian on x86 hardware on generic Debian.
On ARM, we only support Raspberry Pi OS.
These are what we develop and test openHABian against.
We do **not** actively **support Ubuntu** so no promises but we provide code "as-is"
that is known to run on there. Several optional components though, such as
WireGuard or Homegear, are known to expose problems.

We expect you to use the current stable distribution, 'buster' for Raspberry
Pi OS (ARM) and Debian (x86) and 'focal' for Ubuntu (x86) this is.
To install openHABian on anything older or newer may work or not. If you
encounter issues, you may need to upgrade first or to live with the consequences
of running an OS on the edge of software development.

Either way, please note that you're on your own when it comes to configuring and
installing the HW with the proper OS yourself.

### 64 bit ?
Although RPi3 and 4 have a 64 bit processor, you cannot run openHAB in 64 bit.
The Azul Java Virtual Machine we currently use is incompatible with the aarch64
ARM architecture. In general you should be aware that to run in 64 bit has a
major drawback: increased memory usage. That is not a good idea on a heavily
memory constrained platform like a RPi. Also remember openHABian makes use of
Raspberry Pi OS which today still is a 32 bit OS.
We are closely observing development and will adapt openHABian once it will
reliably work on 64 bit.<br/>
So things may change in the future, but for the time being, you should not
manually enforce to install a 64 bit JVM.

On x86 hardware, 64 bit is the standard.

## Installation and Setup
Please check the [official documentation article](https://www.openhab.org/docs/installation/openhabian.html)
to learn about openHABian and please visit and subscribe to our very active
[community forum thread](https://community.openhab.org/t/13379).

If you want to install openHABian on non-supported hardware, you can actually
fake it to make openHABian treat your box as if it was one of the supported
ones. Needless to say that that may work out or not, but it's worth a try. See
[openhabian](openhabian.md) for how to edit openhabian.conf before booting. Set
the hw, hwarch and release parameters to match your system best.

## Development
openHABian is foremost a collection of `bash` scripts versioned and deployed
using git. In the current state the scripts can only be invoked through the
terminal menu system [whiptail](https://en.wikibooks.org/wiki/Bash_Shell_Scripting/Whiptail).
There is a longterm need to better separate the UI part from the script code. A
work has started to define conventions and further explain the code base in the
document [CONTRIBUTING](CONTRIBUTING.md) along with development guidelines in
general.

A good place to look at to start to understand the code is the file
`openhabian-setup.sh`.

### Building Hardware Images
Take a look at the `build.bash` script to get an idea of the process.
Run the code below with `platform` being `rpi`.
The RPi image is based on the [Raspberry Pi OS Lite](https://www.raspberrypi.org/downloads/raspberry-pi-os/)
(previously called Raspbian) standard image.
```
sudo bash ./build.bash platform
```

As the script uses `openhab/openhabian` git repository during installation it
must sometimes be changed to test code from other repositories, like a new
feature in a fork. There are two commands for replacing the git repo with a
custom one. The first command uses the current checked-out repository used in
the filesystem:
```
sudo bash build.bash platform dev-git
```
The second command uses a fully customizable repository:
```
sudo bash build.bash platform dev-url branch url
```

### Testing
Testing is done continuously with Travis-CI using the test framework
[BATS](https://github.com/bats-core/bats-core) and the linter
[ShellCheck](https://www.shellcheck.net/).  As the tests focus on installing
software, a [Docker](https://www.docker.com/) solution is used for easy build-up
and teardown.

To run the test suite on a `amd64` platform execute the commands below.
[Docker](https://www.docker.com/) and [ShellCheck](https://www.shellcheck.net/)
need to be installed first. For more details regarding the tests see
[Test Architecture](https://github.com/openhab/openhabian/blob/master/CONTRIBUTING.md#test-architecture)
in CONTRIBUTING.md.

```
docker build --tag openhabian/bats-openhabian -f Dockerfile.amd64 .
docker run --rm --name "unit-tests" -i openhabian/bats-openhabian bash -c 'bats --tap --recursive --filter "unit-." .'
docker run --rm --name "installation-tests" -i openhabian/bats-openhabian bash -c 'bats --tap --recursive --filter "installation-." .'
docker run --rm --name "destructive-tests" -i openhabian/bats-openhabian bash -c 'bats --tap --recursive --filter "destructive-." .'

docker build --tag openhabian/install-openhabian -f Dockerfile.amd64 .
docker run --name "install-test" --privileged -d openhabian/bats-openhabian
docker exec -i "install-test" bash -c "./build.bash local-test && mv ~/.profile ~/.bash_profile && /boot/first-boot.bash"

docker stop install-test
docker rm install-test
```

The [ShellCheck](https://www.shellcheck.net/) linter can be run by using the
following commands:
```
shellcheck -x -s bash openhabian-setup.sh
shellcheck -x -s bash functions/*.bash
shellcheck -x -s bash build-image/*.bash
shellcheck -x -s bash build.bash ci-setup.bash
```


Happy Hacking!
