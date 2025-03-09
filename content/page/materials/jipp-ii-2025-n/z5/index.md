---
title: Języki i Paradygmaty Programowania II laboratorium 5
layout: singleNoHeader
date: 2024-05-11T08:38:12.637Z
lastmod: 2024-05-11T11:09:31.677Z
---

# Laboratorium 5

### Cele laboratorium i poruszane zagadnienia

* obsługa błędów
* wyjątki
* lambdy
* RTTI
* biblioteka STL

{{< space 7 >}}

## Inicjalizacja zmiennych

(Drobne uzupełnienie) Inizjalizować zmienne można na różne sposoby jednak warto wiedzieć, czym one się różnią.

```cpp
// Wywołanie konstruktora
int k1 = 10;
int k2(10);

// Może następować konwersja typów
int k3 = 10.10;
int k4(10.10);

// Inicjalizacja bez konwersji typów
int k5{10};
int k6{10.10}; // błąd
```


## Obsługa błędów

Podczas działania naszej aplikacji mogą zdarzyć się błędy, nawet jeżeli wszystko dobrze zaprogramujemy. Naszym zadaniem, jako programistów, jest zabezpieczyć aplikację, tak aby ona sobie z tym radziła, albo kończyła jej działanie w kontrolowany sposób dający jasny komunikat, co się stało.

