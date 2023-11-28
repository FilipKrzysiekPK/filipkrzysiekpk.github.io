---
title: "Laboratorium 7"
layout: singleNoHeader
date: 2023-11-28
---


# Laboratorium 7

### Cele laboratorium i poruszane zagadnienia

* Wyrażenia lambda
* Wyjątki i błędy
* Obsługa wyjątków


{{< space 7 >}}

# Wyrażenia lambda

Wyrażenie lambda można określić, jako lokalna funkcja pomocnicza. Pozwala ona na oczyszczenie kodu z funkcji, które są bardzo krótkie i są wykorzystywane tylko i wyłącznie w jednym miejscu.

Dodatkowym atutem jest dość łatwe zapisanie ich w zmiennych, lecz funkcje też da się przypisać do zmiennej, chociaż trzeba nieco więcej pokombinować.

```cpp
cout << [](int a, int b) -> int {
    if ( a > b )
        return a;

    return b;
} (10, 15) << endl;
```

Powyżej możemy zauważyć bardzo prosty przykład takiej funkcji. Składa się ona z:

* `[]` - oznaczają one początek wyrażenia lambda i definiują przechwytywane nazwy (captures)
* `()` - lista parametrów, analogicznie jak w funkcji (opcjonalne)
* atrybuty - pomijamy
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

* `[=, &a]` - przekaż wszystko przez wartość, ale a przez referencję
* `[&, a]` - przekaż wszystko przez referencję, ale a przez wartość

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

{{< space 2 >}}

### Kiedy ich używać, jest to w ogóle potrzebne?

Najlepszym przykładem, kiedy używać, jest funkcja z biblioteki standardowej `sort`, która jako ostatni argument przyjmuje funkcję, albo wyrażenie lambda. Pozwala nam to wtedy nie definiować dodatkowej funkcji potrzebnej tylko i wyłącznie dla zdefiniowania, który obiekt ma się znaleźć po prawej, a który po lewej stronie. Dodatkowo w takim przypadku bez definiowania operatorów można posortować zadeklarowane klasy według własnego konceptu.

{{< space 6 >}}

# Błędy i awarie aplikacji

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

Możemy nie być tego świadomi, ale możemy, albo nawet nieświadomie unikamy błędów poprzez stosowanie typów z biblioteki standardowej (np. `string`, `vector`, `map`) oraz algorytmami (np. `sort()`, `find_if()`). Można pomyśleć, no stosowanie tego, co nam daje język, co za unikanie błędów? Zastanów się, gdyby nie było tych typów, ile błędów moglibyśmy popełnić, tworząc implementację podobnych rozwiązań, albo radząc sobie bez nich.

{{< space 5 >}}

## Przechwytywanie błędów C

Wróćmy się na chwilę do języka C. Jak zapewne kojarzycie obsługa błędów w nim jest dokonywana za pomocą `errno`, albo zwracania odpowiedniej wartości przez funkcję. Oczywiście czasem inaczej nie da się tego zrealizować, albo  byłby to przerost formy nad treścią.


## Jak to wygląda w c++?

