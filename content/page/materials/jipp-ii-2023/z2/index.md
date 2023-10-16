---
title: "Laboratorium 1"
layout: singleNoHeader
date: 2023-10-10
---


# Laboratorium 1

### Cele laboratorium i poruszane zagadnienia

* Podstawy teoretyczne - dokumentacja
* Zapoznanie się ze zmianami w pliku `main` pomiędzy c i c++
* Poznanie struktury projektu
* Zapoznanie się ze strumieniami wyjścia i wejścia w c++
* Alokacja pamięci
* Przypomnienie wiadomości o `const` i wskaźnikach
* Referencja

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

{{< space 5 >}}

## Plik `main`

Porównania plików `main` dokonamy na najprostszym programie `Hello world!`. Przypomnijmy sobie, jak to wyglądało w c:

```c
#include <stdio.h>

int main()
{
    printf("Hello World\n");
    return 0;
}

```

Natomiast w c++ napiszemy to w taki sposób:

```cpp
#include <iostream>

using namespace std;

int main() {
    cout << "Hello World" << endl;
    return 0;
}
```
(kod omówiony na zajęciach)

**Czy jest różnica pomiędzy `#include "iostream"`, a `#include <iostream>`?**
{{< br >}}
Jeżeli użyjemy cudzysłowów, będziemy wyszukiwać plików do załączenia w bieżącym katalogu, bądź względem pliku, w którym załączamy. Jednakże w przypadku użycia znaków mniejszości i większości, będziemy wyszukiwać w standardowym miejscu bibliotek.

**Dlaczego i po co używa się `return 0` na końcu funkcji `main`?**
{{< br >}}
Jeżeli uruchomimy program, to po zakończeniu działania zwracany jest status. 0 oznacza sukces, każdy inny, program nie zakończył się powodzeniem.

#### Do przypomnienia z c

* komentarze
* czym się różni implementacja od deklaracji
* tworzenie funkcji i czy implementacja musi znajdować się w tym samym pliku, co jej deklaracja
* instrukcje warunkowe i pętle
* deklaracja zmiennych

{{< space 9 >}}

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

## Udogodnienia c++

Podczas poprzedniego laboratorium poznaliśmy już kilka udogodnień, które przynosi c++ względem c. Dziś wspomnę o kilku kolejnych, lecz nie będę ich szczegółowo omawiał.

Pierwszym z nich jest `string`, który służy do przechowywania tekstu. Ułatwia on pracę z nim w znaczącym stopniu. Nie musimy się martwić, czy tekst zmieści się w zarezerwowanym miejscu, przechowywać rozmiaru tekstu, albo obliczać go. Możemy też zdecydowanie łatwiej je łączyć, a dodatkowo znajdziemy funkcje, które pomogą nam nim wykonywać różne operacje.

