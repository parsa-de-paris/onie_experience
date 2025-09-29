# ONIE Build Experience for accton_as7326_56x using DUE project

This repository documents the process I followed to build ONIE (Open Network Install Environment) for the device **accton_as7326_56x**.
I got ONIE built using `ubuntu-live-server` version `20.04`.

You can also refer to the official ONIE build instructions here:  
[ONIE Building Documentation](https://opencomputeproject.github.io/onie/developers/building.html#branches-and-build-environment-compatibility)
[ONIE for accton_as7326_56x](https://github.com/opencomputeproject/onie/blob/master/machine/accton/accton_as7326_56x/INSTALL)


## Steps to Build ONIE

### 1. Install Essential Dependencies

Before starting, make sure to install all required dependencies for your system. (You can add specific dependency commands here if you want.)
```bash
DEBIAN_BUILD_HOST_PACKAGES	= build-essential stgit u-boot-tools util-linux \
				  gperf device-tree-compiler python-all-dev xorriso \
				  autoconf automake bison flex texinfo libtool libtool-bin \
				  gawk libncurses5 libncurses5-dev bc \
				  dosfstools mtools pkg-config git wget help2man libexpat1 \
				  libexpat1-dev fakeroot python-sphinx rst2pdf \
				  libefivar-dev libnss3-tools libnss3-dev libpopt-dev \
				  libssl-dev sbsigntool uuid-runtime uuid-dev cpio \
				  bsdmainutils unzip
```

---

### 2. Clone the ONIE and DUE Repositories

```bash
git clone https://github.com/opennetworkproject/onie.git
git clone https://github.com/CumulusNetworks/DUE.git
```

### 3. Create the Docker Image using DUE

Navigate to the DUE directory and create a Docker image for the ONIE build environment:
```bash
cd DUE
./due --create --platform linux/amd64 --name onie-build-debian-9 --prompt ONIE-9 --tag onie-9 --use-template onie --from debian:9 --description 'ONIE Build Debian 9' --image-patch debian/9/filesystem
```

### 4. Run the Docker Container

Start the Docker container that provides the environment to build ONIE for your vendor device:
```bash
./due --run
```

### 5. Prepare to Build ONIE

Inside the running Docker container, navigate to the ONIE directory:
```bash
cd onie
```
Before building, install these additional packages and update your `PATH`:
```bash
sudo apt install -y unzip
sudo apt install -y libelf-dev
export PATH="/sbin:/usr:$PATH"
sudo chown -R $USER:$USER ~/onie
sudo chown -R $USER:$USER ~/onie/build
```
### 6. Build ONIE for accton_as7326_56x

Run the following command to start the build process:
```bash
make -j4 MACHINEROOT=../machine/accton MACHINE=accton_as7326_56x all recovery-iso
```
if everything goes well, in the `onie/build/images` directory you will see:
```bash
user@due-onie-build-debian-9:~/onie/build/images$ ls -l
total 101124
-rw-r--r-- 1 user user 10685636 Sep 28 07:16 accton_as7326_56x-r0.initrd
-rw-r--r-- 1 user user 4932256 Sep 28 07:15 accton_as7326_56x-r0.vmlinuz
-rw-r--r-- 1 user user 37588992 Sep 28 07:17 onie-recovery-x86_64-accton_as7326_56x-r0.efi64.pxe
-rw-r--r-- 1 user user 34930688 Sep 28 07:17 onie-recovery-x86_64-accton_as7326_56x-r0.iso
-rw-r--r-- 1 user user 15484922 Sep 28 07:16 onie-updater-x86_64-accton_as7326_56x-r0
```



## Notes

- Ensure you have a steady internet connection during the build process.

- Adjust the -j4 flag in the make command based on the number of CPU cores available to speed up the build.