Najprostsza i najdokładniejsza odpowiedź, to różnie. Po pierwsze dziedziczymy wszystkie sposoby z języka c, ale do tego dochodzą inne. Zacznijmy od przykładu na podstawie strumieni wejścia i wyjścia, a dokładniej interfejsu `basic_ios`. Patrząc w [dokumentację](https://en.cppreference.com/w/cpp/io/basic_ios) widzimy, że posiada on metody `fail`, `bad` i `good` pozwalajace stwierdzić, czy wszystko jest OK.

```cpp

int main() {
    cout << "Podaj liczbę całkowitą" << endl;
    int a;
    cin >> a;
    if (cin.fail()) 
        cout << "Nie wiesz co to jest liczba całkowita!?" << endl;
    else
        cout << "Twoja liczba to: " << a << endl;
    return 0;
}
```
*Najszybciej przetestujesz tego działanie [tutaj](https://www.onlinegdb.com/)*

Jak zapewne zauważyliście, pozwala to w jakimś stopniu sprawdzić, czy wszystko jest poprawne, ale nie daje całkowitej pewności. Spójrzmy jednak na to, co jest najważniejsze w tym przykładzie. Wywołując metodę na `cin` zwracany jest nam status, czy wszystko jest poprawnie (z jego punktu widzenia), czyli pierwszy sposób wykrywania błędów.

Warto dodać, że flaga `fail` będzie podniesiona, dopóki jej nie opuścimy. W przypadku `cin` należy jeszcze wyczyścić bufor, ponieważ on będzie przechowywać kolejne dane czekające na wprowadzenie.

{{< space 4 >}}

# Wyjątki

Kolejnym sposobem są wyjątki, o których już zapewne mogliśmy się dowiedzieć kilku rzeczy, czytając przytoczony fragment książki. Po pierwsze nie powinniśmy ich nadużywać, ale zanim zaczniemy próbować to robić, to skupmy się nad tym, czym one są. Rozważmy poniższy przykład:

```cpp
int main() {   
    vector<int> v;
    cout <<"V: " << v.at(100) << endl; 
    return 0;
}
```

Co będzie wynikiem działania tego programu? Program wyrzuci błąd:

```console
terminate called after throwing an instance of 'std::out_of_range'
  what():  vector::_M_range_check: __n (which is 100) >= this->size() (which is 0)
```

Mamy szczęście został rzucony wyjątek i program się zakończył z poinformowaniem nas o tym. W gorszym przypadku program zakończyłby swoje działanie natychmiast bez żadnej informacji, ewentualnie w trybie debugowania byśmy się dowiedzieli, jaki sygnał został wysłany (np. `SIGSEGV` - błąd naruszenia pamięci). Co jeżeli byśmy chcieli w jakiś sposób to kontrolować? Właśnie od tego jest mechanizm łapania wyjątków:

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

Jeszcze jeden przykład, tym razem mamy dodatkowo blok `catch` z trzema kropeczkami, jako argumentem. Wejdziemy do niego, gdy żaden wcześniejszy blok nie złapie wyjątku.

```cpp
int main()
{
    try {
         int f = std::stoi("ABBA");
    } catch (const invalid_argument& e) {
        cout << "Allocation failed: " << e.what() << '\n';
    } catch (...) {
        cout << "Caught exception in default block" << endl;
    }
}
```

{{< br >}}

Jeżeli już mamy ogólne pojęcie o tym, czym są wyjątki i jak je łapać, to teraz przejdźmy do ich rzucania. Dokonuje sie tego za pomocą słowa kluczowego `throw` i następnie podajemy wyjątek, jaki chcemy rzucić z opisem, jako argument.

```cpp
int main() {
    try {
        throw invalid_argument("try block argument not found");
    } catch (const invalid_argument& e) {
        cout << "Allocation failed: " << e.what() << '\n';
    } catch (...) {
        cout << "Caught exception in default block" << endl;
    }
    return 0;
}
```

Należy dodać, że zazwyczaj nie robimy `cout` w catchu, najwyżej dodajemy informacje do loga, ewentualnie na strumień błędów `cerr`. Najważniejsze jest to, aby "usprawnić" nasz program, zablokować daną akcję i pozwolić działać dalej, albo spróbować dokonać akcji jeszcze raz. Jeszcze raz podsumowując najważniejsze w bloku catch jest "usprawnieni" naszego programu i pozwolenie mu działać dalej.

{{< br >}}

Listę zdefiniowanych wyjątków znajdziemy w [dokumentacji](https://en.cppreference.com/w/cpp/error/exception).

{{< space 6 >}}

# Klasa do łapania wyjątków

Oczywiście, jak już się zapewne domyślacie i zdążyliście poznać c++ nie pozostajemy tylko na tych wyjątkach, które zostały zdefiniowane, sami możemy tworzyć własne wyjątki. Aby móc z tego skorzystać, skorzystamy z dziedziczenia, ponieważ odziedziczymy po klasie `exception` przepis, jak mają wyglądać takie klasy.

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

Co nam to mówi? Na pewno musimy zaimplementować metodę `what()`. Więc stwórzmy implementację naszego wyjątku.

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

Tutaj też możemy zastosować polimorfizm, aby złapać każdy wyjątek, ponieważ każda klasa wyjątku powinna dziedziczyć po klasie `exception`.

```cpp
try{
    //some code
} catch (exception &e) {
    // do sth with this!
}
```

Co nam to dało? Jeżeli zostanie rzucony jakikolwiek wyjątek, to nasz blok `catch` go złapie 😀


{{< space 8 >}}



# Zadania

1. Stwórz funkcję wyszukującą maksimum dla przesłanego równania. Równanie powinno zostać przekazane za pomocą wyrażenia lambda. (`lambda(double)`, `start x`, `end x`, `krok` - wszystko typu `double`, zwraca `double`). (jeżeli start = 1, end = 1.5, a krok = 0.2, to dla 1.5 nie trzeba sprawdzać).
   1. Przetestuj działanie dla kilku równań.
   2. Przetestuj działanie dla równań z przesłanymi dodatkowymi wartościami przez przechwytywanie nazw (captures).
2. Stwórz kontener, który wypełnisz danymi dowolnego typu. Następnie użyj funkcji z biblioteki standardowej `find_if`, aby znaleźć element z wymyślonym przez Ciebie warunkiem. Przykładowo, kontener może przechowywać napisy, a Ty chcesz znaleźć pierwszy, który ma w sobie 3 litery `a`.
3. Stwórz program, który będzie wczytywać wartość, dopóki nie uda się jej zrzutować ze `string` do `double`.
4. Stwórz deklarację swojej klasy wyjątku, która będzie przechowywać kod błędu (numer) oraz jego opis.
5. Stwórz program rzucający Twój stworzony wyjątek.