> Obsługa błędów to bardzo poważna kwestia w prawdziwych programach, więc nic dziwnego, że powstało wiele różnych technik. Kiedy w funkcji wystąpi błąd i nie ma możliwości obsłużenia go lokalnie, funkcja ta musi o tym jakoś poinformować, moduł, który ją wywołał.
> 
> (...)
> 
> Aby poinformować o braku możliwości realizacji zadania, funkcja może:
> 
> * zgłosić wyjątek
> * zwrócić wartość oznaczającą niepowodzenie
> * zamknąć program (wywołując funkcję `terminate()`, `exit()`, lub `abort()`)
> 
> O błędzie za pomoca specjalnego kodu informujemy w następujących sytuacjach:
>
> * Awaria jest czymś normalnym i spodziewanym. Na przykład nie ma nic dziwnego w tym, że czasami nie udaje się otworzyć pliku (może po prostu nie ma pliku o takiej nazwie albo nie można go otworzyć z wymaganymi uprawnieniami).
> * Bezpośredni moduł wywołujący prawdopodobnie będzie w stanie poradzić z zaistniałą sytuacją.``
>
> Wyjątek zgłaszamy w następujących sytuacjach:
> * Błąd jest tak rzadki, że programista może zapomnieć się na niego przygotować. Kto na przykład sprawdza wartość zwrotną funkcji printf()?
> * Błąd nie może zostać obsłużony przez bezpośredni moduł wywołujący i musi on zostać przekazany aż do samego początku struktury wywołań. Nie da się na przykład sprawić, by każda funkcja w aplikacji w sposób niezawodny obsługiwała każdy błąd i zanik sieci.
> * Nowe rodzaje błędów można dodawać w niższych modułach aplikacji, aby moduły na wyższym poziomie nie musiały ich obsługiwać. Przykładem może być jednowątkowa aplikacja przerobiona na wielowątkową lub umieszczenie zasobów w zdalnym magazynie i udostępnienie ich przez sieć.
> * Brak dostępnych ścieżek zwrotnych dla błędów. Na przykład konstruktor nie ma wartości zwrotnej, którą mógłby sprawdzić „wywołujący”. W szczególności konstruktory mogą być wywoływane dla kilku zmiennych lokalnych lub w częściowo zbudowanych złożonych obiektach, przez co porządkowanie na podstawie kodów błędu może być bardzo skomplikowane.
> * Ścieżkę zwrotu funkcji komplikuje lub utrudnia konieczność przekazania zarówno wartości, jak i wskaźnika błędu (np. pary — 13.4.3), przez co konieczne jest użycie, parametrów wyjściowych, nielokalnych wskaźników statusu błędu lub zastosowanie innych sztuczek.
> * Błąd musi zostać przekazany w górę łańcucha wywołań do „pierwszego wywołującego”. Wielokrotne sprawdzanie kodu błędu byłoby żmudne, nieefektywne i łatwo byłoby przy tym popełnić błąd.
> * Odzyskiwanie sprawności po wystąpieniu błędów zależy od wyników wywołań kilku funkcji, przez co konieczne jest utrzymywanie lokalnego stanu między wywołaniami i skomplikowanymi strukturami sterowania.
> * Funkcja, która znalazła błąd, była funkcją zwrotną (argumentem funkcji), przez co bezpośredni moduł wywołujący może nawet nie wiedzieć, jaka funkcja była wywołana.
> * Błąd implikuje konieczność „wycofania” pewnych działań.
> 
> Program należy zamknąć w następujących sytuacjach:
> * Nie da się naprawić błędu, na przykład w wielu (nie we wszystkich) systemach nie ma dobrego wyjścia z sytuacji, gdy zabraknie pamięci.
> * W danym systemie obsługa każdego niebanalnego błędu polega na ponownym uruchomieniu wątku, procesu lub komputera.
> 
> Jednym ze sposobów, by zapewnić zamknięcie programu, jest dodanie słowa kluczowege noexcept do funkcji, aby każde zgłoszenie wyjątku w obrębie jej implementacji zamieniało się w wywołanie funkcji terminate(). Należy mieć na uwadze, że istnieją aplikacje nie akceptujące bezwarunkowego zamknięcia i wówczas konieczne jest zastosowanie innego rozwiązania.
> 
>   Niestety wymienione warunki nie zawsze są logicznie rozdzielne i łatwe do zastosowania. Duże znaczenie ma rozmiar i poziom złożoności programu. Czasami w miarę ewolucji programu warunki się zmieniają. Potrzebne jest doświadczenie. W razie wątpliwości lepiej jest wybierać wyjątki, ponieważ łatwiej dostosowują się do zmian skali i nie wymagają używania zewnętrznych narzędzi, aby sprawdzić, czy wszystkie błędy są obsługiwane.
> 
>   Twierdzenia, że wszystkie kody błędu albo wszystkie wyjątki to zło, są błędne. Jedne i drugi mają swoje zastosowania. Ponadto mitem jest, że obsługa wyjątków jest powolna. Często jest szybsza niż poprawna obsługa skomplikowanych lub rzadkich warunków oraz wielokrotnie powtarzanych sprawdzeń kodów błędów.
> 
>   Podstawą prostej i efektywnej obsługi błędów jest metoda RAII (4.2.2, 5.3). Kod źródłowy upstrzony blokami try często ilustruje najgorsze aspekty strategii obsługi błędów wiązane z kodami błędów.

*C++ Podróż po języku dla zaawansowanych - Bjarne Stroustrup*

{{< space 3 >}}

Pozostaje jedno ważne pytanie. Czy musimy się panicznie bać błędów? Nie, po prostu musimy być świadomi, że mogą one wystąpić, wiedzieć jak się przed nimi zabezpieczyć i umieć sobie poradzić.

Możemy nie być tego świadomi, ale możemy, albo nawet nieświadomie unikamy błędów poprzez stosowanie typów złożonych z biblioteki standardowej (np. `string`, `vector`, `map`) oraz algorytmami (np. `sort()`, `find_if()`). Można pomyśleć, stosowanie tego, co nam daje język, co to za unikanie błędów? Zastanów się, gdyby nie było tych typów, ile błędów moglibyśmy popełnić, tworząc implementację podobnych rozwiązań, albo radząc sobie bez nich.

{{< space 5 >}}

## Przechwytywanie błędów C

Wróćmy się na chwilę do języka C. Jak zapewne kojarzycie obsługa błędów w nim jest dokonywana za pomocą `errno`, albo zwracania odpowiedniej wartości przez funkcję. Oczywiście czasem inaczej nie da się tego zrealizować, albo  byłby to przerost formy nad treścią.


## Jak to wygląda w c++?

