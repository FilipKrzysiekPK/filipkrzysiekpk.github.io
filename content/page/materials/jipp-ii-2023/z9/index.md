---
title: "Laboratorium 8"
layout: singleNoHeader
date: 2023-12-04
---


# Laboratorium 8

### Cele laboratorium i poruszane zagadnienia

* Szablony


{{< space 7 >}}

# Szablony

Przypomnijmy sobie jedno z pierwszych zadań domowych, gdzie mieliśmy tworzyć bibliotekę do obsługi macierzy. Zostały narzucone tam z góry typy, jakie miały być obsługiwane, ale jednocześnie dość mocno to ograniczało uniwersalność kodu. Szablony są narzędziem, które pozwolą to naprawić.

Może jednak zacznijmy troszkę inaczej. Szablony są prostym narzędziem, które jednocześnie jest bardzo potężne. Są one podstawą programowania generycznego, które polega na pisaniu kodu w sposób niezależny od konkretnego typu. Możemy je przyrównać do parametrów funkcji, ale muszą być one znane podczas kompilacji. Przez taki "parametr" możemy przekazać:

* typ
* stałą (ewentualnie zmienną, która zostanie skonwertowana do stałej)

{{< space 3 >}}

Czy już gdzieś się spotkaliśmy z szablonami?

Oczywiście, że tak, używaliśmy nich przy deklaracji kontenerów (np. `vector`). Tak było wtedy powiedziane, że zapis pomiędzy `<>` musimy zaakceptować, ale dziś się dowiemy, co to robiło i mniej więcej, jak mogło działać.

{{< space 5 >}}

## Szablon funkcji

Zacznijmy od najprostszej rzeczy, czyli szablonu funkcji. Stwórzmy prostą metodę, która będzie dodawać 2 liczby.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    cout << add(2, 5) << endl;
    cout << add(2.7, 5.6) << endl;
    cout << add(2, 5.6) << endl;
}
```

Tutaj pierwsza uwaga, w aktualnym przypadku i większości przypadków szablony będziemy deklarować i *implementować* w tym samym pliku i będzie to plik nagłówkowy.

Spróbuj uruchomić powyższy kod. Czy wszystko się poprawnie skompilowało? Zakomentuj problematyczną linię i spróbuj znowu skompilować. Wywołując funkcję podawałeś w którymś miejscu, jaki to będzie typ? Nie, kompilator sam wydedukował to na podstawie przekazywanych parametrów. Teraz podmień problematyczną linię na tą poniżej, gdzie deklarujemy, jaki chcemy mieć typ.

```cpp
    cout << add<double>(2, 5.6) << endl;
```

Zaczęło działać? Dlaczego? Została dokonana niejawna konwersja typu i dlatego działa. Teraz pytanie, czy jesteśmy w stanie zrobić taką funkcję, aby były przyjmowane 2 różne typy bez jawnego deklarowania tego? Oczywiście, że tak, wystarczy, że przez szablon przekażemy 2 "parametry". Tak, pojawi się problem, jeden z nich będzie musiał być typem zwracanym. Który? Trzeba dokonać wyboru.

```cpp
template<typename T, typename T1>
T add(T a, T1 b) {
    return a + b;
}
```

{{< space 4 >}}

A jak mogę przekazać zwykłą zmienną przez taki szablon?

Wystarczy, że po prostu go tam zadeklarujesz i zostanie przekazany.

```cpp
template<typename T, int x>
T add(T a, T b) {
    return (a + b) * x;
}

int main() {
    cout << add<int, 6>(2, 5) << endl;
    cout << add<double, 8>(2.7, 5.6) << endl;
    cout << add<float, 9>(2, 5.6) << endl;
}
```

Może się teraz pojawić pytanie, czy można tak zrobić, aby nie musieć przekazywać tej wartości `x`? Oczywiście, że tak. Tak jak mieliśmy parametry domyślne w funkcji, to tutaj też możemy przypisać domyślną wartość tych "parametrów".

```cpp
template<typename T, int x = 1>
```

{{< space 4 >}}

**Przeciążanie szablonów**

Istnieje możliwość przeciążania szablonów, albo funkcji szablonowych. Panuje tutaj kilka zasad:

- Przeciążane szablony muszą się różnić argumentami
- Przeciążany szablon odnosi się do przeciążonej funkcji
- Istnieje jakaś funkcja, a my tworzymy szablon, wtedy ten szablon tworzy niezadeklarowane przeciążenia funkcji
- Specjalizacje - definiujemy dla konkretnych wartości nowe zachowanie naszej funkcji


```c++
template<typename T>
T addS(T a, T b) {
    return a + b + cons;
}


