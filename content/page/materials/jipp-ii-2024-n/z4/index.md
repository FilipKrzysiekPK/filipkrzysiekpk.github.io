---
title: "Języki i Paradygmaty Programowania II laboratorium 4"
layout: singleNoHeader
date: 2023-04-18
---

# Laboratorium 4

### Cele laboratorium i poruszane zagadnienia

* szablony funkcji
* szablony klas
* konwersje
* obsługa plików

{{< space 7 >}}

## Konwersja typów

Konwersja typów jest to bardzo potrzebne narzędzie, pozwala nam bezproblemowo zamieniać przykładowo liczbę całkowitą na zmiennoprzecinkową. W c++ występuje niejawna konwersja typów, czyli my nie musimy mówić dosłownie "Hej skonwertuj mi tę wartość na liczbę zmiennoprzecinkową". Jest to plus, a zarazem minus, może ona też w szczególnych przypadkach powodować różne błędy.

### Niejawna konwersja typów

Jest to automatyczna konwersja typu, która wykonuje się gdy typy "argumentów" są niezgodne. Dokonywana jest ona do typów wyższych zgodnie z kolejnością:

> bool -> char -> short int -> int -> unsigned int -> long int -> unsigned long int -> long long int -> float -> double -> long double 

Przykład:

```cpp
int main() {
    int a = 5;
    float b = 8;
    cout << (a + b) << endl;
    return 0;
}
```

{{< space 3 >}}

### Jawna konwersja typów

Jawna konwersja typów, to jest taka, gdzie mówimy kompilatorowi "Hej chcę to skompilować na taki typ". Ogromnym jej plusem jest uwidocznienie takiego zabiegu i powinniśmy dążyć do tego, aby minimalizować niejawną konwersję typów. Jako ciekawostka dodam, że Rust nie posiada niejawnej konwersji typów.

Wyróżniamy 4 rodzaje konwersji typów:

* `static_cast` - rzutowanie typów prostych, które nie są ani wskaźnikami, ani referencją
* `dynamic_cast` - rzutowanie wskaźników i klas na ich pochodne, można dokonywać tego w górę i w dół
* `const_cast` - rzutowanie stałych na zmienne, zmiennych na stałe lub stałych jednego typu na inny typ
* `reinterpret_cast` - rzutowanie umożliwiające zmianę dowolnego wskaźnika jednego typu na wskaźnik innego typu (bez konwersji danych)

Składnia rzutowania:

```cpp
typ_rzutowania<typ_docelowy>(element rzutowany);
```

Poniżej przykład prostego rzutowania typów prostych:

```cpp
int main()  
{  
    float f2 = 6.7;  
    int x = static_cast<int>(f2);  
    cout << "The value of x is: " << x;  
    return 0;  
} 
```

Rzutowanie obiektów:

```cpp

class Stream {

};

class FileStream: public Stream {

};

class ConsoleStream: public Stream {

};

class Logger {
    Stream *stream;

public:
    Logger(Stream *stream);
    Stream *getStream();
}

int main() {
    Logger mainLogger = Logger(new ConsoleStream);

    // do sth

    auto cStream = dynamic_cast<ConsoleStream *>(mainLogger.getStream());
    return 0;
}

```

{{< space 3 >}}

### Reinterpret cast

Jest to bardzo specyficzne rzutowanie, które omówię osobno, ponieważ jest całkowicie odmienne od pozostałych. Do tej poty każde rzutowanie pozostawiało nam taką samą wartość (przykładowo liczba 31), a zmieniało typ. Czyli, jeżeli byśmy sobie popatrzyli dokładniej, to zmieniał się zapis binarny, ale nie była dokonywana żadna zmiana wartości.

Reinterpret cast działa tutaj troszkę odmiennie. W żaden sposób nie modyfikuje wartości (zapisu binarnego) w pamięci, zmienia jedynie sposób jej interpretowania. Dlatego też jako argument szablonu podajemy wskaźniki.

Zobaczmy sobie to na poniższym przykładzie. 

