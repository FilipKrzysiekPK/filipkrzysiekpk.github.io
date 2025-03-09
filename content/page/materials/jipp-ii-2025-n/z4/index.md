---
title: "Języki i Paradygmaty Programowania II laboratorium 4"
layout: singleNoHeader
date: 2023-04-18
---

# Laboratorium 4

### Cele laboratorium i poruszane zagadnienia

* szablony funkcji
* szablony klas
* konwersje
* obsługa plików

{{< space 7 >}}

## Konwersja typów

Konwersja typów jest to bardzo potrzebne narzędzie, pozwala nam bezproblemowo zamieniać przykładowo liczbę całkowitą na zmiennoprzecinkową. W c++ występuje niejawna konwersja typów, czyli my nie musimy mówić dosłownie "Hej skonwertuj mi tę wartość na liczbę zmiennoprzecinkową". Jest to plus, a zarazem minus, może ona też w szczególnych przypadkach powodować różne błędy.

### Niejawna konwersja typów

Jest to automatyczna konwersja typu, która wykonuje się gdy typy "argumentów" są niezgodne. Dokonywana jest ona do typów wyższych zgodnie z kolejnością:

> bool -> char -> short int -> int -> unsigned int -> long int -> unsigned long int -> long long int -> float -> double -> long double 

Przykład:

```cpp
int main() {
    int a = 5;
    float b = 8;
    cout << (a + b) << endl;
    return 0;
}
```

{{< space 3 >}}

### Jawna konwersja typów

Jawna konwersja typów, to jest taka, gdzie mówimy kompilatorowi "Hej chcę to skompilować na taki typ". Ogromnym jej plusem jest uwidocznienie takiego zabiegu i powinniśmy dążyć do tego, aby minimalizować niejawną konwersję typów. Jako ciekawostka dodam, że Rust nie posiada niejawnej konwersji typów.

Wyróżniamy 4 rodzaje konwersji typów:

* `static_cast` - rzutowanie typów prostych, które nie są ani wskaźnikami, ani referencją
* `dynamic_cast` - rzutowanie wskaźników i klas na ich pochodne, można dokonywać tego w górę i w dół
* `const_cast` - rzutowanie stałych na zmienne, zmiennych na stałe lub stałych jednego typu na inny typ
* `reinterpret_cast` - rzutowanie umożliwiające zmianę dowolnego wskaźnika jednego typu na wskaźnik innego typu (bez konwersji danych)

Składnia rzutowania:

```cpp
typ_rzutowania<typ_docelowy>(element rzutowany);
```

Poniżej przykład prostego rzutowania typów prostych:

```cpp
int main()  
{  
    float f2 = 6.7;  
    int x = static_cast<int>(f2);  
    cout << "The value of x is: " << x;  
    return 0;  
} 
```

Rzutowanie obiektów:

```cpp

class Stream {

};

class FileStream: public Stream {

};

class ConsoleStream: public Stream {

};

class Logger {
    Stream *stream;

public:
    Logger(Stream *stream);
    Stream *getStream();
}

int main() {
    Logger mainLogger = Logger(new ConsoleStream);

    // do sth

    auto cStream = dynamic_cast<ConsoleStream *>(mainLogger.getStream());
    return 0;
}

```

{{< space 3 >}}

### Reinterpret cast

Jest to bardzo specyficzne rzutowanie, które omówię osobno, ponieważ jest całkowicie odmienne od pozostałych. Do tej poty każde rzutowanie pozostawiało nam taką samą wartość (przykładowo liczba 31), a zmieniało typ. Czyli, jeżeli byśmy sobie popatrzyli dokładniej, to zmieniał się zapis binarny, ale nie była dokonywana żadna zmiana wartości.

Reinterpret cast działa tutaj troszkę odmiennie. W żaden sposób nie modyfikuje wartości (zapisu binarnego) w pamięci, zmienia jedynie sposób jej interpretowania. Dlatego też jako argument szablonu podajemy wskaźniki.