template<typename T, int cons>
T addS(T a, T b) {
    return a + b + cons;
}
```

{{< space 1 >}}

**Szablon metod**

Podobnie jak w funkcjach możemy stworzyć szablon dla metod w klasie. Nie musimy wtedy tworzyć szablonu dla całej klasy, wystarczy dla jednej metody. Działa ona na dosłownie takiej samej zasadzie jak dla funkcji.

{{< space 6 >}}

## Szablon klasy

Deklarowanie szablonu dla klasy wygląda bardzo podobnie co do funkcji, jednakże deklarujemy go dla całej klasy.

```cpp
template<typename T>
class Variable {
    T var;

public:
    Variable() {

    }

    Variable(T var): var(var) {}

    T getValue() {
        return var;
    }

    void setValue(T newVal) {
        var = newVal;
    } 
}
```

Tak samo, jak dla funkcji deklaracja i implementacja musi znajdować się w tym samym pliku. (Jak się bardzo postara, to da się to rozdzielić.)

{{< space 3 >}}

## Ograniczanie tego przekazywanych wartości przez szablon

Przed c++ 20, który wprowadza pewną nowość w tej dziedzinie był inny sposób na narzucenie ograniczeń.
Jest to forawrd declarations, czyli deklarowanie argumentów, jakie może przyjąć szablon. Niestety robi to odrobinę zamieszania, ponieważ tutaj musimy rozdzielić deklarację od implementacji 🙄. Zobaczmy przykład.

Plik nagłówkowy (.h)

```cpp
template <typename T>
T add(T a, T b);
```

Plik źródłowy (.cpp)

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

template int add<int>(int, int);
template double add<double>(double, double);
```

{{< space 2>}}

Oczywiście istnieje jeszcze inny sposób na blokowanie "niechcianych" argumentów już na etapie kompilacji. Jest to rzucanie błędów kompilacji (`static_assert`).

{{< space 5>}}

## RTTI - Run-TIme Type Identyfication

[Tutaj można o tym poczytać](https://cpp-polska.pl/post/dynamic-cast-oraz-type-id-jako-narzedzia-rtti?isPreview=true)

> RTTI (Run Time Type Information – informacja o typie w trakcie wykonywania programu) jest techniką stosowaną w nowoczesnych obiektowych językach programowania. Technika ta polega na dołączeniu do kodu programu informacji o typach (klasach), czasami też ich własnościach i dostępnych metodach.
>
> ## W C++
> W standardowym C++ każdej klasie towarzyszy struktura przechowująca nazwę klasy. Dostęp do niej uzyskuje się poprzez typeid.
>
> Wyrażenie typeid(e).name() zwraca nazwę klasy, do której należy obiekt e. Standard C++ nie określa co ma zawierać zwrócony napis.
> 
> [Wikipedia](https://pl.wikipedia.org/wiki/RTTI)

Przeanalizuj przykładowy kod:

```cpp
#include <typeinfo>

class A {

};

class B: public A {

};

int main() {
    int a;
    cout << "a: " << typeid(a).name() << endl;

    A aa;
    B bb;
    A& ab = bb;
    A* abptr = &bb;

    cout << "aa: " << typeid(aa).name() << endl;
    cout << "bb: " << typeid(bb).name() << endl;
    cout << "ab: " << typeid(ab).name() << endl;
    cout << "abptr: " << typeid(abptr).name() << endl;
    return 0;
}
```

Co program zwraca? Dlaczego tak? Możesz doczytać w przytoczonym artykule (zescrolluj do odpowiedniego miejsca).



{{< space 7 >}}

# Zadania

1. Stwórz funkcję `min`, `max`, które będą wykorzystywać mechanizm szablonów. Funkcja przyjmuje 2 argumenty i zwraca wartość.
2. Stwórz funkcję `find`, która jako parametr przyjmuje `vector` dowolnego typu i wyszukiwaną wartość i zwraca indeks poszukiwanego elementu. Jeżeli nie zostanie znaleziony, to -1.

3. Stwórz klasę `Triple`, która będzie przechowywać wartości 3 typów.
    * Posiada konstruktor bezparametryczny
    * Posiada konstruktor z 3 parametrami
    * Posiada gettery (`getFirst`, `getSecond`, `getThird`)
    * Posiada settery (`setFirst`, `setSecond`, `setThird`)
4. Stwórz klasę `MyArray`, która będzie tworzyć tablice dla elementów dowolnego typu. Wielkość tablicy powinna być przekazywana przez parametr szablonu. Klasa powinna być zgodna z zasadą RAII. Powinna posiadać metody:
    * `setValueOn(index, wartość)` - ustawianie wartości na podanym indeksie z wyrzucaniem wyjątku `out_of_range`
    * `getValoue(index)` - pobieranie wartości spod indeksu z wyrzucaniem wyjątku `out_of_range`
    * tylko konstruktor bezparametryczny



