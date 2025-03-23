---
title: CMake - wprowadzenie
layout: singleNoHeader
date: 2024-03-10
toc: true
lastmod: 2025-03-23T08:49:15.539Z
draft: false
---

# CMake - wprowadzenie

## CMake

Cytując wikipedię:

> CMake – wieloplatformowe narzędzie do automatycznego zarządzania procesem kompilacji programu. Jego główna cecha to niezależność od używanego kompilatora oraz platformy sprzętowej. CMake nie kompiluje programu samodzielnie, lecz tworzy pliki z regułami kompilacji dla konkretnego środowiska; przykładowo w systemie GNU/Linux będą to pliki Makefile, natomiast pod Windowsem — pliki projektu dla Microsoft Visual Studio.

Jak można wyczytać w powyższej definicji, CMake służy do automatycznego zarządzania procesem kompilacji. Zamieniając 
na polskie, na podstawie konfiguracji wywołuje odpowiednie komendy, których efektem jest kompilacja. Dołącza on 
biblioteki, a nawet potrafi pobierać je z githuba.

### Podstawowe komendy

Deklaracja minimalnej wersji CMake, która będzie używana:

```cmake
cmake_minimum_required(VERSION 3.16)
```

Ustawianie używanej wersji języka c++:

```cmake
set(CMAKE_CXX_STANDARD 17)
```

Deklaracja nazwy projektu:

```cmake
project(ProjectName)
```

Deklarowanie listy plików, które mają byc skompilowane, jako program:

```cmake
add_executable(ProjectName main.cpp)
```

Deklarowanie listy plików, które mają byc skompilowane, jako biblioteka:

```cmake
add_library(ProjectName STATIC main.cpp)
```

Deklarowanie używanych bibliotek przez nasz program:

```cmake
target_link_libraries(ProjectName pthread)
```

Deklarowanie folderu, który ma być przeszukiwany pod kątem plikow nagłówkowych (mówienie, gdzie znajdują się pliki 
nagłówkowe):

```cmake
include_directories(include)
```

Dodawanie podfolderów z kolejnymi częściami aplikacji (gdzie znajduje się kolejny plik `CMakeLists.txt`)

```cmake
add_subdirectory(sth)
```

#### Stałe

`${CMAKE_CURRENT_LIST_DIR}` - zwraca absolutną ścieżkę do folderu aktualnego pliku CMakeLists.txt

`${CMAKE_SOURCE_DIR}` - zwraca absolutną ścieżkę do najwyższego folderu, w którym znajduje się plik CMakeLists.txt.



### Przykładowy plik

```cmake
cmake_minimum_required(VERSION 3.16)
project(HelloWorld)

set(CMAKE_CXX_STANDARD 20)

add_executable(HelloWorld main.cpp)

target_link_libraries(HelloWorld pthread)
```

Analizując powyższy plik, można zauważyć, że będzie kompilowany w standardzie c++20 plik `main.cpp` oraz dołączana 
będzie biblioteka pthread.

```cmake
cmake_minimum_required(VERSION 3.16)
project(HelloWorld)

set(CMAKE_CXX_STANDARD 20)

include_directories(include)

add_executable(HelloWorld 
        src/main.cpp 
        src/HelloWorld.cpp)

target_link_libraries(HelloWorld pthread)
```

### Komendy cmake

Używając CLion lub innego narzędzia z interfejsem graficznym można wszystko wyklikać, lecz jeżeli chcielibyśmy 
zrobić to z poziomu konsoli, to musielibyśmy użyć następujących komend.

> Załóżmy, że znajdujemy się w głównym folderze plików i struktura plików jest następująca:
> ```
> .
> ├── CMakeLists.txt
> ├── include
> │   └── HelloWorld.h
> └── src
>     ├── HelloWorld.cpp
>     └── main.cpp
> ```

Najpierw tworzymy folder na wyniki pracy CMake i skompilowane pliki oraz wchodzimy do niego:

```console
mkdir build
cd build
```

Wywołujemy komendę `cmake` ze wskazaniem, gdzie znajduje sięgłówny plik `CMakeLists.txt`, aby CMake utworzył konfiguracje projektu:

```console
cmake ..
```

Uruchamianie kompilacji projektu `HelloWorld` (linux):

```console
make HelloWorld
```

Window:

```console
cmake --build . --target HelloWorld
```

`--build` powinno wskazywać folder, w którym wywołaliśmy komendę `cmake ..`, w naszym przypadku użyliśmy `.`, ponieważ znajdujemy się w tym samym folderze


### Dokumentacja CMake

[Dokumentacja CMake](https://cmake.org/cmake/help/latest/index.html)

[Tutorial CMake](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

