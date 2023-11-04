---
title: "Języki i Paradygmaty Programowania II laboratorium 3"
layout: singleNoHeader
date: 2023-11-03
---

# Laboratorium 3

### Cele laboratorium i poruszane zagadnienia

* inline functions
* przeciążanie operatorów
* funkcje zaprzyjaźnione
* przekazywanie obiektów do funkcji

{{< space 7 >}}

## Przekazywanie obiektów do funkcji

Jakie znasz sposoby przekazywania obiektów do funkcji? Tak, przez kopię, przez referencję i jeszcze jest przez wskaźnik. Z którego z nich najczęściej korzystasz? Zapewne przez kopię, domyslnie się z niego korzysta i praktycznie zawsze w taki sposób przekazuje się typy proste. Rozważmy prosty przypadek, masz jakiś kontener, albo ciąg znaków, który zajmuje 100 MB. Potrzebujesz przekazać go do funkcji, aby wykonać jakąś operację. Rozważmy pierwszy przypadek, przekazanie przez kopie. Jakie będą tego cechy i konsekwencje? Pierwsza, najprostsza rzecz, Zajmiemy 2x wiecej pamięci operacyjnej, a do tego czas/operacje procesora potrzebne do wykonania kopii. Podsumowując jest to bardzo zasobochłonne. Kolejny przypadek (troszkę je zgrupuję), przekazanie przez referencję, albo przez wskaźnik. Czy problem z zajęciem dodatkowej pamięci nadal występuje? Nie, dodatkowo nie musimy wykonywać operacji kopiowania pamięci. Pozostaje jeden problem modyfikując wartości w funkcji, modyfikujemy oryginał. Na to też mamy rozwiązanie, tworzymy stalą referencję/wskaźnik.

Przykład przekazania obiektu przez kopię:

```cpp
void fun(string txt) {
    //do sth
}

int main() {
    string txt;
    fun(txt);

    return 0;
}
```

Przykład przekazania stałej przez referencję:

```cpp
void fun(const string &txt) {
    //do sth
}

int main() {
    string txt;
    fun(txt);

    return 0;
}
```

{{< space 2 >}}

Oczywiście jeżeli będziemy chcieli modyfikować obiekt wewnątrz funkcji (i nie chcieli zmieniać oryginału), to przekazujemy go przez kopię. W innym przypadku praktycznie zawsze będziemy przekazywać obiekty (**nie typy proste**) jako stałe przez referencję, dzięki czemu w znaczacy sposób optymalizując nasz program.

Jeszcze raz podkreślam, dla optymalizacji nie przekazujemy typów prostych przez referencję, tylko typy złożone.

{{< space 5 >}}

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


{{< space 7 >}}

## Wyrażenia lambda

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

{{< space 4 >}}

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
void fun(function&lt;int( int )> f, int n) {
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


