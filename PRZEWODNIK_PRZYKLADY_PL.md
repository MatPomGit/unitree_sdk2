# Przewodnik po Przykładach - Unitree SDK2

Ten dokument szczegółowo wyjaśnia jak działają przykładowe programy w repozytorium.

## Przegląd Przykładów

### Przykłady dla GO2 (`example/go2/`)

#### 1. `go2_stand_example.cpp` - Pozycje stojące

**Co robi:** Przeprowadza robota przez 4 etapy ruchu pokazując podstawowe sterowanie.

**Sekwencja działania:**
1. **Inicjalizacja** (pierwsze 500 kroków = 1 sekunda)
   - Robot czeka i wyświetla dane z czujników
   - Pokazuje: pozycję stawu 0, akcelerometr IMU, siłę stopy

2. **Etap 1** (1 sekunda, 500 kroków)
   - Przejście z pozycji startowej do pozycji leżącej z rozstawionymi nogami
   - Kąty: biodra (-0.2, 0.2), uda (1.36), podudzia (-2.65)

3. **Etap 2** (1 sekunda, 500 kroków)
   - Podniesienie do pozycji stojącej
   - Kąty: biodra (0.0), uda (0.67), podudzia (-1.3)

4. **Etap 3** (2 sekundy, 1000 kroków)
   - Utrzymanie pozycji stojącej (bez ruchu)

5. **Etap 4** (1.8 sekundy, 900 kroków)
   - Rozstawienie bioder w pozycji leżącej
   - Kąty: biodra (-0.35, 0.35, -0.5, 0.5)

**Kluczowe parametry:**
- Kp = 60.0 (średnia sztywność)
- Kd = 5.0 (średnie tłumienie)
- Częstotliwość: 500 Hz (co 2ms)

**Nauka:**
- Jak płynnie interpolować między pozycjami
- Jak zarządzać sekwencją wielu ruchów
- Jak odczytywać dane z czujników

#### 2. `go2_low_level.cpp` - Niskopoziomowe sterowanie

**Co robi:** Pokazuje bezpośrednie sterowanie wszystkimi 12 silnikami.

**Kluczowe cechy:**
- Bezpośredni dostęp do komend silników
- Możliwość ustawienia różnych parametrów dla każdego stawu
- Demonstracja pełnej kontroli

**Kiedy używać:**
- Gdy potrzebujesz precyzyjnej kontroli nad każdym stawem
- Gdy implementujesz własne algorytmy chodu
- Gdy testujesz zachowanie pojedynczych stawów

#### 3. `go2_trajectory_follow.cpp` - Śledzenie trajektorii

**Co robi:** Robot śledzi zadaną trajektorię w czasie rzeczywistym.

**Zastosowania:**
- Chodzenie po określonej ścieżce
- Wykonywanie złożonych ruchów
- Synchronizacja wielu stawów

### Przykłady dla B2 (`example/b2/`)

#### 1. `b2_stand_example.cpp` - Pozycje stojące dla B2

**Różnice od GO2:**
- Inny tryb silnika: `0x0A` zamiast `0x01`
- Wyższe wartości Kp (1000.0) i Kd (10.0)
- 5 etapów ruchu zamiast 4
- Robot musi leżeć (nie może być zawieszony!)

**Sekwencja dla B2:**
1. Przejście do pozycji z lekko rozstawionymi nogami
2. Podniesienie do pozycji stojącej
3. Utrzymanie pozycji
4. Powrót do pozycji leżącej
5. Rozstawienie nóg

**Uwagi specyficzne dla B2:**
- B2 jest cięższy i wymaga wyższych parametrów PD
- Pozycja startowa jest inna niż w GO2
- Należy uważać na stabilność podczas przejść

#### 2. `b2_sport_client.cpp` - Klient trybu sportowego

**Co robi:** Łączy się z wysokopoziomową usługą sterowania ruchem.

