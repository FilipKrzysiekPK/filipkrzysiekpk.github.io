---
title: Wprowadzenie do githuba
layout: singleNoHeader
lastmod: 2025-03-10T16:39:27.612Z
---

# Wprowadzenie do githuba

Głównym celem systemu kontroli wersji jest ułatwienie pracy grupowej nad jednym kodem oraz umożliwienie łatwego przejrzenia zmian, czy tez cofnięcia się do wcześniejszej wersji.

System kontroli wersji możemy uruchomić lokalnie, czy też pracować ze zdalnym repozytorium. Najpopularniejsi dostawcy rozwiązania:

- github
- gitlab
- bitbucket

Więcej szczegółów oraz historię możesz przeczytać na [wikipedii](https://pl.wikipedia.org/wiki/Git_(oprogramowanie)).

{{< space 5 >}}

## Od czego zacząć?

Na samym początku musimy zainstalować git-a.

#### Windows

Pobieramy ze strony i instalujemy: [pobierz](https://git-scm.com/downloads)
{{< br >}}
Najlepiej podczas instalacji wybrać opcję, aby zainstalowała sie także konsola BASH. Można kliknąć dalej na wszystkich krokach.

#### Linux

W zależności od dystrybucji używamy odpowiedniej komendy do instalacji. Nazwa pakietu: git

### Po instalacji

Jeżeli chcemy korzystać z narzędzia nie tylko lokalnie, to nalezy założyć konto na jednym z portali. Na zajęciach będziemy korzystać z [githuba](github.com).

Gdy mamy już stworzone konto, to musimy się zalogować z poziomu konsoli, czy też aplikacji, której będziemy używać. 
Jest to konieczny krok, aby móc przesyłać swoje commity do repozytorium, albo mieć dostęp do prywatnych repozytoriów.

Autoryzacji możemy dokonać na kilka sposobów:

- Zalogowanie się przez konsolę
- Użycie tokena

### Logowanie przez konsolę

[Autentykacja](https://docs.github.com/en/get-started/quickstart/set-up-git#authenticating-with-github-from-git)

Na powyższej stronie został przedstawiony sposób autoryzacji.

### Użycie VS Code do pokazywania różnic zamiast w konsoli

[Otwórz](https://www.roboleary.net/vscode/2020/09/15/vscode-git.html)

{{< space 4 >}}

## Co przetrzymywać na repozytorium?

Na repozytorium przechowujemy wszystkie kluczowe pliki, do zbudowania i uruchomienia naszego kodu, czy też konfiguracji narzędzi środowiska programistycznego.

Na repozytorium nie umieszczamy:

- Plików konfiguracyjnych środowiska programistycznego (`.vscode`, `.idea`)
- Plików generowanych na podstawie naszego kodu (`cmake-build`, `build`)
- Plików binarnych projektów (`*.exe`, `*.o`)
- itd itp

Tych plików nie umieszcza się, ponieważ jeżeli sklonujemy na innym urządzeniu nasze repozytorium, to i tak będziemy musieli wygenerować te pliki, aby były zgodne z narzędziami, które mamy zainstalowane. Jeżeli na jednym komputerze korzystamy z jednego IDE, a na drugim z innego, to te pliki będą zbędne, a jeżeli jest takie samo narzędzie, to istnieje prawdopodobieństwo, że i tak wystąpią błędy. Dodatkowo w tych folderach znajduje się wiele plików tymczasowych.

Najważniejszym argumentem jest zdecydowanie łatwiejsze przeglądanie zmian pomiędzy commitami, jeżeli mamy tam tylko kluczowe pliki.

Na początku tego podrozdziału wspomniane było, że można przechowywać konfiguracje narzędzi środowisk programistycznych. Przykładowo takimi narzędziami są:

- `clang-tidy` - narzędzie do sprawdzania poprawności naszego kodu, plik konfiguracyjny (najczęściej pojedynczy) `.clang-tidy`
- `clang-formatter` - narzędzie do formatowania kodu, plik konfiguracyjny (jeden na projekt) `.clang-format`

Są to pojedyncze pliki, które zazwyczaj nie są modyfikowane zbyt często i pozwalają zachować spójność projektu.

#### Jak w łatwy sposób wykluczyć pliki/foldery z systemu kontroli wersji

Należy utworzyć plik `.gitignore`. Wewnątrz niego definiujemy, które pliki maja byc pomijane.

{{< space 4 >}}

## Podstawowe komendy

### Przygotowanie repozytorium

`git init` - inicjalizacja repozytorium w folderze, w którym się znajdujemy

`git clone <adres repozytorium>` - klonowanie repozytorium znajdującego się pod `<adres repozytorium>` do folderu, w którym się znajdujemy (zostanie utworzony folder o nazwie repozytorium)


### Status repozytorium

`git status` - komenda pokazująca status repozytorium. Często powie, co mamy dalej zrobić. Wylistuje pliki, które są dodane do kontroli wersji, które zostały zmodyfikowane. Gdy nie wiesz, co zrobić, możesz wpisać `git status`

`git diff` - pokazywanie zmian od ostatniego commita

`git diff <numer commita>` - pokazywanie zmian pomiędzy aktualnym stanem, a wskazanym commitem `<numer commita>`

`git difftool` - użycie narzędzia GUI do pokazania zmian (patrz wcześniejsza sekcja, gdzie opisano konfigurację)


### Praca na repozytorium

`git pull` - ściąganie zmian ze zdalnego repozytorium na danej gałęzi

`git pull --all` - ściąganie zmian ze zdalnego repozytorium na wszystkich gałęziach

`git fetch` - ściąganie zmian ze zdalnego repozytorium

`git add <nazwa pliku>` - dodawanie zmodyfikowanych plików do commita

`git add ./*` - dodawanie wszystkich plików do commita

`git commit -m <wiadomość>` - zatwierdzanie zmian oraz dodawanie wiadomości `<wiadomość>`. Teraz to jest zapisane lokalnie

`git push` - przesyłanie lokalnych commitów do repozytorium zdalnego

### Praca na gałęziach

`git branch` - wyświetlanie aktualnej gałęzi

`git branch --all` - wylistowywanie wszystkich gałęzi

`git checkout <nazwa gałęzi>` - zmiana aktualnej gałęzi na `<nazwa gałęzi>`

`git merge <nazwa gałęzi>` - łączenie `<nazwa gałęzi>` do aktualnej gałęzi

`git rebase <nazwa gałęzi>` - łączenie `<nazwa gałęzi>` do aktualnej gałęzi  (w inny sposób, niż `merge`)


{{< space 15 >}}

## Scenariusze pracy

### Najprostsza praca na jednej gałęzi

1. Klonowanie repozytorium
2. Praca na repozytorium
   1. Modyfikowanie plików
   2. Dodawanie zmian do commita
   3. Tworzenie commita
3. Przesyłanie zmian z lokalnego repozytorium do zdalnego


{{< space 5 >}}

---

## Gdy coś pójdzie nie tak

[Pomocna strona](https://dangitgit.com/pl) gdy cos pójdzie nie tak. Znajdziesz na niej wskazówki, co zrobić, aby uratować napisany kod i nie musieć ponownie klonować repozytorium. Istnieje też inna odmiana tej strony 😉.
