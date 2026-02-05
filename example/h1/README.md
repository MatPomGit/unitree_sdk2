# Sterowanie Mechanizmem Równoległym Unitree H1_2

Polski | [English (oryginał)](README_zh.md)

## Interfejs Sterowania Mechanizmem Równoległym

### Wprowadzenie dla studentów

Mechanizm równoległy w robocie Unitree H1_2 dotyczy stawów kostek lewej i prawej nogi. Ponieważ stawy kostek lewej i prawej są symetryczne, poniższe wyjaśnienie użyje lewego stawu kostki H1_2 jako przykładu:

<p align="center"><img src="doc/images/ankle.png" width="30%"/></p>

### Budowa mechanizmu kostki

Po stronie sprzętowej, lewy staw kostki H1_2 wykorzystuje mechanizm równoległy składający się z czterech stawów:

**Stawy równoległe:**
* Staw A (Joint A)
* Staw B (Joint B)

**Stawy szeregowe:**
* Staw pochylenia - Pitch (staw P)
* Staw przechylenia - Roll (staw R)

### Jak to działa?

**Ważne:** Tylko stawy A i B mogą być bezpośrednio sterowane przez silniki, podczas gdy stawy P i R nie mogą być kontrolowane bezpośrednio. 

Model URDF robota i algorytmy sterowania ruchem zazwyczaj uwzględniają tylko szeregowe stawy P i R. Aby kontrolować te stawy:
1. Sterujemy pośrednio stawami A i B
2. To pozwala nam osiągnąć kontrolę nad stawami P i R

### Tryb PR (PR Mode)

Po stronie oprogramowania zapewniamy równoważny interfejs sterowania stawami szeregowymi, zwany `Trybem PR` (PR Mode). Umożliwia on użytkownikom bezpośrednie sterowanie stawami P i R. 

Podobnie jak w typowej metodzie sterowania stawami, w `Trybie PR` każdy staw szeregowy otrzymuje następujące komendy:

| Nazwa komendy           | Zmienna | Opis |
| ----------------------- | -------- | ---- |
| Moment wyprzedzający    | `tau`    | Dodatkowy moment siły stosowany bezpośrednio |
| Kąt docelowy           | `q`      | Pozycja, którą chcemy osiągnąć |
| Prędkość kątowa docelowa | `dq`     | Prędkość, z jaką ma się poruszać staw |
| Sztywność stawu        | `kp`     | Jak mocno staw dąży do pozycji docelowej |
| Tłumienie stawu        | `kd`     | Jak mocno staw tłumi oscylacje |

### Wzór na całkowity moment siły

Całkowity moment zastosowany do stawu szeregowego jest obliczany według wzoru:

```
T = kp * (q - q_m) + kd * (dq - dq_m) + tau
```

Gdzie:
- `q` - pozycja docelowa
- `q_m` - rzeczywista zmierzona pozycja
- `dq` - prędkość docelowa  
- `dq_m` - rzeczywista zmierzona prędkość
- `kp` - współczynnik sztywności
- `kd` - współczynnik tłumienia
- `tau` - moment wyprzedzający

### Konwersja wewnętrzna

Aby poprawić precyzję sterowania stawami szeregowymi, robot H1_2 wewnętrznie konwertuje komendy (moment wyprzedzający, kąt docelowy, prędkość kątową, sztywność i tłumienie) dla stawów P i R na rzeczywiste komendy dla stawów A i B, bazując na zależnościach kinematycznych i dynamicznych.

## Eksperyment śledzenia pozycji stawów szeregowych

### Cel eksperymentu

