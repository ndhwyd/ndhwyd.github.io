## Установка OpenBSD на ROC-RK3399-PC (Renegade Elite)

Небольшой гайд как установить OpenBSD 6.7 на ROC-RK3399-PC (Renegade Elite) с использованием U-Boot Mainline.

### Потребуется

```markdown

# Hardware
1. ROC-RK3399-PC
2. USB-to-UART Adapter
3. SD Card

# Software (Windows or Linux)
## Windows 
1. Windows 10 + Windows subsystem for Linux
2. https://github.com/ndhwyd/openbsd-miniroot-patcher

## Linux
1. dd
```

### Установка компиляторов
```markdown
sudo apt-get install gcc-aarch64-linux-gnu
```

## U-Boot
Сначала нужно скомпилировать u-boot для возможности загрузки ОС.

Качаем исходные коды ARM Trusted Firmware и U-Boot
```markdown
git clone https://github.com/ARM-software/arm-trusted-firmware
git clone https://github.com/u-boot/u-boot
```


Компилируем ARM Trusted Firmware
```markdown
cd trusted-firmware-a
trusted-firmware-a$ make CROSS_COMPILE=aarch64-linux-gnu- PLAT=rk3399 bl31
```


Компилируем U-Boot
```markdown
trusted-firmware-a$ cp build/rk3399/release/bl31/bl31.elf ../u-boot/
trusted-firmware-a$ cd ../u-boot/
u-boot$ make CROSS_COMPILE=aarch64-linux-gnu- roc-pc-rk3399_defconfig
u-boot$ make CROSS_COMPILE=aarch64-linux-gnu-
```

Если все прошло без ошибок, в каталоге u-boot появятся файлы idbloader.img и u-boot.itb
Эти файлы будут использоваться для загрузки.


## Подготовка SD

Скачиваем образ OpenBSD под ARM64: https://cdn.openbsd.org/pub/OpenBSD/snapshots/arm64/miniroot67.img

### On Linux
У меня sdcard -- это /dev/sdc. На вашем железе может отличаться, внимательно убедитесь что нашли правильное устройство, иначе вы можете повредить данные на жестких дисках.
```markdown
sudo dd if=miniroot67.img of=/dev/sdc bs=1M
u-boot$ sudo dd if=idbloader.img of=/dev/sdc seek=64
u-boot$ sudo dd if=u=boot.itb of=/dev/sdc seek=16384
u-boot$ sudo sync
u-boot$ sudo mount /dev/sdc1 /mnt
u-boot$ sudo mkdir /mnt/rockchip
u-boot$ sudo cp arch/arm/dts/rk3399-roc-pc.dtb /mnt/rockchip/
u-boot$ sudo umount /mnt
```
### On Windows
```markdown
Берем утилиту https://github.com/ndhwyd/openbsd-miniroot-patcher и патчим скачанный minirootXX.img
Записываем получившийся файл на sd карту.
Создаем в разделе BOOT на sd карте каталог rockchip и копируем туда файл rk3399-roc-pc.dtb
Вытаскиваем карту
```

### Подключение UART 
Подключаем UART к ROC-RK3399-PC как на изображении.
В данном случае используется адаптер на базе чипа CP2102.
![Image](https://github.com/ndhwyd/ndhwyd.github.io/blob/master/img/roc-rk3399-pc_uart_cp210x.jpg)

Настраиваем соединение на нужный COM порт и скорость 1500000.
Драйвера из Windows 10 не позволяли установить такую скорость, я использовал драйвера версии 6.6.1
https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers

### Готово
![Image](https://github.com/ndhwyd/ndhwyd.github.io/blob/master/img/uboot_openbsd_uart.png)
