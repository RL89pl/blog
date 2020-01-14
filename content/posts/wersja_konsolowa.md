---
title: "Car manager - wersja konsolowa cz.1"
date: 2020-01-14T16:38:49+01:00
draft: false
description: "Tworzenie wersji konsolowej aplikacji."
tags:
- Python
series:
- Car Manager
categories:
- Projekt
libraries:
- mermaid
featured_image: "ikony_tematow/programming.png"
---
### Wprowadzenie

```mermaid
gantt
section Car Manager
Skrypt + baza danych :active,    des1, 2020-01-14,2020-01-16
Django        :  des2, after des1, 2d
Desktop app   :         des3, after des2, 2d
Mobile app   :         des4, after des3, 2d

```

Czas na rozpoczęcie pracy nad pierwszą wersją aplikacji, wpierw konsolowej napisanej w Pythonie.
Baza danych w prostym SQlite.
Repozytorium projektu: [Car Manager](https://github.com/RL89pl/car-manager)
By ułatwić sobie pracę z opisywaniem kodu na blogu, kod będzie dzielony na funkcje. Na sam koniec przerobię to na klasy, by mieć później lepszy wgląd do kodu.

### Praca z bazą danych

Przy uruchamianiu programu chcemy, aby pokazał się aktualny stan pojazdu.
Czyli w pierwszej kolejności musimy odczytać z bazy danych interesujące nas tabele, a jeśli jej nie ma, to utworzyć nową bazę. 

Sprawdzamy, czy w folderze z programem jest plik bazy danych `carmng.db` i czy można go odczytać.
Jeżeli go nie ma, wywołujemy funkcję, która będzie tworzyła bazę danych:

```Python
import os
import sqlite3
from sqlite3 import Error

def main():
    database = "carmng.db"
    if not os.access(database, os.R_OK):
        #jeżeli nie ma pliku, tworzy bazę danych z dwiema tabelami
        connection = sqlite3.connect(database)
        create_table(connection)
```
Tworzymy tabelę w bazie przeznaczoną dla informacji o autach i odrębną o statystykach (tankowania, koszty, etc.):
```Python
def create_table(connection):
    sql_create_car_table = """ CREATE TABLE IF NOT EXISTS car (
                                        id integer PRIMARY KEY AUTOINCREMENT UNIQUE,
                                        nazwa text NOT NULL,
                                        marka text,
                                        rocznik text
                                    ); """
 
    sql_create_stats_table = """CREATE TABLE IF NOT EXISTS stats (
                                    id integer PRIMARY KEY AUTOINCREMENT UNIQUE,
                                    data text,
                                    godzina text,
                                    licznik_km integer,
                                    cena numeric, 
                                    ilosc_litrow integer,
                                    koszt integer,
                                    do_pelna integer,
                                    car_id integer NOT NULL,
                                    FOREIGN KEY (car_id) REFERENCES car (id)
                                );"""
    try:
        cursor = connection.cursor()
        cursor.execute(sql_create_car_table)
        cursor.execute(sql_create_stats_table)
        cursor.close()
    except Error as e:
        print(e)
    finally:
        if (connection):
            connection.close()
```
Gdy baza już jest, wywołamy funkcję do odczytu danych z bazy, dla konkretnego auta:
```Python
showAuto(database)
```
Teraz trzeba wyświetlić rekordy:
```Python
def showAuto(database):
    try:
        connection = sqlite3.connect(database)
        cursor = connection.cursor()
        select_query = """SELECT * from car"""
        cursor.execute(select_query)
        records = cursor.fetchall()
        print("Ilość rekordów:  ", len(records))
        for row in records:
            print("Id: ", row[0])
            print("Nazwa: ", row[1]) 
            print("Marka: ", row[2])
            print("Rocznik: ", row[3])
            print("\n")

        cursor.close()

    except sqlite3.Error as error:
        print("Błąd odczytu danych z bazy", error)
    finally:
        if (connection):
            connection.close()
```
Pokażemy sobie na tę chwilę wszystkie rekordy z tabeli ze statystykami dla podanego auta:
```Python
showStats(database, car_id)
```
Printujemy rekordy:
```Python
def showStats(database, car_id):
    try:
        connection = sqlite3.connect(database)
        cursor = connection.cursor()
        print(car_id)
        select_query = """SELECT * from stats WHERE car_id = {}""".format(car_id)
        cursor.execute(select_query)
        records = cursor.fetchall()
        print("Total rows are:  ", len(records))
        print("Printing each row")
        for row in records:
            print("Id: ", row[0])
            print("Data: ", row[1]) 
            print("Godzina: ", row[2])
            print("Licznik KM: ", row[3])
            print("Cena: ", row[4])
            print("Ilość litrów: ", row[5])
            print("Koszt: ", row[6])
            print("Do pełna: ", row[7])
            print("\n")
        cursor.close()

    except sqlite3.Error as error:
        print("Błąd odczytu danych z bazy", error)
    finally:
        if (connection):
            connection.close()

```
### Czyszczenie ekranu
Dobrze byłoby czyścić ekran, dlatego dodam wpis (dla windowsa czyszczenie to `cls` pod linuxem `clear`):
```Python
clear = lambda: os.system('cls')
```
Teraz za każdym razem, gdy będę chciał wyczyścić ekran, wystarczy, że wpiszę:
```Python
clear()
```
### Wybieralne menu
Skoro mamy już rozpisane wyświetlanie, czas na dodawanie wpisów.
Na początek wprowadzimy możliwość dodawania aut:
```Python
def addAutoToDatabase(database):
    nazwa = input("Podaj nazwę auta: ")
    marka = input("Podaj markę auta: ")
    rocznik = input("Podaj rocznik auta: ")
    
    try:
        sqliteConnection = sqlite3.connect(database)
        cursor = sqliteConnection.cursor()

        sqlite_insert_query = """INSERT INTO `car`
                            ('nazwa', 'marka', 'rocznik') 
                            VALUES 
                            ('{}','{}',{})""".format(nazwa, marka, rocznik)

        count = cursor.execute(sqlite_insert_query)
        sqliteConnection.commit()
        print("Dodano wpis do bazy danych ", cursor.rowcount)
        cursor.close()

    except sqlite3.Error as error:
        print("Błąd przy dodawaniu wpisu do bazy danych", error)
    finally:
        if (sqliteConnection):
            sqliteConnection.close()
```
Skoro mamy już opcję dodania aut, to czas na opcję dodania wpisów:
```Python
def addStatDatabase(database,auto = 1):
    wybor_daty = False
    wybor_do_pelna = False
    while wybor_daty == False:
        data_dzis = input("Wstawić dzisiejszą datę? T/N")
        if data_dzis == "t" or data_dzis == "T":
            data = date.today()
            wybor_daty = True
        elif data_dzis == "n" or data_dzis == "N":
            data = input("Podaj datę tankowania: ")
            wybor_daty = True
        else:
            wybor_daty = False
    godzina = input("Podaj godzinę: ")
    licznik_km = input("Podaj stan licznika kilometrów: ")
    cena = float(input("Podaj cenę: "))
    ilosc_litrow = float(input("Podaj ilość zatankowanego paliwa: "))
    while wybor_do_pelna == False:
        do_pelna = input("Czy zatankowałeś do pełna? T/N")
        if do_pelna == "t" or do_pelna == "T":
            do_pelna = 1
            wybor_do_pelna = True
        elif do_pelna == "n" or do_pelna == "N":
            do_pelna = 0
            wybor_do_pelna = True
        else:
            wybor_do_pelna = False
    koszt = cena * ilosc_litrow
    try:
        sqliteConnection = sqlite3.connect(database)
        cursor = sqliteConnection.cursor()

        sqlite_insert_query = """INSERT INTO `stats`
                            ('data', 'godzina', 'licznik_km', 'cena', 'ilosc_litrow', 'koszt', 'do_pelna', 'car_id') 
                            VALUES 
                            ('{}','{}',{},{},{},{},{},{})""".format(data, godzina, licznik_km, cena, ilosc_litrow, koszt,do_pelna, auto)

        count = cursor.execute(sqlite_insert_query)
        sqliteConnection.commit()
        print("Dodano wpis do bazy danych ", cursor.rowcount)
        cursor.close()

    except sqlite3.Error as error:
        print("Błąd przy dodawaniu wpisu do bazy danych", error)
    finally:
        if (sqliteConnection):
            sqliteConnection.close()
```
### Konfiguracja w pliku ini
Dobrym rozwiązaniem będzie dodanie domyślnych ustawień i wpływu na nie.
Do pracy z plikami konfiguracyjnymi, użyję modułu `configparser`
Na początku chcę, bym mógł ustawić jakieś auto domyślne, by od razu pokazywały dla niego statystyki.
Do funkcji dodawania auta, dam możliwość wyboru, czy wprowadzone auto ma być domyślne:
```Python
domyslne_auto = input("\nDodać jako auto domyślne?: ")
if domyslne_auto == "t" or domyslne_auto == "T":
    config = configparser.ConfigParser()
    config['DEFAULT'] = {}
    config['DEFAULT']['CAR_ID'] = str(cursor.lastrowid)
    with open('config.ini', 'w') as configfile:
        config.write(configfile)
```
Wpierw standardowo w main sprawdzę, czy jest plik `.ini` tak jak to było w przypadku bazy danych i wczytam ID dla domyślnego auta:
```Python
conf = "config.ini"
car_id = 1
if os.access(conf, os.R_OK):
    config.read(conf)
    config.sections()
    if 'DEFAULT' in config:
        car_id = int(config['DEFAULT']['CAR_ID'])
clear()
```
### Wybieralne menu
By całość lepiej się prezentowała, dodamy menu wyboru poszczególnych opcji. 
Wykorzystamy w tym celu bibliotekę [pick](https://pypi.org/project/pick/)
```Python
from pick import pick
import sys
```
Do fukcji main dopiszemu sobie kod dla menu:
```Python
wybor = input("\nWciśnij 'Q', by wyjść, lub inny dowolny klawisz by przejść dalej...")
    clear()
    if wybor == "q" or wybor == "Q":
        sys.exit()
    wyjscie = False
    while wyjscie == False:

        title = 'Wybierz rodzaj: '
        options = ['Ekran główny','Dodaj wpis','Auta','Wyjście']
        option1, index = pick(options, title)
        print(index)
        if index == 0:
            showStats(database, car_id)
            wybor = input("\nWciśnij 'Q', by wyjść, lub inny dowolny klawisz by przejść dalej...")
            clear()
            if wybor == "q" or wybor == "Q":
                wyjscie = True
        elif index == 1:
            addStatDatabase(database,car_id)
            wybor = input("\nWciśnij 'Q', by wyjść, lub inny dowolny klawisz by przejść dalej...")
            clear()
            if wybor == "q" or wybor == "Q":
                wyjscie = True
        elif index == 2:
            connection = sqlite3.connect(database)
            cursor = connection.cursor()
            select_query = """SELECT * from car"""
            cursor.execute(select_query)
            records = cursor.fetchall()
            if len(records) <= 0:
                addAutoToDatabase(database)
            else:
                showAuto(database)
                dodaj_auto = input("\nDodać auto?: ")
                if dodaj_auto == "t" or dodaj_auto == "T":
                    addAutoToDatabase(database)
            wybor = input("\nWciśnij 'Q', by wyjść, lub inny dowolny klawisz by przejść dalej...")
            clear()
            if wybor == "q" or wybor == "Q":
                wyjscie = True
        elif index == 3:
            wyjscie = True

    ## po wszystkim
    try:
        if (connection):
            connection.close()
        clear()
    except:
        clear()
```
### Koniec pierwszej części

Na tę chwilę tylko dodajemy wpisy i wyświetlamy je. Kolejnym krokiem będą obliczenia, które będą wykonywane na tych danych, a na koniec refaktoryzacja kodu.

---

