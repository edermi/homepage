+++
date = "2017-02-03"
title = "Präsentationen mit Markdown (oder was auch immer)"
tags = ["LaTeX", "markdown", "presentation", "pandoc"]
+++

Mir ist zwar das Tool [pandoc](http://pandoc.org/) als universeller Dokumentconverter schon länger
ein Begriff, aber ich bin erst vor kurzem wieder darauf aufmerksam geworden, und zwar um damit 
Präsentationen in Markdown (oder reStructuredText, Docbook, whatever) zu erstellen und diese dann 
zu LaTeX Beamer Präsentationen zu konvertieren. Es werden aber auch andere Formate unterstützt, 
zum Beispiel HTML5 Präsentationen mit reveal.js oder anderen Frameworks.

![Brent Rambo Gif](/img/brent_rambo.gif)

Folgende Markdown-Datei lässt sich mittels pandoc direkt in eine Beamer-Präsentation konvertieren:

{{< highlight md >}}
# First slide

\title{Sample presentation}
\author{Michael Eder}
\date{03.02.2017}
\maketitle

# Second slide

- Lists
- are
- also
- supported

1. Even
2. enumerated
3. ones

# Third slide

- [My awesome Blog!](https://michael-eder.net)
- And it also has images!
- ![A meme, resized](fry.png){ width=70% }


# Math

- Math can be inlined 
- $2^3 = 2000$

# Code works also

```python
for i in range(0,5):
    print("Hello World!") # prints "Hello World" 5 times
```
{{< /highlight >}}

Nach einem Aufruf von `pandoc -t beamer -s -V theme:metropolis -o presentation.tex presentation.md && pdflatex presentation.tex` 
findet man eine [fertige Präsentation](/others/presentation.pdf) vor. (Ehrlich gesagt kann man den
Befehl sicherlich noch eleganter schreiben, ich sah aber bisher keinen Bedarf das zu optimieren. Ich
bin da aber für Tips dankbar!)

**Update:** Anscheinend reicht `pandoc -t beamer -s -V theme:metropolis -o presentation.pdf presentation.md` zum direkten Übersetzen 
in ein PDF. Danke Sven für den Hinweis.

Ein paar coole Dinge will ich noch Anmerken:

- Wenn einem der Funktionsumfang an manchen Stellen nicht reicht (beispielsweise beim Einbinden von
Bildern) kann man oftmals noch zusätzliche Parameter angeben, die von pandoc in dem Kontext verstanden
und richtig umgesetzt werden (siehe Beispiel mit dem Bild, Breite auf 70% setzen).
- Man kann LaTeX inlinen, zum Beispiel für Formeln oder um Makros zu verwenden.
- Man kann auch eigene LaTeX-Dateien als Header einbinden, zum Beispiel um eigene Makros zu definieren
- Es spart furchtbar viel Zeit, da man sich noch mehr auf den Content als auf die Form konzentrieren
kann - eine der ursprünglichen Ideen von LaTeX.

Ein lauffähiges Beispiel habe ich [hier](/others/sample_presentation.zip) mal zusammen gezippt.

Wer lieber HTML5-Präsentationen macht kann sich [hier](https://github.com/jgm/pandoc/wiki/Using-pandoc-to-produce-reveal.js-slides) 
an einem Beispiel anschauen wie man das umsetzt.