Zobaczmy sobie to na poniższym przykładzie. 

*Do wypisania binarnie użyjemy bitset.*

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

{{< space 2 >}}

Tutaj mamy inny przykład. niestety nie jest łatwo wypisać `float` binarnie (bez używania reinterpret_cast). Dokonujemy tutaj konwersji za pomocą `static_cast` i `reinterpret_cast` z `unsigned` do `float`, a następnie do `int`. Mogłoby się wydawać, że obydwa wyniki będą takie same, jednakże tak się nie dzieje.

```cpp
#include <bitset>
#include <iostream>

using namespace std;

int main() {
    unsigned a = 4294967295;
    
    float t_s = static_cast<float>(a);
    float t_d = *reinterpret_cast<float *>(&a);

    int a_staticCast = static_cast<int>(t_s);
    int a_reinterpretCast = *reinterpret_cast<int *>(&t_d);

    cout << "Original          " << a << "\t" << bitset<16>(a) << endl;
    cout << "Static cast:      " << a_staticCast << "\t" << bitset<16>(a_staticCast) << endl; 
    cout << "Reinterpret cast: " << a_reinterpretCast << "\t" << bitset<16>(a_reinterpretCast) << endl; 
    
    return 0;
}
```

{{< space 4 >}}


## Szablony

Pisząc różne funkcje, czy też tworząc klasę, czasem potrzebujemy, aby mogły funkcjonować na wszystkich typach danych, albo po prostu na typach, których nie znamy pisząc daną funkcjonalność. Załóżmy że chcemy stworzyć funkcję `min`. W momencie jej tworzenia nie wiemy na jakim typie danych użytkownik będzie z niej korzystał. Moglibyśmy przeciążyć ją każdym możliwym typem prostym, ale okazałoby się, że programista stworzyłby własny typ, który też można by było używać.

Może podejdźmy do tematu troszkę inaczej. Czy korzystaliśmy już gdzieś, gdzie bazowa klasa potrzebowała, aby jej przekazać typ? Może jakaś funkja? Gdy popatrzymy na poprzedni temat, to przykładowo:

```cpp
int a = 100;
float b = static_cast<float>(a);
```

Mamy rzutowanie, gdzie w nawiasach `< >` podawaliśmy typ. Jeszcze możemy przypomnieć sobie `vectory` do których przekazywaliśmy typ. Korzystaliśmy wtedy właśnie z szablonów (już gotowych).

### Czym są szablony?

Szablony możemy określić, jako "dodatkowa" lista parametrów, która jest wykonywana podczas kompilacji programu. W przeciwieństwie do "normalnych" parametrów, w szablonie możey przekazać typ.

{{< space 5 >}}

### Szablon funkcji

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