**Możliwości:**
- Chodzenie do przodu/tyłu
- Skręcanie
- Zmiana prędkości
- Automatyczna stabilizacja

**Zalety trybu sportowego:**
- Nie musisz implementować algorytmu chodu
- Automatyczne zachowanie równowagi
- Łatwiejsze w użyciu dla początkujących

### Przykłady dla H1 (`example/h1/`)

Robot humanoidalny H1 ma więcej stawów i bardziej złożone sterowanie.

#### Wysokopoziomowe (`h1/high_level/`)

**`h1_loco_client_example.cpp`** - Sterowanie lokomocją
- Chodzenie dwunożne
- Utrzymanie równowagi
- Zaawansowane algorytmy ruchu

**`h1_arm_sdk_dds_example.cpp`** - Sterowanie ramieniem
- Kontrola manipulatora
- Kinematyka ramienia
- Chwytanie obiektów

#### Niskopoziomowe (`h1/low_level/`)

**`h1_27dof_example.cpp`** - Kontrola wszystkich 27 stopni swobody
- Precyzyjna kontrola każdego stawu
- Złożona koordinacja ruchu
- Zaawansowane programowanie

**`h1_2_ankle_track.cpp`** - Śledzenie kostek w trybie PR
- Specjalny tryb dla mechanizmu równoległego
- Sterowanie stawami Pitch i Roll kostki
- Zobacz szczegóły w `example/h1/README.md`

### Przykłady dla G1 (`example/g1/`)

Robot G1 to zaawansowany humanoid z dodatkowymi możliwościami.

#### `g1_loco_client_example.cpp` - Lokomocja G1
- Podobny do H1 ale z nowymi funkcjami
- Ulepszone algorytmy chodu

#### `g1_dual_arm_example.cpp` - Sterowanie dwoma ramionami
- Skoordynowane ruchy obu ramion
- Współpraca lewego i prawego ramienia

#### `g1_audio_client_example.cpp` - Sterowanie audio
- Odtwarzanie dźwięków
- Synteza mowy
- Feedback dźwiękowy

## Wspólne Elementy Wszystkich Przykładów

### 1. Funkcja `crc32_core()`

**Cel:** Oblicza sumę kontrolną CRC32 dla weryfikacji integralności danych.

**Dlaczego jest potrzebna:**
- Zapewnia że dane nie zostały uszkodzone podczas transmisji
- Robot weryfikuje czy komenda jest poprawna
- Zwiększa bezpieczeństwo komunikacji

**Kiedy jest używana:**
- Przed każdym wysłaniem komendy do robota
- Automatycznie dodawana na końcu struktury LowCmd

### 2. Klasa `MotionSwitcherClient`

**Cel:** Zarządza trybami sterowania robota.

**Metody:**
- `CheckMode()` - sprawdza aktywny tryb
- `ReleaseMode()` - wyłącza aktywne usługi

**Dlaczego to ważne:**
- Tylko jeden program może sterować robotem jednocześnie
- Inne usługi (sport_mode, ai_sport) muszą być wyłączone
- Zapobiega konfliktom sterowania

### 3. Publisher/Subscriber

**Publisher (Wydawca):**
- Wysyła dane DO robota
- Temat: "rt/lowcmd"
- Wywołanie: `lowcmd_publisher->Write(low_cmd)`

**Subscriber (Subskrybent):**
- Odbiera dane Z robota
- Temat: "rt/lowstate"
- Handler: funkcja wywoływana automatycznie przy nowych danych

### 4. Wątek Sterowania

**Tworzenie:**
```cpp
CreateRecurrentThreadEx("nazwa", CPU_ID, microseconds, funkcja, this)
```

**Parametry:**
- Nazwa: identyfikator wątku
- CPU_ID: na którym rdzeniu procesora uruchomić (UT_CPU_ID_NONE = dowolny)
- Mikrosekundy: 2000 µs = 2 ms = 500 Hz
- Funkcja: `LowCmdWrite` - wywoływana cyklicznie

