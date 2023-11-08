---
title: "Laboratorium 4"
layout: singleNoHeader
date: 2023-11-07
---


# Laboratorium 4

### Cele laboratorium i poruszane zagadnienia

* Dziedziczenie
* Polimorfizm statyczny
* Polimorfizm dynamiczny
* Virtual

{{< space 7 >}}


# Dziedziczenie

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

{{< space 2 >}}

### Modyfikator dostępu przed klasą dziedziczoną

Jak mogłeś dostrzec przed klasą, po której dziedziczymy można znaleźć modyfikator dostępu. Służy on do zmiany widzialności elementów z klasy bazowej. Jest to bardzo rzadko używane.

Więcej szczegółów znajdziesz [tutaj](https://cpp0x.pl/kursy/Programowanie-obiektowe-C++/Polimorfizm/Dziedziczenie/494).

{{< space 4 >}}

Skoro omówiliśmy już ogólny zarys i ideę, to przejdźmy do szczegółów. Pierwszym z nich będą konstruktory. Wymyśl swój własny przykład i stwórz klasę bazową oraz pochodną. Stwórz w obydwóch klasach konstruktory i destruktory, dodaj w nich jakieś wypisania na ekran, a następnie stwórz obiekt klasy bazowej i pochodnej.
{{< br >}}
Co się wypisało na ekran?
{{< br >}}
Co, jeżeli przypiszemy klasę bazową do zmiennej referncyjnej klasy bazowej? Konstruktory i destruktory zadziałały poprawnie?

No nie do końca. Tym przypisaniem weszliśmy już w tematykę polimorfizmu.

{{< br >}}

Jeszcze zanim omówimy polimorfizm rozważmy jeszcze jeden przypadek. Dodaj parametr w konstruktorze klasy bazowej (ma nie być konstruktora bezparametrycznego). Czy jesteś w stanie teraz skompilować projekt? No jest problem 😐. Musimy wywołać konstruktor w momencie konstruowania klasy bazowej. Pamietasz jak to zrobić? Czy chciałeś wywołać ten konstruktor w liście inicjalizacyjnej? Bardzo dobry pomysł i dokładnie tak trzeba to zrobić.

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

Nie można stworzyć obiektu klasy czysto wirtualnej, dopiero można stworzyć obiekt klasy dziedziczącej.


