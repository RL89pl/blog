---
title: "Markdown do Worda"
date: 2020-01-04T01:28:26+01:00
description: "Generowanie dokumentu docx (Word) z pliku md (Markdown)"
tags:
- Linux
- Markdown
series:
-
categories:
-
featured_image: "ikony_tematow/mathbook.png"
---

By móc generować dokumenty Worda z MD, należy pobrać pandoc (LINUX).
W terminalu wpisujemy poniższe polecenie:
``` Bash
pandoc -o output.docx -f markdown -t docx filename.md
```