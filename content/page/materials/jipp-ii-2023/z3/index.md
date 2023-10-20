---
title: "Laboratorium 2"
layout: singleNoHeader
date: 2023-10-10
---


# Laboratorium 2

### Cele laboratorium i poruszane zagadnienia

* Poznanie najprostszego kontenera
* Poznanie kolejnych udogodnień c++
* Parametry domyślne
* Przeciążenia
* Poznanie celu obiektowości
* Porównanie struktury z klasą
* Co to są i po co się stosuje gettery oraz settery

{{< space 7 >}}

## Najprostszy kontener

Najprostszym kontenerem, o którym wspomniemy na samym początku w oderwaniu od omawiania pozostałych jest `array`. Ogólnym i głównym celem kontenerów jest zastąpienie tablic dynamicznie alokowanych, przez co zmniejszenie możliwości wycieku pamięci.

Kontener `array` jest najprostszym z nich, posiada on stały rozmiar, który definiujemy przy jego tworzeniu. Dodatkowo przechowuje on także swój rozmiar, a dostęp do wartości jest realizowany dosłownie w taki sam sposób.

[Dokumentacja](https://en.cppreference.com/w/cpp/container/array)

```cpp
#include <array>
#include <iostream>

using namespace std;

int main() {
    array <int, 5> myArray;             //Tworzenie kontenera
    myArray[0] = 8;                     //Przypisywanie wartości do elementu na indeksie 0

    cout << myArray[0] << " Size: " << myArray.size() << endl;  //Dostęp do wartości i wypisanie rozmiaru

    // cout << myArray.at(8) << endl;   //Dostęp do wartości z wykorzystaniem metody at, która oferuje dodatkowe zabezpieczenia


    return 0;
}
```

{{< space 3 >}}

Kontener `vector` jest jednym z najczęściej stosowanych. Jego przewaga nad `array`, to dynamiczne dostosowywanie rozmiarów.

```cpp
#include <vector>
#include <iostream>

using namespace std;

int main() {
    vector <int> myVector;      //Deklaracja vectora, który będzie przechowywał int
    myVector.pushBack(5);       //Dodawanie elementu na koniec vectora
    myVector.pushBack(6);
    myVector.pushBack(15);

    cout << myVector[1] << endl;    //Wypisywanie zawartości pod pierwszym indekdem wektora

    return 0;
}
```

[Dokumentacja dla kontenerów](https://en.cppreference.com/w/cpp/container)

{{< space 4 >}}

## Kolejne udogodnienia c++

Czy zawsze musimy deklarować jakiego typu jest nasza zmienna? Poniekąd tak, poniekąd nie. C++ 11 wprowadza typ zastępczy `auto`, dokonuje on dedukcji typu na podstawie przypisywanej do niej wartości. Dlaczego i poco w języku silnie typowanym stworzono typ dedukowany? Odpowiedź jest prozaiczna, aby nie musieć tyle pisać i ułatwić sobie życie. W większości przypadków nie będziemy z nich korzystać przy typach prostych. Zazwyczaj będziemy sobie upraszczać życie, przy alokacji pamięci, czy też przechwytywaniu typu ze zwracanej funkcji.

Załóżmy poniekąd absurdalny przypadek. Chcemy stworzyć wskaźnik na kontener `array`. Aby tego dokonać musielibyśmy zrobić to w następujący sposób:
```cpp
std::array<int, 50> *tab = new std::array<int, 50>();
```

Używając słowa kluczowego `auto` możemy uniknąć powtarzania pisania tego samego:
```cpp
auto *tab = new std::array<int, 50>();
```

Kolejny bardzo częsty sposób wykorzystania za chwilę.

{{< space 3 >}}

Kolejną nowością jest range-based for loop, czyli pętla for bazująca na zakresie. Skraca ona znacznie zapis samej pętli i sposób dostępu do poszczególnych elementów. Korzystając z kontenerów będziemy dostawać dostęp do elementów za pomocą iteratorów. O takich szczegółach będziemy mówić na dalszych zajęciach, póki co skupmy się na samej pętli i tym, jak może nam ułatwić życie.

Bardzo ważna różnica względem zwykłego fora, to tutaj nie tworzymy zmiennej, aby dostawać się do wartości pod poszczególnymi indeksami, tutaj dostajemy konkretnie te elementy. Spójrzmy na pierwszy przykład:

```cpp
for (int i : {1, 2, 3}) {
    cout << i << "\t";
}
```

Był to zdecydowanie najprostszy przypadek takiej pętli. Znacznie ciekawszy jest bazujący na kontenerach, albo stringu:

```cpp
string myText = "Hello World or studenciaki";
for (auto ch: myText) {
    cout << ch << "\t";
}
```
Czy to można jeszcze ulepszyć? Oczywiście, że tak, wystarczy wykorzystać referencję, mamy wtedy możliwość modyfikowania bazowego stringa i oczywiście nasz kod działa optymalniej.

```cpp
string myText = "Hello World or studenciaki";
for (auto &ch: myText) {
    cout << ch << "\t";
}
```

Właśnie to jest przykład, gdzie będziemy najczęściej wykorzystywać dedukcję typu.

Bardzo ważne pytanie, czy zawsze będziemy mogli/będzie zasadne użycie range-based for?

{{< space 3 >}}

Inne udogodnienia, o których warto wiedzieć, że istnieją:

* [inteligentne wskaźniki](https://en.cppreference.com/book/intro/smart_pointers)
* [biblioteka chrono - rzeczy związane z czasem](https://en.cppreference.com/w/cpp/chrono)
* [prosta wielowątkowość](https://en.cppreference.com/w/cpp/thread/thread)
* [filesystem operacje na systemie plikowym](https://en.cppreference.com/w/cpp/filesystem)

{{< space 7 >}}

## Parametry domyślne funkcji

Wyobraźmy sobie funkcję, w której zakładamy, że użytkownik może często używać jakiejś wartości parametru. Przykładowo stwórzmy taką, która wypisuje przekazany tekst na ekran i dodaje na koniec znak.

```cpp
void textToScreen(const string &text, char lastCha = '\n') {
    cout << text << lastCh;
}
```

Dokonaj teraz testowego wywołania tej funkcji z jednym, a następnie z 2 parametrami. Co uzyskałeś?

Tu powinien być jakiś inny ciekawy przykład użycia, ale nie mam pomysłu na niego (można ich znaleźć bardzo dużo).

<br><br>

## Przeciązanie funckji

Funkcje można w C++ (ale nie w C) przeciążać. Oznacza to sytuację, gdy w tym samym zakresie są widoczne deklaracje/definicje kilku funkcji o tej samej nazwie, ale z różnymi parametrami. Na podstawie listy parametrów kompilator jest w stanie stwierdzić, o którą dokładnie chodzi.

Zastanów się, czy jesteśmy w stanie rozróżnić 2 funkcje, jeżeli różnią się tylko zwracanym typem? Jak popatrzymy się, początkowo stwierdzimy, że tak. Teraz zastanów się, czy wywołując jakąkolwiek funkcję, mówisz, że chcesz uzyskać taki zwracany typ. No nie. Mówisz: chcę funkcję o takiej nazwie, która ma parametry takiego typu (tu ich lista). Więc dwie funkcje, które różnią się tylko i wyłącznie zwracanym typem są błędnym przeciążeniem.

Sygnatura funkcji składa się z nazwy oraz liczby i typu parametrów nie licząc tych z wartościami domyślnymi. Typ funkcji, czyli typ zwracanej wartości nie zalicza sie do jej sygnatury.


PRZYKLAD:
```c++
  int fun(double x, int k = 0);
  double fun(double z);
```
To dwie deklaracje różnych funkcji, ale o takiej samej sygnaturze, a mianowicie o sygnaturze `fun(double)`. I rzeczywiście, wywołanie `fun(1.5)` byłoby najzupełniej legalnym i nie wymagającym żadnej niejawnej konwersji wywołaniem zarówno pierwszej, jak i drugiej z tych funkcji. Takie przeciążenie jest zatem nielegalne.

Natomiast:
```c++
double fun(int);
double fun(unsigned);
```
to deklaracje funkcji różniących się sygnaturą. Wywołanie `fun(15)` jest wywołaniem pierwszej z nich, bo `15` jest literałem wartości typu `int` i do przekształcenia tej wartości do typu `unsigned` potrzebna byłaby konwersja. Zatem takie przeciążenie jest prawidłowe.

Z drugiej strony, różne sygnatury nie są jeszcze warunkiem dostatecznym na legalność przeciążenia. Widzimy to na przykładzie funkcji.

```c++
void fun(int i);
void fun(int& i);
```
które mają różną sygnaturę, ale wywołanie 'fun(k)', gdzie 'k' jest typu 'int', może być traktowane jako wywołanie zarówno pierwszej, jak i drugiej z nich. Zatem takie przeciążenie byłoby nieprawidłowe. Podobnie nieprawidłowe byłoby przeciążenie:
```c++
void fun(int tab[]);
void fun(int * p);
```
lub
```c++
void fun(tab[3][3]);
void fun(tab[5][3]);
```
bo pierwszy wymiar tablicy wielowymiarowej nie ma znaczenia do określenia typu (jest i tak pomijany w tego rodzaju deklaracji/definicji). Natomiast:

```c++
void fun(tab[3][3]);
void fun(tab[3][5]);
```
prawidłowo deklaruje dwie przeciążone funkcje `fun`, gdyż tablice wielowymiarowe różniące się wymiarem innym niż pierwszy są różnych typów i pomiędzy tymi typami nie ma niejawnej konwersji.

{{< space 7 >}}

## Obiektowość

Zanim zaczniemy próbować zrozumieć obiektowość wykonajmy ćwiczenie.

1. Zapisz informacje o osobie: imię, nazwisko, wiek, wzrost, waga, ulubiony kolor, ilość pieniędzy w portfelu, ilość punktów (domyślnie 0).
2. Stwórz funkcję, która wczytuje te informacje z ekranu.
3. Stwórz funkcję wypisującą wszystkie informacje o osobie na ekran.
4. Stwórz funkcję obliczającą ilość punktów na podstawie znanych danych (wymyśl jak, dowolnie).
5. Stwórz funkcję tworzącą kod osoby na podstawie imienia, nazwiska i wieku (inicjały + wiek).
3. Stwórz 3 osoby.
7. Wywołaj dla nich te funkcje.

Jakie masz pomysły, na wykonanie tego ćwiczenia?

<br>

Jakie masz przemyślenia po tym ćwiczeniu? Zauważyłeś, że większości funkcji przekazywałeś dokładnie te same parametry?

W zależności od pomysłu albo przekazywałeś całą strukturę, albo każdy osobno.

<br>

### Odrobina teorii

Czas na odrobinę teorii, a raczej przypomnienia sobie struktury.

> Struktura jest to deklaracja nowego typu, który może przechowywać w sobie wiele różnych typów, czy też funkcji. Cała zawartość funkcji jest publiczna.

Jeżeli już to sobie przypomnieliśmy, to możemy przejść dalej i dostrzec główną różnicę pomiędzy klasami, a strukturami.

C++ posiada wszystko, co niezbędne do praktycznej realizacji idei programowanie zorientowanego obiektowo. Pisanie programu zgodnie z filozofią OOP(Object-Oriented Programming) polega na definiowaniu i implementowaniu odpowiednich klas oraz tworzeniu z nich obiektów i manipulowaniu nimi. Klasa jest więc dla nas pojęciem kluczowym, które na początek wypadałoby wyjaśnić. 

> Klasa jest to złożony typ zmiennych, składający się z pól, przechowujących dane, oraz posiadający metody, wykonujące zaprogramowane czynności. Zmienne należące do tych typów obiektowych nazywamy obiektami. 

Każda klasa posiada pola (zmienne) oraz metody (funkcje).

Określenie klasy jest najczęściej rozdzielone na dwie części:

- definicję, wstawianą w pliku nagłówkowym, w której określamy pola klasy oraz wpisujemy prototypy jej metod (taki przepis na klasę)
- implementację, umieszczaną w module, będącą po prostu kodem wcześniej zdefiniowanych metod

Układ ten nie dość, że działa nadzwyczaj dobrze, to jeszcze realizuje jeden z postulatów programowania obiektowego, jakim jest ukrywanie niepotrzebnych szczegółów.


Przyjrzymy się składni definicji klasy:

```c++
class nazwa_klasy
{
[ specyfikator_dostępu : ]
[ pola ]
[ metody ]
};
```

<br>

**Modyfikatory dostępu**

Pierwszym elementem, który widzimy w naszym przykładzie jest `specyfikator_dostępu`. Służą one do ukrywania elementów (pól i metod), które nie muszą być udostępniane publicznie. Możemy wyróżnić:
* `private` - elementy są prywatne, dostępne tylko i wyłącznie z klasy, w której zostały zdefiniowane
* `protected` - dostęp do tych elementów mają dodatkowo klasy, które dziedziczą po naszej klasie
* `public` - dostęp do elementów mają wszyscy

Danym specyfikatorem objęte są wszystkie następujące po nim części klasy, aż do jej końca lub kolejnego specyfikatora. Ich ilość nie jest bowiem niczym ograniczona. Domyślnym specyfikatorem dostępu jest `private`, czyli, jeżeli po zadeklarowaniu klasy nie utworzymy żadnego specyfikatora, to elementy będą prywatne dopóki nie zostanie to zmienione innym specyfikatorem dostepu.

PRZYKŁAD:

```cpp
#include <string>

class PrzykladowaKlasa
{
public:
    double liczba; //prawo dostępu: publiczne
    char tablica[20]; //prawo dostępu: publiczne
    
private:
    int abc; //prawo dostępu: prywatne
    char znak; //prawo dostępu: prywatne
    std::string napis; //prawo dostępu: prywatne
};

int main()
{
    PrzykladowaKlasa nazwaZmiennej;
    return 0;
}
```

Przykład 2:

```cpp
class MyClass {
    string name;
    string lastName;
    int age;

public:
    void readData();
    void printData();
};

//plik cpp
void MyClass::readData() {
}

int main() {
    MyClass person1;
    person1.readData();
    return 0;
}
```

<br>

### Różnica pomiędzy klasą a strukturą

Mam nadzieję, że udało Ci się już dostrzec główną różnicę pomiędzy klasą, a strukturą. W strukturze wszystko jest publiczne.

<br>
<br>
<br>

## Zadanie domowe

Zadanie domowe znajduje się na delcie.