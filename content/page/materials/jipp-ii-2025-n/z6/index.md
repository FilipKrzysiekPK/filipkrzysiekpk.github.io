---
title: Języki i Paradygmaty Programowania II laboratorium 5
layout: singleNoHeader
date: 2024-06-09T07:15:29.707Z
lastmod: 2025-06-06T20:06:13.043Z
---

# Laboratorium 6

### Cele laboratorium i poruszane zagadnienia

* Kontenery dokładniejszy opis
* Parametry uruchamiania programu
* Statyczna analiza kodu
* biblioteka stl

{{< space 7 >}}

## Kontenery

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

{{< space 5 >}}

## Mierzenie czasu

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

## Uzupełnienie względem biblioteki standardowej

Bardzo często funkcjonalności udostępniane w nowszych wersjach c++ są dostępne dla starszych w bibliotece `boost`.

{{< space 7 >}}

## Parametry uruchomienia programu

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

## Statyczna analiza kodu

Jak pisać kod lepiej? Czy zrobiłem jakieś dziwne błędy w kodzie? Można się tego dowiedzieć, używając odpowiednich narzędzi do statycznej analizy kodu, które będą podpowiadać, jak nasz kod ulepszyć. Są one często zintegrowane ze środowiskami programistycznymi, wyświetlając różne ostrzeżenia. Możemy też sami uruchomić taką analizę, należy tylko uważać, jakie testy uruchamiamy.

Przykładem takiego narzędzia jest [`clang-tidy`](https://clang.llvm.org/extra/clang-tidy/). Jest on domyślnie wbudowany w CLion, co niestety spowalnia jego działanie, jednakże daje ogromne możliwości pisania lepszego i bardziej poprawnego kodu. Oczywiście należy uważać, ponieważ nie wszystkie poprawki będą "poprawne".

{{< space 5 >}}

## Formatowanie kodu

Istnieje kilka styli formatowania kodu jednak to do użytkownika/projektu należy decyzja, jak kod ma być wystylowany. Aby podczas współpracy wielu osób mieć podobne formatowanie kodu można używać narzędzia `clang-fromat` i dodać do projektu plik konfiguracyjny `.clnag-format`, który będzie przechowywał reguły według, których kod ma zostać sformatowany.

Przeanalizuj możliwości dostosowania kodu pod własne preferencje oraz na jakie rzeczy należy zwracać uwagę na przykładzie [narzędzia do generowanioa plików konfiguracyjnych](https://clang-format-configurator.site/)

{{< space 5 >}}

## Zadania biblioteka STL

1. Wygeneruj tablicę wypełnioną losowymi liczbami.
2. Posortuj wygenerowaną tablicę za pomocą funkcji [sort z biblioteki standardowej](https://en.cppreference.com/w/cpp/algorithm/sort.html?ref=leetsolve.com). Zmierz czas tej operacji.
3. Wykonaj zadanie 2, ale przekaż jako pierwszy parametr: `std::execution::par`. Porównaj czas wcześniejszego sortowania z następnym.
4. Przetestuj [ranges::sort](https://en.cppreference.com/w/cpp/algorithm/ranges/sort.html) (c++20). Czym się różni od wcześniejszej funkcji?
5. Przemnóż każdą wartość z zadania pierwszego przez średnią wszystkich wartości w tablicy podzieloną przez 10. Wszystkie operacje wykonaj za pomocą funkcji z biblioteki standardowej.
