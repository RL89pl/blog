---
title: "Automatyzacja projektow"
date: 2020-01-19T01:55:00+01:00
description: "Skrypty automatyzujące rozpoczęcie nowego projektu"
tags:
- Python
- Windows
- Produktywność
series:
- Automatyzacja
categories:
- Automatyzacja
libraries:
- mermaid
- msc
featured_image: "ikony_tematow/graph2.png"
---
### Wprowadzenie
![Automatyzacja](/images/ikony_tematow/graph2.png) 
Trzeba szanować swój czas i w miarę możliwości automatyzować wszystko, co jest uciążliwe, monotonne, często powtarzalne. 
Postanowiłem ułatwić sobie pracę przy tworzeniu nowego projektu, a także szybkie rozpoczęcie pisania nawet testowego skryptu.

Przy tworzeniu projektu musiałbym wpierw utworzyć odpowiedni folder, zalogować się na github.com w celu utworzenia repozytorium, zainicjowanie go, utworzenie pliku README i przesłanie na platformę github. Dodatkowo utworzenie pustego pliku python i uruchomienie VS Code.
Trochę to czasu zajmuje, a gdyby to wszystko zastąpić wyłącznie komendą w `cmd`, lub `powershell` w formie komendy z dowolnego miejsca w systemie?
```bash
pro.py Projekt
```
Ułatwienie niesamowite, dlatego czas zająć się tym.

Repozytorium projektu: [Auto_create](https://github.com/RL89pl/Auto_create)

### Uruchamianie skryptów

Aby uruchomić skrypt z dowolnego miejsca w systemie należy lokalizację folderu dodać do `PATH` - zmiennych środowiskowych systemu Windows.
Proponuję utworzyć folder, gdzie będą znajdować się wszystkie skrypty, które chcemy uruchomić w ten sposób.

1. W pierwszej kolejności w `Panelu sterowania` wybieramy `System`:
![System](/images/scrsht/automatyzacja_projektow/1.PNG) 
2. Następnie klikamy w `Zmienne środowiskowe`:
![Zmienne środowiskowe](/images/scrsht/automatyzacja_projektow/2.PNG) 
3. Wybieramy `Path` i edytujemy:
![Lista sieci](/images/scrsht/automatyzacja_projektow/3.PNG)
4. Dodajemy nowy wpis i podajemy pełną ścieżkę do folderu 
![Lista sieci](/images/scrsht/automatyzacja_projektow/4.PNG) 

Ważne, by ustawić uruchamianie plików `.py` bezpośrednio przez `Python` (właściwości -> uruchamiany jako `Python`)

### Skrypt do tworzenia projektów

Zgodnie z tym, co wypisałem na początku, chcemy utworzyć folder z nowym projektem, utworzyć repozytorium, przesłać go do githuba, 
oraz utworzyć pliki readme i skryptu wraz z otwarciem VS Code w folderze.

Do tworzenia miejsca dla naszego repozytorium na githubie, przyda nam się biblioteka [PyGithub](https://pygithub.readthedocs.io/en/latest/index.html)
`pip install PyGithub`
Importujemy potrzebne biblioteki i tworzymy zmienne zawierające dane do logowania na platformę github, oraz lokalizację folderu, gdzie trafiać będą wszystkie nasze utworzone projekty.
```python
import sys
import os
from github import Github

init_path = "C:\\Projekty\\" # folder z projektami
username = "XYZ" # login github
password = "12345"    # hasło github
```
Chcemy mieć możliwość szybkiego tworzenia projektów, dlatego nazwę będziemy przekazywać argumentem, przy uruchamianiu skryptu. 
Dodamy oczywiście opcję defaultową, by tworzył projekt z ustaloną na starcie nazwą, gdybyśmy nie podali argumentu.
Tworzymy folder i "przechodzimy" do jego lokalizacji:
```python
folder = "Projekt_X"
if len(sys.argv) > 1:
    folder = sys.argv[1]
os.mkdir(path) 
os.chdir(path)
```
Na swoim koncie na Githubie tworzymy nowe repozytorium
```python
user = Github(username, password).get_user()
repo = user.create_repo(folder)
```
I standardowo inicjujemy lokalne repozytorium, tworzymy plik `README.md` i przesyłamy do wcześniej utworzonego repo na Githubie:
```python
os.system("git init")
os.system("git remote add origin https://github.com/UŻYTKOWNIK/{}.git".format(folder))
with open("README.md","w") as f:
    f.write("### "+folder)
os.system("git add .")
os.system('git commit -m "pierwszy commit"')
os.system("git push -u origin master")
open("main.py","w").close()
os.system("code .")
```
Na koniec tworzymy pusty plik `Python` i uruchamiamy folder z projektem w `VS Code`:
```python
open("main.py","w").close()
os.system("code .")
```
Zapisujemy skrypt w folderze, który na początku dodaliśmy do `Path`. 
Od teraz będzie możliwe uruchamianie z dowolnego miejsca w systemie skryptu poleceniem:
`nazwa_skryptu.py NAZWA_PROJEKTU`

### Skrypt do szybkiej pracy z kodem
Bardzo często nie chcemy tworzyć dużego projektu z repozytorium na githubie, 
lecz tylko rozpocząć kodowanie z małym skryptem.
Prosty skrypcik będzie okrojoną wersją poprzedniego:

```python
import sys
import os
init_path = "C:\\Skrypty\\" # folder ze skryptami
folder = "Skrypt_X"
if len(sys.argv) > 1:
    folder = sys.argv[1]
path = init_path + folder
os.mkdir(path) 
os.chdir(path)
open("run.py","w").close()
os.system("code .")
```
---