# Solaris VMWare

In VMWare create new VM and add mount the [Solaris 10 x86 image](http://www.oracle.com/technetwork/server-storage/solaris10/downloads/index.html) in the virtual CD drive.

user: root
pass: Solaris

### bash and make

To make bash default user shell

```
usermod -s /usr/bin/bash root
```

To add 'gmake' and 'make' to path you need:

```
PATH=${PATH}:/usr/ccs/bin:/usr/sfw/bin
```

### OpenCSW

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

## ORD

ORD install script requires sudo (from OpenCSW).

```
PATH=$PATH:/opt/csw/bin
```
Then follow steps:

 1. Download [ORD installer](https://oss.oracle.com/ORD/ord-3.2.0-sol10-x86-64-sunstudio12u3.tar.gz) and run install script
 2. Download [Supporting Libraries](https://oss.oracle.com/ORD/ord-3.2.0-supporting-sol10-x86-64-sunstudio12u3.tar.gz) and extract libs to `/usr/lib/64/R/lib/`
 3. Download [Solaris Studio 12.3](http://download.oracle.com/otn/solaris/studio/SolarisStudio12.3-solaris-x86-bin.tar.bz2) and extract to `/opt/SunProd/studio12u3/`.

Test by running `/usr/bin/R` and install a package with C code (e.g. jsonlite).

## Rprofile

Add to `~/.Rprofile` script:

```
Sys.setenv(PATH=paste0(Sys.getenv("PATH"), ":/usr/ccs/bin:/usr/sfw/bin"))
options(repos = "http://cloud.r-project.org")
```