[Dokumentacja `string`](https://en.cppreference.com/w/cpp/string)

[Dokumentacja `string_view` (c++17)](https://en.cppreference.com/w/cpp/string/basic_string_view)

### Zadanie

1. Napisz program pytający o imię i nazwisko, a następnie sprawdzający czy mają one taką samą długość.
2. Połącz imię i nazwisko oraz dodaj na samym początku `Pani` albo `Pan` w zależności, czy imię kończy się na `a`.

{{< space 9 >}}

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

{{< space 9 >}}

## Stałe i referencja

Przypomnijmy sobie krótko informacje o stałych. Deklaracja zmiennej jako stała oznacza obietnicę, że nie zmienię wartości zmiennej. Oczywiście kompilator nam nie ufa i nas sprawdza, czy dotrzymujemy słowa. Jeżeli przyłapie nas na próbie zmiany wartości, to kompilacja zakończy się niepowodzeniem.

```cpp
    const double pi = 3.14;
```

## Stałe wyrażenia

Stałe wyrażenia zostały wprowadzone w c++11. Są to takie kawałki kodu, które są uruchamiane i obliczane na etapie kompilacji, a nie w trakcje wykonywania programu. Przykładowo stworzyliśmy funkcję, obliczającą jakąś wartość, gdzie zawsze podajemy wartości znane podczas kompilacji. Dodając przed jej deklarację słowo kluczowe `constexpr`, będzie ona wykonywana tylko i wyłącznie podczas kompilacji.

Więcej informacji oraz przykład możesz zobaczyć w [dokumentacji](https://en.cppreference.com/w/cpp/language/constexpr).

{{< space 3 >}}

### Stałe wskaźniki

Należy nauczyć się rozróżniać stały wskaźnik i wskaźniki na stałe. Poniższe dwa zapisy wygladają bardzo podobnie, lecz znaczą zupełnie coś innego.

```c++
const int * k;
int *const k1;
```

Zanim zaczniemy szukać różnicy pomiędzy tymi dwoma zapisami, to zdefiniujmy sobie operacje, jakie możemy chcieć na nich wykonywać:
- odczyt wartości
- odczyt adresu
- modyfikacja wartości
- modyfikacja adresu

W stałych zabroniona jest modyfikacja, a odczyt jest zawsze dostępny, więc dwa pierwsze warunki będą zawsze spełnione.

{{< space 5 >}}

Zacznijmy od **wskaźnika na stałą**, jak nazwa wskazuje: wskaźnik wskazuje na stałą wartość.

```c++
int cVal = 314;
const int *p = &cVal;
```

Czy będzie można zmodyfikować wartość zmiennej `p`? Jest ona stałą, więc nie możemy tego zrobić.  

Czy będzie można zmodyfikować adres zmiennej `p`? Adres nie jest stały, więc będzie to możliwe.

Przetestuj, czy faktycznie tak to działa:

```c++
int cVal = 314;
const int *p = &cVal;

*p = 628;

const int cVal2 = 666;
p = &cVal2;
```

> Możemy modyfikować wskaźnik, ale nie możemy modyfikować wartości.

{{< space 5 >}}

Przejdźmy teraz do **stałego wskaźnika**, czyli jak nazwa nam wskazuje: wskaźnik jest niezmienny.

```c++
int cVal = 314;
int *const p = &cVal;
```

Czy będzie można zmodyfikować wartość zmiennej `p`? Zmienna nie jest stała, więc będzie to możliwe.

Czy będzie można zmodyfikować adres zmiennej `p`? Adres jest stały, więc nie będzie to możliwe.

Przetestuj, czy faktycznie tak to działa:

```c++
int cVal = 314;
int *const p = &cVal;

*p = 628;

int cVal2 = 666;
p = &cVal2;
```

> Możemy modyfikować wartość, ale nie możemy modyfikować wskaźnika.

{{< space 5 >}}

**Czy można połączyć obydwie własności?**

Oczywiście, że tak, stworzymy wtedy zmienną, gdzie nie będzie można modyfikować wartości i wskaźnika (adresu).

```c++
int cVal = 314;
const int* const youCantModifyMe = cVal; 
```


|                  | Modyfikowanie wartości | Modyfikowanie wskaźnika |
|------------------|------------------------|-------------------------|
| `const int*`       | <center>❌ </center> |  <center>✔ </center>  |
| `int *const`       | <center>✔ </center>  | <center>❌ </center>  |
| `const int* const` | <center>❌ </center>  | <center>❌ </center> |

{{< space 5 >}}

### Referencja

Mając już omówione stałe wskaźniki, można przejść do referencji. Można ją porównać do wskaźnika, ale nie potrzebujemy używać `*`, aby dostać do jego wartości. Jednakże nie możemy zmienić jego wartości, czyli możemy porównać referencję do stałego wskaźnika.

Referencję najczęściej wykorzystuje się podczas przekazywania argumentów do funkcji. Może to nastąpić w momencie, gdy chcemy zwrócić więcej, niż jedną wartość, albo modyfikować oryginalną zmienną, ale należy pamiętać, że przez referencję możemy przekazywać tylko i wyłącznie pojedyncze obiekty, nie tablice. Spójrzmy na praktyczny przykład, mamy zdefiniowane 2 funkcje, która z nich wykona się szybciej?

```cpp
    void doSth(string text) {
        //do sth
    }

    void doSth1(string &text) {
        //do Sth (the same)
    }
```

Próbując porównać czas dla krótkiego tekstu nie zauważymy różnicy, jednakże, gdy przekazywany parametr byłby dość duży, to funkcja `doSth1` działałaby szybciej, ponieważ nie trzeba by było wykonywać kopii zmiennej. Zyskaliśmy na optymalności działania naszej funkcji, ale nie możemy być teraz pewni, czy ktoś w funkcji nie zmodyfikuje wartości. Dodajmy słowo kluczowe `const`, to zabezpieczy nas przed tym, a nadal będziemy mogli w efektywniejszy sposób przekazywać parametr.

```cpp
    void doSth1(const string &text) {
        //do Sth (the same)
    }
```

Powinniśmy preferować przekazywanie parametrów przez referencję, jednakże nie jest to zawsze wymagane. Przykładem może być sytuacja, gdy będziemy modyfikować jej zawartość, a chcemy, aby oryginał pozostał nienaruszony.

Należy też zapamiętać, że głównie stosujemy (w celach optymalizacyjnych) przekazywanie zmiennych złożonych (nie np. `int`, `double` itp).

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

### Zadanie

Jak działać z zadaniami na gitclassroom?

1. Wejdź w link podany na delcie
2. (W przypadku wykonywania zadania na komputerach uczelnianych) Wygeneruj `fine-grained personal access token `. Najlepiej ustaw tylko i wyłącznie dostęp do repozytorium z zadaniem. Długość życia ustaw na 1 dzień.
3. Sklonuj repozytorium podane na delcie.
4. Wykonaj zadania
5. Zatwierdź zmiany za pomocą `commit` oraz prześlij je na repozytorium zdalne (`push`).
6. Usuń swoje poświadczenia [według instrukcji](https://stackoverflow.com/questions/15381198/remove-credentials-from-git).


