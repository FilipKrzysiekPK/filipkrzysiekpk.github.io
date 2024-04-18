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

Tutaj mamy iny przykład. niestety nie jest łatwo wypisać `float` binarnie (bez używania reinterpret_cast). Dokonujemy tutaj konwersji za pomocą `static_cast` i `reinterpret_cast` z `unsigned` do `float`, a następnie do `int`. Mogłoby się wydawać, że obydwa wyniki będą takie same, jednakże tak się nie dzieje.

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
