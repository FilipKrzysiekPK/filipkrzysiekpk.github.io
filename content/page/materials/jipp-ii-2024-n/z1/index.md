---
title: "Języki i Paradygmaty Programowania II"
layout: singleNoHeader
date: 2023-10-20
toc: "enabled"
TOCEnabled: "enabled"
---


# Laboratorium 1

### Cele laboratorium i poruszane zagadnienia

* Podstawy teoretyczne - źródła wiedzy
* Zmiany pomiędzy c i c++
* Struktura projektu (układ katalogów)
* Strumienie wejścia wyjścia
* Alokacja pamięci
* Przypomnienie wiadomości
* CMake
* Obiektowość, klasy
* Konstruktory
* Gettery oraz settery
* Destruktory
* RAII

{{< space 9 >}}

### Skąd czerpać wiedzę?

Pierwszym najbardziej wiarygodnym źródłem są książki, gdzie warto wyróżnić:
- *Teoria i praktyka z wykorzystaniem C++. Wydanie II poprawione* [Stroustrup, 2013]
- *C++ Podróż po języku dla zaawansowanych Wydanie II* [Stroustrup, 2018] - Autor sugeruje, aby zapoznać się z poprzednią pozycją, ale i tak temat jest omawiany od podstaw. Na pewno warto wiedzieć o tej książce w formie źródła, gdzie poszukać informacji o konkretnych rzeczach.

