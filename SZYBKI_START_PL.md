# Szybki Przewodnik - Unitree SDK2

## Pierwsze Kroki (5 minut)

### 1. Instalacja Zależności
```bash
sudo apt-get update
sudo apt-get install -y cmake g++ build-essential libyaml-cpp-dev \
    libeigen3-dev libboost-all-dev libspdlog-dev libfmt-dev
```

### 2. Kompilacja SDK
```bash
cd ~/unitree_sdk2  # Przejdź do katalogu gdzie sklonowałeś repozytorium
mkdir build && cd build
cmake ..
make
```

### 3. Uruchomienie Pierwszego Przykładu
```bash
# Zamień "eth0" na swój interfejs sieciowy (sprawdź: ifconfig lub ip a)
./go2_stand_example eth0
```

**UWAGA:** Robot musi być zawieszony lub leżeć na ziemi!

## Najważniejsze Pliki

| Plik | Przeznaczenie |
|------|---------------|
| `README.md` | Główna dokumentacja i instrukcje instalacji |
| `PRZEWODNIK_PL.md` | Szczegółowy przewodnik dla studentów |
| `PRZEWODNIK_PRZYKLADY_PL.md` | Wyjaśnienie wszystkich przykładów |
| `example/go2/go2_stand_example.cpp` | Podstawowy przykład dla GO2 |
| `example/b2/b2_stand_example.cpp` | Podstawowy przykład dla B2 |
| `example/h1/README.md` | Instrukcje dla robota H1 |

## Parametry Sterowania - Ściągawka

### Parametry Regulatora PD

| Zastosowanie | Kp | Kd | Efekt |
|--------------|----|----|-------|
| Delikatne ruchy | 10-30 | 0.5-2 | Wolne, miękkie |
| Normalne ruchy | 40-80 | 3-7 | Zbalansowane |
| Szybkie ruchy | 100-200 | 8-15 | Szybkie, sztywne |

**Zasady:**
- ↑ Kp = mocniejsze dążenie do celu (ale może oscylować)
- ↑ Kd = płynniejszy ruch (ale może być wolniejszy)

### Typowe Pozycje dla GO2

```cpp
// POZYCJA STOJĄCA (gotowy do chodzenia)
float standing[12] = {
    0.0, 0.67, -1.3,    // Przednia lewa
    0.0, 0.67, -1.3,    // Przednia prawa
    0.0, 0.67, -1.3,    // Tylna lewa
    0.0, 0.67, -1.3     // Tylna prawa
};

// POZYCJA LEŻĄCA (bezpieczna, relaks)
float lying[12] = {
    0.0, 1.36, -2.65,
    0.0, 1.36, -2.65,
    0.0, 1.36, -2.65,
    0.0, 1.36, -2.65
};

// NOGI ROZSTAWIONE (stabilna)
float wide[12] = {
    -0.3, 1.36, -2.65,  // Lewa: biodro na zewnątrz
     0.3, 1.36, -2.65,  // Prawa: biodro na zewnątrz
    -0.3, 1.36, -2.65,
     0.3, 1.36, -2.65
};
```

### Mapowanie Stawów GO2

```
Indeks | Staw
-------|----------------------------------
  0    | Przednia Lewa - Biodro (Hip)
  1    | Przednia Lewa - Udo (Thigh)
  2    | Przednia Lewa - Podudzie (Calf)
  3    | Przednia Prawa - Biodro
  4    | Przednia Prawa - Udo
  5    | Przednia Prawa - Podudzie
  6    | Tylna Lewa - Biodro
  7    | Tylna Lewa - Udo
  8    | Tylna Lewa - Podudzie
  9    | Tylna Prawa - Biodro
 10    | Tylna Prawa - Udo
 11    | Tylna Prawa - Podudzie
```

## Komendy Szybkiego Dostępu

### Sprawdzanie Interfejsu Sieciowego
```bash
# Pokaż wszystkie interfejsy
ip a
# lub
ifconfig

# Szukaj interfejsu połączonego z robotem (zazwyczaj eth0, enp3s0, etc.)
```

### Kompilacja Pojedynczego Przykładu
```bash
cd build
cmake ..
make go2_stand_example  # Kompiluj tylko ten plik
```

### Sprawdzenie czy Robot jest Dostępny
```bash
# Pinguj robota (zmień IP na adres robota)
ping 192.168.123.10
```

## Szybkie Rozwiązania Problemów

| Problem | Rozwiązanie |
|---------|-------------|
| Błąd: "network interface not found" | Sprawdź nazwę interfejsu: `ip a`, użyj poprawnej nazwy |
| Robot drga/oscyluje | Zmniejsz Kp o połowę lub zwiększ Kd |
| Robot nie reaguje | Inne usługi aktywne - poczekaj na "ReleaseMode succeeded" |
| Błąd kompilacji | Zainstaluj ponownie zależności: apt-get install... |
| Segmentation fault | Sprawdź czy robot jest włączony i połączony |

