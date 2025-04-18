---
title: Języki i Paradygmaty Programowania II laboratorium 2
layout: singleNoHeader
date: 2023-10-25
lastmod: 2025-03-29T12:53:07.113Z
categories:
    - jipp2-2025-n
---

# Laboratorium 2

### Cele laboratorium i poruszane zagadnienia

* kontenery
* konstruktor kopiujący
* sposoby przekazywania elementów do funkcji
* usuwanie konstruktora przenoszenia
* dziedziczenie
* polimorfizm

{{< space 7 >}}

## Badanie wydajności

Poniżej znajduje się archiwum z plikami zawierającymi klasę służącą do mierzenia czasu oraz plik main z zainicjalizowanym generatorem liczb pseudolosowych. Przykład użycia:

```cpp
    ElapsedTimer elTimer;
    elTimer.start();
    //do sth

    elTimer.stop();

    cout << "Elapsed: " << elTimer.elapsedInUs() << " us.\n";
```

Klasa posiada także możliwość obliczania średniej zmierzonych czasów. Resetu zliczania można dokonać za pomocą metody `reset()`:

```cpp
    ElapsedTimer elTimer;
    for (int i = 0; i < 1000; ++i) {
        elTimer.start();
        //do sth

        elTimer.stop();
    }

    cout << "Avg. elapsed: " << elTimer.getAverageTimeInUs() << " us.\n";
```

Zwracany czas może być w:

* nanosekundach (`getAverageTimeInNs` i `elapsedInNs`)
* mikrosekundach (`getAverageTimeInUs` i `elapsedInUs`)
* milisekundach (`getAverageTimeInMs` i `elapsedInMs`)

{{< space 3 >}}

**Uwaga to są tylko pliki źródłowe, należy je dodać do projektu**

[Pobierz](/simpleTimer.zip)

### Na co zwrócić uwagę przy badaniu wydajności

Podczas badania wydajności działania aplikacji należy zwrócić uwagę, aby była ona kompilowana w trybie release. Najważniejszą rzeczą, jest wielokrotne powtarzanie badanego scenariusza, ponieważ podczas pojedynczej próby czas procesora może zostać zajęty przez inną aplikację, co zaburzy wyniki.

## Kontenery - badanie wydajności

Przebadaj wydajność zapełniania kontenera w różne sposoby. Badanym kontenerem będzie `std::vector<double>`, który przechowuje liczby zmiennoprzecinkowe podwójnej precyzji. Porównaj czas wypełniania kontenera 1 000 000 liczbami pseudolosowymi dla przypadków:

