# Rozwiązywanie Problemów - Unitree SDK2

Ten przewodnik pomoże Ci rozwiązać najczęstsze problemy napotykane podczas pracy z Unitree SDK2.

## Problemy z Kompilacją

### ❌ Błąd: "CMake not found"

**Objaw:**
```
bash: cmake: command not found
```

**Rozwiązanie:**
```bash
sudo apt-get update
sudo apt-get install cmake
```

**Weryfikacja:**
```bash
cmake --version  # Powinna być wersja 3.10+
```

---

### ❌ Błąd: "Cannot find yaml-cpp"

**Objaw:**
```
CMake Error: Could not find yaml-cpp
```

**Rozwiązanie:**
```bash
sudo apt-get install libyaml-cpp-dev
```

**Jeśli dalej nie działa:**
```bash
# Zainstaluj z źródeł
cd /tmp
git clone https://github.com/jbeder/yaml-cpp.git
cd yaml-cpp
mkdir build && cd build
cmake ..
make
sudo make install
```

---

### ❌ Błąd: "Eigen3 not found"

**Objaw:**
```
Could not find Eigen3
```

**Rozwiązanie:**
```bash
sudo apt-get install libeigen3-dev
```

---

### ❌ Błąd: "make: *** No targets specified"

**Objaw:**
Pusty katalog build lub brak Makefile

**Rozwiązanie:**
```bash
# Usuń katalog build i zacznij od nowa
rm -rf build
mkdir build
cd build
cmake ..
make
```

---

### ❌ Błąd kompilacji z C++17/C++20

**Objaw:**
```
error: 'filesystem' is not a member of 'std'
```

**Rozwiązanie:**
Upewnij się że używasz gcc 9.4.0 (wersja testowana i rekomendowana dla tego SDK):
```bash
gcc --version
g++ --version

# Jeśli nie masz wersji 9.4.0, zainstaluj:
sudo apt-get install gcc-9 g++-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 90
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 90
```

**Uwaga:** SDK2 jest testowane z gcc 9.4.0. Nowsze wersje powinny też działać, ale 9.4.0 jest oficjalnie wspierana.

---

## Problemy z Uruchomieniem

### ❌ Błąd: "Network interface not found"

**Objaw:**
```
Error: Cannot find network interface eth0
```

**Rozwiązanie 1:** Sprawdź dostępne interfejsy
```bash
ip a
# lub
ifconfig
```

Znajdź nazwę interfejsu połączonego z robotem (np. `enp3s0`, `eth1`, `wlan0`)

**Rozwiązanie 2:** Użyj poprawnej nazwy
```bash
# Zamiast:
./go2_stand_example eth0

# Użyj:
./go2_stand_example enp3s0  # lub inna nazwa z ip a
```

---

### ❌ Błąd: "Connection timeout"

**Objaw:**
Program się zawiesza lub wyświetla timeout

**Diagnoza:**
```bash
# Sprawdź czy robot jest dostępny
ping 192.168.123.10  # Zmień IP na adres robota

# Sprawdź połączenie sieciowe
ip link show  # Interfejs powinien być "UP"
```

**Rozwiązanie:**
1. Upewnij się że robot jest włączony
2. Sprawdź kabel Ethernet (czy jest podłączony)
3. Zrestartuj interfejs sieciowy:
```bash
sudo ip link set enp3s0 down
sudo ip link set enp3s0 up
```

4. Skonfiguruj statyczne IP (jeśli potrzebne):
```bash
sudo ip addr add 192.168.123.100/24 dev enp3s0
```

---

### ❌ Błąd: "ReleaseMode failed"

**Objaw:**
```
ReleaseMode failed. Error code: -1
Try to deactivate the motion control-related service.
```

**Przyczyna:**
Inna usługa (sport_mode, ai_sport) jest aktywna

**Rozwiązanie 1:** Poczekaj
Program automatycznie próbuje wyłączyć usługi co 5 sekund. Daj mu minutę.

**Rozwiązanie 2:** Ręcznie wyłącz przez aplikację Unitree
- Otwórz aplikację Unitree Go/B2 na telefonie
- Wyłącz wszystkie tryby ruchu
- Spróbuj ponownie uruchomić program

**Rozwiązanie 3:** Zrestartuj robota
```bash
# Czasami restart rozwiązuje problem
# Użyj przycisku power na robocie
```

---

### ❌ Segmentation Fault

**Objaw:**
```
Segmentation fault (core dumped)
```

**Przyczyny i rozwiązania:**

**1. Robot nie jest włączony/połączony**
```bash
# Sprawdź połączenie
ping [IP_ROBOTA]
```

**2. Błędny interfejs sieciowy**
```bash
# Użyj poprawnej nazwy interfejsu
./program eth0  # ← może być błędne
./program enp3s0  # ← poprawne
```

**3. Uszkodzone biblioteki**
```bash
# Przebuduj SDK
cd build
rm -rf *
cmake ..
make clean
make
```

