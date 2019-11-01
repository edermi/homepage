+++
date = "2015-05-25"
title = "Windows 10 Installationsimage unter Linux auf USB Stick installieren"
tags = ["Windows", "USB"]
+++

Ich wollte mir mal Windows 10 außerhalb einer virtuellen Umgebung anschauen - mein Laptop hat leider kein 
DVD-Laufwerk, weswegen ich die Installation vom USB-Stick durchführen muss. Prinzipiell ist das meiner 
Meinung nach sowieso immer der way-to-go, da man den Stick wiederverwenden kann und alles sowieso schneller 
geht als von der DVD. Bei iso-Dateien von Linux-Distributionen hat bisher immer ein `dd` gereicht um die 
Images bootbar auf den USB-Stick zu verfrachten. Wie sich heraus stellt ist das bei Windows etwas mehr Arbeit, 
weswegen ich diesen Post schreibe falls andere ähnliche Probleme haben.

__Hinweis:__ Für diese Anleitung wurde die `Windows10_InsiderPreview_x64_DE-DE_10074.iso` verwendet. Falls es 
mit anderen (neueren) Images Probleme gibt würde ich mich über einen Hinweis freuen, damit ich Stolpersteine 
dokumentieren und die Anleitung aktualisieren kann.

## USB-Stick vorbereiten

Das habe ich mit Gparted gemacht. Den Stick einfach mit NTFS formatieren und als bootable markieren.

## MBR auf USB-Stick schreiben

Ja, MBR. Mein Laptop unterstützt AFAIK auch EFI, aber das brauche und nutze ich nicht. Wer das alles hier mit 
EFI macht kann sich aber gerne melden, dann füge ich das der Vollständigkeit halber natürlich hier hinzu.

Der MBR wird mit einem Tool names `ms-sys` geschrieben. Bei Arch Linux gibt es das 
[Paket im AUR](https://aur.archlinux.org/packages/ms-sys/), bei anderen Distributionen müsst ihr selbst 
schauen wie ihr an ein Paket kommt oder euch das Tool selbst bauen. Nachdem das Tool vorhanden ist, schreibt

```bash
sudo ms-sys -7 /dev/sdX
```

den MBR auf das Gerät `/dev/sdX`, wobei das sdx vermutlich an die eigenen Umstände angepasst werden muss.

## Daten auf USB-Stick kopieren

Dafür muss das Windows ISO gemountet werden. Ich habe mir unter `/mnt/` einen Ordner angelegt, wo das Image 
eingehängt wird:

```bash
sudo mkdir /mnt/Windows_ISO
```

Das Einhängen erfolgt anschließend mit:

```bash
sudo mount -o loop /Pfad/zum/Image/Windows10_InsiderPreview_x64_DE-DE_10074.iso /mnt/Windows_ISO
```

Falls folgender Hinweis auftaucht:

```bash
mount: /dev/loop0 ist schreibgeschützt, wird eingehängt im Nur-Lese-Modus
```

Einfach ignorieren, wir wollen eh nur von da lesen.

Mit

```bash
cp -R /mnt/Windows_ISO/* /run/media/michael/Stick 
```

wird anschließend alles auf den USB-Stick kopiert. `/run/media/michael/Stick` muss dabei durch den Pfad 
ersetzt werden, an dem der USB-Stick gemountet ist. Das Kopieren kann nun ein wenig dauern, nachdem es 
abgeschlossen ist können alle Verzeichnisse wieder ausgehängt werden und Windows sollte nun vom USB-Stick 
booten!


**Nachtrag:**

Benjamin Tegge hat mir eine Mail bezüglich des Vorgehens für EFI-Systeme geschrieben, aus der ich hier 
zitieren möchte:


> Das ist Prinzipiell ganz ähnlich und die cp + ms-sys Methode gefällt mir deutlich besser als dd. 
> Allerdings muss das Dateisystem dafür wie im UEFI Standard vorgeschrieben FAT sein (oder ggf. andere Formate 
> die die Firmware lesen kann). Im EFI Ordner liegen mehrere EFI-Binaries die teilweise aufeinander verweisen 
> und letztendlich den eigentlichen EFI Bootloader für die Installation starten. Das Setzen des Bootflag bei 
> einer einzelnen Partition ist mit UEFI nicht unbedingt nötig.


**Update:**
Ein Kommilitone hat wie oben beschrieben 
`de_windows_10_education_version_1511_updated_feb_2016_x64_dvd_8380172.iso` mit md5 Prüfsumme 
`1b252f096cdc73deffd733907d79e377` vom USB-Stick booten können
