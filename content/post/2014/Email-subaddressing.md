+++
date = "2014-09-17"
title = "Accountspezifische Email-Adressen mit E-Mail subaddressing"
tags = ["Email", "E-Mail"]
draft = false
+++

Bisher habe ich für alle Seiten, bei denen ich mich registriert habe, eine eigene E-Mail Adresse mit 
Weiterleitung auf ein Sammelpostfach angelegt. Das hat den Vorteil, dass man Emails relativ leicht sortieren 
kann, man sofort sieht wo der Spam her kommt und man sich einer solchen Adresse auch wieder relativ
schnell entledigen kann. Das Anlegen einer neuen E-Mail Adresse ist in meinem Fall auch nicht viel mehr als 
einmal per SSH einloggen, Name und Passwort für die Adresse ausdenken und Weiterleitung einrichten.

## Subaddressing

Gestern hat mich dann ein Kommilitone darauf aufmerksam gemacht, dass man bei Gmail anscheinend Unteradressen 
durch eine Modifikation der Email-Adresse anlegen kann. Hat man also das Postfach `spam@gmail.com`, so könnte 
man für die Registrierung bei einer Seite namens `foo` die Adresse `spam+foo@gmail.com` verwenden. Alle 
E-Mails an diese Adresse landen ganz normal im Posteingang des Empfängers `spam@gmail.com` sofern man keine 
weitere Filterung vornimmt. Im Header der E-Mail steht beim Empfänger nach wie vor `spam+foo@gmail.com`, 
weshalb man den Ursprung leicht nachvollziehen kann.

## Huh? Hab kein Gmail!

Kein Problem, das ganze ist nicht spezifisch für Gmail. Es gibt den 
[RFC 5233](http://tools.ietf.org/html/rfc5233) der das Ganze näher erläutert. Wer RFCs lesen nicht so toll 
findet kann sich stattdessen auch die entsprechende Stelle in der 
[Wikipedia](https://en.wikipedia.org/wiki/Email_address#Address_tags) durchlesen, wo noch auf zwei wichtige 
Dinge im Hinblick auf E-Mail Subaddressing hingewiesen wird:

* Der Trenner ist nicht zwingend ein `+`, je nach Server/Provider kann es auch ein `-` oder etwas ganz 
anderes sein. Am einfachsten ist da wohl die entsprechende Dokumentation zu lesen oder es einfach 
auszuprobieren.
* Manche Websites erlauben kein `+`-Zeichen in der E-Mail Adresse. Das Zeichen ist nach 
[RFC 5322](http://tools.ietf.org/html/rfc5322) definitiv erlaubt, in so einem Fall hilft nur eine neue 
Adresse für den Anbieter anzulegen oder den Anbieter explizit darauf hinzuweisen, dass er Standards verletzt.

