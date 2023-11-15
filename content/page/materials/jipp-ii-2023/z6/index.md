---
title: "Laboratorium 5"
layout: singleNoHeader
date: 2023-11-11
---


# Laboratorium 5

### Cele laboratorium i poruszane zagadnienia

* Operatory
* Funkcje zaprzyjaźnione
* Funkcje inline

{{< space 7 >}}


## Inline functions

Do zrozumienia problematyki posłużymy się [aplikacją zamieniającą kod na assembler](https://godbolt.org/).

Wklej następujący kod i przeanalizuj, czy są jakieś różnice pomiędzy metodą `inline`, a zwykłą.

```cpp
class Point
{
public:
    // Define "accessor" functions as
    //  reference types.
    unsigned& x();
    unsigned& y();
private:
    unsigned _x;
    unsigned _y;
};

inline unsigned& Point::x()
{
    return _x;
}
unsigned& Point::y()
{
    return _y;
}
int main()
{
    Point p1;
    p1.x();
    p1.y();
    return 0;
}
```

Można zauważyć, że w metodzie `y()` jest wykonywana dodatkowa operacja. Może to nie przedstawiać całości działania tej funkcjonalności, ponieważ następuje tutaj kompilacja w trybie debugowania, a nie z włączonymi optymalizacjami.

Bardziej teoretycznie patrząc na temat, `inline functions` powinny działać troszkę szybciej od zwykłych, ponieważ ich zawartość powinna zostać wklejona w miejsce ich wywołania. Oczywiście nie jest aż tak kolorowo, ponieważ to kompilator zdecyduje, jak to będzie działać. Funkcje `inline` powinny być stosunkowo małe. Będziemy to stosować w przypadku, gdy walczymy o bardzo mocne zoptymalizowanie naszej aplikacji i liczy się każda operacja.

Uwaga, funkcje inline muszą zostać zaimplementowane w pliku nagłówkowym.

[Przykładowe omówienie tematu](https://pl.wikibooks.org/wiki/C%2B%2B/Funkcje_inline)

{{< space 5 >}}

# Funkcje zaprzyjaźnione

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

## Przeciążanie operatorów

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

{{< space 5 >}}

## Zadania do zrobienia na zajęciach

1. Dla przykładu 1 (klasa point i funkcja zaprzyjaźniona) zaktualizuj i dopisz drugą funkcję zaprzyjaźnioną, która będzie liczyć dystans pomiędzy punktami.
2. Dodaj do klasy operatory `==`, `!=`.
3. Stwórz operator `<<` oraz `>>`.
4. Sprawdź działanie.
5. Stwórz klasę do przechowywania liczby zespolonej.
6. Stwórz operatory `<<` i `>>`.
7. Stwórz operator `==` i `!=`.
8. Stwórz operator `+` oraz `+=`.


