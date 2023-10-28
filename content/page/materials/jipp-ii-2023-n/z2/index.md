---
title: "Języki i Paradygmaty Programowania II laboratorium 2"
layout: singleNoHeader
date: 2023-10-25
---

# Laboratorium 2

### Cele laboratorium i poruszane zagadnienia

* konstruktor kopiujący
* sposoby przekazywania elementów do funkcji
* dziedziczenie
* polimorfizm

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