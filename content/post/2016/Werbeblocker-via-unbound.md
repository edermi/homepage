+++
date = "2016-03-01"
title = "Werbeblocker via unbound"
tags = ["Werbung", "unbound", "DNS"]
draft = false
+++

Ich betreibe seit längerem auf einem ansonsten ungenutzten Raspberry Pi meinen eigenen DNS-Resolver.
Auf dem Raspberry Pi läuft dabei Arch Linux und unbound als DNS Resolver. Natürlich könnte man 
auch einen anderen Resolver, bspw. von Google oder dem Telco nehmen, aber auf der einen Seite läuft man dann
Gefahr, dass alle DNS-Anfragen getrackt werden, Antworten manipuliert werden oder man sogar auf unerwünschte
Werbeseiten umgeleitet wird (die Telekom hat das mal gemacht und macht es schätzungsweise immer noch).

Der lokale Resolver ist also erst einmal Privatsphärenschutz. Lokales Caching erhöht zudem Zugriffszeiten
bei häufig besuchten Websites. Durch aktiviertes DNSSEC werden DNS-Antworten soweit wie möglich 
kryptographisch verifiziert. Über den Sinn und Unsinn von DNSSEC kann man gerne streiten,
aber pragmatisch gesehen ist es mit Abstand das Beste was man heute schon nutzen kann. Wie man sowas aufsetzt
findet man problemlos im Netz. Worum es heute gehen soll: Werbung mittels unbound im kompletten Heimnetz 
blocken.

## Setup

Dazu werden folgende Pakete benötigt:

- unbound (klar)
- perl (sollte standardmäßig da sein)
- perl-libwww (sollte so oder so ähnlich in den Paketquellen liegen, falls noch nicht installiert)
- git (optional)
- systemd timers / cron (/ whatever für reguläre updates, idR schon da)

Dass man perl für die Geschichte braucht bereitet mir auch ein wenig Bauchschmerzen, da könnte man sich mal 
eine Alternative schreiben. Wer da etwas hat, bitte melden!

Um nun Werbung schon an der Wurzel zu blocken, holt man sich von Github 
[unbound-block-hosts](https://github.com/jodrell/unbound-block-hosts) welches die Hosts-Datei von 
[Dan Pollock](http://someonewhocares.org/) runter lädt, in ein für unbound verständliches Format konvertiert 
und speichert. Auf Github findet sich eine Dokumentation der verfügbaren Kommandozeilenparameter, ich fand 
die defaults sinnvoll und habe daher lediglich das Skript `unbound-block-hosts` aufgerufen. Obwohl es bei 
Github anders beschrieben ist speichert das Skript die Blockierliste direkt nach 
`/etc/unbound/local-blocking-data.conf`.

Anschließend muss nur noch die unbound-Konfiguration um folgende Zeile in der `server`-Sektion erweitert 
werden:

```shell
include: /etc/unbound/local-blocking-data.conf
```

Nachdem man unbound neu gestartet hat, sollten DNS-Anfragen bezüglich Werbedomains ins Leere laufen!

## Liste aktuell halten

Ich habe mir dafür einen systemd-timer geschrieben, Leute die lieber cronjobs/runwhen/whatever verwenden
schaffen das in der Regel selbst.

Die `/etc/systemd/system/block-hosts.timer` sieht folgendermaßen aus:

```ini
[Unit]
Description=Run unbound-block-hosts weekly and on boot

[Timer]
OnBootSec=15min
OnUnitActiveSec=1w 

[Install]
WantedBy=timers.target
```

Der dazugehörige Service `/etc/systemd/system/block-hosts.service`:

```ini
[Unit]
Description=execute unbound-block-hosts

[Service]
Type=oneshot
ExecStart=/root/unbound-block-hosts/unbound-block-hosts
```

`systemctl enable block-hosts.timer` aktiviert anschließend den Timer, nach dem nächsten Neustart oder 
expliziten Start des Timers läuft dieser dann wöchentlich.


## Abschließende Gedanken
Bevor ihr euch sowas aufsetzt solltet ihr euch im Klaren sein, dass ihr einer Industrie schadet indem ihr euch
nichtmehr von ihr melken lasst. Da jedoch seitens der Werbeindustrie jahrelang die Privatsphäre und Sicherheit
der Nutzer ignoriert wurde um sie auszuschlachten fällt mir persönlich diese Entscheidung nicht schwer.
Liebe Werbeindustrie, ihr hattet eure Chance (u.a. mit Do Not Track) und habt sie nicht genutzt. Selbst 
Schuld.

Unabhängig davon wie man zur Werbeindustrie steht sollte man das Einrichten dieses Setup im Netzwerk mit den
Nutzern absprechen. Hierbei handelt es sich auch um eine Form der Zensur, wenn Nutzer Werbung sehen wollen
ist das auch ihr gutes Recht. Zudem kantet man einige "legitime" Seiten raus, die Adblock-Nutzer blockieren
weil das Geschäftsmodell auf Werbung basiert. Wenn der Besuch solcher Seiten von Nutzern erwünscht wird, 
sollte man dies respektieren.

Da ich auch schon von Abmahnungen für Werbeblocker-Tutorials gehört habe, möchte ich nochmal klarstellen,
dass es sich hierbei um keine Anleitung zum Werbung Blocken handelt. Diese Ideennotiz ist in keinster
Weise vollständig und sowas setzen nur Administratoren auf, die das auch ohne diesen Blogpost tun würden.
Sollte ich trotzdem Post bekommen hat der Absender sich hoffentlich folgende Fragen gestellt und mit "Ja" 

beantwortet:

- Habe ich Verstanden was hier gemacht wird?
- Was ist DNS, unbound, Linux, ein Raspberry Pi, Github und Perl, was davon habe ich daheim und wie gehe ich 
damit um?
- Kann ich/der Ottonormalverbraucher den beschriebenen Setup in einer halben Stunde nachbauen sodass er 
funktioniert?


**Update**
Hinweis von Danny Korpan per Mail:


> Kleiner Tipp, mit dem wird zwar perl für unbound-block-hosts nicht obsolet, jedoch kann man bei folgender 
> Blocklist direkt mit wget arbeiten:
> https://pgl.yoyo.org/adservers/serverlist.php?hostformat=unbound&showintro=0&startdate[day]=&startdate[month]=&startdate[year]=&mimetype=plaintext
> bzw.
> https://pgl.yoyo.org/adservers/