* wypełnianie za pomocą `push_back`, kompilacja w trybie debug
* wypełnianie za pomocą `push_back`, kompilacja w trybie release
* najpierw zarezerwowanie miejsca ([`reserve`](https://en.cppreference.com/w/cpp/container/vector/reserve)), a następnie wypełnianie za pomocą `push_back`, kompilacja w trybie debug
* najpierw zarezerwowanie miejsca ([`reserve`](https://en.cppreference.com/w/cpp/container/vector/reserve)), a następnie wypełnianie za pomocą `push_back`, kompilacja w trybie release

{{< space 5 >}}

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

### Realne porównanie wydajności

Korzystając z wcześniej przeprowadzonych testów stwórz funkcję sumującą wszystkie elementy wewnątrz kontenera, a następnie zwracającą wynik tego działania. Przetestuj wydajność przy przekazywaniu kontenera przez:

* kopię
* stałą referencję
* wskaźnik

Badania dokonuj kompilując w trybie release.

{{< space 5 >}}

## Konstruktory kopiujące

Zanim zaczniemy omawiać tematykę konstruktorów przebadajmy to zjawisko. Naszym scenariuszem testowym będzie:

* stworzenie punktu `p1`
* stworzenie punktu `p2` na podstawie `p1`
* wyświetlenie punktów
* zmodyfikowanie punktu `p1`
* wyświetlenie punktów

Pierwszą badana klasa punktu: 

```cpp
class PointXY {
    double x;
    double y;

public:
    PointXY(double x, double y): x(x), y(y) {}

    void print() {
        cout << "x: " << x << "   y: " << y << endl;
    }

    void updatePoint(double x, double y) {
        this->x = x;
        this->y = y;
    }
};
```

Druga badana klasa:

```cpp
class Point {
    double *cords;
    const unsigned size;

public:
    Point(unsigned size): size(size) {
        cords = new double[size];
    }

    void print() {
        for (unsigned i = 0; i < size; ++i) {
            cout << i << ": " << cords[i] << endl;
        }
    }

    void updatePoint(unsigned index, double value) {
        cords[index] = value;
    }
};
```

Jakie anomalie zauważyłeś?{{< rawhtml >}}<sup id="answear1Sourece">{{< /rawhtml >}}[1](#answear1){{< rawhtml >}}</sup>{{< /rawhtml >}}

Uruchom błędnie działający przykład w trybie debugowania, w którym miejscu jest wyrzucany błąd?

Dopisz destruktor, z wypisaniem informacji, że jest niszczony obiekt.

Dodaj do destruktora, zwalnianie pamięci dla zmiennej `cords`. Co się stało? Czy program działa poprawnie i dlaczego?{{< rawhtml >}}<sup id="answear2Sourece">{{< /rawhtml >}}[2](#answear2){{< rawhtml >}}</sup>{{< /rawhtml >}}

### Deklaracja

Deklaracja konstruktora kopiującego wygląda podobnie do zwykłego, tylko tyle, że przekazywana w parametrze (przez referencję — z `&`) jest druga klasa. Jej ciało powinno zawierać w sobie przepisanie wartości i jeżeli występuje taka konieczność **zaalokowanie** pamięci.

```c++
ClassName(ClassName &className) {
    //copy values
}
```

Taki konstruktor można wywołać, tak samo, jak inny konstruktor, albo pop przez przypisanie wartości za pomocą znaku = (tylko przy deklarowaniu obiektu (zmiennej)).

```c++
ClassName c1;
ClassName c2(c1);
Classname c3 = c1;
```

Stwórz konstruktor kopiujący we wcześniej analizowanym przykładzie. Sprawdź, czy program teraz działa poprawnie.

{{< space 5 >}}

### Konstruktor kopiujący teoria
**To samo, co przed chwilą, tylko opisane, a nie zademonstrowane na przykładzie.**

Konstruktory kopiujące mają za zadanie wykonywanie kopii obiektu. Kompilator domyślnie tworzy konstruktory kopiujące, lecz dokonuje kopii wartości każdej zmiennej. Problem z jego działaniem zaczyna być widoczny przy alokowaniu pamięci w klasie, ponieważ domyślny konstruktor kopiujący kopiuje wskaźnik, a nie alokuje na nowo pamięć i dopiero później dokonuje kopii.

Konstruktor kopiujący jako parametr przyjmuje **referencję** do klasy tego samego typu jakiej jest. W jej ciele należy samemu, ręcznie zadeklarować kopiowanie każdej wartości, bądź alokowanie pamięci.

```c++
class ExampleClass{
private:
    double p1;
    double *p2;
    
public:
    ExampleClass() {
        p2 = new double;
    }

    ExampleClass(ExampleClass& exampleClass) {
        p1 = exampleClass.p1;
        p2 = new double;
        *p2 = *exampleClass.p2;
    }
    
    ~ExampleClass() {
        delete p2;
    }
};
```

### Czy muszę kopiować wszystkie wartości, czy mogę zadeklarować kopię tylko tych, które dobrze się nie skopiują

Najlepiej jest zdefiniować skopiowanie wszystkich elementów. [Dłuższa odpowiedź](https://stackoverflow.com/questions/70911220/c-copy-constructors-must-i-spell-out-all-member-variables-in-the-initializer)

{{< space 7 >}}

### Zadanie

Popraw implementację powyższych przykładów, aby działały poprawnie.

{{< space 7 >}}

## Przenoszenie

Czy zawsze musimy kopiować obiekt? Co gdy przykładowo go zwracamy z jakiejś funkcji? Wtedy bo zrobieniu jego kopii zostanie on zniszczony. W takim przypadku najczęściej zostanie on przeniesiony. Jednakże jest to materiał wykraczający poza zakres laboratorium i poniżej pokazuję jedynie deklarację takiego konstruktora. (Z tym tematem będzie też powiązane lvalue oraz rvalue.)

```cpp
class TestClass {
public:
    TestClass(TestClass &&testClass);
};
```

{{< space 5 >}}

## Używanie domyślnego konstruktora, albo usuwanie go

Dobrą praktyką jest deklarowanie wszystkich konstruktorów (nawet tych domyślnych), ewentualnie usuwanie tych niepotrzebnych. Do tego celu możemy użyć słów kluczowych `default` oraz `delete`.

```cpp
class Test {
public:
    Test() = default;
    Test(const Test&) = delete;
    Test(Test&&) = delete;
};
```


{{< space 7 >}}

## Dziedziczenie

Dziedziczenie, jak mówi słownik języka polskiego, dziedziczyć, to otrzymywać coś w spadku. W programowaniu też ma to zastosowanie, ponieważ klasę bazową może odziedziczyć inna klasa i otrzyma od niej wszystkie pola i metody, które były w przestrzeni `protected` i `public`. Można określić to inaczej, dziedziczenie, to rozszerzanie, ale nie tylko. Przeanalizujmy poniższe klasy.

```c++
class Student {
protected:
    string firstName, lastName;
    string idNumber;
    vector<unsigned short> grades; //*10
    
public:
    void printPerson();
    
    void printGrades();
    
    void printIdNumber();
};

class Worker {
protected:
    string firstName, lastName;
    unsigned salary;
    string emplacement;
    
public:
    void printPerson();
    
    void printSalary();
    
    void printEmplacement();
};

class Teacher {
protected:
    string firstName, lastName;
    unsigned salary;
    string emplacement;
    vector<string> subjets;
    
public:
    void printPerson();
    
    void printSalary();
    
    void printEmplacement();
    
    void printSubjects();
    
    void printAllData();
};
```

Dostrzegłeś coś? Mają one części wspólne. Pierwszą częścią, która się powtarza wszędzie jest `string firstName, lastName;` i `void printPerson();`. Stwórzmy z tego osobną klasę, która będzie takim ogólnikiem i będzie przechowywać podstawowe informacje o osobie, nazwijmy ją `Person`.

`Student` zawiera tylko pola z klasy `Person` i jakieś swoje, które są charakterystyczne dla tej klasy. Spójrz teraz na klasę `Worker` i `Teacher`. Mają one znowu te same pola. Zróbmy to samo co wcześniej, czyli wyłączmy z klasy `Teacher` i `Worker` część wspólną tych klas. Wyszło na to, że to będzie cała klasa `Worker`, niech tak zostanie. Czyli `Teacher` będzie specjalizował klasę `Worker`.

Podsumujmy stworzyliśmy klasę bazową `Person`, po której dziedziczą klasy `Student` i `Worker`. Następnie Stworzyliśmy dziedziczenie `Teacher`-a z `Worker`-a. Zobacz poniżej, jak będzie to wyglądać.

```c++
class Person {
protected:
    string firstName, lastName;
    
public:
    void printPerson();
};

class Student : public Person {
protected:
    string idNumber;
    vector<unsigned short> grades; //*10
    
public:
    void printGrades();
    void printIdNumber();
};

class Worker : public Person {
protected:
    unsigned salary;
    string emplacement;
    
public:
    void printSalary();
    void printEmplacement();
};

class Teacher: public Worker {
protected:
    vector<string> subjets;
    
public:
    void printSubjects();
    
    void printAllData();
};
```


Wiesz już, jakie jest podstawowe zadanie dziedziczenia. Wyciąganie części wspólnej, czyli mówiąc inaczej, wyłączanie przed nawias. Co nam to daje? Po pierwsze upraszcza strukturę i kod (nie mamy tego samego w 10 miejscach), upraszcza tworzenie modyfikacji, ponieważ nie musimy robić zmian w 10 miejscach, wystarczy, że zrobimy w jednym.

{{< space 4 >}}

Skoro omówiliśmy już ogólny zarys i ideę, to przejdźmy do szczegółów. Pierwszym z nich będą konstruktory. Wymyśl swój własny przykład i stwórz klasę bazową oraz pochodną. Stwórz w obydwóch klasach konstruktory i destruktory, a następnie stwórz obiekt klasy bazowej i pochodnej.
{{< br >}}
Co się wypisało na ekran?
{{< br >}}
Co, jeżeli przypiszemy klasę bazową do zmiennej referncyjnej klasy bazowej? Konstruktory i destruktory zadziałały poprawnie?

No nie do końca. Tym przypisaniem weszliśmy już w tematykę polimorfizmu.

{{< space 4 >}}

# Polimorfizm statyczny

Polimorfizm statyczny jest to poniekąd przeciążanie metod i operatorów. Program już na etapie kompilacji będzie wiedział, co zostanie wybrane do działania. 
Poznaliśmy już wcześniej przeciążenia funkcji, czy możemy zrobić takie przeciążenia w klasach? Oczywiście, że tak, a nawet możemy pójść o krok dalej. Możemy w klasie bazowej stworzyć metodę, a w klasie pochodnej stworzyć dosłownie taką samą metodę (w znaczeniu z takimi samymi argumentami, ale inną zawartością ciała, bo nie ma sensu robić dosłownie tego samego 2 razy), tylko że w tym przypadku ona przesłoni metodę z klasy bazowej.

```cpp
class Elephant {
public:
    void print() {
        cout << "I'm Elephant!" << endl;
    }
};

class SmallElephant: public Elephant {
public:
    void print() {
        cout << "I'm small Elephant!" << endl;
    }
};
```

Tak, tak ten przykład nie ma dużo sensu patrząc na dziedziczenie, ale skupmy się na przeciążonej metodzie. Jeżeli stworzymy obiekt typu `Elephant`, to na ekranie co uzyskamy? `I'm Elephant!`, a jeżeli stworzymy obiekt typu `SmallElephant`, to co uzyskamy na ekranie? `I'm small Elephant!`.


{{< space 7 >}}

# Polimorfizm

Polimorfizm (wielopostaciowość) jest to cecha programowania obiektowego, umożliwiająca różne zachowanie tych samych metod wirtualnych (funkcji wirtualnych) w czasie wykonywania programu.

Najprościej rzecz ujmując możemy przechowywać w obiekcie typu klasy bazowej, obiekt typu klasy dziedziczącej. Czyli takie troszkę oszukiwanie.

```c++
Figure *fig = new Circle();
```

Polimorfizm działa tylko i wyłącznie ze wskaźnikami lub referencją

```c++
Rectangle rect = new Rectangle();
Figure& fig1 = rect
```

Klasa Figure jest klasą bazową i posiada przepis na naszą klasę, część metod pokrywa się z klasą Circle.
Przykładem niech będzie `Figure`.


{{< space 5 >}}

# Funkcje wirtualne

Sprobujmy w sposób eksperymentalny dowiedzieć się czym są funkcje wirtualne. [Pobierz projekt z przykładem](/Polimorfizm.zip), a następnie przeanalizuj, jak działa program. Działa on poprawnie?

Dodaj słowo kluczowe `virtual` przed destruktor oraz metodę `print` w klasie `Base`. Co to zmieniło w działaniu programu?

{{< space 3 >}}

Słowo kluczowe `virtual` jest bardzo ważne w polimorfizmie dynamicznym, ponieważ znacząco zmienia, która metoda jest wywoływana. Dodanie słowa `virtual` przed metodą w klasie bazowej sprawi, że będzie wywoływana zawsze metoda z obiektu, ktory został przypisany, a nie z typu do którego został przypisany:

```cpp
Base *p1 = new InherBase;
InherBase *p2 = new InherBase;
```

Jeżeli byśmy nie użyli słowa kluczowego `virtual`, to w `p1` wywoływane były by metody z klasy `Base`, jeżeli uzyjemy tego słowa kluczowego, to wtedy metody zostaną wywołane z klasy `InherBase`.

{{< space 2 >}}

Dodatkowym zabezpieczeniem, czy nie zrobilismy literówki jest dodanie słowa `override` w pliku nagłówkowym, jeżeli nadpisujemy jakąś metodę wirtualną. Jeżeli kompilator nie znajdzie takiej metody w klasie bazowej, to wtedy wyrzuci błąd kompilacji, a my nie będziemy się zastanawiać, dlaczego to nie działa 😃.

{{< space 5 >}}

### Wirtualny destruktor

Ponownie odwołam się do analizowanego przykładu. Można tam było zauważyć, że podczas używania polimorfizmu dynamicznego destruktor z klasy bazowej nie wywołał się. Dodając słowo kluczowe `virtual` sprawiliśmy, że obydwa destruktory zostały wywołane. Od teraz możesz zawsze już tworzyć destruktory wirtualne.

[Inny przykład](https://cpp0x.pl/kursy/Programowanie-obiektowe-C++/Polimorfizm/Metody-wirtualne/495)

{{< space 5 >}}

## Interfejsy, klasy wirtualne/abstrakcyjne

Stosunkowo często może się zdarzyć, że chcemy stworzyć klasę, która będzie przepisem na kolejne klasy. Mówiąc dokładniej klasę, która będzie jakąś abstrakcją, ogółem, który następnie będziemy definiować w klasach pochodnych. Przykładowo mamy klasę `Task` będącą reprezentacją zadania. Zadanie to możemy przechowywać/chcieć wykonać w różny sposób:

* jako funkcja
* jako lambda
* jako klasa

Koniec końców potrzebujemy stworzyć jeszcze 3 klasy, które będą reprezentować poszczególny sposób wykonania zadania, a dokładniej wykonanie zadania z innego źródła. Jednakże będą one posiadać taką samą metodę: `runTask()`. Właśnie to możemy wyciągnąć do klasy abstrakcyjnej, dzięki czemu wszystkie zadania będziemy mogli przechowywać w jednym kontenerze/tablicy niezależnie od tego jakie są one typu.

{{< br >}}

Metody czysto wirtualne to są takie metody, które nie posiadaja ciała, ich implementacja musi się znaleźć w klasie dziedziczącej. Deklaracja metody czysto wirtualnej:

```cpp
virtual void virtualMethod() = 0;
```

{{< space 8 >}}

## Zadania do wykonania

**Zadanie 1**

Stwórz klasę `MyString`, będzie ona przechowywać tekst w tablicy `char`. Zaimplementuj konstruktor inicjalizujący nasz string za pomocą `std::string` (dla ułatwienia). Dodaj metodę `print` wypisującą na ekran zawartość.

Zaimplementuj następnie odpowiednie dodatkowe metody, aby nie występowały wycieki pamięci oraz inne problemy.

**Zadanie 2**

Wybierz dowolny przykład klasy ogólnej (Figura, osoba, pojazd itp), stwórz ją, a następnie stwórz 2 klasy pochodne.

{{< br >}}

**Zadanie 3**

Stwórz klasę `Logger`, która będzie posiadać wirtualne metody:

* `warning(string)` - dodawanie wpisu na poziomie ostrzeżenia
* `error(string)` - dodawanie wpisu na poziomie błędu

Następnie stwórz 2 klasy dziedziczące:

* `ConsoleLogger` - wszystkie logi będą wyrzucane do konsoli za pomocą `cout`
* `FileLogger` - wszystkie logi będą wyrzucane do pliku, ale my dla ułatwienia wyrzucimy je do `cout` z dopiskiem `To file: `

Stwórz następnie obiekty tych loggerów.

{{< br >}}

**Zadanie 4**

Stwórz klasę abstrakcyjną `Task`, ktora będzie posiadać metode czysto wirtualną `void runTask()`.

Następnie stwórz klasy, ktore będą posiadać przeciążoną tę metodę i pozwalać na uruchomienie zadania na podstawie:

* przekazanej funkcji (np w konstruktorze)
* przekazanej klasy


{{< space 15 >}}

### Odpowiedzi

<b id="answear1">[1]</b> program działa niepoprawnie, ponieważ zmieniając wartości w punktach `p1` i `p2` zmieniają się
również wartości w punktach `p1c` i `p2c`. [↩](#answear1Sourece)

<b id="answear2">[2]</b> Program powinien działać nie poprawnie, powinien kończyć działanie zaraz po wyjściu z sekcji,
gdzie zostały zadeklarowane zmienne `p1c` i `p2c` (zaraz przed coutem z komunikatem `End program`). Dzieje się tak,
ponieważ domyślny konstruktor kopiujący kopiuje referencję do zmiennej i przy utworzeniu kopii klasy mamy 2 wskaźniki
odwołujące się do tego samego adresu. Zwalniając raz pamięć, nie możemy zrobić tego po raz drugi i dlatego pojawia się
błąd, który mówi nam dosłownie co jest nie tak (`free(): double free detected in tcache 2` - wykryto podwójne zwalnianie pamięci) [↩](#answear2Sourece)