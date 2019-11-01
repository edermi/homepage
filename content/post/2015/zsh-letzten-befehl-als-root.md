+++
date = "2015-09-29"
title = "zsh: letzten Befehl als root ausführen"
tags = ["zsh"]
+++

Klassiker im Alltag eines Terminal-Nutzers: Das `sudo` vergessen. Viele gehen dann in der History zurück,
scrollen von Hand an den anfang und tippen halt `sudo` noch davor hin. Etwas bequemer ist da schon `sudo !!`, 
wobei mir das auch noch zu viel Schreibarbeit ist. Inspiriert von einem 
[askubuntu-Thread](http://askubuntu.com/questions/165327/rerun-previous-command-under-sudo) habe ich mir den 
`pls`-Befehl definiert. Dazu einfach folgendes ans Ende der `~/.zshrc` schreiben:

```bash
alias pls='sudo $(fc -ln -1)'
```

Anschließend führt `pls` den letzten Befehl nochmal mit einem `sudo` vorne dran aus: 

```bash
[michael@arch-desktop ~]$ pacman -Syu
Fehler: Sie benötigen Root-Rechte, um diese Operation auszuführen.
[michael@arch-desktop ~]$ pls
[sudo] Passwort für michael: 
:: Synchronisiere Paketdatenbanken...
 core ist aktuell
 extra ist aktuell
 community ist aktuell
 archlinuxfr ist aktuell
:: Starte vollständige Systemaktualisierung...
 Es gibt nichts zu tun
[michael@arch-desktop ~]$
```


