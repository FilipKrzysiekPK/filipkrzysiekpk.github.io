---
title: "Laboratorium 3"
layout: singleNoHeader
date: 2023-10-10
---


# Laboratorium 3

### Cele laboratorium i poruszane zagadnienia

* Konstruktory
* Destruktory
* Co to są i po co się stosuje gettery oraz settery
* Podstawy debugowania
* Konstruktory kopiujące

{{< space 7 >}}


## Konstruktory

Na poprzednich zajęciach stworzyliśmy pierwszą bardzo podstawową klasę. Dziś będziemy się uczyć o kolejnych elementach klasy, które bardzo ułatwiają życie i kontrolę nad nimi. PIerwszym z nich jest konstruktor.

Konstruktor jest to specjalna metoda wykonywana podczas tworzenia obiektu (instancji klasy). Czy każda klasa posiada konstruktor? Tak nawet gdy go nie zadeklarujemy. Możesz to sobie sprawdzić tworząc klasą, taką jak na poprzednich zajęciach i stwórz jej instancję dodając na końcu nawiasy półokrągłe. Coś Ci podpowiedział edytor? Tak jest tam kilka domyślnych konstruktorów.

```cpp
class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

Powyżej można dostrzec prosty przykład klasy z zadeklarowanym konstruktorem oraz 2 wywołania w klasie `main`. Czy obydwa wywołania są poprawne? Spróbuj skompilować. Wyskoczył błąd kompilacji. Dodaj nawiasy do `teacher1` i już kompilacja zakończona powodzeniem. Stało się tak, ponieważ nie wywołaliśmy konstruktora. Niestety przy inicjalizacji należy bardzo uważać. Jeżeli nie przekazujemy argumentów, to nie używamy nawiasów, albo używamy nawiasów tego typu `{}`. Jest jeszcze jedna opcja możemy użyć przypisania.

```cpp
    Teacher teacher1 = Teacher1();
```

W takich przypadkach mamy pewność, że wywołaliśmy konstruktor bezparametrowy.

{{< space 3 >}}

Czy musimy w konstruktorze inicjalizować wszystkie pola naszej klasy? Poniekąd, każda zmienna, pole powinno zostać zainicjalizowane, więc jeżeli nie nadajemy wartości w konstruktorze, to powinniśmy ją nadać podczas deklaracji. Przez konstruktory zazwyczaj przekazujemy kilka wartości, którymi chcemy, aby klasa została zainicjalizowana, więc w liście argumentów nie muszą znajdować się wszystkie pola.

Konstruktory domyślne są to konstruktory, które są dodawane przez kompilator i mają zdefiniowaną z góry strukturę. Dzięki nim nie musimy za każdym razem tworzyć konstruktorów w klasie. Przykładowo domyślnym konstruktorem jest bez parametrów. Co, jeżeli my takiego nie chcemy? Teoretycznie wystarczy stworzyć inny konstruktor i ten przestanie istnieć, lecz aby mieć pewność możemy go usunąć.

```cpp
class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
    Teacher() = delete;
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

{{< space 4 >}}

#### Słowo kluczowe `this`

W powyższych przykładach możemy zauważyć, że jest używane słowo kluczowe `this`. Służy ono do wskazania, o którą konkretnie zmienną nam chodzi. Jeżeli użyjemy zmienną bez słowa kluczowego `this`, to dostaniemy się do zmiennej zadeklarowanej w parametrze funkcji. Natomiast dodając słowo kluczowe `this` informujemy, że chcemy dostać zmienną będącą polem klasy. Oczywiście możemy nazywać zmienne i pola w ten sposób, aby nie występowała kolizja nazw i w takim przypadku nie musimy się martwić, którą zmienną lub pole modyfikujemy.

{{< space 3 >}}

## Konstruktor - lista inicjalizacyjna

Czy w klasie można zadeklarować, jako pole stałą, albo referencję? Tak można, tylko jest jeden warunek, musimy ją zainicjalizować w konstruktorze. Jednakże nie możemy tego zrobić w ciele konstruktora, wtedy jest już zbyt późno, pola klasy zostały zainicjalizowane. Musimy użyć listy inicjalizacyjnej.

