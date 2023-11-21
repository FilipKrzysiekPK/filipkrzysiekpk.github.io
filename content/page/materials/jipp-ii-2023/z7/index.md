---
title: "Laboratorium 6"
layout: singleNoHeader
date: 2023-11-20
---


# Laboratorium 5

### Cele laboratorium i poruszane zagadnienia

* Operator przypisania
* Zmienne statyczne
* Metody statyczne
* Wyrażenia lambda
* Typ wyliczeniowy
* Konwersja typów


{{< space 7 >}}


# Operator =

Osobno dodatkowo omówimy operator przypisania, ponieważ wymaga on szczególnej uwagi. Skorzystajmy z kodu z poprzednich zajęć:

```cpp
class Point {
    double x;
    double y;

public:
    Point() : 
    x(0), y(0) {}

    Point(double x, double y):
    x(x), y(y) {}

    void print() {
        cout << x << " " << y << endl;
    }

    bool operator==(const Point &rhs) {
        return x == rhs.x && y == rhs.y;
    }
};

int main() {
    Point p1;
    Point p2{5, 5};
    Point p3;

    cout << (p1 == p2) << endl;
    cout << (p1 == p3) << endl;
    return 0;
}
```

Czy klasa ma domyślnie zdefiniowany operator przypisania? Sprawdźmy to, dopisz po `Point p3;`:

```cpp
Point p5(82, 13);
Point p4 = p5;
p3 = p5;

p4.print();
p3.print();
p5.print();
cout << "------" << endl;
```

Wszystko się poprawnie skompilowało, więc tak operator przypisania jest domyślnie zaimplementowany. Dodajmy teraz konstruktor kopiujący:

```cpp
Point (const Point &point) {
    cout << "Copy constructor" << endl;
    x = point.x;
    y = point.y;
}
```

Czy używamy gdzieś to konstruktora kopiującego? Mogłoby się wydawać, że nie, ale gdy ponownie uruchomisz program, to nagle na ekranie pojawi się `Copy constructor`. Operacja `Point p4 = p5` jest wywołaniem konstruktora kopiującego, ponieważ `p4` nie istnieje, więc należy go zainicjalizować, dlatego właśnie jest wywoływany konstruktor kopiujący.

Co z bezpieczeństwem pamięci? Dopisz wskaźnik alokowany w konstruktorze, dealokowany w destruktorze i oczywiście kopiowany w konstruktorze kopiującym. Dopisz też do `print` wypisanie wartości i adresu na ekran.

Okazuje się, że pamięć zostaje podwójnie zwolniona. Możemy zablokować przypisanie po przez usunięcie operatora przypisania:

```cpp
Point &operator=(const Point &rhs) = delete;
```

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

{{< space 5 >}}

# Wyrażenia lambda

Wyrażenie lambda można określić, jako lokalna funkcja pomocnicza. Pozwala ona na oczyszczenie kodu z funkcji, które są bardzo krótkie i są wykorzystywane tylko i wyłącznie w jednym miejscu.

Dodatkowym atutem jest dość łatwe zapisanie ich w zmiennych, lecz funkcje też da się przypisać do zmiennej, chociaż trzeba nieco więcej pokombinować.

```cpp
cout << [](int a, int b) -> int {
    if ( a > b )
        return a;

    return b;
} (10, 15) << endl;
```

Powyżej możemy zauważyć bardzo prosty przykład takiej funkcji. Składa się ona z:

* `[]` - oznaczają one początek wyrażenia lambda i definiują przechwytywane nazwy (captures)
* `()` - lista parametrów, analogicznie jak w funkcji (opcjonalne)
* atrybuty - pomijamy
* `-> T` - typ zwracany przez wyrażenie lambda (opcjonalne)
* `{}` - ciało wyrażenia lambda, które jest analogiczne do funkcji


`()` - nawiasy na końcu służą do wywołania wyrażenia, nie są jego częścią!

