---
title: Języki i Paradygmaty Programowania II laboratorium 4
layout: singleNoHeader
date: 2023-11-03
lastmod: 2025-05-10T05:41:25.721Z
---

# Laboratorium 4

### Cele laboratorium i poruszane zagadnienia

* debugowanie
* metody const
* zmienne i metody statyczne
* inline functions
* przeciążanie operatorów
* funkcje zaprzyjaźnione

{{< space 7 >}}

## Debugowanie

Najprostszym sposobem "debugowania" jest dodawanie `cout`, czy też `printf` w miejscach, gdzie podejrzewamy, że występuje błąd. Możemy jednak pójść o krok dalej i skorzystać z odpowiednich narzędzi, czyli debugera, który obsługuje się w podobny sposób niezależnie od środowiska programistycznego. Jeżeli mamy taką ochotę możemy też z debuggera skorzystać w konsoli. Najważniejszą zaletą debugowania jest możliwość podglądu wartości zmiennych w trakcie działania aplikacji oraz dokładne prześledzenie jej działania. Dzięki debugowaniu możemy też dowiedzieć się, w którym dokładnie miejscu aplikacja przerwała działanie. 

Podstawowym sposobem debugowania jest dodawania brakepointów, czyli takich punktów, w których aplikacja zatrzyma swoje działanie oraz podpatrywanie wartości zmiennych, czy też śledzenie linia po linii, jak ten kod się wykonuje. Aby dodać brakepoint kliknij obok numeru linii. Jeżeli chcemy zatrzymać wykonywanie programu w konkretnym momencie, np w 100 iteracji pętli, to możemy dodać warunkowy brakepoint. 

Większość bugów można wyśledzić za pomocą odpowiedniego debugowania wykorzystując powyższe metody, czyli brakepointy i śledzenie linia po linii wykonywanego kodu.

Wykryj błąd w aplikacji dostępnej na delcie.


{{< space 4 >}}

## Stałe metody

Do tej pory wszystkie metody, jakie tworzyliśmy oznaczaliśmy co najwyżej słowem kluczowym `override`, albo `virtual`. Teraz dodamy kolejne słowo kluczowe, które już wszyscy doskonale znamy, `const`.

**Po co oznaczać metodę, jako `const`?**

Dajemy w ten sposób informację kompilatorowi, że nie będziemy modyfikować żadnych pól/atrybutów klasy. Oczywiście kompilator nam nie ufa i nas sprawdzi. Jednakże kluczową funkcjonalnością w tym przypadku jest, gdy będziemy chcieli użyć stałego obiektu naszej klasy. Jeżeli jest ona stała, to nie możemy modyfikować w żaden sposób jej zawartości. Gdy nie oznaczymy metody jako `const`, to skąd obiekt ma wiedzieć, że nie modyfikujemy zawartości klasy? Właśnie po to się to robi 😊.

```cpp
// .h/.hpp file
class Example {
public:
    void unnecessary() const;
};

// .cpp file
Example::unnecessary() const {
    cout << "Jestem całkowicie niepotrzebną klasą i metodą stworzoną tylko i wyłącznie na potrzeby tego przykładu" << endl;
}
```