{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/vwIoLCrn0?theme=dark"></script>
{{< /rawhtml >}}

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

### Przeciążanie szablonów funkcji

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

### Szablon metod

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

## Strumienie plikowe

Strumienie plikowe można przyrównać do strumieni `cin` i `cout`. Dzieje się tak, ponieważ wszystkie one dziedziczą z tej samej klasy bazowej:

![Diagram klas](imgJipp/IO_library.png)
*[cppreference](https://en.cppreference.com/w/cpp/io)*

Strumienie plikowe:

![Diagram klas](imgJipp/fstream.png)
*[cppreference](https://en.cppreference.com/w/cpp/io/basic_fstream)*

Teraz można się prosto domyślić, że obsługa plików wygląda bardzo podobnie, jak strumienie `cin` i `cout`. Prześledźmy kilka najważniejszych metod tych strumieni.

### Załączanie bibliotek

Aby móc korzystać ze strumieni plikowych musimy załączyć bibliotekę `fstream`.

{{< space 5 >}}

### Inicjalizacja strumienia

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

### Sprawdzanie poprawności

Po otwarciu strumienia plikowego należy sprawdzić, czy na pewno się on otworzył. Najlepiej użyć do tego metody `is_open`. 

* `good()` - (basic_ios) sprawdza, czy nie wystąpiły jakieś błędy
* `is_open()` - sprawdza, czy strumień ma przypisany plik
* `bad()` - (basic_ios) sprawdza, czy wystąpił nieodwracalny błąd
* `fail()` - (basic_ios) sprawdza, czy nie wystąpiły jakieś błędy

{{< space 5 >}}

### Obsługa strumieni

Obsługa strumieni wygląda tak samo, jak `cin` i `cout`, tylko tyle, że zamiast tych nazw używamy obiektu przechowującego nasz strumień.

```cpp
ifstream in = ifstream("hello.txt");

if(in.is_open()) {
    string temp;
    in >> temp;
    cout << temp;
}
```

{{< space 5 >}}

### Zamykanie strumieni

Każdy strumień należy zamknąć po zakończeniu jego używania, ponieważ może to blokować dostęp do pliku. Co więcej, jeżeli go zamkniemy, to mamy pewność, że dane zostały do niego zrzucone i w razie nagłej awarii naszego programu nie zostaną one utracone. Strumienie zamykamy za pomocą metody `close()`. Możemy też ręcznie wywołać zrzucenie strumienia do pliku, służy do tego metoda `flush()`.

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

### Czytanie pliku

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

### Binarne zapisywanie i czytanie plików

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

### Binarny zapis i odczyt obiektu

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

{{< space 2 >}}

### Binarny zapis i odczyt stringów

```cpp
int main() {
    {
        ofstream out("file.bin", ios::binary | ios::out);

        string str("Hello world!");

        if (out.is_open()) {
            int size = str.size();
            out.write(reinterpret_cast<char*>(&size), sizeof(int));

            out.write(str.data(), size);
        }
    }

    // Rozwiązanie prostsze
    {
        ifstream out("file.bin", ios::binary | ios::in);

        string str;

        if (out.is_open()) {
            int size = str.size();
            out.read(reinterpret_cast<char*>(&size), sizeof(int));

            char buff[512];
            out.read(buff, size);
            str = string(buff, size);
            cout << str << endl;
        }
    }

    // Rozwiązanie sprytniejsze
    {
        ifstream out("file.bin", ios::binary | ios::in);

        string str;

        if (out.is_open()) {
            int size = str.size();
            out.read(reinterpret_cast<char*>(&size), sizeof(int));

            str.resize(size);
            out.read(str.data(), size);
            cout << str << endl;
        }
    }
    return 0;
}
```

{{< space 2 >}}

### Czy wystarczy dodać flagę bin, aby zapisywać binarnie?

Flaga `bin` podczas otwierania pliku informuje strumień, że będzie binarny. Nie wpływa to na odczytywanie wartości. Zmienia to spoósb interpretacji i wyamgań co do (mówiąc w dużym skrócie) znaków końca linii. Więcej szczegółów w [dokumentacji](https://en.cppreference.com/w/cpp/io/c/FILE#Binary_and_text_modes).

Parafrazując, jeżeli użyjemy takiej samej składni do zapisu liczby do pliku, to niezależnie, czy był to strumień binarny, czy nie, zapisze się tak samo.


{{< space 5 >}}

### Co z kodowaniem plików?

Niestey nie ma na to prostej odpowiedzi i prostego rozwiązania. W przypadku linuxa, domyślnie kodowanie plików to UTF-8 (także konsoli), więc wszystko działa poprawnie. W przypadku windowsa, a dokładniej Visual Studio, jest o wiele trudniej i zagadka jest o wiele większa. Teoretycznie będzie on forsować "swoje" kodowanie.

Nie ma bezpośredniej prostej funkcji, aby to sprawdzić i ustawić kodowanie pliku.