---
title: "Języki i Paradygmaty Programowania II laboratorium 2"
layout: singleNoHeader
date: 2023-10-25
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

# Kontenery

Najczęściej w c dynamicznie alokowane tablice były wykorzystywane do tworzenia tablic, których rozmiaru nie znaliśmy na etapie kompilacji programu. Język c++ udostępnia "inteligentne tablice", które są nazywane kontenerami. Najpopularniejszym z nich jest `vector` oraz `map`a.

Vextor to taka, tablica, która dynamicznie zmienia swój rozmiar dostosowując swoją pojemność, tak aby zmieścić wszystkie elementy. Jeżeli chcemy dodać nowy element do tego kontenera, to musimy użyć specjalnych metod: `insert`, albo `push_back`. Dostęp i modyfikowanie elementów odbywa się na taki sam sposób, jak w zwykłej tablicy. Poniżej przedstawione zostało przykładowe zastosowanie.

```cpp
#include <vector>
// ...
vector<int> myTab;
myTab.push_bask(10);
myTab.push_bask(11);
myTab.push_bask(12);
myTab.push_bask(13);

cout << myTab.size() << " myTab[0]" << myTab[0] << endl;
```

Oczywiście istnieje też wiele innych metod, które pozwalają operować na tym kontenerze, można z nimi się zapoznać w [dokumentacji](https://en.cppreference.com/w/cpp/container/vector).

{{< space 3 >}}

Drugi kontener, który omówię już tylko w dwóch zdaniach, to `map`a. Tutaj wartości przechowujemy na zasadzie klucz - wartość, gdzie klucz i wartość mogą być dowolnego typu. Więcej informacji można znaleźć w [dokumentacji](https://en.cppreference.com/w/cpp/container/map).

[Dokumentacja wszystkich kontenerów w c++.](https://en.cppreference.com/w/cpp/container)

{{< space 7 >}}

## Konstruktory kopiujące

Zanim zaczniemy omawiać tematykę konstruktorów kopiujących przetestujmy działanie kodu znajdującego
się na delcie.

Jakie anomalie zauważyłeś?{{< rawhtml >}}<sup id="answear1Sourece">{{< /rawhtml >}}[1](#answear1){{< rawhtml >}}</sup>{{< /rawhtml >}}

Dodaj destruktor, który będzie zwalniał pamięć (dla `x` i `y`). Co się stało? Czy program działa poprawnie i
dlaczego?{{< rawhtml >}}<sup id="answear2Sourece">{{< /rawhtml >}}[2](#answear2){{< rawhtml >}}</sup>{{< /rawhtml >}}

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

{{< space 7 >}}

## Zadania do wykonania na zajęciach

**Zadanie 1** 

Popraw implementację z przykładu 1 na delcie.

**Zadanie 2**

Do zadania z poprzednich zajęć (z `Polygon`) dodaj konstruktor kopiujący.

**Zadanie 3**

{{< space 7 >}}

## Przenoszenie

Czy zawsze musimy kopiowiać obiekt? Co gdy przykładowo go zwracamy z jakiejś funkcji? Wtedy bo zrobieniu jego kopii zostanie on zniszczony. W takim przypadku najczęściej zostanie on przeniesiony. Jednakże jest to materiał wykraczający poza zakres laboratorium i poniżej pokazuję jedynie deklarację takiego konstruktora. (Z tym tematem będzie też powiązane lvalue oraz rvalue.)

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

Dziedziczenie, jak mówi słownik języka polskiego, dziedziczyć, to otrzymywać coś w spadku. W programowaniu też ma to zastosowanie, ponieważ klasę bazową może odziedziczyć inna klasa i otrzyma od niej wszystkie pola i metody, które były w przestrzeni `protected` i `public`. Można określić to inaczej, dziedziczenie, to rozszerzanie, ale nie tylko. Przeanalizujmy poniższe klasy. Przeanalizujmy poniższy przykład.

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

Dostrzegłeś coś? Mają one części wspólne. Pierwszą częścią, która się powtarza wszędzie jest `string firstName, lastName;` i `void printPerson();`. Stwórzmy z tego osobną klasę, która będzie takim ogólnikiem i będzie przechowywać podstawowe informacje o osobie, będzie się ona nazywać `Person`.

`Student` zawiera tylko pola z klasy `Person` i jakieś swoje, które są charakterystyczne dla tej klasy. Spójrz teraz na klasę `Worker` i `Teacher`. Mają one znowu te same pola. Zróbmy to samo co wcześniej, czyli wyłączmy z klasy `Teacher` i `Worker` część wspólną tych klas. Wyszło na to, że to będzie cała klasa `Worker`, niech tak zostanie. Czyli `Teacher` będzie specjalizował klasę `Worker`.

Podsumujmy stworzyliśmy klasę bazową `Person`, po której dziedziczą klasy `Student` i `Worker`. Następnie Stworzyliśmy dziedziczenie `Teachera` z `Workera`. Zobacz poniżej, jak będzie to wyglądać.

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
Poznaliśmy już wcześniej przeciążenia funkcji, czy możemy zrobić takie przeciążenia w klasach? Oczywiście, że tak, a nawet możemy pójść o krok dalej. Możemy w klasie bazowej stworzyć metodę, a w klasie pochodne stworzyć dosłownie taką samą metodę (w znaczeniu z takimi samymi argumentami, ale inną zawartością ciała, bo nie ma sensu robić dosłownie tego samego 2 razy), tylko że w tym przypadku ona przesłoni metodę z klasy bazowej.

```cpp
class Elephant {
public:
    void print() {
        cout << "I'm Elephant!" << endl;
    }
};

class SmallElephant: public Elephant {
public:
    void print() override {
        cout << "I'm small Elephant!" << endl;
    }
};
```

Tak, tak ten przykład nie ma dużo sensu patrząc na dziedziczenie, ale skupmy się na przeciążonej metodzie. Jeżeli stworzymy obiekt typu `Elephant`, to na ekranie co uzyskamy? `I'm Elephant!`, a jeżeli stworzymy obiekt typu `SmallElephant`, to co uzyskamy na ekranie? `I'm small Elephant!`.
<br>
Jak można zauważyć w przeciążonej metodzie doszło słowo `override`, służy ono do tego, aby kompilator nas sprawdził, czy nie zrobiliśmy jakiejś literówki, albo błędu chcąc nadpisać poprzednią deklarację metody w klasie bazowej.

{{< space 7 >}}

# Polimorfizm

Polimorfizm (wielopostaciowość) jest to cecha programowania obiektowego, umożliwiająca różne zachowanie tych samych metod wirtualnych (funkcji wirtualnych) w czasie wykonywania programu.

Najprościej rzecz ujmując możemy przechowywać w obiekcie typu klasy bazowej, obiekt typu klasy dziedziczącej. Czyli takie troszkę oszukiwanie.

```c++
Figure *fig = new Circle();
```

Polimorfizm działa tylko i wyłącznie ze wskaźnikami i referencją

```c++
Rectangle rect = new Rectangle();
Figure& fig1 = rect
```

Klasa Figure jest klasą bazową i posiada przepis na naszą klasę, część metod pokrywa się z klasą Circle.
Przykładem niech będzie `Figure`.


{{< space 5 >}}

# Funkcje wirtualne

Sprobujmy w sposób eksperymentalny dowiedzieć się czym są funkcje wirtualne. Pobierz przykład 2 z delty, a następnie przeanalizuj, jak działa program. Czy działa on poprawnie?

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

Wybierz dowolny przykład klasy ogólnej (Figura, osoba, pojazd itp), stwórz ją, a następnie stwórz 2 klasy pochodne.

{{< br >}}

**Zadanie 2**

Stwórz klasę służącą do przechowywania współrzędnych geograficznych (może być tylko w stopniach). Dodaj odpowiednie metody (według uznania).

Dodaj klasę, która będzie umożliwiała przechowywanie tych współrzędnych przyjmując wartości z innego systemu. Będą one obliczane według następującego wzoru, gdzie `number` to wartość współrzędnej z innego systemu, a `deg` to wartość w stopniach.

> deg = number / 3600000

{{< br >}}

**Zadanie 3**

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