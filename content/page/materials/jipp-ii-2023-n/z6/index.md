---
title: "Języki i Paradygmaty Programowania II laboratorium 5"
layout: singleNoHeader
date: 2023-12-08
---

# Laboratorium 5

### Cele laboratorium i poruszane zagadnienia

* Parametry uruchomienia programu
* Doxygen
* Statyczna analiza kodu
* Czysty kod

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
