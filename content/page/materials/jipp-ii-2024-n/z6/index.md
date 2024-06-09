---
title: Języki i Paradygmaty Programowania II laboratorium 5
layout: singleNoHeader
date: 2024-06-09T07:15:29.707Z
lastmod: 2024-06-09T07:15:40.596Z
---

# Laboratorium 6

### Cele laboratorium i poruszane zagadnienia

* Kontenery dokładniejszy opis
* Parametry uruchamiania programu
* Doxygen
Statyczna analiza kodu

{{< space 7 >}}

# Kontenery

[Dokumentacja](https://en.cppreference.com/w/cpp/container)

Kontenery wielokrotnie przewijały się na wcześniejszych laboratoriach, jednakże nie były wystarczająco dokładnie omawiane.

Aktualnie wiemy, że istnieją i są to takie inteligentne tablice, których nie musimy dynamicznie alokować. Całą kontrolę pamięci robią za nas. Dodatkowo można zauważyć, że mają w sobie wiele wbudowanych funkcjonalności.

Jak wiemy przechowywanie elementów w pamięci możemy dokonywać na różne sposoby i same potrzeby korzystania z kontenerów mogą być różne. Dlatego w bibliotece standardowej możemy znaleźć wiele ich rodzajów. Zostały podzielone na kategorie:

* Kontenery sekwencyjne
  * `array` - statyczna tablica
  * `vector` - dynamicznie zmieniająca rozmiar tablica
  * `deque` - podwójnie indeksowany kontener
  * `forward_list` - lista linkowana w jedną stronę
  * `list` - lista linkowana w obydwie strony
* Kontenery asocjacyjne
  * `set` - kolekcja unikatowych kluczy sortowana po kluczach
  * `map` - kolekcja par klucz - wartość, gdzie klucze są unikatowe i sortowane 
  * `multiset` - kolekcja kluczy, gdzie mogą się one powtarzać i są posortowane
  * `multimap` - kolekcja par klucz - wartość, klucze mogą się powtarzać i są posortowane
* Niesortowane kontenery asocjacyjne

Dodatkowo zostały zaimplementowane adaptery kontenerów. Pozwala nam to używać kontenerów sekwencyjnych, jako "nowych" kontenerów. Możemy w tej grupie wyróżnić:

* `stack` - kolejka LIFO
* `queue` - kolejka FIFO
* `priority_queue` - kolejka FIFO z priorytetami

Dodatkowo w c++23 mają zostać dodane:

* `flat_set`
* `flat_map`
* `flat_multiset`
* `flat_multimap`

Jak można zauważyć istnieje wiele kontenerów, możemy dobierać odpowiednie do zapotrzebowania. Przyjrzymy się głębiej najczęściej używanemu `vector` -owi.

{{< space 3 >}}

### vector

Jest to kontener, w którym mamy dostęp sekwencyjny do kolejnych elementów. Przechowuje on dane w pamięci, tak jak "normalna" tablica. Główną różnicą względem najprostszej tablicy jest możliwość dynamicznej zmiany swojej wielkości.

Vector jest to jeden z najczęściej używanych kontenerów. Przypomina on w zachowaniu "tradycyjną" tablicę i też właśnie w takiej formie przechowuje dane w pamięci, ale w przeciwieństwie do niej pozwala na dynamiczną zmianę swojej wielkości. Omówmy podstawowe funkcjonalności:

* Dostęp do elementów:
  * `at(indx)` - dostęp do elementu ze sprawdzaniem, czy przekazany indeks jest w zakresie tablicy
  * `operator[]` - dostęp do elementu
  * `front` - dostęp do pierwszego elementu
  * `back` - dostęp do ostatniego elementu
  * `data` - bezpośredni dostęp do danych
* Iteratory:
  * `begin` - zwraca iterator wskazujący na poczatek
  * `end` - zwraca iterator wskazujący na koniec 
  * `rbegin` - zwraca wsteczny iterator wskazujący na poczatek
  * `rend` - zwraca wsteczny iterator wskazujący na koniec
* Zarządzanie wielkością:
  * `empty` - informacja czy kontener jest pusty
  * `size` - zwraca rozmiar, ile elementów przechowuje kontener
  * `max_size` - zwraca ile maksymalnie elementów może przechowywać kontener
  * `reserve(elems)` - rezerwacja pamięci "magazynu" dla przechowywania `elems` elementów
  * `capacity` - zwraca ile elementów może być przechowywane w "magazynie"
  * `shrink_to_fit` - zmniejszanie "magazynu" do aktualnie znajdującej się w nim liczby elementów
* Modyfikatory:
  * `clear` - usuwanie wszystkich elementów ("magazyn" pozostaje nienaruszony)
  * `insert` (c++23) - wstawianie elementu we wskazanym miejscu (pod wskazanym indeksem)
  * `insert_range` - wstawianie tablicy elementów we wskazanym miejscu
  * `emplace` - wywoływanie konstruktora elementu na wskazanej pozycji
  * `erase` - usuwanie elementu/ów pod wskazanym indeksem/zakresie
  * `push_back` - dodawanie elementu na końcu kontenera
  * `emplace_back` - wywoływanie konstruktora elementu na końcu kontenera
  * `append_range` (c++23) - dodawanie wielu elementów na końcu kontenera
  * `pop_back` - usuwanie ostatniego elementu z kontenera
  * `resize` - zmiana wielkości kontenera (ile elementów może on przechowywać)
  * `swap` - zamiana miejscami dwóch elementów w kontenerze

Jak można zauważyć kontener posiada wiele pomocnych metod, stąd też jego tak duża popularność, w szczególności, że jest bardzo podobny do "tradycyjnej" tablicy.

{{< space 2 >}}

Zastanówmy się teraz nad jego działaniem. Czy `capacity` i `size` to jest to samo? Vector działa na takiej zasadzie, że wewnątrz siebie tworzy sobie tablicę, którą nazwiemy roboczo "magazynem". Nasz magazyn może mieć inny rozmiar (być taki sam, bądź większy), niż liczba elementów, które przechowujemy wewnątrz naszego kontenera. Czyli możemy mieć puste półki, jeżeli jednak zapełnimy je wszystkie, to musimy powiększyć nasz magazyn. Gdy pytamy się o wielkość kontenera, to udzielamy informacji ile półek jest zajętych, natomiast, gdy pytamy się o pojemność, to mówimy ile półek jest w całym magazynie. Przeanalizuj poniższy kawałek kodu i porównaj z wynikami.

```cpp
#include <iostream>
#include <vector>

using namespace std;

void printSizeAndCapacity(const vector<int> &v) {
    cout << "Size:  " << v.size() << "\t\tCapacity: " << v.capacity() << endl;
}
 
int main()
{
    vector<int> v;
    printSizeAndCapacity(v);

    v.push_back(100);
    printSizeAndCapacity(v);

    v.push_back(100);
    printSizeAndCapacity(v);

    v.push_back(100);
    printSizeAndCapacity(v);

    v.reserve(100);
    printSizeAndCapacity(v);

    v.resize(50);
    printSizeAndCapacity(v);

    v.resize(150);
    printSizeAndCapacity(v);

    v.reserve(100);
    printSizeAndCapacity(v);
}
```

**Jakie niesie to konsekwencje?**

Główną i najważniejszą konsekwencją tego jest problem z dostępem do elementów przez wskaźnik. Możemy to robić bezpiecznie, jeżeli będziemy na 100% pewni, że nikt nie będzie modyfikować tego kontenera. Gdyby ktoś dodał kolejny element i musiałaby zajść realokacja, to nasz wskaźnik przestanie wskazywać na nasze dane. Jednakże w takich przypadkach o wiele lepiej użyć mapy, czy też listy.

Drugi ważny aspekt, kontener sam automatycznie nie zmniejsza swojej wielkości, musimy wywoływać to sami ręcznie.

```cpp
#include <iostream>
#include <vector>

using namespace std;

void printSizeAndCapacity(const vector<int> &v) {
    cout << "Size:  " << v.size() << "\t\tCapacity: " << v.capacity() << endl;
}
 
int main()
{
    vector<int> v;
    printSizeAndCapacity(v);

    v.resize(150);
    printSizeAndCapacity(v);

    v.resize(50);
    printSizeAndCapacity(v);
}
```

{{< space 5 >}}

# Mierzenie czasu

Może się zdarzyć sytuacja, gdzie chcemy po prostu mierzyć czas, albo chcemy poeksperymentować i dowiedzieć się, jak szybko działa nasz kod. Możemy tego dokonać na kilka sposobów. Możemy mierzyć czas w stylu języka C, albo szukać bibliotek systemowych. Możemy próbować używać środowisk programistycznych i trybu debugowania. Jednakże c++ daje nam możliwość skorzystania z biblioteki, która jest uniwersalna pomiędzy różnymi systemami. Biblioteki `chrono`.

Można w niej zauważyć, że możemy korzystać z kilku różnych rodzajów zegarów przykładowo: `system_clock`, `steady_clock`, czy też `high_resolution_clock`. W naszym eksperymencie będziemy początkowo używać `system_clock`, jeżeli jego dokładność będzie niewystarczająca, to użyjemy `high_resolution_clock`.

Aby zmierzyć, ile czasu trwała jakaś akcja będziemy musieli pobrać aktualny czas przed i po mierzonym elemencie, a następnie zwrócić różnicę czasu.

```cpp
#include <chrono>
#include <iostream>

using namespace std;

int main() {
    auto start = chrono::system_clock::now();

    // Do sth
    cout << "Hello world" << endl;

    auto stop = chrono::system_clock::now();

    auto elapsed = chrono::duration_cast<chrono::milliseconds>(stop - start).count();
    cout << elapsed << " ms" << endl;
}
```

Chcąc zmienić dokładność wyświetlanego wyniku możemy zmienić obliczanie różnicy czasu z milisekund na nanosekundy. W tym celu zmień `chrono::milliseconds`, na `chrono::nanoseconds` i oczywiście opis w cout.

Najlepiej w tym przypadku używać uruchamiania bez debugowania, aby wyeliminować narzut narzędzi deweloperskich i ewentualne brakepointy.


{{< space 7 >}}

# Uzupełnienie względem biblioteki standardowej

Bardzo często funkcjonalności udostępniane w nowszych wersjach c++ są dostępne dla starszych w bibliotece `boost`.

{{< space 7 >}}

# Parametry uruchomienia programu

Istnieje prawdopodobieństwo, że temat był wcześniej poruszany. Jeżeli tak było to będzie tylko przypomnienie informacji.

Zacznijmy może od tego, czym są te parametry uruchomienia programu. Są to wszystkie "słowa" i "wyrażenia" podane po nazwie naszego programu, gdy go uruchamiamy z linii komand. Przechwytywane są one przez argumenty funkcję `main`.

```cpp
int main(int argc, char *argv[]) {

}
```

* `argc` - liczba przekazywanych argumentów
* `argv[]` - tablica argumentów

Można dostrzec, że teksty argumentów są zapisywane w postaci tablicy `char`, czyli w stylu C. Niestety sprawia to, że zarządzanie nimi jest odrobinę trudniejsze i należy mocno zwrócić uwagę na operację porównania. Dodatkowo należy mocno zwrócić uwagę na funkcje `main` w windowsie, ponieważ może ona przybierać odrobinę inną formę, a niż pod innymi kompilatorami. Na szczęście są dodatkowe formy, a nie zastępcze. Więcej szczegółów można znaleźć w dokumentacji:

* [cppreference](https://en.cppreference.com/w/cpp/language/main_function)
* [MSDN](https://learn.microsoft.com/en-us/cpp/cpp/main-function-command-line-args?view=msvc-170)

{{< br >}}

Stwórzmy prosty przykład wypisujący wszystkie argumenty przekazane:

```cpp
int main(int argc, char *argv[]) {
    for (int i = 0; i < argc; ++i) {
        cout << argv[i] << endl;
    }
}
```

Możemy zauważyć, że pierwszym przekazanym argumentem jest nazwa naszego programu, a dopiero kolejnym są przekazywane argumenty. Podział na kolejne "słowa" został dokonany na białych snakach (spacje), chyba że został zamknięty w cudzysłowie.

{{< space 5 >}}

# Dokumentacja doxygen

Przed rozpoczęciem omawiania zagadnienia, pochylmy się nad ustawieniami Visual Studio. Należy zmienić styl generowanych komentarzy do dokumentacji z domyślnie ustawionego na XML na doxygen. Obojętne, która wersja, osobiście preferuję `/**`.

![Ustawienia Visual studio - doxygen](/imgJipp/Doxygen.png)

**Czym jest doxygen?** {{< br >}}
Jest to generator dokumentacji na podstawie specjalnych komentarzy zawartych w kodzie. Obsługuje on między innymi takie języki jak C, C++, Java, Python oraz częściowo PHP, C#.

**Co daje pisanie dokumentacji w takiej formie?** {{< br >}}
Pierwszym zasadniczym plusem jest możliwość łatwego doprecyzowania, co dana funkcja robi, dodania opisów do przekazywanych parametrów (nazwy nie zawsze są wystarczające) oraz co będzie zwracane. Środowiska programistyczne często wspierają dokumentację napisaną w takim formacie i przy wywołaniu danej funkcji oraz najechaniu na nią myszką dostajemy jej podgląd. Dodatkowo można ją wyeksportować do takich formatów jak: pdf, html, LaTeX.

![Podgląd podpowiedzi będącą wycinkiem dokumentacji](/imgJipp/doxygen%20VS.png)

Na zajęciach nie będziemy się zajmować generowaniem dokumentacji, jedynie jej tworzeniem. Informacje o jej generowaniu można znaleźć na [stronie projektu](https://www.doxygen.nl/manual/index.html).

[Przykładowa dokumentacja wygenerowana doxygenem.](https://xerces.apache.org/xerces-c/apiDocs-3/classes.html)

[JsonCpp Documentation](https://open-source-parsers.github.io/jsoncpp-docs/doxygen/index.html)

{{< space 5 >}}

## Tworzenie dokumentacji

Ogromnym plusem dokumentacji doxygen jest ogromna łatwość jej tworzenia. Wystarczy napisać deklarację funkcji, którą chcemy stworzyć, a następnie wejść do linii nad nią i wpisać `/**`, większość środowisk programistycznych rozpozna i wygeneruje odpowiednie linie. Jednakże prześledźmy kilka najważniejszych:

* `@brief` - opis funkcji, można także pisać opis bez używania tego słowa kluczowego
* `@param <nazwa_parametru>` - opis parametru nazw_parametru
* `@return` - opis wartości zwracanej

```cpp
/**
 * @brief This is my function and I don't know what it's doing
 * @param charlie number of charlie lamp
 * @param name full name of charlie lamp
 * @return number of charlie lamp
*/
int myFunction(int charlie, string name) {

}
```

{{< space 5 >}}

# Statyczna analiza kodu

Jak pisać kod lepiej? Czy zrobiłem jakieś dziwne błędy w kodzie? Można się tego dowiedzieć, używając odpowiednich narzędzi do statycznej analizy kodu, które będą podpowiadać, jak nasz kod ulepszyć. Są one często zintegrowane ze środowiskami programistycznymi, wyświetlając różne ostrzeżenia. Możemy też sami uruchomić taką analizę, należy tylko uważać, jakie testy uruchamiamy.

Przykładem takiego narzędzia jest [`clang-tidy`](https://clang.llvm.org/extra/clang-tidy/). Jest on domyślnie wbudowany w CLion, co niestety spowalnia jego działanie, jednakże daje ogromne możliwości pisania lepszego i bardziej poprawnego kodu. Oczywiście należy uważać, ponieważ nie wszystkie poprawki będą "poprawne".
