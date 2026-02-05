# Przewodnik dla Studentów - Unitree SDK2

## Wprowadzenie

Ten przewodnik pomoże Ci zrozumieć jak programować roboty Unitree. Jest przeznaczony dla osób, które po raz pierwszy stykają się z programowaniem robotów.

## Podstawowe Koncepcje

### 1. Komunikacja z Robotem

Robot i komputer komunikują się za pomocą systemu **DDS (Data Distribution Service)**. To jak poczta - wysyłasz listy (komendy) do robota i otrzymujesz listy (stan robota) z powrotem.

**Dwa główne kanały:**
- **LowCmd** - wysyłasz komendy DO robota (co ma robić)
- **LowState** - otrzymujesz stan Z robota (co aktualnie robi, pozycje stawów, dane z czujników)

### 2. Sterowanie Silnikami

Każdy staw robota ma silnik. Aby kontrolować silnik, wysyłasz do niego 5 parametrów:

| Parametr | Symbol | Co oznacza | Przykład |
|----------|--------|------------|----------|
| Pozycja docelowa | `q` | Kąt jaki chcesz osiągnąć | 1.5 rad (≈86°) |
| Prędkość docelowa | `dq` | Jak szybko ma się poruszać | 0.0 rad/s |
| Sztywność | `kp` | Jak mocno dążyć do celu | 60.0 |
| Tłumienie | `kd` | Jak mocno hamować ruch | 5.0 |
| Moment siły | `tau` | Dodatkowa siła | 0.0 Nm |

**Wzór działania silnika:**
```
Siła = kp × (pozycja_docelowa - pozycja_aktualna) + kd × (prędkość_docelowa - prędkość_aktualna) + tau
```

### 3. Regulator PD

To jest jak tempomat w samochodzie:
- **Kp (Proportional)** - im większy, tym mocniej silnik dąży do pozycji docelowej (ale może oscylować)
- **Kd (Derivative)** - im większy, tym bardziej wygładza ruch (zmniejsza oscylacje)

**Przykłady wartości:**
- Małe wartości (Kp=10, Kd=1): powolne, miękkie ruchy
- Średnie wartości (Kp=60, Kd=5): normalne ruchy
- Duże wartości (Kp=200, Kd=10): szybkie, sztywne ruchy

### 4. Interpolacja Ruchu

Aby robot poruszał się płynnie między pozycjami, używamy **interpolacji liniowej**:

```
pozycja_aktualna = (1 - procent) × pozycja_start + procent × pozycja_cel
```

Gdzie `procent` zmienia się od 0.0 do 1.0 w czasie:
- procent = 0.0 → robot w pozycji startowej
- procent = 0.5 → robot w połowie drogi
- procent = 1.0 → robot osiągnął cel

**Przykład:**
```
czas_trwania = 500 kroków (500 × 2ms = 1 sekunda)
procent += 1.0 / 500  (co krok zwiększamy o 0.002)
```

## Struktura Typowego Programu

### Faza 1: Inicjalizacja

```
1. Przygotuj struktury danych (LowCmd, LowState)
2. Utwórz Publisher (do wysyłania komend)
3. Utwórz Subscriber (do odbierania stanu)
4. Wyłącz inne usługi sterowania
5. Odczytaj pozycję początkową robota
```

### Faza 2: Pętla Sterowania

```
Powtarzaj co 2ms (500 Hz):
  1. Odbierz aktualny stan robota
  2. Oblicz pożądane pozycje stawów
  3. Wyślij komendy do silników
  4. Pokaż dane na ekranie (opcjonalnie)
```

### Faza 3: Sekwencja Ruchów

```
Etap 1: Przejdź z pozycji startowej do pozycji_1
Etap 2: Przejdź z pozycji_1 do pozycji_2
Etap 3: Utrzymuj pozycję_2
Etap 4: Przejdź z pozycji_2 do pozycji_3
```

## Anatomia Robota GO2

Robot GO2 ma **12 stawów** (4 nogi × 3 stawy):

```
Przednia Lewa (FL)     Przednia Prawa (FR)
     [0,1,2]                [3,4,5]
        |                      |
        |                      |
     [6,7,8]                [9,10,11]
 Tylna Lewa (RL)        Tylna Prawa (RR)
```

**Dla każdej nogi:**
- Indeks 0: Biodro (obrót w bok)
- Indeks 1: Udo (góra-dół)
- Indeks 2: Podudzie (zgięcie kolana)

## Bezpieczeństwo

### ⚠️ WAŻNE ZASADY BEZPIECZEŃSTWA

1. **ZAWSZE** zawieszaj robota lub połóż na ziemi przed pierwszym uruchomieniem
2. **NIGDY** nie uruchamiaj kodu z dużą sztywnością (Kp > 100) bez testowania
3. **ZAWSZE** zacznij od małych ruchów (małe zmiany kątów)
4. **ZAWSZE** miej przycisk awaryjnego zatrzymania w zasięgu ręki
5. **SPRAWDŹ** czy inne usługi sterowania są wyłączone przed uruchomieniem

## Częste Problemy i Rozwiązania

### Problem: Robot się trzęsie

**Przyczyna:** Za duże wartości Kp lub Kd  
**Rozwiązanie:** Zmniejsz Kp i Kd (np. do Kp=30, Kd=2)

### Problem: Robot porusza się za wolno

**Przyczyna:** Za małe wartości Kp lub za długi czas interpolacji  
**Rozwiązanie:** Zwiększ Kp (np. do 80) lub zmniejsz duration

### Problem: Robot nie reaguje

**Przyczyna:** Inna usługa sterowania jest aktywna  
**Rozwiązanie:** Program automatycznie próbuje wyłączyć inne usługi - poczekaj

### Problem: Błąd kompilacji

**Przyczyna:** Brak zainstalowanych bibliotek  
**Rozwiązanie:** Uruchom ponownie instalację zależności z głównego README.md

## Przykładowe Wartości Pozycji

### Pozycja stojąca (normalna)
```
Biodra: 0.0 rad (prosto)
Uda: 0.67 rad (≈38°)
Podudzia: -1.3 rad (≈-74°)
```

### Pozycja leżąca
```
Biodra: 0.0 rad
Uda: 1.36 rad (≈78°)
Podudzia: -2.65 rad (≈-152°)
```

### Pozycja szeroka
```
Biodra: ±0.35 rad (nogi rozstawione)
Uda: 1.36 rad
Podudzia: -2.65 rad
```

## Wskazówki dla Początkujących

1. **Zacznij od przykładów** - uruchom najpierw `go2_stand_example` lub `b2_stand_example`
2. **Zmień jedną rzecz** - modyfikuj tylko jeden parametr na raz (np. tylko Kp)
3. **Obserwuj wyniki** - użyj `std::cout` aby zobaczyć wartości
4. **Testuj bezpiecznie** - zawsze najpierw w symulatorze lub z zawieszonym robotem
5. **Zapisuj działające konfiguracje** - gdy coś działa dobrze, zapisz parametry

## Następne Kroki

Po opanowaniu podstaw, możesz eksplorować:

1. **Sterowanie wysokopoziomowe** - przykłady w `high_level/`
2. **Sterowanie audio** - przykłady w `audio/`
3. **Sterowanie manipulatorem** - przykłady w `arm*/`
4. **Własne trajektorie** - tworzenie własnych sekwencji ruchów

## Dodatkowe Zasoby

- Centrum Dokumentacji Unitree: https://support.unitree.com/home/zh/developer
- Przykłady w katalogu `example/`
- README w poszczególnych katalogach przykładów
