# unitree_sdk2
Unitree SDK robota - wersja 2.

## Wprowadzenie dla studentów

Ten pakiet SDK (Software Development Kit - Zestaw Narzędzi Programistycznych) umożliwia komunikację z robotami firmy Unitree i ich programowanie. Jest to narzędzie dla osób, które chcą nauczyć się sterowania robotami czworonożnymi i humanoidalnymi firmy Unitree.

### Środowisko wymagane przed kompilacją
* System operacyjny: Ubuntu 20.04 LTS  
* Architektura procesora: aarch64 lub x86_64   
* Kompilator: gcc w wersji 9.4.0 

### Przygotowanie środowiska programistycznego

Przed rozpoczęciem kompilacji SDK (budowania programu z kodu źródłowego), należy zainstalować wymagane zależności (biblioteki i narzędzia programistyczne):

**Wymagane narzędzia:**
- CMake (wersja 3.10 lub nowsza) - system do zarządzania procesem kompilacji
- GCC (wersja 9.4.0) - kompilator języka C++
- Make - narzędzie do automatyzacji kompilacji

**Krok 1: Instalacja wszystkich wymaganych pakietów**

Otwórz terminal i wykonaj następujące komendy na systemie Ubuntu 20.04:

```bash
# Aktualizacja listy dostępnych pakietów
apt-get update

# Instalacja wszystkich wymaganych bibliotek i narzędzi
apt-get install -y cmake g++ build-essential libyaml-cpp-dev libeigen3-dev libboost-all-dev libspdlog-dev libfmt-dev
```

**Wyjaśnienie zainstalowanych bibliotek:**
- `cmake` - system budowania projektów
- `g++` - kompilator C++
- `build-essential` - podstawowe narzędzia do kompilacji
- `libyaml-cpp-dev` - biblioteka do parsowania plików YAML
- `libeigen3-dev` - biblioteka algebry liniowej
- `libboost-all-dev` - zestaw bibliotek C++
- `libspdlog-dev` - biblioteka do logowania
- `libfmt-dev` - biblioteka do formatowania tekstu

### Kompilacja przykładów

**Krok 2: Zbudowanie przykładowych programów z repozytorium**

Aby skompilować (zbudować) przykłady zawarte w tym repozytorium, wykonaj następujące kroki:

```bash
# Utwórz katalog na pliki kompilacji
mkdir build

# Przejdź do katalogu build
cd build

# Skonfiguruj projekt za pomocą CMake (przygotowanie do kompilacji)
cmake ..

# Skompiluj wszystkie przykłady
make
```

**Co się dzieje w tych krokach:**
1. Tworzymy osobny katalog `build` aby oddzielić pliki wynikowe od kodu źródłowego
2. `cmake ..` analizuje plik CMakeLists.txt i przygotowuje Makefile
3. `make` kompiluje kod źródłowy i tworzy programy wykonywalne

Po wykonaniu tych kroków w katalogu `build` znajdziesz skompilowane programy przykładowe, które możesz uruchomić.

### Instalacja SDK w systemie

**Krok 3: Instalacja biblioteki do używania we własnych projektach**

Jeśli chcesz stworzyć własną aplikację wykorzystującą unitree_sdk2, możesz zainstalować SDK w systemie. Dzięki temu będzie dostępne dla wszystkich Twoich projektów.

**Instalacja w domyślnym katalogu systemowym:**

```bash
# Utwórz katalog build (jeśli jeszcze nie istnieje)
mkdir build
cd build

# Skonfiguruj projekt
cmake ..

# Zainstaluj SDK w systemie (wymaga uprawnień administratora)
sudo make install
```

**Instalacja w wybranym katalogu:**

Możesz też zainstalować SDK w innym miejscu, np. `/opt/unitree_robotics`:

```bash
mkdir build
cd build

# Skonfiguruj z własną ścieżką instalacji
cmake .. -DCMAKE_INSTALL_PREFIX=/opt/unitree_robotics

# Zainstaluj (wymaga uprawnień administratora)
sudo make install
```

**Krok 4: Użycie SDK we własnych projektach**

Po instalacji możesz używać unitree_sdk2 w swoich projektach CMake. Zobacz przykład w katalogu `example/cmake_sample`, który pokazuje jak zaimportować bibliotekę do własnego projektu.

**Ważna uwaga:** Jeśli instalujesz bibliotekę w miejscu innym niż `/opt/unitree_robotics`, musisz upewnić się, że ta ścieżka jest dodana do zmiennej środowiskowej `CMAKE_PREFIX_PATH`, aby CMake mógł znaleźć bibliotekę za pomocą funkcji `find_package()`.

### Dodatkowe informacje

Więcej informacji referencyjnych i dokumentacji znajdziesz w [Centrum Dokumentacji Unitree](https://support.unitree.com/home/zh/developer).

### Struktura repozytorium

- `example/` - Katalog z przykładowymi programami dla różnych modeli robotów
- `include/` - Pliki nagłówkowe biblioteki SDK
- `lib/` - Prekompilowane biblioteki
- `cmake/` - Pliki konfiguracyjne CMake
- `thirdparty/` - Biblioteki zewnętrzne
