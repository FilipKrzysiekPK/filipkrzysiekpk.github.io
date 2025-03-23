---
title: Struktura projektu c++
layout: singleNoHeader
date: 2025-03-09T14:42:08.865Z
toc: false
lastmod: 2025-03-23T06:55:04.060Z
categories:
    - jipp-p-2025-n
draft: false
---

# Struktura projektu c++

## Struktura projektu

C++ nie posiada jednej, jedynej poprawnej struktury projektu, przeszukując różne źródła, znajdziemy różne jej wersje. Jeden sposób mówi o separowaniu plików nagłówkowych od implementacyjnych, a kolejny o trzymaniu ich razem. Każda z tych koncepcji ma swoje wady i zalety, jednakże istnieje też propozycja pośrednia.

```console
.
├── build       Wyniki kompilacji i pliki potrzebne do tego
├── doc         Dokumentacja
├── include     Publiczne pliki nagłówkowe
├── lib         Biblioteki
├── src         Pliki źródłowe i nagłówkowe
└── tests       Testy
```

Powyższa struktura trzyma pliki nagłówkowe i źródłowe razem, jednakże oddziela te publiczne.