**4. Błąd w kodzie (jeśli modyfikowałeś)**
- Sprawdź indeksy tablic (czy nie wykraczasz poza zakres)
- Sprawdź czy inicjalizujesz wszystkie zmienne
- Użyj debuggera:
```bash
gdb ./go2_stand_example
run enp3s0
# Po crash: bt (backtrace)
```

---

## Problemy z Zachowaniem Robota

### ⚠️ Robot drga/oscyluje

**Objaw:**
Robot trzęsie się lub wykonuje małe oscylacje

**Przyczyna:**
Za wysokie wartości Kp lub za niskie Kd

**Rozwiązanie:**
```cpp
// Zmniejsz Kp o połowę
float Kp = 30.0;  // Było 60.0

// Lub zwiększ Kd
float Kd = 8.0;   // Było 5.0

// Lub oba
float Kp = 40.0;
float Kd = 7.0;
```

**Testowanie:**
1. Zacznij od bardzo niskich wartości (Kp=10, Kd=1)
2. Stopniowo zwiększaj Kp
3. Jeśli zaczyna oscylować - zwiększ Kd

---

### ⚠️ Robot porusza się za wolno

**Objaw:**
Ruchy są ospałe, robot słabo reaguje

**Przyczyna:**
Za niskie wartości Kp lub za długi czas interpolacji

**Rozwiązanie:**
```cpp
// Zwiększ Kp
float Kp = 80.0;  // Było 60.0

// Lub skróć czas ruchu
float _duration_1 = 250;  // Było 500 (będzie 2× szybciej)
```

---

### ⚠️ Robot wykonuje gwałtowne ruchy

**Objaw:**
Szarpnięcia, niebezpieczne ruchy

**Przyczyna:**
Za krótki czas interpolacji lub za duże różnice w pozycjach

**Rozwiązanie:**
```cpp
// Wydłuż czas interpolacji
float _duration_1 = 1000;  // Było 500 (będzie 2× wolniej)

// Lub zmniejsz różnicę w pozycjach
// Zamiast skoku o 1.0 rad, zrób kilka mniejszych kroków
```

---

### ⚠️ Robot nie utrzymuje pozycji

**Objaw:**
Robot opada lub stawy się rozluźniają

**Przyczyna:**
Za niskie Kp lub zbyt duże obciążenie

**Rozwiązanie:**
```cpp
// Zwiększ Kp
float Kp = 100.0;  // Było 60.0

// Dla B2:
float Kp = 1500.0;  // Było 1000.0
```

**Sprawdź obciążenie:**
- GO2: max ~5kg
- B2: max ~20kg
- Czy robot nie jest przeciążony?

---

### ⚠️ Niektóre stawy nie reagują

**Objaw:**
Część stawów pracuje, inne stoją w miejscu

**Przyczyna:**
Niepoprawne indeksy lub wartości specjalne (PosStopF)

**Diagnoza:**
```cpp
// Dodaj wyświetlanie w kodzie
for(int i = 0; i < 12; i++) {
    std::cout << "Staw " << i << ": " 
              << low_state.motor_state()[i].q() << std::endl;
}
```

**Rozwiązanie:**
- Sprawdź czy używasz poprawnych indeksów (0-11 dla czworonogów)
- Upewnij się że nie ustawiasz q = PosStopF (to wyłącza kontrolę pozycji)
- Sprawdź czy mode jest poprawny (0x01 dla GO2, 0x0A dla B2)

---

## Problemy z Danymi

### 📊 Dane z czujników są błędne

**Objaw:**
IMU, siły stóp, pozycje pokazują dziwne wartości

**Rozwiązanie 1:** Sprawdź czy odczytujesz aktualne dane
```cpp
// Upewnij się że subscriber działa
lowstate_subscriber->InitChannel(
    std::bind(&Custom::LowStateMessageHandler, this, std::placeholders::_1), 
    1  // <- priorytet
);
```

**Rozwiązanie 2:** Dodaj opóźnienie przed odczytem
```cpp
// W Init() lub na początku programu
sleep(1);  // Poczekaj na pierwsze dane
```

**Rozwiązanie 3:** Sprawdź jednostki
- Kąty: radiany (nie stopnie!)
- Przyspieszenie: m/s²
- Prędkość kątowa: rad/s
- Siła: N (niutony)

---

### 📊 CRC Error

**Objaw:**
```
CRC check failed
```

**Przyczyna:**
Suma kontrolna niepoprawna - dane mogły być uszkodzone

**Rozwiązanie:**
```cpp
// Upewnij się że obliczasz CRC przed wysłaniem
low_cmd.crc() = crc32_core(
    (uint32_t *)&low_cmd, 
    (sizeof(unitree_go::msg::dds_::LowCmd_)>>2)-1
);

lowcmd_publisher->Write(low_cmd);
```

---

## Problemy Specyficzne dla Modelu

### GO2: "Robot spadł z zawieszenia"

**Rozwiązanie:**
- Upewnij się że mocowanie jest bezpieczne
- Zacznij od pozycji leżącej, nie od stojącej
- Użyj niższych wartości Kp podczas testowania

---

### B2: "Robot nie może się podnieść"

**Przyczyna:**
Za niskie Kp lub niepoprawny tryb silnika