```cpp
Teacher(const string &name, const string &strengths): name(name), strengths(strngths) {

}
```

Działa to na zasadzie, że wartości z listy inicjalizacyjnej są przypisywane do pól, a dopiero później następuje stworzenie obiektu i wywołanie ciała konstruktora. (Lista inicjalizacyjna to jest ta część po :, a przed {.)

Czy są jeszcze jakieś plusy stosowania listy inicjalizacyjnej? Można dostrzec, że taki konstruktor działa szybciej.

{{< space 4 >}}

## Wymuszanie braku konwersji typu (ponad programowe)

Czy zawsze niejawna konwersja typu jest pożądana? Nie zawsze, dlatego, jeżeli chcemy, aby podczas wywołania naszego konstruktora nie dochodziło do niej możemy przed deklarację i implementację dodać słowo kluczowe `explicit`. W przypadku, gdy chcemy wywołać konstruktor bez konwersji typu, możemy też użyć nawiasów `{}` zamiast półokrągłych.

{{< space 7 >}}

## Gettery i settery

Getery i settery są to metody służące odpowiednio do pobierania wartości pól, jak i ich ustawiania. Stosujemy je, aby móc modyfikować wartości, bez posiadania bezpośredniego dostępu. Szczególnie są one potrzebne, jeżeli mamy pola powiązane ze sobą, gdzie modyfikacja jednego z nich wymaga modyfikacji drugiego. W przypadku modyfikowania przez bezpośredni dostęp nie bylibyśmy w stanie się zabezpieczyć i zapewnić, że użytkownik, modyfikując będzie wiedział, o zmodyfikowaniu drugiego pola. Setter w takim przypadku zapewnia nam spójność i wymusza aktualizację wszystkich pól.

```cpp
class Teacher {
    string name;
    string strengths;
    int tiredLevel = 0;

public:
    Teacher(const string &name, const string &strengths) {
        this->name = name;
        this->strengths = strengths;
    }
    Teacher() = delete;

    string getName() {
        return name;
    }

    void setName(const string &name) {
        this->name = name;
    }
};

int main() {
    Teacher teacher1;
    Teacher teacher2("The Best", "");
    return 0;
}

```

Bardzo ważna rzecz do zapamiętania gettery i settery **nie mogą** w sobie zawierać `cin` i `cout`. Mają one służyć do ustawiania wartości i jej zwracania, a nie wypisywania na ekran, albo zczytywanie z niego!

{{< space 7 >}}

## Destruktory

Destruktory są to metody wywoływane zaraz przed zniszczeniem obiektu. Stosuje się je zawsze wtedy, gdy musimy posprzątać podczas usuwania obiektu naszej klasy. Używamy ich zazwyczaj, gdy alokujemy w klasie jakąś pamięć, ponieważ trzeba ją zwolnić, aby nie była bezsensownie zaalokowana bez możliwości dostępu do niej. Innym przypadkiem są jakieś połączenia, otwarte sockety itp. Należy je zamknąć przed zniszczeniem obiektu.

```c++
class NazwaTwojejKlasy
{
public:
    ~NazwaTwojejKlasy(); //To jest definicja destruktora
};

NazwaTwojejKlasy::~NazwaTwojejKlasy()
{
    //Tu wykonujemy wszystkie operacje jakie mają się wykonać automatycznie tuż przed zwolnieniem pamięci zajmowanej przez klasę.
}
```

Destruktor, tak samo, jak konstruktor nie posiada zwracanego typu. Drugą ważną cechą jest to, że destruktor musi być zawsze bezparametrowy. Trzecią, a zarazem ostatnią ważną cechą jest możliwość zdefiniowania tylko i wyłącznie jednego destruktora dla danej klasy.
Poniżej prosty przykład, demonstrujący działanie konstruktora i destruktora.

```c++
class KlasaCL
{
public:
    KlasaCL();
    ~KlasaCL();
};

int main()
{
    KlasaCL * tKlasa;
    cout << "Rezerwuje pamiec za pomoca new" << endl;
    tKlasa = new KlasaCL;
    cout << "Wchodze do bloku {" << endl;
    {
        KlasaCL tKlasa;
    }
    cout << "Wyszedlem z bloku }" << endl;
    cout << "Zwalniam pamiec, ktora zostala zarezerwowana za pomoca new" << endl;
    delete tKlasa;
    getch();
    return( 0 );
}

KlasaCL::KlasaCL()
{
    cout << "=> Konstruktor wywolany!" << endl;
}

KlasaCL::~KlasaCL()
{
    cout << "=> Destruktor wywolany!" << endl;
}
```

Zasady tworzenia destruktora są podobne do konstruktora. Nazwa destruktora zaczyna się od ~ i nazwy klasy.

{{< space 3 >}}

### Zasada RAII

> Konstruktor alokuje elementy i odpowiednio inicjalizuje składowe obiektu klasy `Vector`. Destruktor z kolei dealokuje te elementy. Ten model uchwytu do danych jest powszechnie wykorzystywany podczas pracy ze zbiorami danych, których rozmiar może się zmieniać w czasie istnienia obiektu.
> Technika zajmowania zasobów za pomocą konstruktora i zwalniania ich za pomocą destruktora, zwana RAII (ang. Resource Acquisition Is Initialization - zajęcie zasobów oznacza inicjalizacjeę), pozwala wyeliminować "gołe operacje `new`", czyli uniknąć alokacji w ogólnym kodzie i ukryć je w logicznie zaimplementowanej abstrakcji. Analogicznie należy też unikać "gołych operacji `delete`". Im mniej gołych operacji `new` i `delete` w kodzie, tym mniejsze ryzyko popełnienia błędu i łatwiej unikać wycieków pamięci (13.2)

[Wikipedia](https://pl.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization)

{{< space 5 >}}

## Konstruktory kopiujące

Zanim zaczniemy omawiać tematykę konstruktorów kopiujących przetestujmy działanie kodu znajdującego
się [tutaj](Examples/E1NoCopyingConstructor).

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

Popraw implementację z przykładu (folder examples).

**Zadanie 2**

1. Stwórz klasę `Point`, która będzie przechowywać informacje o punkcie w typie `int`. Dodaj do niego odpowiednie metody oraz konstruktory.
2. Stwórz klasę `Polygon`, która będzie w konstruktorze przyjmować wartość z ilu punktów się składa. Do jej implementacji użyj tablicy dynamicznie alokowanej, a nie kontenerów (trzeba to przećwiczyć i zasadę RAII).
3. Dodaj metody:
* przypisująca poszczególnym punktom ich wartości
* obliczająca obwód
4. Dodaj konstruktor kopiujący.

**Zadanie 3**

Stwórz klasę przechowującą długość i szerokość geograficzną (`double`). Stwórz odpowiednie metody i konstruktory. Dodatkowo stwórz metody pozwalające pobierać i zapisywać te wartości w innym formacie.

Przyjmując, że `int number` to długość w innym formacie, musimy użyć następującego wzoru, aby uzyskać w zapisie dziesiętnym stopnie:

deg = number / 3600000



{{< space 15 >}}

### Odpowiedzi

<b id="answear1">[1]</b> program działa niepoprawnie, ponieważ zmieniając wartości w punktach `p1` i `p2` zmieniają się
również wartości w punktach `p1c` i `p2c`. [↩](#answear1Sourece)

<b id="answear2">[2]</b> Program powinien działać nie poprawnie, powinien kończyć działanie zaraz po wyjściu z sekcji,
gdzie zostały zadeklarowane zmienne `p1c` i `p2c` (zaraz przed coutem z komunikatem `End program`). Dzieje się tak,
ponieważ domyślny konstruktor kopiujący kopiuje referencję do zmiennej i przy utworzeniu kopii klasy mamy 2 wskaźniki
odwołujące się do tego samego adresu. Zwalniając raz pamięć, nie możemy zrobić tego po raz drugi i dlatego pojawia się
błąd, który mówi nam dosłownie co jest nie tak (`free(): double free detected in tcache 2` - wykryto podwójne zwalnianie pamięci) [↩](#answear2Sourece)