*Do wypisania binarnie użyjemy bitset.*

```cpp
#include <bitset>
#include <iostream>

using namespace std;

int main() {
    int a = 100;
    char *p1 = reinterpret_cast <char*>(&a);

    cout << a << "\t" << bitset<10>(a) << endl;
    cout << *p1 << "\t" << bitset<10>(*p1) << endl;

    return 0;
}
```

Po uruchomieniu tego programu możemy zauważyć, że Na początku wypisze się nam wartość 100, a w drugim przypadku litera `d`, która odpowiada wartości 100 w tablicy asci. Zapis binarny obydwóch wartości jest taki sam.

Jak to zadziałało? Została dokonana konwersja wskaźnika, zamiast inetrpretować `int`, teraz zaczęliśmy go uznawać, jako `char`. Czyli nie zmieniamy postaci danych, tylko i wyłącznie zmieniamy sposób ich interpretowania.

Pamiętając (albo sprawdzając [dokumentację](https://en.cppreference.com/w/cpp/language/types)) wiemy, że `int` ma 16/32 bity, a `char` 8, co oznacza, że w jednym `int` zmieścimy 4 `char`. Używając przesunięcia bitowego dodajmy do zmiennej int znak z tabeli ASCI o numerze 103, a następnie wypiszmy te dane.

{{< space 2 >}}

Tutaj mamy inny przykład. niestety nie jest łatwo wypisać `float` binarnie (bez używania reinterpret_cast). Dokonujemy tutaj konwersji za pomocą `static_cast` i `reinterpret_cast` z `unsigned` do `float`, a następnie do `int`. Mogłoby się wydawać, że obydwa wyniki będą takie same, jednakże tak się nie dzieje.

```cpp
#include <bitset>
#include <iostream>

using namespace std;

int main() {
    unsigned a = 4294967295;
    
    float t_s = static_cast<float>(a);
    float t_d = *reinterpret_cast<float *>(&a);

    int a_staticCast = static_cast<int>(t_s);
    int a_reinterpretCast = *reinterpret_cast<int *>(&t_d);

    cout << "Original          " << a << "\t" << bitset<16>(a) << endl;
    cout << "Static cast:      " << a_staticCast << "\t" << bitset<16>(a_staticCast) << endl; 
    cout << "Reinterpret cast: " << a_reinterpretCast << "\t" << bitset<16>(a_reinterpretCast) << endl; 
    
    return 0;
}
```

{{< space 4 >}}


## Szablony

Pisząc różne funkcje, czy też tworząc klasę, czasem potrzebujemy, aby mogły funkcjonować na wszystkich typach danych, albo po prostu na typach, których nie znamy pisząc daną funkcjonalność. Załóżmy że chcemy stworzyć funkcję `min`. W momencie jej tworzenia nie wiemy na jakim typie danych użytkownik będzie z niej korzystał. Moglibyśmy przeciążyć ją każdym możliwym typem prostym, ale okazałoby się, że programista stworzyłby własny typ, który też można by było używać.

Może podejdźmy do tematu troszkę inaczej. Czy korzystaliśmy już gdzieś, gdzie bazowa klasa potrzebowała, aby jej przekazać typ? Może jakaś funkja? Gdy popatrzymy na poprzedni temat, to przykładowo:

```cpp
int a = 100;
float b = static_cast<float>(a);
```

Mamy rzutowanie, gdzie w nawiasach `< >` podawaliśmy typ. Jeszcze możemy przypomnieć sobie `vectory` do których przekazywaliśmy typ. Korzystaliśmy wtedy właśnie z szablonów (już gotowych).

### Czym są szablony?

Szablony możemy określić, jako "dodatkowa" lista parametrów, która jest wykonywana podczas kompilacji programu. W przeciwieństwie do "normalnych" parametrów, w szablonie możey przekazać typ.

{{< space 5 >}}

### Szablon funkcji

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


{{< html script src="//onlinegdb.com/embed/js/vwIoLCrn0?theme=dark" >}}

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

{{< space 7 >}}