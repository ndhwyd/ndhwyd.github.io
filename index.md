## Установка OpenBSD на ROC-RK3399-PC (Renegate Elite)

Небольшой гайд как установить OpenBSD 6.7 на ROC-RK3399-PC (Renegate Elite) с использованием U-Boot Mainline.

### Потребуется

```markdown

# Hardware
1. ROC-RK3399-PC
2. USB-to-UART Adapter
3. SD Card

# Software (Windows or Linux)
## Windows 
1. Windows 10 + Windows subsystem for Linux
2. balenaEtcher
3. WinHex

## Linux
1. dd
```

### Установка компиляторов
```markdown
sudo apt-get install gcc-aarch64-linux-gnu
```

### U-Boot
Сначала нужно скомпилировать u-boot для возможности загрузки ОС.

Качаем исходные коды ARM Trusted Firmware и U-Boot
```markdown
git clone https://github.com/ARM-software/arm-trusted-firmware
git clone https://github.com/u-boot/u-boot
```

Компилируем ARM Trusted Firmware
```markdown
cd trusted-firmware-a
**trusted-firmware-a$** make CROSS_COMPILE=aarch64-linux-gnu- PLAT=rk3399 bl31
```

Компилируем U-Boot
```markdown
**trusted-firmware-a$** cp build/rk3399/release/bl31/bl31.elf ../u-boot/
**trusted-firmware-a$** cd ../u-boot/
**u-boot$** make CROSS_COMPILE=aarch64-linux-gnu- roc-pc-rk3399_defconfig
**u-boot$** make CROSS_COMPILE=aarch64-linux-gnu-
```
Если все прошло без ошибок, в каталоге u-boot появятся файлы idbloader.img и u-boot.itb
Эти файлы будут использоваться для загрузки.


You can use the [editor on GitHub](https://github.com/ndhwyd/ndhwyd.github.io/edit/master/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ndhwyd/ndhwyd.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
