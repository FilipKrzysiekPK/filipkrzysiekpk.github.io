---
title: "Laboratorium 9"
layout: singleNoHeader
date: 2023-12-11
---


# Laboratorium 9

### Cele laboratorium i poruszane zagadnienia

* Obsługa plików


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
* `fail()`

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

# Zadanie pliki

1. Stwórz program, który będzie wczytywał plik csv, w którym są zapisane kolejno:
   - numer linii
   - przystanek początkowy
   - przystanek końcowy
   - operator

   Następnie zapisywał w osobnym pliku linie tramwajowe, a w osobnym autobusowe (linie tramwajowe mają 1 lub 2 cyfry, a autobusowe 3), w takim samym układzie (takie same kolumny, taka sama ich kolejność).

   Pliki:
   - [napisy zapisane w cudzysłowach](/filesJipp/dataQuotes.csv)
   - [napisy zapisane bez cudzysłowów](/filesJipp/dataNoQ.csv)

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