Jeżeli nie określimy zwracanego typu, kompilator dedukuje sam, jaki typ jest zwracany. Spójrzmy na poniższy kod.

```cpp
cout << [](int a, int b) {
    if ( a > b )
        return a;

    return b * 1.0;
} (10, 15) << endl;
```

Nie ma wyspecyfikowanego typu zwracanego, a zwracane są 2 różne typy. Skończyło się to zwróceniem błędu kompilacji:

```console
main.cpp: In lambda function:
main.cpp:30:16: error: inconsistent types ‘int’ and ‘double’ deduced for lambda return type
   30 |     return b * 1.0;
      |                ^~~
```

Jeżeli dodamy deklarację zwracanego typu, wtedy kod się poprawnie skompiluje i zwróci wartość.

{{< space 2 >}}

### Przechwytywanie nazw (captures)

Przechwytywanie nazw, a raczej przekazywanie zmiennych do wyrażenia lambda. Pozwala ono przekazać zadeklarowaną wcześniej zmienną przekazać do wyrażenia lambda. Możemy tego dokonać na 2 sposoby, przekazując przez wartość, albo referencję.

```cpp
int main() {
    int a = 0;
    int b = 4;

    cout << [a, b] { return a + b }() << endl;

    return 0;
}
```

Jeżeli chcemy przekazać wszystkie zmienne, to możemy użyć:

* `[=]` - przekaż wszystko przez wartość
* `[&]` - przekaż wszystko przez referencję

Oczywiście o wiele lepiej jest wymieniać wszystkie zmienne, które chcemy tak przekazać, ponieważ zwiększa to czytelność i intencje. 

Warto jeszcze wspomnieć, że przy przekazywaniu wszystkiego możemy wyspecyfikować, że któryś element ma zostać przekazany w inny sposób:

* `[=, &a]` - przekaż wszystko przez wartość, ale a przez referencję
* `[&, a]` - przekaż wszystko przez referencję, ale a przez wartość

{{< space 2 >}}

### Przekazywanie wyrażeń lambda

Jak już wcześniej było wspomniane, wyrażenie lambda możemy zapisać do zmiennej. Zapewne użyjemy wtedy słowa kluczowego `auto`, ponieważ nie będziemy musieli się męczyć z jego definiowaniem. Jednakże tworząc deklarację funkcji, już tak postąpić nie możemy. Aby móc z tego korzystać, musimy załączyć bibliotekę `functional`.

```cpp
void fun(function<int( int )> f, int n) {
    cout << f(n) << endl;
}

int main() {
    fun([]( int x ) { return x; }, 5);
    fun([]( int x ) { return x * x; }, 5);
    return 0;
}
```

{{< space 2 >}}

### Kiedy ich używać, jest to w ogóle potrzebne?

Najlepszym przykładem, kiedy używać, jest funkcja z biblioteki standardowej `sort`, która jako ostatni argument przyjmuje funkcję, albo wyrażenie lambda. Pozwala nam to wtedy nie definiować dodatkowej funkcji potrzebnej tylko i wyłącznie dla zdefiniowania, który obiekt ma się znaleźć po prawej, a który po lewej stronie. Dodatkowo w takim przypadku bez definiowania operatorów można posortować zadeklarowane klasy według własnego konceptu.

{{< space 6 >}}

# Typ wyliczeniowy

Rozważmy przypadek, gdzie nasza funkcja ma kilka trybów działania. Tryb definiujemy poprzez przekazywany do niej parametr. Jakiego typu być do użył?

`int` ? No wtedy musimy wiedzieć, którą konkretnie wartość podać. `string` ? No ładnie widzimy nazwę trybu, ale musimy nadal znać wartość. Rozwiązaniem tego problemu są typy wyliczeniowe.

```cpp
enum mode {
    WRITE,
    READ,
    DANCE
};
```