## Jak Modyfikować Przykłady

### Zmiana Pozycji Docelowych

1. Znajdź tablice `_targetPos_X[12]`
2. Zmień wartości kątów (w radianach)
3. Pamiętaj: 1 radian ≈ 57.3 stopnia

**Przykład:**
```cpp
// Oryginał
float _targetPos_1[12] = {0.0, 1.36, -2.65, ...};

// Twoja modyfikacja (delikatniejsze zgięcie)
float _targetPos_1[12] = {0.0, 1.0, -2.0, ...};
```

### Zmiana Czasu Trwania Ruchu

1. Znajdź zmienne `_duration_X`
2. Zmień wartości (liczba kroków × 2ms = czas w sekundach)

**Przykład:**
```cpp
// Oryginał: 500 kroków = 1 sekunda
float _duration_1 = 500;

// Wolniej: 1000 kroków = 2 sekundy
float _duration_1 = 1000;

// Szybciej: 250 kroków = 0.5 sekundy
float _duration_1 = 250;
```

### Zmiana Parametrów PD

1. Znajdź zmienne `Kp` i `Kd`
2. Eksperymentuj z wartościami

**Wskazówki:**
- Jeśli robot oscyluje: zmniejsz Kp lub zwiększ Kd
- Jeśli robot jest za słaby: zwiększ Kp
- Jeśli ruch jest szarpany: zwiększ Kd

### Dodanie Nowego Etapu Ruchu

1. Dodaj nową tablicę pozycji: `_targetPos_5[12]`
2. Dodaj czas trwania: `_duration_5`
3. Dodaj procent: `_percent_5`
4. Dodaj warunek w funkcji `LowCmdWrite()`

## Debugowanie i Testowanie

### Wyświetlanie Wartości

Dodaj w funkcji `LowCmdWrite()`:

```cpp
std::cout << "Staw 0: " << low_state.motor_state()[0].q() << std::endl;
std::cout << "Procent_1: " << _percent_1 << std::endl;
```

### Sprawdzanie Połączenia

Program automatycznie wyświetla:
- "CheckMode succeeded" - połączenie OK
- "ReleaseMode succeeded" - wyłączono inne usługi
- Błąd połączenia - sprawdź interfejs sieciowy

### Testowanie Bezpieczne

1. Uruchom z robotem zawieszonym/leżącym
2. Obserwuj czy ruchy są płynne
3. Sprawdź czy robot nie drgnie gwałtownie
4. Dopiero potem pozwól robotowi stanąć

## Częste Pytania

**Q: Dlaczego robot nie reaguje na pierwsze 500 kroków?**  
A: To okres oczekiwania (`motiontime >= 500`) aby system się ustabilizował.

**Q: Co oznacza "PosStopF" i "VelStopF"?**  
A: To specjalne wartości oznaczające "nie kontroluj tej wielkości".

**Q: Czy mogę uruchomić dwa programy jednocześnie?**  
A: Nie! Tylko jeden program może sterować robotem w danym momencie.

**Q: Jak zatrzymać program awaryjnie?**  
A: Ctrl+C w terminalu, lub fizyczny przycisk awaryjny na robocie.

**Q: Jakie są bezpieczne wartości dla testowania?**  
A: Kp=20-60, Kd=1-5, małe zmiany pozycji (< 0.5 rad), długie czasy (> 500 kroków).

## Kolejne Eksperymenty

Po opanowaniu podstawowych przykładów, spróbuj:

1. **Zmienić kolejność pozycji** - odwróć sekwencję ruchów
2. **Stworzyć pętlę ruchu** - powtarzaj sekwencję w nieskończoność
3. **Dodać ruch sinusoidalny** - użyj `sin()` do płynnych oscylacji
4. **Sterować tylko wybranymi nogami** - poruszaj tylko przednimi lub tylko tylnymi
5. **Zaimplementować prostą stabilizację** - użyj danych z IMU

Powodzenia w programowaniu robotów Unitree!