Źródła internetowe i dokumentacje:
- [Serwis programistyczny c++](https://cpp0x.pl/)
- [Dokumentacja c++ (najbardziej oficjalna)](https://en.cppreference.com/w/)
- [Dokumentacja c++ Microsoft](https://learn.microsoft.com/en-us/cpp/?view=msvc-170)
- [ISO c++](https://isocpp.org/)

{{< space 5 >}}

# Czy c i c++ to to samo?

Ogólnie rzecz ujmując to tak, to jest to samo. C++ został stworzony w kompatybilności z C. Mówiąc pewnym uogólnieniem C++ to rozszerzony język C. Posiada on wiele udogodnień i bibliotek, które ułatwiają nam życie i sprawiają, że tworzenie aplikacji jest łatwiejsze oraz nie musimy zawsze wszystkiego tworzyć od 0.

## Zagadnienia do powtórzenia oraz opracowania we własnym zakresie

Jako, że niestety nie mamy wystarczającej ilości czasu, którą możemy poświęcić na dokładne omówienie wszystkich zagadnień oraz usystematyzowanie wiedzy, poniżej przedstawiam listę elementów, z którymi trzeba się zapoznać (pomijając te najbardziej podstawowe):

* [inicjalizacja](https://en.cppreference.com/w/cpp/language/initialization) (dobrze opisane w książce *C++ podróż po języku dla zaawansowanych* strona 18)
* zakres i cykl życia
* [stałe, stałe wskaźniki i referencja](https://en.cppreference.com/w/cpp/language/cv)
* [range based for](https://en.cppreference.com/w/cpp/language/range-for)
* [wyrażenia stałe](https://en.cppreference.com/w/cpp/language/constexpr) (ponadprogramowe)
* typy wyliczeniowe
* unie (dobrze opisane w książce *C++ podróż po języku dla zaawansowanych* strona 35)
* parametry domyślne
* przeciążenia funkcji

## Jakie udogodnienia posiada c++?

* [ciągi znakowe - stringi](https://en.cppreference.com/w/cpp/string/basic_string)
* [inteligentne tablice](https://en.cppreference.com/w/cpp/container/vector)
* [inteligentne wskaźniki](https://en.cppreference.com/book/intro/smart_pointers)
* [biblioteka chrono - rzeczy związane z czasem](https://en.cppreference.com/w/cpp/chrono)
* [prosta wielowątkowość](https://en.cppreference.com/w/cpp/thread/thread)
* [filesystem operacje na systemie plikowym](https://en.cppreference.com/w/cpp/filesystem)

{{< space 5 >}}

**Czy jest różnica pomiędzy `#include "iostream"`, a `#include <iostream>`?**
{{< br >}}
Jeżeli użyjemy cudzysłowów, będziemy wyszukiwać plików do załączenia w bieżącym katalogu, bądź względem pliku, w którym załączamy. Jednakże w przypadku użycia znaków mniejszości i większości, będziemy wyszukiwać w standardowym miejscu bibliotek.

**Dlaczego i po co używa się `return 0` na końcu funkcji `main`?**
{{< br >}}
Jeżeli uruchomimy program, to po zakończeniu działania zwracany jest status. 0 oznacza sukces, każdy inny, program nie zakończył się powodzeniem.

{{< space 5 >}}

## Struktura projektu

C++ nie posiada jednej, jedynej poprawnej struktury projektu, przeszukując różne źródła, znajdziemy różne jej wersje. Jeden sposób mówi o separowaniu plików nagłówkowych od implementacyjnych, a kolejny o trzymaniu ich razem. Każda z tych koncepcji ma swoje wady i zalety, jednakże istnieje też propozycja pośrednia.

```console
.
├── build       Wyniki kompilacji i pliki potrzebne do tego
├── doc         Dokumentacja
├── include     Publiczne pliki nagłówkowe
├── lib         Biblioteki
├── src         Pliki źródłowe i nagłówkowe
└── tests       Testy
```

Powyższa struktura trzyma pliki nagłówkowe i źródłowe razem, jednakże oddziela te publiczne.

{{< space 9 >}}

## Komendy preprocesora w pliku nagłówkowym

Jeżeli prześledzimy sobie, w jaki sposób działa kompilator, to zauważymy, że łączy on wszystkie pliki w jeden. Często się zdarza, że jeden nagłówek dołączamy w kilku miejscach i aby uniknąć powielania go po złączeniu stosuje się odpowiednie polecenia preprocesora.

```cpp
#pragma once
```

lub

```cpp
#ifndef MOJA_KLASA_H
#define MOJA_KLASA_H

// Kod

#endif //MOJA_KLASA_H
```

## Strumienie wejścia i wyjścia

W c++ w znaczący sposób zostało ułatwione wypisywanie do konsoli oraz wczytywanie z niej. Operacje tego typu bazują na jednej klasie, dlatego, przy korzystaniu z plików będziemy używać praktycznie takiej samej składni. Dokładniej wyjaśni się to w dalszej części kursu. 

Wypisywanie na ekran dokonujemy za pomocą `cout`, strumienia wyjściowego (nazwa sama nam na to wskazuje). Dodatkowo musimy użyć też operatora `<<`, można to zinterpretować, jak strzałkę, czyli to, co podamy po prawej, jest przekazywane do strumienia.

```cpp
    int num = 5;
    cout << "Hello world " << num << endl;
```

Oczywiście, aby można było używać `cout` i `endl` (znak nowej linii) w ten sposób musimy wcześniej napisać `using namespace std;`. Informuje to kompilator, aby dodatkowo przeszukiwać przestrzeń `std`. Jeżeli nie zadeklarujemy `using`, to musielibysmy powyższy przykład zapisać w taki sposób:

```cpp
    int num = 5;
    stds::cout << "Hello world " << num << std::endl;
```

{{< space 5 >}}

Strumień wejściowy działa analogicznie co do strumienia wyjściowego, tyle, że operator jest odwrócony.

```cpp
    int num;
    cin >> num;
```

### Zadanie

Stwórz program, który pyta o wiek, a następnie wypisuje go na ekran z wykorzystaniem strumieni wejścia/wyjścia c++.

{{< space 4 >}}

## Alokacja i dealokacja pamięci

Jeżeli powrócimy pamięcią do języka c alokacja odbywała się za pomocą komendy `malloc`, gdzie podawany argument był dość skomplikowany. Oczywiście zostało to ułatwione:

```cpp
    int *tab = new int[100];
    //do sth
    delete [] tab;
```

Alokacja jest dokonywana za pomocą słowa kluczowego new, gdzie później podajemy typ zmiennej i jeżeli ma być to tablica, to jej wielkość. Przy zwalnianiu pamięci należy zwrócić uwagę czy chcemy usunąć zaalokowaną pamięć dla pojedynczej zmiennej, czy całej tablicy.

* `delete` - zwalnianie pamięci dla pojedynczej zmiennej
* `delete []` - zwalnianie pamięci dla tablicy

### Zadanie

Stwórz program wczytujący wielkość macierzy, następnie stwórz funkcję generującą macierz o podanym rozmiarze, wypełniającą ją [losowymi wartościami](https://en.cppreference.com/w/cpp/numeric/random/rand), a następnie sumując wszystkie wartości. Macierz ma być dynamicznie alokowana.

Wywołaj funkcję (bez ponownego wczytywania rozmiaru macierzy) 1000 razy. Sprawdź, czy nie ma wycieku pamięci.

{{< space 4 >}}

## Udogodnienia c++ - stringi

Bardzo dużym udogodnieniem w c++ jest `string`, który służy do przechowywania tekstu. Ułatwia on pracę z nim w znaczącym stopniu. Nie musimy się martwić, czy tekst zmieści się w zarezerwowanym miejscu, przechowywać rozmiaru tekstu, albo obliczać go. Możemy też zdecydowanie łatwiej je łączyć, a dodatkowo znajdziemy funkcje, które pomogą nam nim wykonywać różne operacje.

[Dokumentacja `string`](https://en.cppreference.com/w/cpp/string)

[Dokumentacja `string_view` (c++17)](https://en.cppreference.com/w/cpp/string/basic_string_view)

Przykładowe użycie, po więcej szczegółów sięgnij do dokumentacji:

```cpp
string pass = "Password";
string input;

do {
    cout << "Podaj hasło" << endl;
    cin >> input;
} while (input != pass);

cout << "Hello inside" << endl;
cout << "Password length: " << pass.size() << endl;
```

Powyższy przykład przedstawia kilka podstawowych możliwości `string` ów. Oczywiście można dostawać się do poszczególnych znaków za pomocą operatora `[]`, czy też za pomocą `+` łączyć dwa ciągi. Powyższy przykład pokazuje bardzo podstawowe możliwości, nie żaden sposób nie wyczerpuje tematu.

{{< space 3 >}}

#### Zadanie

Wczytaj z klawiatury tekst, następnie zlicz w nim litery `a` oraz wypisz na ekran ich liczbę. Zamień wszystkie litery `a` na znak `_`.

{{< space 9 >}}

## CMake

Cytując wikipedię:

> CMake – wieloplatformowe narzędzie do automatycznego zarządzania procesem kompilacji programu. Jego główna cecha to niezależność od używanego kompilatora oraz platformy sprzętowej. CMake nie kompiluje programu samodzielnie, lecz tworzy pliki z regułami kompilacji dla konkretnego środowiska; przykładowo w systemie GNU/Linux będą to pliki Makefile, natomiast pod Windowsem — pliki projektu dla Microsoft Visual Studio.

Jak można wyczytać w powyższej definicji, CMake służy do automatycznego zarządzania procesem kompilacji. Zamieniając 
na polskie, na podstawie konfiguracji wywołuje odpowiednie komendy, których efektem jest kompilacja. Dołącza on 
biblioteki, a nawet potrafi pobierać je z githuba.

### Podstawowe komendy

Deklaracja minimalnej wersji CMake, która będzie używana:

```cmake
cmake_minimum_required(VERSION 3.16)
```

Ustawianie używanej wersji języka c++:

```cmake
set(CMAKE_CXX_STANDARD 17)
```

Deklaracja nazwy projektu:

```cmake
project(ProjectName)
```

Deklarowanie listy plików, które mają byc skompilowane, jako program:

```cmake
add_executable(ProjectName main.cpp)
```

Deklarowanie listy plików, które mają byc skompilowane, jako biblioteka:

```cmake
add_library(ProjectName STATIC main.cpp)
```

Deklarowanie używanych bibliotek przez nasz program:

```cmake
target_link_libraries(ProjectName pthread)
```

Deklarowanie folderu, który ma być przeszukiwany pod kątem plikow nagłówkowych (mówienie, gdzie znajdują się pliki 
nagłówkowe):

```cmake
include_directories(include)
```

Dodawanie podfolderów z kolejnymi częściami aplikacji (gdzie znajduje się kolejny plik `CMakeLists.txt`)

```cmake
add_subdirectory(sth)
```

#### Stałe

`${CMAKE_CURRENT_LIST_DIR}` - zwraca absolutną ścieżkę do folderu aktualnego pliku CMakeLists.txt

`${CMAKE_SOURCE_DIR}` - zwraca absolutną ścieżkę do najwyższego folderu, w którym znajduje się plik CMakeLists.txt.



### Przykładowy plik

```cmake
cmake_minimum_required(VERSION 3.16)
project(HelloWorld)

set(CMAKE_CXX_STANDARD 20)

add_executable(HelloWorld main.cpp)

target_link_libraries(HelloWorld pthread)
```

Analizując powyższy plik, można zauważyć, że będzie kompilowany w standardzie c++20 plik `main.cpp` oraz dołączana 
będzie biblioteka pthread.

```cmake
cmake_minimum_required(VERSION 3.16)
project(HelloWorld)

set(CMAKE_CXX_STANDARD 20)

include_directories(include)

add_executable(HelloWorld 
        src/main.cpp 
        src/HelloWorld.cpp)

target_link_libraries(HelloWorld pthread)
```

### Komendy cmake

Używając CLion lub innego narzędzia z interfejsem graficznym można wszystko wyklikać, lecz jeżeli chcielibyśmy 
zrobić to z poziomu konsoli, to musielibyśmy użyć następujących komend.

> Załóżmy, że znajdujemy się w głównym folderze plików i struktura plików jest następująca:
> ```
> .
> ├── CMakeLists.txt
> ├── include
> │   └── HelloWorld.h
> └── src
>     ├── HelloWorld.cpp
>     └── main.cpp
> ```

Najpierw tworzymy folder na wyniki pracy CMake i skompilowane pliki oraz wchodzimy do niego:

```console
mkdir build
cd build
```

Wywołujemy komendę `cmake` ze wskazaniem, gdzie znajduje sięgłówny plik `CMakeLists.txt`, aby CMake utworzył konfiguracje projektu:

```console
cmake ..
```

Uruchamianie kompilacji projektu `HelloWorld` (linux):

```console
make HelloWorld
```

Window:

```console
cmake --build . --target HelloWorld
```

`--build` powinno wskazywać folder, w którym wywołaliśmy komendę `cmake ..`, w naszym przypadku użyliśmy `.`, ponieważ znajdujemy się w tym samym folderze


### Dokumentacja CMake

[Dokumentacja CMake](https://cmake.org/cmake/help/latest/index.html)

[Tutorial CMake](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)


{{< space 9 >}}

## Obiektowość

Zanim zaczniemy próbować zrozumieć obiektowość wykonajmy ćwiczenie.

1. Zapisz informacje o osobie: imię, nazwisko, wiek, wzrost, waga, ulubiony kolor, ilość pieniędzy w portfelu, ilość punktów (domyślnie 0).
2. Stwórz funkcję, która wczytuje te informacje z ekranu.
3. Stwórz funkcję wypisującą wszystkie informacje o osobie na ekran.
4. Stwórz funkcję obliczającą ilość punktów na podstawie znanych danych (wymyśl jak, dowolnie).
5. Stwórz funkcję tworzącą kod osoby na podstawie imienia, nazwiska i wieku (inicjały + wiek).
3. Stwórz 3 osoby.
7. Wywołaj dla nich te funkcje.

Jakie masz pomysły, na wykonanie tego ćwiczenia?

{{< space 3 >}}

Jakie masz przemyślenia po tym ćwiczeniu? Zauważyłeś, że większości funkcji przekazywałeś dokładnie te same parametry?

W zależności od pomysłu albo przekazywałeś całą strukturę, albo każdy osobno.

{{< space 3 >}}

### Odrobina teorii

Czas na odrobinę teorii, a raczej przypomnienia sobie struktury.

> Struktura jest to deklaracja nowego typu, który może przechowywać w sobie wiele różnych typów, czy też funkcji. Cała zawartość funkcji jest publiczna.

Jeżeli już to sobie przypomnieliśmy, to możemy przejść dalej i dostrzec główną różnicę pomiędzy klasami, a strukturami.

C++ posiada wszystko, co niezbędne do praktycznej realizacji idei programowanie zorientowanego obiektowo. Pisanie programu zgodnie z filozofią OOP(Object-Oriented Programming) polega na definiowaniu i implementowaniu odpowiednich klas oraz tworzeniu z nich obiektów i manipulowaniu nimi. Klasa jest więc dla nas pojęciem kluczowym, które na początek wypadałoby wyjaśnić. 

> Klasa jest to złożony typ zmiennych, składający się z pól, przechowujących dane, oraz posiadający metody, wykonujące zaprogramowane czynności. Zmienne należące do tych typów obiektowych nazywamy obiektami. 

Każda klasa posiada pola (zmienne) oraz metody (funkcje).

Określenie klasy jest najczęściej rozdzielone na dwie części:

- definicję, wstawianą w pliku nagłówkowym, w której określamy pola klasy oraz wpisujemy prototypy jej metod (taki przepis na klasę)
- implementację, umieszczaną w module, będącą po prostu kodem wcześniej zdefiniowanych metod

Układ ten nie dość, że działa nadzwyczaj dobrze, to jeszcze realizuje jeden z postulatów programowania obiektowego, jakim jest ukrywanie niepotrzebnych szczegółów.


Przyjrzymy się składni definicji klasy:

```c++
class nazwa_klasy
{
[ specyfikator_dostępu : ]
[ pola ]
[ metody ]
};
```

{{< space 3 >}}

**Modyfikatory dostępu**

Pierwszym elementem, który widzimy w naszym przykładzie jest `specyfikator_dostępu`. Służą one do ukrywania elementów (pól i metod), które nie muszą być udostępniane publicznie. Możemy wyróżnić:
* `private` - elementy są prywatne, dostępne tylko i wyłącznie z klasy, w której zostały zdefiniowane
* `protected` - dostęp do tych elementów mają dodatkowo klasy, które dziedziczą po naszej klasie
* `public` - dostęp do elementów mają wszyscy

Danym specyfikatorem objęte są wszystkie następujące po nim części klasy, aż do jej końca lub kolejnego specyfikatora. Ich ilość nie jest bowiem niczym ograniczona. Domyślnym specyfikatorem dostępu jest `private`, czyli, jeżeli po zadeklarowaniu klasy nie utworzymy żadnego specyfikatora, to elementy będą prywatne dopóki nie zostanie to zmienione innym specyfikatorem dostepu.

PRZYKŁAD:

```cpp
#include <string>

class PrzykladowaKlasa
{
public:
    double liczba; //prawo dostępu: publiczne
    char tablica[20]; //prawo dostępu: publiczne
    
private:
    int abc; //prawo dostępu: prywatne
    char znak; //prawo dostępu: prywatne
    std::string napis; //prawo dostępu: prywatne
};

int main()
{
    PrzykladowaKlasa nazwaZmiennej;
    return 0;
}
```

Przykład 2:

```cpp
class MyClass {
    string name;
    string lastName;
    int age;

public:
    void readData();
    void printData();
};

//plik cpp
void MyClass::readData() {
}

int main() {
    MyClass person1;
    person1.readData();
    return 0;
}
```

{{< space 3 >}}

### Różnica pomiędzy klasą a strukturą

Mam nadzieję, że udało Ci się już dostrzec główną różnicę pomiędzy klasą, a strukturą. W strukturze wszystko jest publiczne.

{{< space 8 >}}

## Konstruktor

Każda klasa zawiera konstruktor, nawet jeżeli go nie zadeklarujemy, to ona posiada takowy. Kompilator dodaje kilka domyślnych konstruktorów. Zanim o tym, to odpowiedzmy sobie na pytanie, co to jest konstruktor. Konstruktor jest to metoda wywoływana podczas tworzenia obiektu.

```cpp

class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

Powyżej można dostrzec prosty przykład klasy z zadeklarowanym konstruktorem oraz 2 wywołania w klasie `main`. Czy obydwa wywołania są poprawne? Spróbuj skompilować. Wyskoczył błąd kompilacji. Dodaj nawiasy do `teacher1` i już kompilacja zakończona powodzeniem. Stało się tak, ponieważ nie wywołaliśmy konstruktora, a operator. Niestety przy inicjalizacji należy bardzo uważać. Jeżeli nie przekazujemy argumentów, to nie używamy nawiasów, albo używamy nawiasów tego typu `{}`. Jest jeszcze jedna opcja możemy użyć przypisania.

```cpp
    Teacher teacher1 = Teacher1();
```

W takich przypadkach mamy pewność, że wywołaliśmy konstruktor bezparametrowy.

{{< space 3 >}}

Czy musimy w konstruktorze inicjalizować wszystkie pola naszej klasy? Poniekąd, każda zmienna, pole powinno zostać zainicjalizowane, więc jeżeli nie nadajemy wartości w konstruktorze, to powinniśmy ją nadać podczas deklaracji. Przez konstruktory zazwyczaj przekazujemy kilka wartości, którymi chcemy, aby klasa została zainicjalizowana, więc w liście argumentów nie muszą znajdować się wszystkie pola.

Konstruktory domyślne są to konstruktory, które są dodawane przez kompilator i mają zdefiniowaną z góry strukturę. Dzięki nim nie musimy za każdym razem tworzyć konstruktorów w klasie. Przykładowo domyślnym konstruktorem jest bez parametrów. Co, jeżeli my takiego nie chcemy? Teoretycznie wystarczy stworzyć inny konstruktor i ten przestanie istnieć, lecz aby mieć pewność możemy go usunąć.

```cpp
class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
    Teacher() = delete;
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

{{< space 4 >}}

#### Słowo kluczowe `this`

W powyższych przykładach możemy zauważyć, że jest używane słowo kluczowe `this`. Służy ono do wskazania, o którą konkretnie zmienną nam chodzi. Jeżeli użyjemy zmienną bez słowa kluczowego `this`, to dostaniemy się do zmiennej zadeklarowanej w parametrze funkcji. Natomiast dodając słowo kluczowe `this` informujemy, że chcemy dostać zmienną będącą polem klasy. Oczywiście możemy nazywać zmienne i pola w ten sposób, aby nie występowała kolizja nazw i w takim przypadku nie musimy się martwić, którą zmienną lub pole modyfikujemy.

{{< space 3 >}}

## Konstruktor - lista inicjalizacyjna

Czy w klasie można zadeklarować, jako pole stałą, albo referencję? Tak można, tylko jest jeden warunek, musimy ją zainicjalizować w konstruktorze. Jednakże nie możemy tego zrobić w ciele konstruktora, wtedy jest już zbyt późno, pola klasy zostały zainicjalizowane. Musimy użyć listy inicjalizacyjnej.

```cpp
Teacher(const string &name, const string &strengths): name(name), strengths(strngths) {

}
```

Działa to na zasadzie, że wartości z listy inicjalizacyjnej są przypisywane do pól, a dopiero później następuje stworzenie obiektu i wywołanie ciała konstruktora. (Lista inicjalizacyjna to jest ta część po :, a przed {.)

Czy są jeszcze jakieś plusy stosowania listy inicjalizacyjnej? Można dostrzec, że taki konstruktor działa szybciej.

{{< space 7 >}}

## Gettery i settery

Getery i settery są to metody służące odpowiednio do pobierania wartości pól, jak i ich ustawiania. Stosujemy je, aby móc modyfikować wartości, bez posiadania bezpośredniego dostępu. Szczególnie są one potrzebne, jeżeli mamy pola powiązane ze sobą, gdzie modyfikacja jednego z nich wymaga modyfikacji drugiego. W przypadku modyfikowania przez bezpośredni dostęp nie bylibyśmy w stanie się zabezpieczyć i zapewnić, że użytkownik, modyfikując będzie wiedział, o zmodyfikowaniu drugiego pola. Setter w takim przypadku zapewnia nam spójność i wymusza aktualizację wszystkich pól.

```cpp
class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
    Teacher() = delete;

    string getName() {
        return name;
    }

    void setName(const string &name) {
        this->name = name;
    }
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

Bardzo ważna rzecz do zapamiętania gettery i settery **nie mogą** w sobie zawierać `cin` i `cout`. Mają one służyć do ustawiania wartości i jej zwracania, a nie wypisywania na ekran, albo zczytywanie z niego!

{{< space 7 >}}

## Destruktory

Destruktory są to metody wywoływane zaraz przed zniszczeniem obiektu. Stosuje się je zawsze wtedy, gdy musimy posprzątać podczas usuwania obiektu naszej klasy. Używamy ich zazwyczaj, gdy alokujemy w klasie jakąś pamięć, ponieważ trzeba ją zwolnić, aby nie była bezsensownie zaalokowana bez możliwości dostępu do niej. Innym przypadkiem są jakieś połączenia, otwarte sockety itp. Należy je zamknąć przed zniszczeniem obiektu.

```c++
class NazwaTwojejKlasy
{
public:
    ~NazwaTwojejKlasy(); //To jest definicja destruktora
};

NazwaTwojejKlasy::~NazwaTwojejKlasy()
{
    //Tu wykonujemy wszystkie operacje jakie mają się wykonać automatycznie tuż przed zwolnieniem pamięci zajmowanej przez klasę.
}
```

Destruktor, tak samo, jak konstruktor nie posiada zwracanego typu. Drugą ważną cechą jest to, że destruktor musi być zawsze bezparametrowy. Trzecią, a zarazem ostatnią ważną cechą jest możliwość zdefiniowania tylko i wyłącznie jednego destruktora dla danej klasy.
Poniżej prosty przykład, demonstrujący działanie konstruktora i destruktora.

```c++
class KlasaCL
{
public:
    KlasaCL();
    ~KlasaCL();
};

int main()
{
    KlasaCL * tKlasa;
    cout << "Rezerwuje pamiec za pomoca new" << endl;
    tKlasa = new KlasaCL;
    cout << "Wchodze do bloku {" << endl;
    {
        KlasaCL tKlasa;
    }
    cout << "Wyszedlem z bloku }" << endl;
    cout << "Zwalniam pamiec, ktora zostala zarezerwowana za pomoca new" << endl;
    delete tKlasa;
    getch();
    return( 0 );
}

KlasaCL::KlasaCL()
{
    cout << "=> Konstruktor wywolany!" << endl;
}

KlasaCL::~KlasaCL()
{
    cout << "=> Destruktor wywolany!" << endl;
}
```

Zasady tworzenia destruktora są podobne do konstruktora. Nazwa destruktora zaczyna się od ~ i nazwy klasy.

{{< space 3 >}}

### Zasada RAII

> Konstruktor alokuje elementy i odpowiednio inicjalizuje składowe obiektu klasy `Vector`. Destruktor z kolei dealokuje te elementy. Ten model uchwytu do danych jest powszechnie wykorzystywany podczas pracy ze zbiorami danych, których rozmiar może się zmieniać w czasie istnienia obiektu.
> Technika zajmowania zasobów za pomocą konstruktora i zwalniania ich za pomocą destruktora, zwana RAII (ang. Resource Acquisition Is Initialization - zajęcie zasobów oznacza inicjalizacjeę), pozwala wyeliminować "gołe operacje `new`", czyli uniknąć alokacji w ogólnym kodzie i ukryć je w logicznie zaimplementowanej abstrakcji. Analogicznie należy też unikać "gołych operacji `delete`". Im mniej gołych operacji `new` i `delete` w kodzie, tym mniejsze ryzyko popełnienia błędu i łatwiej unikać wycieków pamięci (13.2)

[Wikipedia](https://pl.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization)

{{< space 7 >}}

## Zadanie

1. Stwórz klasę `Point`, która będzie przechowywać informacje o punkcie. Dodaj do niego odpowiednie metody.
2. Stwórz klasę `Polygon`, która będzie w konstruktorze przyjmować wartość z ilu punktów się składa. Do jej implementacji użyj tablicy dynamicznie alokowanej, a nie kontenerów (trzeba to przećwiczyć i zasadę RAII).
3. Dodaj metody, do przypisywania punktów, do poszczególnych indeksów, wypisywania wszystkich punktów oraz wyliczania obwodu.