Aby przetestować wydajność kostki H1_2 w `Trybie PR`, sprawimy że stawy P i R będą śledzić krzywą sinusoidalną (falę). Możesz zobaczyć [przykładowy kod](https://github.com/unitreerobotics/unitree_sdk2/blob/main/example/h1/low_level/h1_2_ankle_track.cpp). 

Poniżej znajdują się kluczowe fragmenty kodu z wyjaśnieniami:

### Fragment 1: Włączanie trybu PR i generowanie krzywej sinusoidalnej

```c++
// [Etap 2]: wahaj kostką w trybie PR
mode_ = PR;  // Włącz tryb PR

// Generuj trajektorię sin/cos (ruch falowy)
double max_P = 0.25;  // Maksymalna amplituda dla stawu Pitch [radiany]
double max_R = 0.25;  // Maksymalna amplituda dla stawu Roll [radiany]
double t = time_ - duration_;  // Aktualny czas w ruchu

// Oblicz pożądane pozycje dla lewej kostki
double L_P_des = max_P * std::cos(2.0 * M_PI * t);  // Pitch - funkcja cosinus
double L_R_des = max_R * std::sin(2.0 * M_PI * t);  // Roll - funkcja sinus

// Oblicz pożądane pozycje dla prawej kostki (symetrycznie)
double R_P_des = max_P * std::cos(2.0 * M_PI * t);  // Pitch - taki sam jak lewy
double R_R_des = -max_R * std::sin(2.0 * M_PI * t); // Roll - odwrócony znak (symetria)
```

**Wyjaśnienie:**
- Każdy staw porusza się według krzywej sinusoidalnej o okresie 1 sekunda (2π)
- `max_P` i `max_R` określają maksymalny zakres ruchu (0.25 radiana ≈ 14.3 stopnia)
- Lewa i prawa kostka poruszają się synchronicznie, ale Roll jest odwrócony dla symetrii

### Fragment 2: Ustawianie komend dla stawów kostki

```c++
// Aktualizuj docelowe pozycje stawów kostki
float Kp_Pitch = 80;  // Sztywność dla stawu Pitch (jak mocno dąży do pozycji)
float Kd_Pitch = 1;   // Tłumienie dla stawu Pitch (redukcja oscylacji)
float Kp_Roll = 80;   // Sztywność dla stawu Roll
float Kd_Roll = 1;    // Tłumienie dla stawu Roll

// === LEWA KOSTKA - Staw Pitch (indeks 4) ===
dds_low_command.motor_cmd().at(4).q() = L_P_des;      // Ustaw pozycję docelową
dds_low_command.motor_cmd().at(4).dq() = 0;           // Prędkość docelowa = 0
dds_low_command.motor_cmd().at(4).kp() = Kp_Pitch;    // Ustaw sztywność
dds_low_command.motor_cmd().at(4).kd() = Kd_Pitch;    // Ustaw tłumienie
dds_low_command.motor_cmd().at(4).tau() = 0;          // Brak dodatkowego momentu

// === LEWA KOSTKA - Staw Roll (indeks 5) ===
dds_low_command.motor_cmd().at(5).q() = L_R_des;      // Ustaw pozycję docelową
dds_low_command.motor_cmd().at(5).dq() = 0;
dds_low_command.motor_cmd().at(5).kp() = Kp_Roll;
dds_low_command.motor_cmd().at(5).kd() = Kd_Roll;
dds_low_command.motor_cmd().at(5).tau() = 0;

// === PRAWA KOSTKA - Staw Pitch (indeks 10) ===
dds_low_command.motor_cmd().at(10).q() = R_P_des;     // Ustaw pozycję docelową
dds_low_command.motor_cmd().at(10).dq() = 0;
dds_low_command.motor_cmd().at(10).kp() = Kp_Pitch;
dds_low_command.motor_cmd().at(10).kd() = Kd_Pitch;
dds_low_command.motor_cmd().at(10).tau() = 0;

// === PRAWA KOSTKA - Staw Roll (indeks 11) ===
dds_low_command.motor_cmd().at(11).q() = R_R_des;     // Ustaw pozycję docelową
dds_low_command.motor_cmd().at(11).dq() = 0;
dds_low_command.motor_cmd().at(11).kp() = Kp_Roll;
dds_low_command.motor_cmd().at(11).kd() = Kd_Roll;
dds_low_command.motor_cmd().at(11).tau() = 0;
```

**Wyjaśnienie indeksów silników:**
- Indeks 4: Lewy Staw Pitch (LeftAnklePitch)
- Indeks 5: Lewy Staw Roll (LeftAnkleRoll)
- Indeks 10: Prawy Staw Pitch (RightAnklePitch)
- Indeks 11: Prawy Staw Roll (RightAnkleRoll)

### Fragment 3: Wyświetlanie wartości pożądanych i zmierzonych

```c++
// Odczytaj aktualne zmierzone pozycje z czujników
float L_P_m = low_state_.motor_state().at(4).q();    // Lewa Pitch - zmierzona
float L_R_m = low_state_.motor_state().at(5).q();    // Lewa Roll - zmierzona
float R_P_m = low_state_.motor_state().at(10).q();   // Prawa Pitch - zmierzona
float R_R_m = low_state_.motor_state().at(11).q();   // Prawa Roll - zmierzona

// Wydrukuj wszystkie wartości w jednej linii (CSV format)
printf("%f,%f,%f,%f,%f,%f,%f,%f\n", 
       L_P_des, L_P_m,    // Lewa Pitch: pożądana, zmierzona
       L_R_des, L_R_m,    // Lewa Roll: pożądana, zmierzona
       R_P_des, R_P_m,    // Prawa Pitch: pożądana, zmierzona
       R_R_des, R_R_m);   // Prawa Roll: pożądana, zmierzona
```

### Wyjaśnienie kolumn danych wyjściowych:

| L_P_des            | L_P_m               | L_R_des           | L_R_m              | R_P_des             | R_P_m                | R_R_des            | R_R_m               |
| ------------------ | ------------------- | ----------------- | ------------------ | ------------------- | -------------------- | ------------------ | ------------------- |
| Lewy Pitch Pożądany | Lewy Pitch Zmierzony | Lewy Roll Pożądany | Lewy Roll Zmierzony | Prawy Pitch Pożądany | Prawy Pitch Zmierzony | Prawy Roll Pożądany | Prawy Roll Zmierzony |

### Jak uruchomić przykład?

**UWAGA BEZPIECZEŃSTWA:** Przed uruchomieniem programu **robot musi być zawieszony w powietrzu** (nie może stać na ziemi)!

**Krok 1:** Zainstaluj i skompiluj [unitree_sdk2](https://github.com/unitreerobotics/unitree_sdk2) zgodnie z instrukcjami w głównym README.

**Krok 2:** Upewnij się, że robot jest **bezpiecznie zawieszony**.

**Krok 3:** Uruchom program testowy w terminalu:

```bash
# Zastąp 'network_interface' nazwą swojego interfejsu sieciowego (np. eth0, enp3s0)
./h1_2_ankle_track network_interface
```

**Co się stanie po uruchomieniu:**
1. Robot najpierw zresetuje się do pozycji zerowej (wszystkie stawy w pozycji początkowej)
2. Następnie zacznie okresowo poruszać kostkami w ruchu falowym
3. Program będzie drukować pożądane i zmierzone wartości dla obu stawów kostek

### Wyniki śledzenia pozycji

Poniższe wykresy pokazują wyniki śledzenia pozycji:

<p float="middle">
  <img src="doc/images/tracking.png" width="49%"/>
  <img src="doc/images/tracking_circle.png" width="49%"/>
</p>

### Objaśnienie symboli na wykresach:

* `L_Pitch_d`: Wartość pożądana lewego stawu P (Pitch)
* `L_Pitch_m`: Wartość zmierzona lewego stawu P (Pitch)
* `L_Roll_d`: Wartość pożądana lewego stawu R (Roll)
* `L_Roll_m`: Wartość zmierzona lewego stawu R (Roll)

**Interpretacja wykresów:**

- **Lewy wykres (tracking.png):** Pokazuje, że szeregowe stawy Pitch i Roll mogą dokładnie śledzić sinusoidalne komendy pozycji docelowej. Linie pożądane i zmierzone prawie się pokrywają, co świadczy o dobrej precyzji sterowania.

- **Prawy wykres (tracking_circle.png):** Ilustruje wydajność śledzenia stawów szeregowych w przestrzeni fazowej (phase space). Ruch tworzy kształt koła/elipsy, pokazując skorelowany ruch obu stawów.

### Podsumowanie dla studenta

Ten eksperyment demonstruje:
1. **Jak działa tryb PR** - umożliwia bezpośrednie sterowanie stawami Pitch i Roll
2. **Precyzję sterowania** - robot może dokładnie śledzić złożone trajektorie
3. **Synchronizację** - oba stawy mogą pracować razem w skoordynowany sposób
4. **Znaczenie parametrów Kp i Kd** - sztywność i tłumienie wpływają na jakość śledzenia