## Bezpieczeństwo - 5 Złotych Zasad

1. ⚠️ **ZAWIESZ lub POŁÓŻ robota przed pierwszym uruchomieniem**
2. 🛑 **MIEJ przycisk awaryjny w zasięgu ręki**
3. 🐌 **ZACZNIJ od małych ruchów** (małe kąty, niska prędkość)
4. 🔍 **TESTUJ bezpiecznie** (najpierw zawieszony, potem na ziemi)
5. ⚡ **WYŁĄCZ inne usługi** przed uruchomieniem kodu

## Szybkie Modyfikacje Kodu

### Zmiana Czasu Ruchu
```cpp
// W kodzie znajdź:
float _duration_1 = 500;  // 500 × 2ms = 1 sekunda

// Zmień na:
float _duration_1 = 1000; // 2 sekundy (wolniej)
// lub
float _duration_1 = 250;  // 0.5 sekundy (szybciej)
```

### Zmiana Siły Sterowania
```cpp
// W kodzie znajdź:
float Kp = 60.0;
float Kd = 5.0;

// Dla delikatniejszych ruchów:
float Kp = 30.0;
float Kd = 3.0;

// Dla mocniejszych ruchów:
float Kp = 100.0;
float Kd = 8.0;
```

### Dodanie Wyświetlania Danych
```cpp
// Dodaj w funkcji LowCmdWrite():
std::cout << "Pozycja stawu 0: " << low_state.motor_state()[0].q() << std::endl;
std::cout << "IMU X: " << low_state.imu_state().accelerometer()[0] << std::endl;
```

## Konwersje Jednostek

### Kąty
```
Radiany → Stopnie: stopnie = radiany × 57.2958
Stopnie → Radiany: radiany = stopnie / 57.2958

Przykłady:
0.5 rad ≈ 28.6°
1.0 rad ≈ 57.3°
1.5 rad ≈ 85.9°
2.0 rad ≈ 114.6°
3.14 rad ≈ 180°
```

### Czas
```
Kroki → Sekundy: sekundy = kroki × 0.002
Sekundy → Kroki: kroki = sekundy / 0.002

Przykłady:
250 kroków = 0.5 s
500 kroków = 1.0 s
1000 kroków = 2.0 s
2500 kroków = 5.0 s
```

## Struktura Danych - Szybki Przegląd

### LowCmd (Komendy DO robota)
```cpp
low_cmd.motor_cmd()[i].q()    // Pozycja docelowa [rad]
low_cmd.motor_cmd()[i].dq()   // Prędkość docelowa [rad/s]
low_cmd.motor_cmd()[i].kp()   // Sztywność
low_cmd.motor_cmd()[i].kd()   // Tłumienie
low_cmd.motor_cmd()[i].tau()  // Moment siły [Nm]
low_cmd.motor_cmd()[i].mode() // Tryb silnika (0x01 lub 0x0A)
```

### LowState (Stan Z robota)
```cpp
low_state.motor_state()[i].q()    // Aktualna pozycja [rad]
low_state.motor_state()[i].dq()   // Aktualna prędkość [rad/s]
low_state.motor_state()[i].tau()  // Aktualny moment [Nm]
low_state.imu_state().quaternion()[i]     // Orientacja (kwaternion)
low_state.imu_state().accelerometer()[i]  // Przyspieszenie [m/s²]
low_state.imu_state().gyroscope()[i]      // Prędkość kątowa [rad/s]
low_state.foot_force()[i]         // Siła na stopę [N]
```

## Workflow Typowego Projektu

```
1. Sklonuj repozytorium
   ↓
2. Zainstaluj zależności (apt-get install...)
   ↓
3. Skompiluj SDK (mkdir build, cmake, make)
   ↓
4. Uruchom przykład (./go2_stand_example eth0)
   ↓
5. Zaobserwuj jak działa
   ↓
6. Zmodyfikuj kod (zmień parametry, pozycje)
   ↓
7. Przekompiluj (make)
   ↓
8. Przetestuj zmiany
   ↓
9. Iteruj (kroki 6-8) aż uzyskasz pożądany efekt
```

## Dodatkowe Zasoby

- 📖 **Szczegółowy przewodnik:** `PRZEWODNIK_PL.md`
- 📚 **Wyjaśnienie przykładów:** `PRZEWODNIK_PRZYKLADY_PL.md`
- 🌐 **Dokumentacja online:** https://support.unitree.com/home/zh/developer
- 📁 **Przykłady:** Katalog `example/`
- 💬 **Community:** Forum Unitree i GitHub Issues

## Kontakt i Pomoc

Jeśli masz problemy:
1. Sprawdź ten przewodnik i README.md
2. Przeczytaj PRZEWODNIK_PL.md
3. Sprawdź Issues na GitHub
4. Odwiedź forum Unitree

---

**Wesołego programowania! 🤖**