Dzięki użyciu takiego typu programista będzie widzieć w środowisku programistycznym podpowiedzi nazw, a nie będzie musieć strzelać wartościami.

Jak pewnie zauważyłeś „wartościom” typów wyliczeniowych, są przypisywane wartości liczbowe, każda kolejna deklaracja posiada zwiększoną o 1 wartość liczbową.

```c++
enum dni {
    PON,
    WT,
    SR = 0,
    CZW = 0,
    PT = 0,
    SO,
    N
};

int main() {
    dni actualDay = PON;
    cout << actualDay << endl;
    
    actualDay = WT;
    cout << actualDay << endl;
    
    actualDay = SR;
    cout << actualDay << endl;
    
    actualDay = CZW;
    cout << actualDay << endl;
    
    actualDay = PT;
    cout << actualDay << endl;
    
    actualDay = SO;
    cout << actualDay << endl;
    
    actualDay = N;
    cout << actualDay << endl;
    
    actualDay = PON;
    dni actualDay1 = WT;
    
    cout << endl << "PON == WT" << endl;
    cout << (actualDay == actualDay1) << endl;
    
    actualDay = PON;
    actualDay1 = SR;
    
    cout << endl << "PON == SR" << endl;
    cout << (actualDay == actualDay1) << endl;
}
```

{{< space 7 >}}

# Konwersje typów

Konwersji typu dokonujemy stosunkowo często, nawet nieświadomie. Przykładem tego może być dodawanie do siebie dwóch liczb, pierwszej typu `int`, a drugiej typu `double`. W takim przypadku zachodzi niejawna konwersja typu. Oczywiście możemy wyróżnić jeszcze jawną konwersję typu przykładowo:

```cpp
int a = 5;
cout << (double) a << endl;
```

## Niejawna konwersja typu

Jest to automatyczna konwersja typu, która jest przeprowadzana przez kompilator bez konieczności wykonania jakiejkolwiek akcji przez użytkownika. Typy są zmieniane zgodnie z kolejnością:

```console
bool -> char -> short int -> int -> unsigned int -> long int -> unsigned long int -> long long int -> float -> double -> long double 
```

Przykład:

```cpp
int main() {
    int a = 5;
    float b = 8;
    cout << (a + b) << endl;
    return 0;
}
```

Myślę, że w tym przypadku nie ma potrzeby większego opisywania

{{< space 3 >}}

## Jawna konwersja typu

Jawna konwersja typu, to jest taka, która wymaga od użytkownika jej deklaracji. Możemy wyróżnić 4 typy rzutowania:

* `static_cast` - rzutowanie typów prostych, które nie są ani wskaźnikami, ani referencją
* `dynamic_cast` - rzutowanie wskaźników i klas na ich pochodne, można dokonywać tego w górę i w dół
* `const_cast` - rzutowanie stałych na zmienne, zmiennych na stałe lub stałych jednego typu na inny typ
* `reinterpret_cast` - rzutowanie umożliwiające zmianę dowolnego wskaźnika jednego typu na wskaźnik innego typu (bez konwersji danych)

Rozważmy kilka przykładów prostych rzutowań:

**static_cast**

```cpp
int main ()  
{  
    float f2 = 6.7;  
    int x = static_cast <int> (f2);  
    cout << " The value of x is: " << x;  
    return 0;  
} 
```

Rzutowanie jest dokonywane za pomocą funkcji `static_cast`, gdzie w nawiasach kwadratowych jest podawany typ, na jaki są rzutowane dane. Takie rzutowanie pozwala na dokładne wyrażenie tego, co ma na myśli programista.

{{< space 4 >}}

## `reinterpret_cast`

Rzutowanie `reinterpret_cast` jest warte osobnego omówienia, ponieważ jest ono zupełnie odmienne od pozostałych.
Nie zmienia ono wartości, a jedynie typ. Rozważmy przykład:

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