Przeanalizuj [przykładowy kod](https://wandbox.org/permlink/aZRIolgblhKtE6oz).

Działa on poprawnie. Ulepsz go zmieniając przekazanie parametru do funkcji `display` za pomocą stałej referencji. Czy kod dalej się kompiluje? Napraw go.

{{< space 5 >}}

## Inline functions

```cpp
inline int add(int a, int b) {
    return a + b;
}
```

Funkcje `inline functions` powinny działać troszkę szybciej od zwykłych, ponieważ ich zawartość powinna zostać wklejona w miejsce ich wywołania. Oczywiście nie jest aż tak kolorowo, ponieważ to kompilator zdecyduje, jak to będzie działać. Funkcje `inline` powinny być stosunkowo małe. Będą one użyteczne to stosować w przypadku, gdy walczymy o bardzo mocne zoptymalizowanie naszej aplikacji i liczy się każda operacja na procesorze.

**Uwaga**, funkcje inline muszą zostać zaimplementowane w pliku nagłówkowym. 

[Przykładowe omówienie tematu](https://pl.wikibooks.org/wiki/C%2B%2B/Funkcje_inline)

[Omówienie większej ilości przypadków](https://cpp0x.pl/dokumentacja/standard-C/inline/9)

[Dokumentacja](https://en.cppreference.com/w/cpp/language/inline)

{{< space 5 >}}

## Zmienne statyczne

Zmiennych statycznych nie da się uniwersalnie opisać, należy popatrzeć, gdzie są one używane. Ich częścią wspólną, to, że zmienna jest umieszczana w innej części pamięci (porównywalnej do tej globalnej).

### Zmienne statyczne w funkcjach

Uruchom poniższy program i sprawdź, jak on działa.

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

[Wandbox](https://wandbox.org/permlink/ipWE48x7XFcHBuC6)

Jak możemy zauważyć każde kolejne wywołanie funkcji `counter` zwiększało wartość `i` o 1. Zmienna ta nie była usuwana przy wychodzeniu z funkcji.

Właśnie na tym polegają zmienne statyczne. Jest ona tworzona jeden raz i usuwana, dopiero wraz z końcem działania programu. Jej działanie można przyrównać do zmiennej globalnej, lecz dostęp do niej jest tylko i wyłącznie z funkcji, w której została stworzona i **zainicjowana**.

{{< space 4 >}}

### Zmienne statyczne w klasach

Jak się zapewne możemy domyślić, zmienne statyczne w klasach będą działać podobnie, jednakże trzeba tu kilka rzeczy dopowiedzieć. Po pierwsze zmienna statyczna jest współdzielona pomiędzy obiektami. Jeżeli stworzymy 10 obiektów naszej klasy, to wszystkie będą miały taką samą wartość zmiennej statycznej, po zmodyfikowaniu jej w jednej klasie, zmodyfikuje się we wszystkich. Po drugie zmienną statyczną należy zainicjalizować poza klasą w pliku cpp. Po trzecie Możemy korzystać ze zmiennej statycznej klasy bez tworzenia obiektu klasy (oczywiście jeżeli jest ona w części publicznej). Zobaczmy przykład dla dwóch pierwszych przypadków.

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

#### inline static od c++17

Od c++17 nie trzeba osobno inicjalizować zmiennej statycznej jeżeli poprzedzi się ją słowem kluczowym `inline`.

```cpp
    inline static int myInt = 0;
```

{{< space 3 >}}

### Metody statyczne

Metody statyczne zyskują ostatnią z własności opisanych w poprzedniej części, czyli nie musimy tworzyć obiektu, aby móc z nich korzystać. Oczywiście są tutaj obostrzenia, nie możemy w takiej metodzie korzystać z pól niestatycznych.

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

## Funkcje zaprzyjaźnione

Dostęp do elementów klasy w C++ możemy realizować, tylko przez metody ewentualnie atrybuty publiczne. Jednakże może wystąpić sytuacja, gdy będziemy potrzebować, skorzystać z części prywatnej klasy w np. funkcji (poza klasą). Istnieje technika umożliwiająca nam taki dostęp. Jest nią zaprzyjaźnienie.

Funkcje zaprzyjaźnione mają pełny dostęp do wszystkich jej składowych (prywatnych i chronionych).
Aby z niej skorzystać, w klasie musimy **zadeklarować** przyjaźń. Implementacja funkcji powinna znajdować się poza deklaracją klasy. Występują różnice pomiędzy implementacją funkcji w klasie, a poza nią.

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

    friend Point getMiddlePoint(const Point &first, const Point &second);
};

Point getMiddlePoint(const Point &first, const Point &second) {
    return {(first.x + second.x) / 2, (first.y + second.y) / 2};
}
```

{{< space 5 >}}

### Przeciążanie operatorów

[Dokumentacja](https://en.cppreference.com/w/cpp/language/operators)

[WikiBooks](https://pl.wikibooks.org/wiki/C%2B%2B/Przeciążanie_operatorów)

[c++0x](https://cpp0x.pl/artykuly/Inne-artykuly/Przeciazanie-operatorow-w-C++/15)

[c++0x cz2](https://cpp0x.pl/kursy/Programowanie-obiektowe-C++/Podstawy/Operatory/498)

[Dokumentacja MS](https://docs.microsoft.com/pl-pl/cpp/cpp/operator-overloading?view=msvc-160)

Sam język dostarcza nam już wiele prostych typów danych. Co, jeżeli chcemy utworzyć złożony typ danych np. dla liczby zespolonej. Jak pamiętamy, składa się on z części zespolonej i rzeczywistej. Możemy na nim też wykonywać podstawowe operacje, takie jak dodawanie, odejmowanie itp. Moglibyśmy do tego definiować metody i je wywoływać, jednakże byłoby to bardzo nie wygodne, a przede wszystkim nieprzejrzyste.

Rozwiązaniem naszego problemu jest przeciążenie operatorów, czyli definiowanie dla nich zachowania. Mamy możliwość
 przeciążenie/przeładowanie następujących operatorów:
`+` `-` `*` `/` `%` `^` `&` `|` `~` `!` `=` `<` `>` `+=` `-=` `*=` `/=` `%=` `^=` `&=` `|=` `<<` `>>` `>>=` `<<=` `==` `!=` `<=` `>=` `<=>` (since C++20) `&&` `||` `++` `--` `,` `->*` `->` `( )` `[ ]` `new` `new[]` `delete` `delete[]`

Należy jednak wiedzieć, że nie wszystkie operatory możemy w dowolny sposób przeciążać. Szczegóły możesz poznać w dokumentacji (szczególnie zwróć uwagę na `->`).
Operatory `=`, `()`, `[]`, `->` muszą zostać przeciążone przez metodę klasy, nie mogą przez funkcję!

{{< space 3 >}}

Ogólny przepis na stworzenie implementacji dla przeładowania/przeciążenia operatorów w metodzie wygląda następująco:

`zwracanyTyp operator@(typPrawegoOperandu &nazwaPrawegoOperandu);`

Gdzie `@`, to operator, który chcemy przeciążyć.

{{< space 5 >}}

Teraz przeanalizujmy kilka przykładów. Co będą robić operatory w danych przypadkach?

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

W pierwszym przypadku zaimplementowaliśmy operator porównania. Zapewne zastanawiasz się, dlaczego jako argument została zdefiniowana nazwa `rhs`. Rozwijając ten skrót `right hand side`, czyli to, co stoi po prawej stronie od znaku, który przeciążamy. Jeżeli implementujemy przeciążenie operatora jako metodę, to zawsze po lewej stronie będzie nasza klasa. 

Rozwińmy naszą klasę bardziej i zaimplementujmy jeszcze operator `[]`, którego będziemy używać do wypisywania x i y, w zależności, co wpiszemy w środku.

```cpp
double operator[](char idx) {
    if (idx == 'x')
        return x;
    else if (idx == 'y')
        return y;
    else
        return 0;  //beter throw error
}
```

Rozważmy jeszcze jeden przykład, tym razem z przypisaniem. Niech to będzie operator `+=`, który będzie przesuwał nasz punkt o przekazaną wartość.

```cpp
Point &operator+=(double rhs) {
    x += rhs;
    y += rhs;
    return *this;
}
```

Można zauważyć, że zwracamy referencję do aktualnego obiektu. Przy przeciążeniu tego typu operatorów zazwyczaj się to robi, aby móc później wykorzystać tę wartość. Oczywiście moglibyśmy zrobić ten operator typu `void`, ale wtedy nie bylibyśmy w stanie zrobić czegoś w tym stylu:

```cpp
Point p2;
Point p3 = p2 += 5;
```

Można tutaj tworzyć wiele przykładów, ale teraz przejdźmy dalej i wykorzystajmy obydwie poznane dziś nowości. Przeciążmy operator przesunięcia bitowego `<<`. Tak ten magiczny operator wykorzystywany przez `cout` jest operatorem przesunięcia bitowego i właśnie zaimplementujemy go w sposób taki, aby nasza klasa działała z `cout`. Pierwszym problemem, który się pojawia, jest to, że po lewej stronie znaku nie występuje nasza klasa, tylko `std::ostream`. Aby rozwiązać ten problem, wykorzystamy funkcję zaprzyjaźnioną, dzięki temu możemy zadeklarować, co stoi po lewej stronie znaku i po prawej. Jak się zapewne domyślasz, jak było `rhs`, to teraz będzie `lhs`. Warto używać tych nazw, ponieważ mówią one dokładnie, z której strony znaku czerpiemy wartość.

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

    friend ostream &operator<<(ostream &lhs, const Point &rhs);
};

ostream &operator<<(ostream &lhs, const Point &rhs) {
    return lhs << rhs.x << " " << rhs.y;
}

int main() {
    Point p1;
    Point p2{5, 5};

    cout << p1 << endl;
    cout << p2 << endl;
    return 0;
}

```



