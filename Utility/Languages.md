Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
# Switch languages in crouton (Debian and Ubuntu)
## Before you begin
You will need to find your locale (see examples below). On Debian/Ubuntu, you can find a list of these in `/usr/share/i18n/SUPPORTED`.

Examples: English is `en` and the United States is `US`. The locale will be `en_US.UTF-8`. The encoding is UTF-8, which should be okay for most people.

British English: `en_GB.UTF-8`

Japanese: `ja_JP.UTF-8`

Canadian French: `fr_CA.UTF-8`

Simplified Chinese: `zh_CN.UTF-8`

Todo after changes:
**You must restart crouton for changes to take effect!**

## Debian
``` 
sudo apt-get install locales keyboard-configuration
```
After, change locales, with rights admin:

```
 dpkg-reconfigure locales
 dpkg-reconfigure console-data
 dpkg-reconfigure keyboard-configuration
```
And, restart the keyboard service:
```
 service keyboard-setup restart
```
Now, you set the graphic session X:

```
setxkbmap de # for deutsch
setxkbmap en # for english
setxkbmap fr # for french
```

Please, read officials documentations about locales, and [Debian Keyboard Configuration](https://wiki.debian.org/Keyboard), for more informations ... 

## Ubuntu

Ubuntu use meta-package...

### Install language packages

Replace {language-code} with the language code from above (`ja` for Japanese, `fr` for French, etc.)
Replace {locale} with your locale (`ja_JP.UTF-8`, `fr_CA.UTF-8`)

### Base language pack (everyone)
```
sudo apt-get install language-pack-{language-code}
```
Example: (French) `sudo apt-get install language-pack-fr`
### GNOME language pack (GNOME shell, Cinnamon, and Unity)
```
sudo apt-get install language-pack-gnome-{language-code}
```
### KDE language pack (KDE)
```
sudo apt-get install language-pack-kde-{language-code}
```
### Libreoffice
`sudo apt-get install libreoffice-l10n-{language-code}`

## Change the default language
```
sudo update-locale LANG="{locale}" LANGUAGE="{language-code}:en"
```
Example: (Japanese) `sudo update-locale LANG="ja_JP.UTF-8" LANGUAGE="ja:en"`

**You must restart crouton for changes to take effect!**

## But I want a GUI!
If you're on Ubuntu, there's a convenient GUI for you! Install `language-selector-gnome` or `language-selector-kde`, and run the "Language Support" application. From there, you can install support for any languages you want, and set preferences for language and regional preferences.