Najprostsza i najdokładniejsza odpowiedź, to różnie. Po pierwsze dziedziczymy wszystkie sposoby z języka c, ale do tego dochodzą inne.

###  Ustawianie flagi błędu

Zacznijmy od przykładu na podstawie strumieni wejścia i wyjścia, a dokładniej interfejsu `basic_ios`. Patrząc w [dokumentację](https://en.cppreference.com/w/cpp/io/basic_ios) widzimy, że posiada on metody `fail`, `bad` i `good` pozwalajace stwierdzić, czy wszystko jest OK.

{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/_w0jP_pt7?theme=dark"></script>
{{< /rawhtml >}}


Jak zapewne zauważyliście, pozwala to w jakimś stopniu sprawdzić, czy wszystko jest poprawne, ale nie daje całkowitej pewności. Spójrzmy jednak na to, co jest najważniejsze w tym przykładzie. Wywołując metodę `fail` na `cin` zwracany jest nam status, czy wszystko jest poprawnie (z jego punktu widzenia).

Warto dodać, że flaga `fail` będzie podniesiona, dopóki jej nie opuścimy. W przypadku `cin` należy jeszcze wyczyścić bufor, ponieważ on będzie przechowywać kolejne dane czekające na wprowadzenie.

{{< space 4 >}}

### Zwracanie, czy zadanie zakończyło się powodzeniem

Drugie podejście do wykrywania błędów, jest zwrócenie wartości. Możemy dokonać tego, zwracając po prostu flagę (`bool`), czy jest wszystko w porządku, albo numer błędu. Częściej raczej będziemy po prostu coś zwrcać i przy okazji odpowiednie wartości będą nam sugerować, że jednak jest coś nie tak. Do przetestowania tego przypadku posłuzmy się `string`-iem. Będziemy chcieli w nim wyszukać znak, aby tego dokonać możemy stworzyć sobie pętlę, albo użyć metody [`find`](https://en.cppreference.com/w/cpp/string/basic_string/find). Przyjrzymy się, co mówi dokumentacja o zwracanych wartościach:

> **Return value**
>
> Position of the first character of the found substring or npos if no such substring is found.

Jeżeli nie zostanie znaleziony podciąg znaków, to zostanie zwrócone `string::npos`.

{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/rNSWhW1Ye?theme=dark"></script>
{{< /rawhtml >}}

{{< space 4 >}}

## Wyjątki

Kolejnym sposobem na radzenie sobie z błędzmi są wyjątki, o których już zapewne mogliśmy się dowiedzieć kilku rzeczy, czytając przytoczony fragment książki. Po pierwsze nie powinniśmy ich nadużywać, ale zanim zaczniemy próbować to robić, to skupmy się nad tym, czym one są. Rozważmy poniższy przykład:

{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/7-nxWhm6p?theme=dark"></script>
{{< /rawhtml >}}

Co będzie wynikiem działania tego programu? Uruchom go w trybie debugowania.

Zamień teraz `v[100]` na `v.at(100)`. Co teraz będzie wynikiem działania programu? Program wyrzuci błąd:

```console
terminate called after throwing an instance of 'std::out_of_range'
  what():  vector::_M_range_check: __n (which is 100) >= this->size() (which is 0)
```

Został wyrzucony wyjątek i program zakończył swoje działanie informując nas o tym. W gorszym przypadku program zakończyłby swoje działanie natychmiast bez żadnej informacji, ewentualnie w trybie debugowania byśmy się dowiedzieli, jaki sygnał został wysłany (np. `SIGSEGV` - błąd naruszenia pamięci). Mogliśmy zaobserwować to w poprzednim przypadku. Co jeżeli byśmy chcieli w jakiś sposób to kontrolować? Właśnie od tego jest mechanizm łapania wyjątków:

```cpp
int main() {
    vector<int> v;
    
    try{
        cout <<"V: " << v.at(100) << endl; 
    }
    catch (out_of_range &err) {
        cerr << err.what() << endl;
    }
    return 0;
}
```

W powyższym przykładzie łapiemy wyjątek standardowy `out_of_range`, czyli jeżeli kod w bloku try wyrzuci wyjątek `out_of_range`, to jest on przerywany i przeskakujemy do bloku `catch`. Co nam to daje? Kontrolę i zabezpiecza nas przed nagłym zamknięciem aplikacji. Przede wszystkim możemy wprowadzić jakieś działanie naprawcze, które będzie w stanie usprawnić naszą aplikację.

### Zadanie utrwalające

Przećwiczmy odrazu to co przed chwilą poznaliśmy. Zmodyfikuj poniższy prgram w ten sposób, aby nie zakańczał się niepowodzeniem.

Funkcja `stoi` wyrzuca `invalid_argument`.

{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/Mx7JKDSRU?theme=dark"></script>
{{< /rawhtml >}}

### Łapanie wielu wyjątków

Co w przypadku, jeżeli nasza aplikacja może wyrzucić więcej wyjątków? Możemy mieć kilka bloków `catch`.

```cpp
int main() {
    vector<int> v;
    
    try{
        v.resize(10000);
        cout <<"V: " << v.at(100) << endl; 
    }
    catch (out_of_range &err) {
        cerr << err.what() << endl;
    }
    catch (bad_alloc &err) {
        cerr << err.what() << endl;
    }
    return 0;
}
```

W tym przypadku łapiemy wyjątki dla wyjścia poza zakres i w razie problemów z alokacją.

Oczywiście jak łapiemy wyjątek w bloku `catch`, no to używamy do tego referencji, nie ma sensu wykonywanie kopii.

### Łapanie wszystkich wyjątków

Jeszcze jeden przykład, tym razem mamy dodatkowo blok `catch` z trzema kropeczkami, jako argumentem. Wejdziemy do niego, gdy żaden wcześniejszy blok nie złapie wyjątku.

```cpp
int main()
{
    try {
         int f = std::stoi("ABBA");
    } catch (const invalid_argument& e) {
        cout << "Allocation failed: " << e.what() << endl;
    } catch (...) {
        cout << "Caught exception in default block" << endl;
    }
}
```

Z definicji wszystkie wyjątki powinny dziedziczyć po klasie `std::exception`. Idąc tą drogą jeżeli w miejscu przechwytywanego wyjątku (dokładniej rzecz mówiąc typu) damy `exception`, to wszystko co po nim dziedziczyło powinno zostać złapane. Poniekąd powracamy do polimorfizmu dynamicznego :) 

```cpp
int main()
{
    try {
         int f = std::stoi("ABBA");
    } catch (exception& e) {
        cout << "Catched exception: " << e.what() << endl;
    }
}
```

### Rzucanie wyjątków

Jeżeli już mamy ogólne pojęcie o tym, czym są wyjątki i jak je łapać, to teraz przejdźmy do ich rzucania. Dokonuje sie tego za pomocą słowa kluczowego `throw` i następnie podajemy wyjątek, jaki chcemy rzucić z opisem, jako argument.

```cpp
int main() {
    try {
        throw invalid_argument("try block argument not found");
    } catch (const invalid_argument& e) {
        cout << "Allocation failed: " << e.what() << '\n';
    }
    return 0;
}
```

Należy dodać, że zazwyczaj nie robimy `cout` w catchu, najwyżej dodajemy informacje do loga, ewentualnie na strumień błędów `cerr`. Najważniejsze jest to, aby "usprawnić" nasz program, zablokować daną akcję i pozwolić działać dalej, albo spróbować dokonać akcji jeszcze raz.

Listę zdefiniowanych wyjątków znajdziemy w [dokumentacji](https://en.cppreference.com/w/cpp/error/exception).

{{< space 6 >}}

### Klasa do łapania wyjątków

Oczywiście, jak już się zapewne domyślacie i zdążyliście poznać c++ nie pozostajemy tylko na tych wyjątkach, które zostały zdefiniowane w bibliotece standardowej, sami możemy tworzyć własne wyjątki. Jak już wcześniej wspominaliśmy wszystkie wyjątki powinny dziedziczyć po klasie `exception`, więc tworząc własny wyjątek będziemy musieli ją odziedziczyć.

**Przykład Klasa exception wydostany z plików nagłowkowych:**
```cpp
namespace std
{
  /**
   * @defgroup exceptions Exceptions
   * @ingroup diagnostics
   *
   * Classes and functions for reporting errors via exception classes.
   * @{
   */

  /**
   *  @brief Base class for all library exceptions.
   *
   *  This is the base class for all exceptions thrown by the standard
   *  library, and by certain language expressions.  You are free to derive
   *  your own %exception classes, or use a different hierarchy, or to
   *  throw non-class data (e.g., fundamental types).
   */
  class exception
  {
  public:
    exception() _GLIBCXX_NOTHROW { }
    virtual ~exception() _GLIBCXX_TXN_SAFE_DYN _GLIBCXX_NOTHROW;
#if __cplusplus >= 201103L
    exception(const exception&) = default;
    exception& operator=(const exception&) = default;
    exception(exception&&) = default;
    exception& operator=(exception&&) = default;
#endif

    /** Returns a C-style character string describing the general cause
     *  of the current error.  */
    virtual const char*
    what() const _GLIBCXX_TXN_SAFE_DYN _GLIBCXX_NOTHROW;
  };

} // namespace std
```

Co nam to mówi? Na pewno musimy zaimplementować metodę `what()`. Więc stwórzmy implementację naszego wyjątku. Będzie ona bardzo prosta, będzie przechowywać tylko treść błędu.

```cpp
class MyException: public exception {
    char *msg;

public:
    MyException(char *message): msg(message) {}

    char* what() {
        return msg;
    }
}
```

Teraz przetestujmy działanie naszego wyjątku.

```cpp
int main() {
    try {
        throw MyException("Oh no!");
    } catch (MyException &e) {
        cerr << e.what() << endl;
    }
    return 0;
}
```

Albo możemy zrobić to też bardziej uniwersalnie 😀 :

```cpp
try{
    throw MyException("Oh no!");
} catch (exception &e) {
    cerr << e.what() << endl;
}
```

Oczywiście klasy wyjątków moga być dużo bardziej rozbudowane, posiadać dodatkowe pola, albo szczegółowszy/bardziej czytelny sposób wypisywania informacji o błędzie. Też po prostu mogą być informacją, jaki błąd został wyrzucony.

{{< space 3 >}}

### Łapanie wyjątków z innego poziomu zagnieżdżenia

Do tej pory wszystkie wyjątki, jakie łapaliśmy łapaliśmy bezpośrednio w tym samym bloku, w którym je wyrzucaliśmy. Czyli de facto podobnie do wcześniejszych rozwiązań. Jednakże ogormną cechą wyjątków, jest to, że nie musimy być bezpośrednio w tym bloku, wyjątek może rzucić element zagnieżdżony gdzieś w tym elemencie.

{{< space 3 >}}

## Łapanie błędów na etapie kompilacji

Są takie przypadki, że możemy już na etapie kompilacji stwierdzić, że pewne działania są błędne. Przykładowo pewne wywołanie funkcji nie jest dozwolone. Wówczas już podczas kompilacji możemy poinformować o tym wyrzucając błąd kompilacji. Służy do tego `static_assert`.

{{< space 7 >}}

## Wyrażenia lambda

Wyrażenie lambda można określić, jako lokalna funkcja pomocnicza. Pozwala ona na oczyszczenie kodu z funkcji, które są bardzo krótkie i są wykorzystywane tylko i wyłącznie w jednym miejscu.

Dodatkowym atutem jest dość łatwe zapisanie ich w zmiennych, lecz funkcje też da się przypisać do zmiennej, chociaż trzeba nieco więcej pokombinować (albo użyć specjalnego typu).

```cpp
cout << [](int a, int b) -> int {
    if ( a > b )
        return a;

    return b;
} (10, 15) << endl;
```

Powyżej możemy zauważyć bardzo prosty przykład takiej funkcji lambda. Znajduje się tam jej inicjalizacja oraz odrazu jej wywołanie. Składa się ona z:

* `[]` - oznaczają one początek wyrażenia lambda i definiują przechwytywane nazwy (captures)
* `()` - lista parametrów, analogicznie jak w funkcji (opcjonalne)
* `-> T` - typ zwracany przez wyrażenie lambda (opcjonalne)
* `{}` - ciało wyrażenia lambda, które jest analogiczne do funkcji


`()` - nawiasy na końcu służą do wywołania wyrażenia, nie są jego częścią!

Jeżeli nie określimy zwracanego typu, kompilator dedukuje sam, jaki typ jest zwracany. Spójrzmy na poniższy kod.

```cpp
cout << [](int a, int b) {
    if ( a > b )
        return a;

    return b * 1.0;
} (10, 15) << endl;
```

Nie ma wyspecyfikowanego typu zwracanego, a zwracane są 2 różne typy. Skończyło się to zwróceniem błędu kompilacji:

```console
main.cpp: In lambda function:
main.cpp:30:16: error: inconsistent types ‘int’ and ‘double’ deduced for lambda return type
   30 |     return b * 1.0;
      |                ^~~
```

Jeżeli dodamy deklarację zwracanego typu, wtedy kod się poprawnie skompiluje i zwróci wartość.

{{< space 2 >}}

### Przechwytywanie nazw (captures)

Przechwytywanie nazw, a raczej przekazywanie zmiennych do wyrażenia lambda. Pozwala ono przekazać zadeklarowaną wcześniej zmienną przekazać do wyrażenia lambda. Możemy tego dokonać na 2 sposoby, przekazując przez wartość, albo referencję.

```cpp
int main() {
    int a = 0;
    int b = 4;

    cout << [a, b] { return a + b }() << endl;

    return 0;
}
```

Jeżeli chcemy przekazać wszystkie zmienne, to możemy użyć:

* `[=]` - przekaż wszystko przez wartość
* `[&]` - przekaż wszystko przez referencję

Oczywiście o wiele lepiej jest wymieniać wszystkie zmienne, które chcemy tak przekazać, ponieważ zwiększa to czytelność i intencje. 

Warto jeszcze wspomnieć, że przy przekazywaniu wszystkiego możemy wyspecyfikować, że któryś element ma zostać przekazany w inny sposób:

* `[=, &a]` - przekaż wszystko przez wartość, ale `a` przez referencję
* `[&, a]` - przekaż wszystko przez referencję, ale `a` przez wartość
* `[&a, b]` - przekaż `a` przez referencję oraz `b` przez wartość 

{{< space 2 >}}

### Przekazywanie wyrażeń lambda

Jak już wcześniej było wspomniane, wyrażenie lambda możemy zapisać do zmiennej. Zapewne użyjemy wtedy słowa kluczowego `auto`, ponieważ nie będziemy musieli się męczyć z jego definiowaniem. Jednakże tworząc deklarację funkcji, już tak postąpić nie możemy. Aby móc z tego korzystać, musimy załączyć bibliotekę `functional`.

```cpp
void fun(function<int( int )> f, int n) {
    cout << f(n) << endl;
}

int main() {
    fun([]( int x ) { return x; }, 5);
    fun([]( int x ) { return x * x; }, 5);
    return 0;
}
```

### Zadanie utrwalające

Dopisz wywołanie funkcji `funMin`, które będzie przekazywać lambdę jako pierwszy parametr z dowolną funkcją jednej zmiennej.

{{< rawhtml >}}
<script src="//onlinegdb.com/embed/js/Cg6lLG6eD?theme=dark"></script>
{{< /rawhtml >}}

{{< space 7 >}}

## RTTI - Run Time Type Information

Run Time Type Information pozwala nam na uzyskanie informacji o typie w czasie działania programu.

Więcejj informacji można uzyskać na blogu [cpp polska](https://cpp-polska.pl/post/dynamic-cast-oraz-type-id-jako-narzedzia-rtti).

{{< space 7 >}}

## Biblioteka STL

Biblioteka standardowa zawiera bardzo dużo ułatwień, zdefiniowanych funkcji oraz gotowych algorytmów. Dzięki temu nie musimy, a nawet w większości przypadków nie powinniśmy (poza celami edukacyjnymi) tworzyć na nowo gotowych rozwiązań.

Spis pogrupowanych elementów można znaleźć na pierwszej stronie [dokumentacji](https://en.cppreference.com/w/)

Wyróżnię tutaj kilka grup elementów, z których korzystałem, korzystam, bądź znam:

1. Biblioteka `chrono` - wszelkie operacje na czasie (od c++20 zaczyna być wsparcie do dostępu do poszczególnych składowych daty i czasu) [dokumentacja](https://en.cppreference.com/w/cpp/chrono)
2. Biblioteka `memory` - inteligentne wskaźniki, czyli sposób, jak całkowicie pożegnać się z alokacją i dealokacją pamięci
3. `pair`, `tuple` - kontener 2, 3 elementowy, pozwalający przechowywać parę, albo trójkę zwiazanych ze soba wartości, przydatne gdy chcemy zwrócić kilka elementów. [dokumentacja](https://en.cppreference.com/w/cpp/utility#General-purpose_utilities)
4. Dużo wydajniejsza zamiana miejscami dwóch elementów, albo podmienianie jednego elementu na drugi (`swap`, `move`).
5. Zmienne do przechowywania napisów (`string`) oraz odchudzony `string` [dokumentacja](https://en.cppreference.com/w/cpp/string)
6. Kontenery, różne z różnym zapisem pod spodem: [dokumentacja](https://en.cppreference.com/w/cpp/container)
7. Iteratory, można go określić, jako wskaźnik ułatwiający poruszanie się po tabeli, czy też kontenerze. [dokumentacja](https://en.cppreference.com/w/cpp/iterator)
8. Biblioteka `algorithm` [dokumentacja](https://en.cppreference.com/w/cpp/algorithm). Możemy w niej wyróżnić:
   * czy wszystkie, którykolwiek, żaden z elementów spełnia przekazany warunek
   * wyszukiwanie elementów
   * zliczanie elementów
   * wypełnianie kontenera
   * usuwanie duplikatów elementów
   * sortowania
   * złączanie dwóch kontenerów
9. Biblioteka `filesystem` - wszelkie operacja na plikach. [dokumentacja](https://en.cppreference.com/w/cpp/filesystem). 
10. Biblioteka `regex` - wyrażenia regularne. [dokumentacja](https://en.cppreference.com/w/cpp/regex).
11. biblioteka do obsługi wielowątkowości. [dokumentacja](https://en.cppreference.com/w/cpp/thread)
12. Zakresy wartości poszczególnych typów zmiennych. [dokumentacja](https://en.cppreference.com/w/cpp/types/numeric_limits)

{{< space 2 >}}

Ciekawostki, które znalazłem przygotowując listę:

1. `format_to` - string z argumentami [dokumentacja](https://en.cppreference.com/w/cpp/utility/format/format_to)
2. `variant` - przechowywanie dowolnego typu w zmiennej [dokumentacja](https://en.cppreference.com/w/cpp/utility/variant)
3. `optional` - nie musimy zwracać elementu [dokumentacja](https://en.cppreference.com/w/cpp/utility/optional)

{{< space 7 >}}

# Zadania 

1. Wypełnij tablicę/kontener losowymi liczbami. Znajdź w bibliotece standardowej funkcje sortujące i posortuj wcześniej stworzoną tablicę/kontener.
2. Stwórz prostą klasę (wszystko może być publiczne). Używając metody z biblioteki standardowej znajdź pierwszy element, który będzie taki sam.
3. (wersja trudniejsza) Znajdź pierwszy element, ktory będzie zawierać dane pole ze wskazaną wartością (przykladowo dla klasy student, znajdź pierwszego studenta, który będzie mieć nazwisko `Kowalski`).