**Rozwiązanie:**
```cpp
// Sprawdź tryb
low_cmd.motor_cmd()[i].mode() = 0x0A;  // Musi być 0x0A dla B2!

// Sprawdź Kp
float Kp = 1000.0;  // Minimum dla B2
```

---

### H1: "Robot traci równowagę"

**Przyczyna:**
Dwunożna równowaga jest trudna i wymaga zaawansowanych algorytmów

**Rozwiązanie:**
- Użyj przykładów high-level (mają wbudowane algorytmy równowagi)
- Nie próbuj low-level sterowania bez doświadczenia
- Zacznij od przykładów `h1_loco_client_example.cpp`

---

## Problemy z Uprawnieniami

### 🔒 "Permission denied" przy /dev/dds

**Objaw:**
```
Permission denied: cannot access /dev/dds
```

**Rozwiązanie:**
```bash
# Dodaj użytkownika do grupy
sudo usermod -a -G dialout $USER

# Wyloguj się i zaloguj ponownie
# lub
sudo reboot
```

---

### 🔒 "Cannot bind to network interface"

**Objaw:**
```
Error: Cannot bind to interface
```

**Rozwiązanie:**
```bash
# Uruchom z sudo
sudo ./go2_stand_example enp3s0

# Lub dodaj uprawnienia CAP_NET_RAW
sudo setcap cap_net_raw+ep ./go2_stand_example
./go2_stand_example enp3s0
```

---

## Narzędzia Diagnostyczne

### Sprawdzanie Połączenia

```bash
# Test 1: Ping robota
ping 192.168.123.10

# Test 2: Sprawdź interfejs
ip link show enp3s0

# Test 3: Sprawdź routing
ip route

# Test 4: Sprawdź czy port jest otwarty (jeśli znasz port)
nc -zv 192.168.123.10 8080
```

### Sprawdzanie Bibliotek

```bash
# Sprawdź czy SDK jest zainstalowane
ldconfig -p | grep unitree

# Sprawdź zależności programu
ldd ./go2_stand_example
```

### Logowanie dla Debugowania

Dodaj w kodzie szczegółowe logowanie:

```cpp
void Custom::LowCmdWrite()
{
    static int debug_counter = 0;
    if(debug_counter++ % 100 == 0) {  // Co 100 kroków = 0.2s
        std::cout << "=== DEBUG ===" << std::endl;
        std::cout << "motiontime: " << motiontime << std::endl;
        std::cout << "_percent_1: " << _percent_1 << std::endl;
        std::cout << "Staw 0 pozycja: " << low_state.motor_state()[0].q() << std::endl;
        std::cout << "Staw 0 komenda: " << low_cmd.motor_cmd()[0].q() << std::endl;
    }
    // ... reszta kodu
}
```

---

## Kiedy Szukać Pomocy

Jeśli problem nadal występuje:

1. **Sprawdź dokumentację:**
   - README.md
   - PRZEWODNIK_PL.md
   - Dokumentacja modelu (example/h1/README.md etc.)

2. **Sprawdź GitHub Issues:**
   - https://github.com/unitreerobotics/unitree_sdk2/issues
   - Poszukaj podobnych problemów

3. **Forum Unitree:**
   - https://support.unitree.com/
   - Opisz dokładnie:
     * Model robota
     * System operacyjny
     * Co próbowałeś zrobić
     * Dokładny komunikat błędu
     * Co już próbowałeś

4. **Utwórz nowy Issue:**
   - Jeśli to błąd w SDK
   - Dołącz: logi, wersję SDK, model robota
   - Opisz kroki do reprodukcji

---

## Checklist Przed Pytaniem o Pomoc

Przed zadaniem pytania, upewnij się że:
- [ ] Przeczytałeś README.md i PRZEWODNIK_PL.md
- [ ] Sprawdziłeś ten przewodnik rozwiązywania problemów
- [ ] Sprawdziłeś istniejące Issues na GitHub
- [ ] Robot jest włączony i połączony
- [ ] Używasz poprawnego interfejsu sieciowego
- [ ] SDK jest poprawnie skompilowany
- [ ] Używasz poprawnych parametrów dla swojego modelu
- [ ] Próbowałeś podstawowych rozwiązań z tego przewodnika

**Im więcej informacji podasz, tym szybciej otrzymasz pomoc!**

---

## Podsumowanie Najczęstszych Problemów

| Problem | Najczęstsza Przyczyna | Szybkie Rozwiązanie |
|---------|----------------------|---------------------|
| Nie kompiluje się | Brak bibliotek | `apt-get install` zależności |
| "Network interface not found" | Zła nazwa interfejsu | Użyj `ip a`, popraw nazwę |
| Robot drga | Za wysokie Kp | Zmniejsz Kp o połowę |
| Robot nie reaguje | Inne usługi aktywne | Poczekaj na ReleaseMode |
| Segmentation fault | Robot nie połączony | Sprawdź połączenie (`ping`) |
| Za wolne ruchy | Niskie Kp lub długi czas | Zwiększ Kp lub skróć duration |

**Powodzenia w rozwiązywaniu problemów! 🔧🤖**
