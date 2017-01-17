# Solaris VMWare

This is a customized version of the publicly available [Solaris 10](http://www.oracle.com/technetwork/server-storage/solaris10/downloads/index.html) for testing R packages. It includes preinstalled [ORD 3.2.0](https://oss.oracle.com/ORD/), [Solaris Studio 12.3](http://www.oracle.com/technetwork/server-storage/developerstudio/downloads/solaris-studio-12-3-2333052.html) and [OpenCSW r_base 3.3.0](https://www.opencsw.org/package/r_base/).

```
VMware Image: https://drive.google.com/open?id=0B9GbYGrVAoVbT25SRFpNeC1mWTQ
Username: root
Password: solaris
```

You must accept the [OTN License Agreement for Oracle Solaris](http://www.oracle.com/technetwork/licenses/solaris-cluster-express-license-167852.html) and [ Oracle Solaris Studio OTN Developer License Agreement](http://www.oracle.com/technetwork/licenses/solaris-studio-license-169628.html) to download this software.

![screenshot](http://i.imgur.com/h6RyBtZ.png)

## Details

This VM It is based of the official [Oracle Solaris 10 x86 ISO](http://www.oracle.com/technetwork/server-storage/solaris10/downloads/index.html). The installation includes two versions of R, two compilers and some other software:

 - [Solaris Studio  12.3](http://www.oracle.com/technetwork/server-storage/developerstudio/downloads/solaris-studio-12-3-2333052.html)
 - Oracle R Distribution aka [ORD](https://oss.oracle.com/ORD/) (uses Solaris Studio) in `/usr/bin/R`
 - OpenCSW:
   - [r_base](http://www.opencsw.org/packages/CSWr-base/) (uses GCC) in `/opt/csw/bin/R`
   - [gcc5](https://www.opencsw.org/packages/gcc5g++)
   - git
   - vim

Simply download and extract the VMWare image zip file and double click to import in VMware.

### Using Solaris

There are Desktop icons to launch both versions of R. 

![screenshot](http://i.imgur.com/00R2GyV.png)


To open R manually, start a terminal by clicking the terminal icon on the top menu bar. The ORD version of R is available from the PATH, just run:

```  
R
```

Programs from CSW are not added to the PATH. To run the GCC build of R:

```
export PATH=/opt/csw/bin:$PATH
R
```

Both installations use separate directories to store their R package libraries so they should not conflict.

### Third party libraries

CSW is an open source package manager and repository that is also used by CRAN. It is preinstalled on this system. Use pkgutil to install a library:

    /opt/csw/bin/pkgutil -y -i libcurl_dev

It is unclear to me where the compilers look for headers. CRAN is setting a bunch of environment variables but it often seems to work out of the box. 

A list of available CSW software: http://www.opencsw.org/get-it/packages/

### Reduce VM size

For some reason vmware keeps around all deleted files ever which results in ever increasing size of the VM image. To wipe old data you need to run in solaris:

```
vmware-toolbox-cmd disk shrink /
```

This does not actually delete any files, it only reduces the size of your VM file by wiping history data (at least I think that's whats happening).

### Compile errors on ORC / Studio

The Solaris Studio 12.3 compiler does not support recent C++ features as used by Rcpp or C++11. Currently CRAN checks these packages only using the GCC build of R.


## How this was created

Create a new Virtual Machine and mount the Solaris 10 iso in the virtual CD drive. Increase the disk size in VMware before starting because it's very tricky to do this after installation.

In the installer I did a *custom* installation in order to increase the size of the root partition. Other than that standard installation.

### Default shell

To make bash default user shell

```
usermod -s /usr/bin/bash root
```

To add `gmake` and `make` to path you need:

```
PATH=${PATH}:/usr/ccs/bin:/usr/sfw/bin
```

### Install OpenCSW

Follow [instructions](https://www.opencsw.org/manual/for-administrators/getting-started.html)

```
pkgadd -d http://get.opencsw.org/now
/opt/csw/bin/pkgutil -U
/opt/csw/bin/pkgutil -y -i sudo vim
```

To install [r_base](https://www.opencsw.org/package/r_base/):

```
/opt/csw/bin/pkgutil -y -i r_base
```

Other stuff

```
/opt/csw/bin/pkgutil -y -i git gmake
```

## Install ORD

ORD install script requires sudo (from OpenCSW).

```
PATH=$PATH:/opt/csw/bin
```
Then follow steps:

 1. Download [ORD installer](https://oss.oracle.com/ORD/ord-3.2.0-sol10-x86-64-sunstudio12u3.tar.gz) and run install script
 2. Download [Supporting Libraries](https://oss.oracle.com/ORD/ord-3.2.0-supporting-sol10-x86-64-sunstudio12u3.tar.gz) and extract libs to `/usr/lib/64/R/lib/`
 3. Download [Solaris Studio 12.3](http://download.oracle.com/otn/solaris/studio/SolarisStudio12.3-solaris-x86-bin.tar.bz2) and extract to `/opt/SunProd/studio12u3/`.

Test by running `/usr/bin/R` and install a package with C code (e.g. jsonlite).

### Set Rprofile

Add to `~/.Rprofile` script:

```
Sys.setenv(PATH=paste0(Sys.getenv("PATH"), ":/usr/ccs/bin:/usr/sfw/bin"))
options(repos = "http://cloud.r-project.org")
```

### Desktop Icons

To create Gnome desktop launchers create a file text file `R-ORD.desktop`:

```
[Desktop Entry]
Type=Application
Terminal=true
Name=R-3.2.0 (ORD)
Exec=/usr/bin/R
Icon=/usr/local/logo/logo-old.png
```

And another one with `R-CSW.desktop`:

```
[Desktop Entry]
Type=Application
Terminal=true
Name=R-3.3.0 (CSW)
Exec=/usr/bin/Rcsw
Icon=/usr/local/logo/logo-new.png
```