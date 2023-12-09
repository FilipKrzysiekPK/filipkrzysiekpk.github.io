---
title: "Języki i Paradygmaty Programowania II laboratorium 5"
layout: singleNoHeader
date: 2023-12-08
---

# Laboratorium 5

### Cele laboratorium i poruszane zagadnienia

* szablony
* static
* biblioteka STL

{{< space 7 >}}

# Zmienne statyczne

Zmiennych statycznych nie da się uniwersalnie opisać, należy popatrzeć, gdzie są one używane. Ich częścią wspólną, to, że zmienna jest umieszczana w innej części pamięci (porównywalnej do tej globalnej).

## Zmienne statyczne w funkcjach

Tym razem przed opisaniem zagadnienia uruchom poniższy program i sprawdź, jak działa.

```cpp
int counter() {
    static int i = 0;
    ++i;
    return i;
}

int main() {
    cout << "Wywołanie 1: " << counter() << endl;
    cout << "Wywołanie 2: " << counter() << endl;
    cout << "Wywołanie 3: " << counter() << endl;
    cout << "Wywołanie 4: " << counter() << endl;
    cout << "Wywołanie 5: " << counter() << endl;
    cout << "Wywołanie 6: " << counter() << endl;
    return 0;
}
```

Jak możemy zauważyć każde kolejne wywołanie funkcji `counter` zwiększało wartość `i` o 1. Zmienna ta nie była usuwana przy wychodzeniu z funkcji.

Właśnie na tym polegają zmienne statyczne. Jest ona tworzona jeden raz i usuwana, dopiero wraz z końcem działania programu. Jej działanie można przyrównać do zmiennej globalnej, lecz dostęp do niej jest tylko i wyłącznie z funkcji, w której została stworzona i **zainicjowana**.

{{< space 4 >}}

## Zmienne statyczne w klasach

Jak się zapewne możemy domyślić, zmienne statyczne w klasach będą działać podobnie, jednakże trzeba tu kilka rzeczy dopowiedzieć. Po pierwsze zmienna statyczna jest współdzielona pomiędzy obiektami. Jeżeli stworzymy 10 obiektów naszej klasy, to wszystkie będą miały taką samą wartość zmiennej statycznej, po zmodyfikowaniu jej w jednej klasie, zmodyfikuje się we wszystkich. Po drugie zmienną statyczną należy zainicjalizować poza klasą. Po trzecie Możemy korzystać ze zmiennej statycznej klasy bez tworzenia obiektu klasy. Zobaczmy przykład dla dwóch pierszych przypadków.

```cpp
class Foo {
    static int myInt;

public:
    void print() {
        cout << myInt << endl;
    }

    void updateMyInt(int n) {
        myInt += n;
    }
};

int Foo::myInt = 100;

int main() {
    Foo k1;
    Foo k2;
    Foo k3;
    Foo k4;
    Foo k5;

    k1.print();
    k2.print();
    k3.print();
    k4.print();
    k5.print();
    cout << "-------------------" << endl;

    k1.updateMyInt(15);

    k1.print();
    k2.print();
    k3.print();
    k4.print();
    k5.print();
    cout << "-------------------" << endl;

    k4.updateMyInt(-200);

    k1.print();
    k2.print();
    k3.print();
    k4.print();
    k5.print();

    return 0;
}
```

Aby zaprezentować ostatnią własność, należy zmienić modyfikator dostępu dla naszej zmiennej statycznej.


```cpp
class Foo {
public:
    static int myInt;

    void print() {
        cout << myInt << endl;
    }

    void updateMyInt(int n) {
        myInt += n;
    }
};

int Foo::myInt = 100;

int main() {
    cout << Foo::myInt << endl;

    Foo::myInt = 200;

    cout << Foo::myInt << endl;

    Foo k1;
    k1.updateMyInt(-200);

    cout << Foo::myInt << endl;

    return 0;
}
```

{{< space 3 >}}

## Metody statyczne

Metody statyczne zyskują ostatnią z własności opisanych w poprzedniej części, czyli nie musimy tworzyć obiektu, aby móc z nich korzystać. Oczywiści są tutaj obostrzenia, nie możemy w takiej metodzie korzystać z pól niestatycznych.

```cpp
class TestClass{
public:
    static void printHelloWorld() {
        cout << "Hello world!" << endl;
    }   
};

int main() {
    TestClass::printHelloWorld();
    return 0;
}
```

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

{{< space 4 >}}

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

{{< space 7 >}}

# Zadanie static

1. Stwórz klasę, `Foo`, która będzie zliczać ilość swoich instancji. Powinna ona posiadać metodę statyczną `getInstances`, która umożliwia wypisanie na ekran ich ilość.
2. Stwórz funkcja, która będzie przyjmować tekst (`string`) i wypisywać go na ekran oraz zliczać wystąpienia litery `a` i `z` (bez wielkich liter), przez cały okres działania programu. Funkcja powinna zwracać sumaryczną ilość wystąpień. (wypisywanie na ekran możemy realizować tylko i wyłącznie za pomocą tej funkcji).

{{< space 4 >}}

# Zadania szablony

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

{{< space 4 >}}

# Zadania 

1. Wypełnij tablicę/kontener losowymi liczbami. Znajdź w bibliotece standardowej funkcje sortujące i posortuj wcześniej stworzoną tablicę/kontener.
2. Stwórz prostą klasę (wszystko może być publiczne). Używając metody z biblioteki standardowej znajdź pierwszy element, który będzie taki sam.
3. (wersja trudniejsza) Znajdź pierwszy element, ktory będzie zawierać dane pole ze wskazaną wartością (przykladowo dla klasy student, znajdź pierwszego studenta, który będzie mieć nazwisko `Kowalski`).
