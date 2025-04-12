---
title: FTXUI - wprowadzenie
layout: singleNoHeader
date: 2024-03-10
toc: true
lastmod: 2025-04-12T12:42:21.109Z
draft: false
---

# FTXUI - wprowadzenie

## O bibliotece

Biblioteka FTXUI (Functional Terminal (X) User Interface) jest to darmowa, prosta międzyplatformowa biblioteka c++ do tworzenia terminalowych interfejsów graficznych. Dokumentacja, prócz części dokumentacyjnej, zawiera tutorial oraz przykłady.
Biblioteka wspiera akcje bazujące na klawiaturze i myszce oraz wsparcie dla UTF-8.

[github.com](https://github.com/ArthurSonzogni/FTXUI)

[dokumentacja](https://arthursonzogni.github.io/FTXUI/)

## Instalacja

Autor zaleca [instalację za pomocą CMake](https://arthursonzogni.github.io/FTXUI/#build-cmake). W tym celu w głównym pliku `CMakeLists.txt` wklej ponizszy kod:

```cmake
include(FetchContent)
set(FETCHCONTENT_UPDATES_DISCONNECTED TRUE)
FetchContent_Declare(ftxui
  GIT_REPOSITORY https://github.com/ArthurSonzogni/ftxui
  GIT_TAG main # Important: Specify a version or a commit hash here.
)
FetchContent_MakeAvailable(ftxui)
```

Oraz pod deklaracją Twojej aplikacji (zakładamy, że target nazywa się `App`):

```cmake
target_link_libraries(App
  ftxui::screen
  ftxui::dom
  ftxui::component
)
```

Przeładuj teraz plik CMake. Biblioteka powinna się zainstalować.

{{< space 7 >}}

## Obsługa biblioteki

### Zacznijmy od Hello world!

Zacznijmy od najprostszej aplikacji wypisującej na ekran `Hello world!`. W tym celu:

* załączymy odpowiednie pliki nagłowkowe
* stworzymy ekran
* stworzymy element tekstowy
* karzemy wyrenderować i wyświetlić na ekranie

```cpp
#include "ftxui/dom/elements.hpp"
#include "ftxui/screen/screen.hpp"

int main() {
    auto screen = ftxui::Screen::Create(ftxui::Dimension::Full());
    ftxui::Element textElem = ftxui::text("Hello World");

    Render(screen, textElem);
    screen.Print();
    return 0;
}
```

Oczywiście możemy sobie ułatwić życie i zadeklarować używanie przestrzeni `ftxui`.

Jako, że jest to pierwszy przykład omówmy sobie go linia po linii.

```cpp
auto screen = ftxui::Screen::Create(ftxui::Dimension::Full());
```

Tworzymy tutaj ekran, tak, jakby urządzenie docelowe i mówimy, że ma użyć całej dostępnej przestrzeni. Zmień `Full` na `Fixed(6)`. Co się stało? Możemy też osobno zadeklarować szerokość i wysokość, gdzie jedna będzie pełna, a druga statyczna:

```cpp
auto screen = ftxui::Screen::Create(ftxui::Dimension::Full(), ftxui::Dimension::Fixed(2));
```

Teraz tworzymy nowy element interfejsu, który jest tekstem:

```cpp
ftxui::Element textElem = ftxui::text("Hello World");
```

Na końcu mówimy przetwórz ten element na dae wyjściowe ekranu, a następnie wyświetl to:

```cpp
Render(screen, textElem);
screen.Print();
```

#### Kolorowanie i modyfikowanie tekstu

Ustawianie parametrów obiektów zazwyczaj realizuje się za pomocą operatora binarnego lub, czyli `|`. Nie bój się, że to jakies binarne lub, po prostu ktoś miał taką koncepcję i akuratnie tego operatora użył.

No to jeszcze raz ustawianie parametrów realizuje się po przez dopisanie informacji o modyfikacji za elementem. Czyli zmiana koloru tekstu na czerwony będzie wyglądać następująco:

```cpp
ftxui::Element textElem = ftxui::text("Hello World") | ftxui::color(ftxui::Color::Red);
```

Teraz zmieńmy jeszcze kolor tła:

```cpp
ftxui::Element textElem = ftxui::text("Hello World") | ftxui::color(ftxui::Color::Red) | ftxui::bgcolor(ftxui::Color::Gold1);
```

Pogrubmy jeszcze tekst:

```cpp
ftxui::Element textElem = ftxui::text("Hello World") | ftxui::color(ftxui::Color::Red) | ftxui::bgcolor(ftxui::Color::Gold1) | ftxui::bold;
```

Dla czytelności załączmy przestrzeń nazw `ftxui`:

```cpp
Element textElem = text("Hello World") | color(Color::Red) | bgcolor(Color::Gold1) | bold;
```

Znacząco zwiększyło to czytelność.

{{< space 7 >}}

## Pierwszy przycisk

Dodajmy teraz pierwszy przycisk do naszej aplikacji. Póki co będzie on służył do jej zamykania.

Aby używać tego typu elementów musimy zmienić `Screen` na `ScreenInteractive`:

```cpp
auto screen = ScreenInteractive::Fullscreen();
```

Stwórzmy teraz pierwszy przycisk. Jako pierwszy parametr przekazujemy label, a jako drugi akcję, która wykonuje się po jego przyciśnięciu.

```cpp
Component closeButton = Button("Close", screen.ExitLoopClosure());
```

Teraz musimy określić, jak te elementy będą miały się rysować. Bedzie się to odbywało w inny sposób, ponieważ wcześniej narysowaliśmy raz nasz interfejs i to wystarczyło, a teraz może się on zmieniać.

Tworzymy obiekt `Renderer`, któremu przekazujemy nasz stworzony przycisk, a następnie wyrażenie lambda, które określa co i jak narysować. Póki co znajomym elementem jest `text` i `closeButton`, który wcześniej stworzyliśmy. Jeżeli chcemy dodać wcześniej stworzone elementy, musimy na nich wykonać metodę `Render()`. Zostaje nam ostatni element, czym jest ten `vbox`? Jest to taki kontenerek, albo po prostu pojemnik, jak dodajemy do niego elementy to układają się one wertykalnie (stąd to v od vertical).

Wróćmy jeszcze doo tego, że do `Renderer`-a przekazujemy nasz przycisk, ale w lambdzie też go dorzucamy. Po co? W lambdzie mówimy, jak go narysować, a przekazując do renderer-a mówimy, żeby go monitorował, czy nie są wykonywane na nim jakieś akcje.

```cpp
Component renderer = Renderer(closeButton, [&] {
    return vbox({
    text("Hello World!"),
    closeButton->Render()
    });
});
```

Ok, czyli pozostało nam tylko kazać to narysować. Tym razem nie będzie to się odbywało, jak wcześniej. tym razem interaktywnemu ekranowi będziemy kazali uruchomić pętlę. Dlatego wcześniej przyciskowi przekazaliśmy akcję `ExitLoopClosure`.

```cpp
screen.Loop(renderer);
```

Oto cały kod, przetestuj jego działanie:

```cpp
#include "ftxui/component/component.hpp"
#include "ftxui/component/screen_interactive.hpp"
#include "ftxui/dom/elements.hpp"

int main() {
    using namespace ftxui;
    auto screen = ScreenInteractive::Fullscreen();

    Component closeButton = Button("Close", screen.ExitLoopClosure());

    Component renderer = Renderer(closeButton, [&] {
        return vbox({
        text("Hello World!"),
        closeButton->Render()
        });
    });

    screen.Loop(renderer);
    return 0;
}
```

Teraz usuń z parametru renderer-a `closeButton` (w lambdzie ma zostać). Jak aplikacja działa teraz?

W późniejszych przykładach będzie sie pojawiać oprócz `vbox` także `hbox`, czyli horyzontalny pojemnik na elementy.

{{< space 7 >}}

## Pole wejściowe

Stworzylismy przycisk, to teraz czas stworzyć jakieś pole wejściowe. Na sam początek stwórzmy aplikację, która będzie chciała, abyśmy podali imię i nazwisko, a następnie wyświetlimy to za pomocą `cout`.

Będziemy działać troszkę analogicznie do wcześniejszego przykładu, tylko zaczniemy od tworzenia `Input`-ów.

```cpp
std::string nameStr;
std::string lastNameStr;

Component nameInput = Input(&nameStr);
Component lastNameInput = Input(&lastNameStr);
```

Następnie musimy stworzyć kontener trzymający elementy. Dzięki niemu biblioteka będzie wiedziała, jak nawigować po tych elementach.

```cpp
Component compContainer = Container::Vertical({
    nameInput,
    lastNameInput,
    closeButton
});
```

Pozostaje nam zaktualizować renderer. Wykorzystaliśmy tutaj stworzony wcześniej przycisk zamykania oraz pojawia się tutaj `hbox`, o którym było już wspominane wcześniej. Zauważ, że teraz przekazujemy `compContainer`, zamiast `closeButton` do `Renderer`-a jako pierwszy parametr!

```cpp
Component renderer = Renderer(compContainer, [&] {
    return vbox({
    hbox({text("Name      : "), nameInput->Render()}),
    hbox({text("Last name : "), lastNameInput->Render()}),
    closeButton->Render()
    });
});
```

Na końcu po `screen.Loop` należy dodać odpowiedniego `cout`. Cały kod znajduje się poniżej.

```cpp
#include <iostream>

#include "ftxui/component/component.hpp"
#include "ftxui/component/screen_interactive.hpp"
#include "ftxui/dom/elements.hpp"

int main() {
    using namespace ftxui;
    auto screen = ScreenInteractive::Fullscreen();

    std::string nameStr;
    std::string lastNameStr;

    Component nameInput = Input(&nameStr);
    Component lastNameInput = Input(&lastNameStr);

    Component closeButton = Button("Close", screen.ExitLoopClosure());

    Component compContainer = Container::Vertical({
        nameInput,
        lastNameInput,
        closeButton
    });

    Component renderer = Renderer(compContainer, [&] {
        return vbox({
        hbox({text("Name      : "), nameInput->Render()}),
        hbox({text("Last name : "), lastNameInput->Render()}),
        closeButton->Render()
        });
    });

    screen.Loop(renderer);

    std::cout << nameStr << " " << lastNameStr << std::endl;
    
    return 0;
}
```

Zauważ, że teraz da się wprowadzić znak nowej linii w polach. Nie chcemy tego. W tym celu zadeklarujmy `InputOptions`, któremu ustawimy odpowiednią flage i przekażemy jako drugi parametr do `Input`-ów:

```cpp
InputOption inputOptions;
inputOptions.multiline = false;

Component nameInput = Input(&nameStr, inputOptions);
Component lastNameInput = Input(&lastNameStr, inputOptions);
```

### Dynamiczne aktualizowanie wartości

Ok, to teraz zaktualizujmy poprzednie zadanie tak, aby dynamicznie zmieniało wartości tekstu wyświetlanego na ekranie. Aby to osiągnąć wystarczy, że zmienimy `Renderer`-a:

```cpp
Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render()}),
        hbox({text("Last name : "), lastNameInput->Render()}),
        separator(),
        hbox({text("Name      : " + nameStr)}),
        hbox({text("Last name : " + lastNameStr)}),
        closeButton->Render()
    });
});
```

### Zmiana wartości po akceptacji

Dynamiczna zmiana jest ok, ale jest zbyt dynamiczna. Lepiej by było, gdyby dokonywała się po przyciśnięciu przycisku. W tym celu dodajmy przycisk i stwórzmy mu wyrażenie lambda, które będzie kopiowało tekst z inputów do tekstu wyświetlanego.

Zacznijmy od stworzenia nowego przycisku:

```cpp
std::string nameStrDisp;
std::string lastNameStrDisp;

Component updateButton = Button("Update", [&] {
    nameStrDisp = nameStr;
    lastNameStrDisp = lastNameStr;
});
```

Teraz oczywiście musimy zaktualizować naszego renderera, aby wyświetlał poprawne teksty oraz dodać przycisk.

```cpp
Component compContainer = Container::Vertical({
    nameInput,
    lastNameInput,
    closeButton,
    updateButton
});

Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render()}),
        hbox({text("Last name : "), lastNameInput->Render()}),
        separator(),
        hbox({text("Name      : " + nameStrDisp)}),
        hbox({text("Last name : " + lastNameStrDisp)}),
        hbox({closeButton->Render(), updateButton->Render()})
    });
});
```

{{< space 7 >}}

## Pozycjonowanie - rozmiary elementów

Powiedzmy, że chcemy teraz lekko zmienić wygląd naszego okienka. Zacznijmy od tego, że inputy maja mieć szerokość 20 znaków. Pamiętasz, jak zmienialiśmy kolor? To ustawianie rozmiarów będzie polegało na tym samym:

```cpp
Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Last name : "), lastNameInput->Render() | size(WIDTH, EQUAL, 20)}),
        separator(),
        hbox({text("Name      : " + nameStrDisp)}),
        hbox({text("Last name : " + lastNameStrDisp)}),
        hbox({closeButton->Render(), updateButton->Render()})
    });
});
```

Przeanalizujmy to co tam wstawiliśmy. Dodaliśmy specyfikator rozmiaru, który ustawia mu szerokość i ona ma być równa 20. Możemy też ustawić wysokość oraz określić, że ma być mniejsza, lub większa od.

Teraz spróbujmy wyśrodkować przyciski. Aby to zrobic musimy je poprzedzić pustym elementem, który jest tutaj określany mianem `filler`.

```cpp
Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Last name : "), lastNameInput->Render() | size(WIDTH, EQUAL, 20)}),
        separator(),
        hbox({text("Name      : " + nameStrDisp)}),
        hbox({text("Last name : " + lastNameStrDisp)}),
        hbox({filler(), closeButton->Render(), updateButton->Render(), filler()})
    });
});
```

## Input liczbowy

Biblioteka niestety nie oferuje możliwości stworzenia bezpośrednio inputu typowo liczbowego. Możemy sobie jednak z tym poradzić na co najmniej dwa sposoby.

### Walidacja przed akceptacją

Możemy przed przejściem do następnego kroku spróbować zrzutować stringa na int (przykładowo). Dokonuje się tego za pomocą funkcji `stoi`. Jeżeli rzutowanie nie powiedzie się, to jest wyrzucany wyjątek.

Zmodyfikujmy w tym celu akcje wykonywaną podczas przyciśnięcia przycisku `Update`:

```cpp
int ageInt = 0;

Component updateButton = Button("Update", [&] {
    nameStrDisp = nameStr;
    lastNameStrDisp = lastNameStr;

    try {
        ageInt = std::stoi(ageStr);
        ageIsInvalid = false;
    } catch (...) {
        ageIsInvalid = true;
    }
});
```

Stwórzmy funkcję zwracającą kolor czerwony, gdy jest nie właściwe i biały, gdy wszystko jest OK:

```cpp
ftxui::Color getColorValidInvalid(bool isOk) {
    if (isOk) {
        return ftxui::Color::White;
    } else {
        return ftxui::Color::Red;
    }
}
```

Teraz dodajmy kolorowanie w zależności od tego, czy wszystko jest OK, czy jest jakiś problem:

```cpp
Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Last name : "), lastNameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Age       : ") | color(getColorValidInvalid(!ageIsInvalid)), ageInput->Render() | size(WIDTH, EQUAL, 20)}),
        separator(),
        hbox({text("Name      : " + nameStrDisp)}),
        hbox({text("Last name : " + lastNameStrDisp)}),
        hbox({filler(), closeButton->Render(), updateButton->Render(), filler()})
    });
});
```

### Ograniczenie przyjmowanych znaków

Drugim sposobem jest zablokowanie wprowadzani innych znaków, niż te dozwolone. W tym celu musimy dodać łapanie wydarzeń. Jeżeli wydarzenie zwróci `true`, to znak nie zostanie, ponieważ zostanie ono złapane i nie chcemy, aby wykonywały się dalsze akcje. Gdy zwracamy `false` oznacza, że nie spełniło naszych warunków, niech będzie dalej przetwarzane.

```cpp
Component ageInput = Input(&ageStr, inputOptions);

ageInput |= CatchEvent([&](Event event) {
    return event.is_character() && ! std::isdigit(event.character()[0]); 
});
```

```cpp
Component compContainer = Container::Vertical({
    nameInput,
    lastNameInput,
    ageInput,
    closeButton,
    updateButton
});
    
Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Name      : "), nameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Last name : "), lastNameInput->Render() | size(WIDTH, EQUAL, 20)}),
        hbox({text("Age       : "), ageInput->Render() | size(WIDTH, EQUAL, 20)}),
        separator(),
        hbox({text("Name      : " + nameStrDisp)}),
        hbox({text("Last name : " + lastNameStrDisp)}),
        hbox({filler(), closeButton->Render(), updateButton->Render(), filler()})
    });
});
```

Pamiętaj o tym, że jak ograniczyliśmy przyjmowane znaki tylko do cyfr, to nadal nie daje nam to pewności, że uda sie nam dokonać konwersji tekstu na liczbę (może być ona zbyt długa).


{{< space 7 >}}

## Pasek progresu

Udało nam się stworzyć pole wejściowe przyjmujące liczby, to teraz możemy spróbować zwizualizować tę wartość na pasku progresu. Przyjmuje on wartości typu `float` z zakresu `0.0` - `0.1. Zwróć uwagę na `guage` w Renderze. Poniżej pełny przykład:

```cpp
#include <iostream>

#include "ftxui/component/component.hpp"
#include "ftxui/component/screen_interactive.hpp"
#include "ftxui/dom/elements.hpp"

ftxui::Color getColorValidInvalid(bool isOk) {
    if (isOk) {
        return ftxui::Color::White;
    } else {
        return ftxui::Color::Red;
    }
}

int main() {
    using namespace ftxui;
    auto screen = ScreenInteractive::Fullscreen();

    std::string ageStr;

    InputOption inputOptions;
    inputOptions.multiline = false;

    Component ageInput = Input(&ageStr, inputOptions);

    ageInput |= CatchEvent([&](Event event) {
       return event.is_character() && ! std::isdigit(event.character()[0]);
    });

    Component closeButton = Button("Close", screen.ExitLoopClosure());

    bool ageIsInvalid = false;

    int ageInt = 0;

    Component updateButton = Button("Update", [&] {
        try {
            ageInt = std::stoi(ageStr);
            ageIsInvalid = false;
        } catch (...) {
            ageIsInvalid = true;
        }
    });

    Component compContainer = Container::Vertical({
        ageInput,
        closeButton,
        updateButton
    });

    Component renderer = Renderer(compContainer, [&] {
        return vbox({
            hbox({text("Age       : ") | color(getColorValidInvalid(!ageIsInvalid)), ageInput->Render() | size(WIDTH, EQUAL, 20)}),
            hbox({gauge(ageInt / 100.)}),
            hbox({filler(), closeButton->Render(), updateButton->Render(), filler()})
        });
    });

    screen.Loop(renderer);

    return 0;
}
```

Jeżelibyśmy chcieli odświeżać pasek progresu w związku z ładowaniem pliku itp itd, to musielibyśmy działać wielowątkowo, ponieważ głowny wątek jest okupowany przez `screen.Loop`.

{{< space 7 >}}

## Menu

Czas stworzyć menu naszej aplikacji. Może się wydawać to bardzo trudne zadanie, ale jest gotowy komponent, który to realizuje. Zacznijmy od stworzenia `vector`-a z elementami, które będziemy chcieli mieć w menu oraz zmiennej, która będzie przechowywać, co zostało wybrane:

```cpp
std::vector<std::string> entries = {
    "Hello world!",
    "Hi Patrick",
    "Today is the day",
};
int selected = 0;
```

Teraz stwórzmy menu, zdefiniujmy akcję, co zrobić po wybraniu elementu (za pomocą opcji).

```cpp
MenuOption option;
option.on_enter = screen.ExitLoopClosure();

Component menu = Menu(&entries, &selected, option);
```

Uruchommy pętlę renderującą, a następnie wypiszmy na ekranie, która opcja została wybrana:

```cpp
screen.Loop(menu);
screen.Clear();

std::cout << "\n\nYou choose option: " << selected << ": " << entries[selected] << std::endl;
```

Całość kodu:

```cpp
#include <iostream>
#include <string>
#include <vector>

#include "ftxui/component/component.hpp"
#include "ftxui/component/component_options.hpp"
#include "ftxui/component/screen_interactive.hpp"

int main() {
    using namespace ftxui;
    auto screen = ScreenInteractive::Fullscreen();

    std::vector<std::string> entries = {
        "Hello world!",
        "Hi Patrick",
        "Today is the day",
    };
    int selected = 0;

    MenuOption option;
    option.on_enter = screen.ExitLoopClosure();

    Component menu = Menu(&entries, &selected, option);

    screen.Loop(menu);
    screen.Clear();

    std::cout << "\n\nYou choose option: " << selected << ": " << entries[selected] << std::endl;

    return 0;
}
```

{{< space 7 >}}

## Elementy przewijane, czyli, co jeżeli brakuje nam miejsca

Tutaj skupimy się na bardzo podstawowym przewijaniu za pomocą skrótów klawiszowych (page up i page down).

### Wstawianie elementów z vectora

Zacznijmy od stworzenia vectora wypełnionego jakimiś danymi. Następnie skopiujmy te imiona do vectora, który przechowuje `Element`-y. Dodamy do środka elementy, tak, jakbysmy chcieli, aby się wyświetlały:

```cpp
std::vector<std::string> names = {
    "Alicja", "Bartek", "Celina", "Daniel", "Ewa",
    "Filip", "Grzegorz", "Hanna", "Iga", "Jakub",
    "Kasia", "Lena", "Marek", "Natalia", "Oskar",
    "Paweł", "Renata", "Szymon", "Tomasz", "Urszula"
};

std::vector<Element> listOfNames;

for (int i = 0; i < names.size(); i++) {
    listOfNames.push_back(text(names[i]));
}
```

Tworzymy teraz zmienną przechowującą stan przewinięcia, a następnie aktualizujemy Renderera. Do obiektu, który chcemy, aby był przewijany musimy dodać:

```cpp
focusPositionRelative(0 , scrollOnY) | frame | flex
```

Informuje to nas o położeniu, o tym, że element jest de facto jedną ramką i ma się rozciągać, aby się dopasować.

Dodajemy jeszcze pionowy pasek postępu, który będzie nam robił za pasek przewijania, w nim też trzeba pamiętać o flexie. Przyciskowi najlepiej przypisać na sztywno wysokość.

```cpp
float scrollOnY = 0.2;

Component renderer = Renderer(compContainer, [&] {
    return vbox({
        hbox({text("Names")}),
        hbox({vbox(listOfNames) | focusPositionRelative(0 , scrollOnY) | frame | flex,
            gaugeDown(scrollOnY) | flex
        }) | flex,
        hbox({filler(), closeButton->Render(), filler()}) | size(HEIGHT, EQUAL, 3)
    });
});
```

Teraz musimy dodać możliwość przechwytywania przycisków. Dodamy do renderera `CatchEvent`, tak, jak robiliśmy to już wcześniej.

```cpp
renderer |= CatchEvent([&] (Event event) {
    if (event == Event::PageUp) {
        scrollOnY = std::max(0.2, scrollOnY - 0.1);
        return true;
    } else if (event == Event::PageDown) {
        scrollOnY = std::min(0.8, scrollOnY + 0.1);
        return true;
    }
    return false;
});
```

Cały kod:

```cpp
int main() {
    using namespace ftxui;
    auto screen = ScreenInteractive::Fullscreen();

    std::vector<std::string> names = {
        "Alicja", "Bartek", "Celina", "Daniel", "Ewa",
        "Filip", "Grzegorz", "Hanna", "Iga", "Jakub",
        "Kasia", "Lena", "Marek", "Natalia", "Oskar",
        "Paweł", "Renata", "Szymon", "Tomasz", "Urszula"
    };

    std::vector<Element> listOfNames;

    for (int i = 0; i < names.size(); i++) {
        listOfNames.push_back(text(names[i]));
    }


    Component closeButton = Button("Close", screen.ExitLoopClosure());

    Component compContainer = Container::Vertical({
        closeButton,
    });

    float scrollOnY = 0.2;

    Component renderer = Renderer(compContainer, [&] {
        return vbox({
            hbox({text("Names")}),
            hbox({vbox(listOfNames) | focusPositionRelative(0 , scrollOnY) | frame | flex,
                gaugeDown(scrollOnY) | flex
            }) | flex,
            hbox({filler(), closeButton->Render(), filler()}) | size(HEIGHT, EQUAL, 3)
        });
    });

    renderer |= CatchEvent([&] (Event event) {
        if (event == Event::PageUp) {
            scrollOnY = std::max(0.2, scrollOnY - 0.1);
            return true;
        } else if (event == Event::PageDown) {
            scrollOnY = std::min(0.8, scrollOnY + 0.1);
            return true;
        }
        return false;
    });

    screen.Loop(renderer);

    return 0;
}
```

Oczywiście to rozwiązanie jest bardzo niedoskonałe. Należałoby dopisać obsługę dostosowywania wysokości paska w zależności od tego ile elementów jest do wyświetlenia oraz kroku przewijania.