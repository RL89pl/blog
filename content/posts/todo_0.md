---
title: "ToDo APP - opis projektu"
date: 2022-02-06T00:45:00+02:00
description: "Opis projektu aplikacji typu TODO"
series:
- ToDo
categories:
- Projekt
featured_image: "ikony_tematow/programming.png"
---
### Dostępne aplikacje

Używałem wielu aplikacji do planowania zadań i jeszcze więcej testowałem.
Przykładowo w każdej można utworzyć listę zadań, np.:

1. Zakup słuchawek
2. Trening
3. Sprzątanie piwnicy

W wielu z nich można także ustalić ile dane zadanie zajmie nam czasu i na podstawie tego planować dzień.

### Problem i rozwiązanie

Nie przypominam sobie jednak, by jakakolwiek z nich miała opcję predefiniowanych podzadań.
Oczywiście wiele z nich miało możliwość dodawania podzadań, ale trzeba je uzupełniać za każdym razem, gdy dodaje się główne zadanie.
Problemem jest to, że zadanie np. `Trening` jest niekompletne.
Dobrze byłoby mieć już wszystko skonfigurowane tak, że gdy doda się takie zadanie do listy, to dodawałyby się automatycznie podzadania, np.:

1. Spakować strój sportowy
2. Zabrać ze sobą wodę
3. Spakować ręcznik kąpielowy
4. Spakować ręczniik na sprzęt

Przy wyznaczaniu czasu w zadaniach też warto uwzględnić np. przejazdy do i z siłowni, oraz przebranie się + prysznic.
Nie zawsze o tym się pamięta, gdy ma się tylko zapis "trening" i zapewne każdy wpisuje przybliżony czas samego treningu :)

Gdyby dodać predefiniowane zadania, można byłoby ustawić sobie taski pod konkretny rodzaj treningu wraz z ćwiczeniami i nie trzeba za każdym razem tego dodawać.
Strasznie uciążliwe byłoby dodawać na nowo ćwiczenia w treningu typu `Split`, gdzie mamy podział na `PUSH`, `PULL` i `LEGS`.


### Projekt

**Ogólne założenia:** 
- dobrze, gdyby aplikacja była multiplatformowa, ponieważ samo planowanie wolę wykonywać na desktopie, ale już przy oznaczaniu wykonanych zadań nie chcę się ograniczać i wolę do tego apkę mobilną
- jeżeli aplikacja ma być multiplatformowa, to musi posiadać też REST API, tym bardziej, że wiele predefiniowanych zadań będę chciał dodać automatem (np. rodzaje treningów)

**Stack technologiczny:**

- **frontend** - Vue/Angular/React
- **backend** - Django 
- **mobile:** Flutter

Na pewno będę chciał przetestować nowe możliwości Fluttera jeśli chodzi o desktopowe aplikacje, ale to taki raczej bonus.