```cpp
#include <bitset>
#include <iostream>

using namespace std;

int main() {
    int a = 100 + (103 << 8);
    char *p1 = reinterpret_cast <char*>(&a);

    cout << a << "\t" << bitset<16>(a) << endl;
    cout << *p1 << "\t" << bitset<16>(*p1) << endl;
    cout << *(p1 + 1) << "\t" << bitset<16>(*(p1 + 1)) << endl;

    return 0;
}
```

`bitset` słuzy do wypisywania w binarnej postaci przekazanych elementów.

Rozważmy jeszcze jeden przykład, który nam pokaże przykładowe zastosowanie.

```cpp

struct sample{
    float f;
    long k;
    bool b;
};

void saveSomeValues(char **buff) {
    struct sample s1{};
    s1.f = 3.14;
    s1.k = 100;
    s1.b = true;

    cout << s1.f << endl;
    cout << s1.k << endl;
    cout << s1.b << endl;

    char *temp = reinterpret_cast <char *> (&s1);

    *buff = new char[sizeof(struct sample)];
    for(int i = 0; i < sizeof(struct sample); ++i) {
        (*buff)[i] = temp[i];
    }
}

void readSomeValues(char *buff) {
    auto *s1 = reinterpret_cast<struct sample *> (buff);

    cout << s1->f << endl;
    cout << s1->k << endl;
    cout << s1->b << endl;
}

int main() {
    char *buff = nullptr;
    saveSomeValues(&buff);

    cout << "------------------------" << endl;

    readSomeValues(buff);

    cout << "------------------------" << endl;

    return 0;
}
```

W funkcji `saveSomeValues` tworzymy strukturę i przypisujemy do nich wartości. Następnie za pomocą `reinterpret_cast` konwertujemy wskaźnik ze struktury na `char` i tworzymy tablicę oraz kopiujemy wartości do przekazanego przez wskaźnik wskaźnika. Następnie wracamy do funkcji `main` i wywołujemy odczytanie wartości. Za pomocą `reinterpret_cast` przywracamy oryginalny typ danym i wypisujemy ich zawartość na ekran.

{{< space 7 >}}

# Zadania

1. Stwórz klasę, `Foo`, która będzie zliczać ilość swoich instancji. Powinna ona posiadać metodę statyczną `getInstances`, która umożliwia wypisanie na ekran ich ilość.
2. Stwórz funkcja, która będzie przyjmować tekst (`string`) i wypisywać go na ekran oraz zliczać wystąpienia litery `a` i `z` (bez wielkich liter), przez cały okres działania programu. Funkcja powinna zwracać sumaryczną ilość wystąpień. (wypisywanie na ekran możemy realizować tylko i wyłącznie za pomocą tej funkcji).
3. Stwórz funkcję wyszukującą maksimum dla przesłanego równania. Równanie powinno zostać przekazane za pomocą wyrażenia lambda. (`lambda(double)`, `start x`, `end x`, `krok` - wszystko typu `double`, zwraca `double`). (jeżeli start = 1, end = 1.5, a krok = 0.2, to dla 1.5 nie trzeba sprawdzać).
   1. Przetestuj działanie dla kilku równań.
   2. Przetestuj działanie dla równań z przesłanymi dodatkowymi wartościami przez przechwytywanie nazw (captures).
4. Stwórz klasę `Variable`, która będzie posiadać metody:
   * `getInt` - pobieranie wartości, jako int
   * `getDouble` - pobieranie wartości, jako double
   * `getChar` - pobieranie wartości, jako znak
   * `getShort` - pobieranie wartości, jako short
   * `getUnsigned` - pobieranie wartości, jako unsigned
   * analogiczne settery
  
  Zadaniem klasy jest możliwość przechowywania różnych typów zmiennych. Do zapisu danych w klasie należy użyć jednego pola. Nie można konwertować typow p(int -> double itp), należy uży `reinterpret_cast`.





