---
title: "Języki i Paradygmaty Programowania II laboratorium 4"
layout: singleNoHeader
date: 2023-12-02
---

# Laboratorium 4

### Cele laboratorium i poruszane zagadnienia

* szablony
* konwersje
* obsługa plików

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

# Strumienie plikowe

Strumienie plikowe można przyrównać do strumieni `cin` i `cout`. Dzieje się tak, ponieważ wszystkie one dziedziczą z tej samej klasy bazowej:

![Diagram klas](imgJipp/IO_library.png)
*[cppreference](https://en.cppreference.com/w/cpp/io)*

Strumienie plikowe:

![Diagram klas](imgJipp/fstream.png)
*[cppreference](https://en.cppreference.com/w/cpp/io/basic_fstream)*

Teraz można się prosto domyślić, że obsługa plików wygląda bardzo podobnie, jak strumienie `cin` i `cout`. Prześledźmy kilka najważniejszych metod tych strumieni.

## Załączanie bibliotek

Aby móc korzystać ze strumieni plikowych musimy załączyć bibliotekę `fstream`.

{{< space 5 >}}

## Inicjalizacja strumienia

Strumień możemy zainicjalizować na kilka sposobów. Pierwszym z nich jest wywołanie konstruktora z parametrami:

* nazwa pliku lub ścieżka do niego
* właściwości strumienia (opcjonalne):
    * `app` - dodawanie do końca pliku
    * `binary` - otwieranie pliku w trybie binarnym
    * `in` - otwieranie pliku do czytania pliku
    * `out` - otwieranie pliku do zapisu
    * `trunc` - usuń zawartość strumienia zaraz po otwarciu
    * `ate` - otwórz strumień i ustaw wewnętrzny wskaźnik na końcu

```cpp
    ifstream input = ifstream("nazwa pliku.txt");
```

```cpp
    ifstream input = ifstream("nazwa pliku.txt", ios::binary | ios::in);
```

Do otwarcia pliku można też użyć metody `open`

```cpp
    ifstream input;
    input.open("nazwa pliku.txt", ios::binary | ios::in);
```

{{< space 5 >}}

## Sprawdzanie poprawności

Po otwarciu strumienia plikowego należy sprawdzić, czy na pewno się on otworzył. Użyjemy do tego jednej z metod:

* `good()`
* `is_open()`
* `bad()`

{{< space 5 >}}

## Obsługa strumieni

Obsługa strumieni wygląda tak samo, jak `cin` i `cout`, tylko tyle, że zamiast tych nazw używamy obiektu przechowującego nasz strumień.

```cpp
ifstream in = ifstream("hello.txt");

if(in.good()) {
    string temp;
    in >> temp;
    cout << temp;
}
```

{{< space 5 >}}

## Zamykanie strumieni

Każdy strumień należy zamknąć po zakończeniu jego używania, ponieważ może to blokować dostęp do pliku. Co więcej, jeżeli go zamkniemy, to mamy pewność, że dane zostały do niego zrzucone i w razie nagłej awarii naszego programu nie zostaną utracone dane. Strumienie zamykamy za pomocą metody `close()`. Możemy też ręcznie wywołać zrzucenie strumienia do pliku, służy do tego metoda `flush()`.

```cpp
int main() {
    ofstream stream{"outputlog.txt"};

    for (int i = 0; i < 10; ++i) {
        stream << i << endl;
    }

    stream.flush();

    cout << "Czy plik się zapisał?" << endl;
    string answear;
    cin >> answear;

    stream << "done";
    stream.close();

    return 0;
}

```

{{< space 5 >}}

## Czytanie pliku

Plik możemy czytać słowo po słowie, a dokładniej od białego znaku do białego znaku, sprawdzając, czy nie dotarliśmy do końca pliku (metoda `eof()`). W takim przypadku będziemy korzystać ze strumienia plikowego, tak jak z `cin`.

```cpp
int main() {
    ifstream stream{"input.txt"};

    if(stream.good()) {
        string temp;
        while(!stream.eof()) {
            temp >> temp;
            cout << temp;
        }
        stream.close();
    }

    return 0;
}
```

Możemy też czytać strumień linijka po linijce, należy do tego użyć funkcji `getline`. Jeżeli nie widzi `getline` należy załączyć bibliotekę `string`.


```cpp
int main() {
    ifstream stream{"input.txt"};

    if(stream.good()) {
        string temp;
        while(!stream.eof()) {
            getline(stream, temp);
            cout << temp << endl;
        }
        stream.close();
    }

    return 0;
}
```

{{< space 5 >}}

## Binarne zapisywanie i czytanie plików

Dotychczas czytaliśmy pliki z wartości zapisanych jawnym tekstem. Jednakże możemy zrzucić zawartość naszych zmiennych bezpośrednio do pliku (bez przeprowadzania konwersji do tekstu). Będzie to zapis binarny i musimy do tego użyć `reinterpret_cast` oraz metody `write`, której mówimy ile znaków chcemy zapisać.

```cpp
int main() {
    ofstream stream{"hello.txt"};
    ofstream streamBin{"helloBin.txt", ios::binary};
    int k = 136;
    stream << k;

    streamBin.write(reinterpret_cast<char*>(&k), sizeof(k));

    stream.close();
    streamBin.close();
}
```

Porównaj zawartość plików, czy jest taka sama? Dlaczego?

{{< space 5 >}}

Zapisaliśmy wartości do pliku, teraz spróbujmy je odczytać. Aby to zrobić, musimy dokonać operacji odwrotnej i musimy z góry wiedzieć, w jakiej kolejności są wartości.

```cpp
int main() {
    ifstream stream{"hello.txt"};
    ifstream streamBin{"helloBin.txt", ios::binary};

    if (stream.good() && streamBin.good()) {
        int k = 0;
        stream >> k;

        cout << k << endl;

        streamBin.read(reinterpret_cast<char*>(&k), sizeof(k));
        cout << k << endl;
    }
    return 0;
}
```
    

{{< space 5 >}}

## Binarny zapis i odczyt obiektu

Przeanalizuj poniższy przykład, sprawdź, czy działa poprawnie.

```cpp
class Obj {
    double x;
    double y;
    double z;
    int *tab;

public:
    Obj(double x, double y, double z): x(x), y(y), z(z) {
        tab = new int[3];
        tab[0] = x;
        tab[1] = y;
        tab[2] = z;
    }

    ~Obj() {
        delete [] tab;
    }

    void print() {
        cout << "x: " << x << endl; 
        cout << "y: " << y << endl; 
        cout << "z: " << z << endl; 
        cout << "tab[0]: " << tab[0] << endl; 
        cout << "tab[1]: " << tab[1] << endl; 
        cout << "tab[2]: " << tab[2] << endl; 
    }

};

void save() {
    Obj obj{5, 8, 3.14};
    obj.print();

    ofstream str{"file.bin", ios::binary};
    if (str.good()) {
        str.write(reinterpret_cast<char*>(&obj), sizeof(obj));
        str.close();
    }
}

void read() {
    Obj obj{0, 0, 0};
    obj.print();

    ifstream str{"file.bin", ios::binary};
    if (str.good()) {
        str.read(reinterpret_cast<char*>(&obj), sizeof(obj));
        str.close();
    }
    obj.print();
}

int main() {
    save();
    read();
    
}
```

Wiesz, dlaczego on działa w taki sposób? Zastanów się, jakie typy zapisałeś do pliku.

Postaraj się to naprawić.

{{< space 7 >}}


# Szablony

Przypomnijmy sobie jedno z pierwszych zadań domowych, gdzie mieliśmy tworzyć bibliotekę do obsługi macierzy. Zostały narzucone tam z góry typy, jakie miały być obsługiwane, ale jednocześnie dość mocno to ograniczało uniwersalność kodu. Szablony są narzędziem, które pozwolą to naprawić.

Może jednak zacznijmy troszkę inaczej. Szablony są prostym narzędziem, które jednocześnie jest bardzo potężne. Są one podstawą programowania generycznego, które polega na pisaniu kodu w sposób niezależny od konkretnego typu. Możemy je przyrównać do parametrów funkcji, ale muszą być one znane podczas kompilacji. Przez taki "parametr" możemy przekazać:

* typ
* stałą (ewentualnie zmienną, która zostanie skonwertowana do stałej)

{{< space 3 >}}

Czy już gdzieś się spotkaliśmy z szablonami?

Oczywiście, że tak, używaliśmy nich przy deklaracji kontenerów (np. `vector`). Tak było wtedy powiedziane, że zapis pomiędzy `<>` musimy zaakceptować, ale dziś się dowiemy, co to robiło i mniej więcej, jak mogło działać.

{{< space 5 >}}

## Szablon funkcji

Zacznijmy od najprostszej rzeczy, czyli szablonu funkcji. Stwórzmy prostą metodę, która będzie dodawać 2 liczby.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    cout << add(2, 5) << endl;
    cout << add(2.7, 5.6) << endl;
    cout << add(2, 5.6) << endl;
}
```

Tutaj pierwsza uwaga, w aktualnym przypadku i większości przypadków szablony będziemy deklarować i *implementować* w tym samym pliku i będzie to plik nagłówkowy.

Spróbuj uruchomić powyższy kod. Czy wszystko się poprawnie skompilowało? Zakomentuj problematyczną linię i spróbuj znowu skompilować. Wywołując funkcję podawałeś w którymś miejscu, jaki to będzie typ? Nie, kompilator sam wydedukował to na podstawie przekazywanych parametrów. Teraz podmień problematyczną linię na tą poniżej, gdzie deklarujemy, jaki chcemy mieć typ.

```cpp
    cout << add<double>(2, 5.6) << endl;
```

Zaczęło działać? Dlaczego? Została dokonana niejawna konwersja typu i dlatego działa. Teraz pytanie, czy jesteśmy w stanie zrobić taką funkcję, aby były przyjmowane 2 różne typy bez jawnego deklarowania tego? Oczywiście, że tak, wystarczy, że przez szablon przekażemy 2 "parametry". Tak, pojawi się problem, jeden z nich będzie musiał być typem zwracanym. Który? Trzeba dokonać wyboru.

```cpp
template<typename T, typename T1>
T add(T a, T1 b) {
    return a + b;
}
```

{{< space 4 >}}

A jak mogę przekazać zwykłą zmienną przez taki szablon?

Wystarczy, że po prostu go tam zadeklarujesz i zostanie przekazany.

```cpp
template<typename T, int x>
T add(T a, T b) {
    return (a + b) * x;
}

int main() {
    cout << add<int, 6>(2, 5) << endl;
    cout << add<double, 8>(2.7, 5.6) << endl;
    cout << add<float, 9>(2, 5.6) << endl;
}
```

Może się teraz pojawić pytanie, czy można tak zrobić, aby nie musieć przekazywać tej wartości `x`? Oczywiście, że tak. Tak jak mieliśmy parametry domyślne w funkcji, to tutaj też możemy przypisać domyślną wartość tych "parametrów".

```cpp
template<typename T, int x = 1>
```

{{< space 4 >}}

**Przeciążanie szablonów**

Istnieje możliwość przeciążania szablonów, albo funkcji szablonowych. Panuje tutaj kilka zasad:

- Przeciążane szablony muszą się różnić argumentami
- Przeciążany szablon odnosi się do przeciążonej funkcji
- Istnieje jakaś funkcja, a my tworzymy szablon, wtedy ten szablon tworzy niezadeklarowane przeciążenia funkcji
- Specjalizacje - definiujemy dla konkretnych wartości nowe zachowanie naszej funkcji


```c++
template<typename T>
T addS(T a, T b) {
    return a + b + cons;
}


template<typename T, int cons>
T addS(T a, T b) {
    return a + b + cons;
}
```

{{< space 1 >}}

**Szablon metod**

Podobnie jak w funkcjach możemy stworzyć szablon dla metod w klasie. Nie musimy wtedy tworzyć szablonu dla całej klasy, wystarczy dla jednej metody. Działa ona na dosłownie takiej samej zasadzie jak dla funkcji.

{{< space 6 >}}

## Szablon klasy

Deklarowanie szablonu dla klasy wygląda bardzo podobnie co do funkcji, jednakże deklarujemy go dla całej klasy.

```cpp
template<typename T>
class Variable {
    T var;

public:
    Variable() {

    }

    Variable(T var): var(var) {}

    T getValue() {
        return var;
    }

    void setValue(T newVal) {
        var = newVal;
    } 
}
```

Tak samo, jak dla funkcji deklaracja i implementacja musi znajdować się w tym samym pliku. (Jak się bardzo postara, to da się to rozdzielić.)

{{< space 3 >}}

## Ograniczanie tego przekazywanych wartości przez szablon

Przed c++ 20, który wprowadza pewną nowość w tej dziedzinie był inny sposób na narzucenie ograniczeń.
Jest to forawrd declarations, czyli deklarowanie argumentów, jakie może przyjąć szablon. Niestety robi to odrobinę zamieszania, ponieważ tutaj musimy rozdzielić deklarację od implementacji 🙄. Zobaczmy przykład.

Plik nagłówkowy (.h)

```cpp
template <typename T>
T add(T a, T b);
```

Plik źródłowy (.cpp)

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

template int add<int>(int, int);
template double add<double>(double, double);
```

{{< space 2>}}

Oczywiście istnieje jeszcze inny sposób na blokowanie "niechcianych" argumentów już na etapie kompilacji. Jest to rzucanie błędów kompilacji (`static_assert`).

{{< space 7 >}}

# Zadanie pliki

1. Stwórz program pytający się o imię, nazwisko i zapisujący te dane w pliku w formacie "kartki". Przykład kartki:
```console
    *****************************
    *  _______________________  *
    * /                       \ *
    * |     Imię nazwisko     | *
    * \_______________________/ *
    *                           *
    *****************************
``````

2. Stwórz klasę przechowującą punkty. Podczas tworzenia punktu użytkownik deklaruje, w ilu wymiarowej przestrzeni będą one sytuowane. Dodatkowo muszą one przechowywać jego nazwę. Klasa powinna posiadać metodę służącą do zapisu tych danych do binarnego strumienia plikowego przekazanego przez parametr oraz kolejną metodę do jego odczytywania.
3. Stwórz bibliotekę służącą do czytania i zapisywania plików csv. Powinna ona posiadać funkcjonalności:
    * ustawiania czy pierwszy wiersz jest wierszem nagłówkowym
    * pobieranie wartości z aktualnego wiersza za pomocą operatora `[]`
        * Argumentem jest liczba będąca numerem kolumny
        * Argumentem jest nazwa kolumny
    * metodę `next` służącą do przechodzenia do następnego wiersza
    * metodę `good` służącą do sprawdzania, czy plik został poprawnie otwarty
    * metodę `fail` służącą do sprawdzania, czy nie wystąpiły błędy podczas czytania pliku (np. błędna liczna kolumn w jednym wierszu)
    * metodę `close` służącą do zamykania pliku

{{< space 4 >}}

# Zadania szablony

1. Stwórz funkcję `min`, `max`, `printArray`, które będą wykorzystywać mechanizm szablonów. Funkcja przyjmuje 2 argumenty i zwraca wartość.
2. Stwórz funkcję `find`, która jako parametr przyujmuje `vector` dowolnego typu i wyszukiwaną wartość i zwraca indeks poszukiwanego elementu. Jeżeli nie zostanie znaleziony, to -1.

3. Stwórz klasę `Triple`, która będzie przechowywać wartości 3 typów.
    * Posiada konstruktor bezparametryczny
    * Posiada konstruktor z 3 parametrami
    * Posiada gettery (`getFirst`, `getSecond`, `getThird`)
    * Posiada settery (`setFirst`, `setSecond`, `setThird`)
4. Stwórz klasę `MyArray`, która będzie tworzyć tablice dla elementów dowolnego typu. Wielkość tablicy powinna być przekazywana przez parametr szablonu. Klasa powinna być zgodna z zasadą RAII. Powinna posiadać metody:
    * `setValueOn(index, wartość)` - ustawianie wartości na podanym indeksie z wyrzucaniem wyjątku `out_of_range`
    * `getValoue(index)` - pobieranie wartości spod indeksu z wyrzucaniem wyjątku `out_of_range`
    * tylko konstruktor bezparametryczny
