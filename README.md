# rpi-precompiled-kernels
Repository of compiled useful kernels

# How are these made?
https://www.raspberrypi.org/documentation/linux/kernel/building.md
https://www.raspberrypi.org/documentation/linux/kernel/configuring.md

```
sudo apt-get -y update
sudo apt-get -y install git bc libncurses5-dev
git clone --depth=1 https://github.com/raspberrypi/linux --branch rpi-4.14.y

cd linux
KERNEL=kernel7
make bcm2709_defconfig

# <enable rl8723bs>
# make menuconfig
# Device Drivers -> Staging drivers -> Realtek RTL8723BS SDIO..
sed -i 's/# CONFIG_RTL8723BS is not set/CONFIG_RTL8723BS=m/' .config

make -j4 zImage modules dtbs

mkdir ../modules
sudo make INSTALL_MOD_PATH=../modules/ modules_install

rm -f ../modules/lib/modules/*/build
rm -f ../modules/lib/modules/*/source

mkdir ../pi
mkdir ../pi/overlays

cp arch/arm/boot/dts/*.dtb ../pi/
cp arch/arm/boot/dts/overlays/*.dtb* ../pi/overlays/
cp arch/arm/boot/dts/overlays/README ../pi/overlays/
cp arch/arm/boot/zImage ../pi/$KERNEL.img

cd ..
zip -R $KERNEL.zip pi/ modules/
```
