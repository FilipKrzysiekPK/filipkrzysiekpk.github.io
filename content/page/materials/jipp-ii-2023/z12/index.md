---
title: "Laboratorium 11"
layout: singleNoHeader
date: 2023-12-18
---


# Laboratorium 11

### Cele laboratorium i poruszane zagadnienia

* Kontenery
* Biblioteka standardowa
* Mierzenie czasu
* Kolejny domyślny konstruktor


{{< space 7 >}}


# Biblioteka STL

Biblioteka standardowa zawiera bardzo dużo ułatwień, zdefiniowanych funkcji oraz gotowych algorytmów. Dzięki temu nie musimy, a nawet w większości przypadków nie powinniśmy (poza celami edukacyjnymi) tworzyć na nowo gotowych rozwiązań.

Spis pogrupowanych elementów można znaleźć na pierwszej stronie [dokumentacji](https://en.cppreference.com/w/)

Wyróżnię tutaj kilka grup elementów, z których korzystałem, korzystam, bądź znam:

1. Biblioteka `chrono` - wszelkie operacje na czasie (od c++20 zaczyna być wsparcie do dostępu do poszczególnych składowych daty i czasu) [dokumentacja](https://en.cppreference.com/w/cpp/chrono)
2. Biblioteka `memory` - inteligentne wskaźniki, czyli sposób, jak całkowicie pożegnać się z alokacją i dealokacją pamięci
3. `pair`, `tuple` - kontener 2, 3 elementowy, pozwalający przechowywać parę, albo trójkę zwiazanych ze soba wartości, przydatne gdy chcemy zwrócić kilka elementów. [dokumentacja](https://en.cppreference.com/w/cpp/utility#General-purpose_utilities)
4. Dużo wydajniejsza zamiana miejscami dwóch elementów, albo podmienianie jednego elementu na drugi (`swap`, `move`).
5. Zmienne do przechowywania napisów (`string`) oraz odchudzony `string` [dokumentacja](https://en.cppreference.com/w/cpp/string)
6. Kontenery, różne z różnym zapisem pod spodem: [dokumentacja](https://en.cppreference.com/w/cpp/container)
7. Iteratory, można go określić, jako wskaźnik ułatwiający poruszanie się po tabeli, czy też kontenerze. [dokumentacja](https://en.cppreference.com/w/cpp/iterator)
8. Biblioteka `algorithm` [dokumentacja](https://en.cppreference.com/w/cpp/algorithm). Możemy w niej wyróżnić:
   * czy wszystkie, którykolwiek, żaden z elementów spełnia przekazany warunek
   * wyszukiwanie elementów
   * zliczanie elementów
   * wypełnianie kontenera
   * usuwanie duplikatów elementów
   * sortowania
   * złączanie dwóch kontenerów
9. Biblioteka `filesystem` - wszelkie operacja na plikach. [dokumentacja](https://en.cppreference.com/w/cpp/filesystem). 
10. Biblioteka `regex` - wyrażenia regularne. [dokumentacja](https://en.cppreference.com/w/cpp/regex).
11. biblioteka do obsługi wielowątkowości. [dokumentacja](https://en.cppreference.com/w/cpp/thread)
12. Zakresy wartości poszczególnych typów zmiennych. [dokumentacja](https://en.cppreference.com/w/cpp/types/numeric_limits)

{{< space 2 >}}

Ciekawostki, które znalazłem przygotowując listę:

1. `format_to` - string z argumentami [dokumentacja](https://en.cppreference.com/w/cpp/utility/format/format_to)
2. `variant` - przechowywanie dowolnego typu w zmiennej [dokumentacja](https://en.cppreference.com/w/cpp/utility/variant)
3. `optional` - nie musimy zwracać elementu [dokumentacja](https://en.cppreference.com/w/cpp/utility/optional)

# Nowe funkcjonalności dla starszych wersji c++

Bardzo często funkcjonalności udostępniane w nowszych wersjach c++ są dostępne dla starszych w bibliotece `boost`.

{{< space 5 >}}

## Konstruktor kopiujący, czy na pewno

Analizując domyślne konstruktory można zauważyć, że jeden z nich jest zadeklarowany z podwójną referencją.

```cpp
class Test {
public:
    Test(Test&& other);
};
```

Czy jest to inna konstrukcja konstruktora kopiującego? Poniekąd tak, ale dokładnie rzecz ujmując jest to konstruktor przeniesienia. W jakim przypadku warto go stosować? Gdy wiemy, że "stary" obiekt zaraz i tak zostanie usunięty. Sam c++ już go stosuje. Dopisz do powyższej klasy operator przypisania, konstruktor kopiujący, wpisz w nim `cout` informujący, który to konstruktor jest wykonywany. Następnie stwórz funkcje, która będzie zwracać obiekt naszej klasy. Który konstruktor się wykonał?

Możemy też jawnie i manualnie wykonać przeniesienia zasobów. Służy do tego `std::move`. Należy uważać, aby nie nadużywać tego mechanizmu, ponieważ sam kompilator wprowadza dużo usprawnień, więc czasem trzeba się zastanowić, czy na pewno chcemy tego użyć.

Więcej szczegółów i dokładniejsze omówienie tematu można znaleźć w [https://cpp-polska.pl/post/zarzadzanie-zasobami-w-c-2-semantyka-przenoszenia-stdmove](artykuł na blogu cpp-polska.pl) oraz w 
[https://en.cppreference.com/w/cpp/language/move_constructor](dokumentacji).


{{< space 5 >}}

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

# Zadania 

1. Porównaj czas wypełniania kontenera `vector` za pomocą `push_back` z rezerwacją pamięci i bez niej. Przyjmij próbkę minimum 1000 elementów.
1. Wypełnij tablicę/kontener losowymi liczbami. Znajdź w bibliotece standardowej funkcje sortujące i posortuj wcześniej stworzoną tablicę/kontener.
2. Stwórz prostą klasę (wszystko może byc publiczne), następnie stwórz kontener z losowymi obiektami tej klasy. Wyspecyfikuj wyszukiwany element. Znajdź:
   a. pierwszy element, który będzie taki sam jak szukany element
   b. będzie zawierał, któreś pole takie samo, jak szukany element (przykładowo dla klasy student, znajdź pierwszego studenta, który będzie mieć nazwisko `Kowalski`)


