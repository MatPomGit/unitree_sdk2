# Różnice Między Modelami Robotów - Unitree SDK2

Ten dokument wyjaśnia kluczowe różnice między modelami robotów Unitree i jak dostosować programy dla każdego z nich.

## Przegląd Modeli

| Model | Typ | Liczba Stawów | Zastosowanie |
|-------|-----|---------------|--------------|
| GO2 | Czworonóg | 12 (4×3) | Ogólne zastosowania, edukacja |
| B2 | Czworonóg | 12 (4×3) | Przemysł, większa ładowność |
| B2W | Czworonóg z kołami | 12+2 | Hybrydowy (chód + koła) |
| H1 | Humanoid | 19-27 | Badania, dwunożne aplikacje |
| G1 | Humanoid | 23-37 | Zaawansowany humanoid |

## GO2 vs B2 - Kluczowe Różnice

### 1. Parametry Sterowania

**GO2:**
```cpp
float Kp = 60.0;   // Niższa sztywność
float Kd = 5.0;    // Niższe tłumienie
```

**B2:**
```cpp
float Kp = 1000.0; // ZNACZNIE wyższa sztywność (16× większa!)
float Kd = 10.0;   // Wyższe tłumienie (2× większe)
```

**Dlaczego różnica?**
- B2 jest cięższy i mocniejszy
- Potrzebuje większej siły do utrzymania pozycji
- Ma mocniejsze silniki mogące obsłużyć wyższe wartości

### 2. Tryb Silnika

**GO2:**
```cpp
low_cmd.motor_cmd()[i].mode() = 0x01;  // Tryb servo PMSM
```

**B2:**
```cpp
low_cmd.motor_cmd()[i].mode() = 0x0A;  // Inny tryb silnika
```

**Znaczenie trybów:**
- `0x01` - standardowy tryb servo dla GO2
- `0x0A` - zoptymalizowany tryb dla B2
- Użycie złego trybu może powodować nieprawidłowe działanie!

### 3. Pozycje i Sekwencje

**GO2 - 4 etapy:**
1. Pozycja rozstawiona
2. Pozycja stojąca
3. Utrzymanie pozycji
4. Powrót do rozstawionej

**B2 - 5 etapów:**
1. Pozycja z lekko rozstawionymi nogami
2. Pozycja stojąca
3. Utrzymanie pozycji
4. Pozycja leżąca
5. Rozstawienie nóg

### 4. Bezpieczeństwo Startowe

**GO2:**
```cpp
std::cout << "WARNING: Make sure the robot is hung up or lying on the ground." << std::endl;
```
✅ Można zawiesić ALBO położyć

**B2:**
```cpp
std::cout << "WARNING: Make sure the robot is lying on the ground." << std::endl;
```
⚠️ TYLKO leżący - nie wieszać! (zbyt ciężki)

## B2 vs B2W - Różnice Kół

### B2W (Wheeled)

B2W posiada dodatkowo 2 koła, co daje możliwość:
- Szybszego przemieszczania się po płaskich powierzchniach
- Hybrydowego trybu (chodzenie + jazda)
- Lepszej efektywności energetycznej na asfalcie

**Dodatkowe stawy:**
- Lewy silnik koła
- Prawy silnik koła

**Usługa specjalna:**
```cpp
if(name == "normal-w") return "wheeled_sport(b2W)";
```

## GO2 vs GO2W

Podobnie jak B2/B2W:
- GO2W ma koła
- Może jeździć i chodzić
- Używa usługi `wheeled_sport(go2W)`

## H1 - Robot Humanoidalny

### Liczba Stopni Swobody

**Warianty H1:**
- **H1**: ~19 DOF (stopni swobody)
- **H1_2**: ~27 DOF (z ulepszonymi kostkami)

### Specjalne Mechanizmy

**Mechanizm równoległy kostek:**
- Stawy A i B (bezpośrednio sterowane)
- Stawy P (Pitch) i R (Roll) (pośrednio sterowane)
- Tryb PR umożliwia sterowanie P i R jak normalnymi stawami

**Czytaj więcej:** `example/h1/README.md`

### Struktura Stawów H1

```
Tułów
├── Lewa noga (6 DOF)
│   ├── Biodro Pitch
│   ├── Biodro Roll
│   ├── Biodro Yaw
│   ├── Kolano
│   ├── Kostka Pitch (P)
│   └── Kostka Roll (R)
├── Prawa noga (6 DOF)
├── Lewe ramię (4-7 DOF)
├── Prawe ramię (4-7 DOF)
└── Głowa/Szyja (opcjonalnie)
```

## G1 - Zaawansowany Humanoid

### Możliwości Dodatkowe

**G1 posiada:**
- Więcej stopni swobody niż H1
- Lepszą manipulację (bardziej zwinne ręce)
- System audio (synteza mowy, odtwarzanie dźwięków)
- Dex3 (trzypala ręka deksteryczna) - opcjonalnie
- Możliwość AGV (Automated Guided Vehicle)

### Wersje Ramion

**G1 Arm5:**
```cpp
g1_arm5_sdk_dds_example.cpp  // 5-stopniowe ramię
```

**G1 Arm7:**
```cpp
g1_arm7_sdk_dds_example.cpp  // 7-stopniowe ramię (więcej swobody)
```

**G1 Dual Arm:**
```cpp
g1_dual_arm_example.cpp  // Skoordynowane sterowanie 2 ramionami
```

## Tabela Porównawcza - Czworonogi

| Cecha | GO2 | GO2W | B2 | B2W |
|-------|-----|------|----|----|
| Waga | ~15kg | ~18kg | ~60kg | ~65kg |
| Ładowność | ~5kg | ~5kg | ~20kg | ~20kg |
| Kp (typowy) | 60 | 60 | 1000 | 1000 |
| Kd (typowy) | 5 | 5 | 10 | 10 |
| Tryb silnika | 0x01 | 0x01 | 0x0A | 0x0A |
| Koła | ❌ | ✅ | ❌ | ✅ |
| Można zawiesić? | ✅ | ✅ | ❌ | ❌ |
| Zastosowanie | Edukacja, badania | Patrol, inspekcja | Przemysł | Transport |

## Tabela Porównawcza - Humanoidy

| Cecha | H1 | H1_2 | G1 |
|-------|----|----|-----|
| DOF nóg | 12 | 12 | 12 |
| DOF ramion | 8-14 | 8-14 | 10-14+ |
| Mechanizm równoległy | ❌ | ✅ | ✅ |
| Audio | ❌ | ❌ | ✅ |
| Dex3 (dłoń) | ❌ | ❌ | ✅ (opcja) |
| AGV | ❌ | ❌ | ✅ (opcja) |

## Jak Wybrać Odpowiedni Model?

### Dla Edukacji i Nauki
**Rekomendacja: GO2**
- Prostszy w użyciu
- Lżejszy (bezpieczniejszy)
- Niższe parametry PD (łatwiej zacząć)
- Tańszy

### Dla Aplikacji Przemysłowych
**Rekomendacja: B2 / B2W**
- Większa ładowność
- Bardziej wytrzymały
- B2W: opcja kół dla efektywności

### Dla Badań Humanoidalnych
**Rekomendacja: H1**
- Dobry początek z humanoidami
- Stabilna platforma
- Duża społeczność

### Dla Zaawansowanych Badań
**Rekomendacja: G1**
- Najwięcej możliwości
- Najbardziej zwinny
- Integracja audio i manipulacji

## Adaptacja Kodu Między Modelami

### Z GO2 na B2

**Kroki:**
1. Zmień parametry PD:
```cpp
// float Kp = 60.0;  // GO2
float Kp = 1000.0;   // B2

// float Kd = 5.0;   // GO2
float Kd = 10.0;     // B2
```

2. Zmień tryb silnika:
```cpp
// low_cmd.motor_cmd()[i].mode() = 0x01;  // GO2
low_cmd.motor_cmd()[i].mode() = 0x0A;     // B2
```

3. Dostosuj komunikaty bezpieczeństwa:
```cpp
// "hung up or lying" -> "lying on the ground"
```

4. Opcjonalnie dostosuj pozycje (B2 może potrzebować innych kątów)

### Z Czworonoga na Humanoida

To jest znacznie bardziej skomplikowane:
- Inna liczba stawów
- Inna kinematyka
- Inne algorytmy równowagi
- Zalecane: zacznij od przykładów dla H1/G1

## Porady Specyficzne dla Modelu

### GO2
- ✅ Dobry do nauki interpolacji i podstaw
- ✅ Testuj z zawieszonym robotem
- ⚠️ Nie przeciążaj (max ~5kg)

### B2
- ⚠️ TYLKO na ziemi, nie wieszaj!
- ✅ Może dźwigać więcej (do ~20kg)
- ⚠️ Wyższe Kp może powodować mocniejsze reakcje
- 💡 Zacznij od niższych wartości Kp (500) i zwiększaj stopniowo

### B2W / GO2W
- ✅ Używaj trybu kół na płaskich powierzchniach
- ✅ Tryb chodu na nierównościach
- 💡 Hybrydowy tryb oszczędza energię

### H1 / H1_2
- ⚠️ Wymaga zaawansowanej wiedzy o równowadze
- ✅ H1_2: używaj trybu PR dla kostek
- 💡 Zacznij od przykładów high-level (łatwiejsze)
- ⚠️ Dwunożna równowaga jest trudniejsza niż czworonożna

### G1
- ✅ Wykorzystuj możliwości audio
- ✅ Dex3 świetny do chwytania
- 💡 AGV mode dla autonomicznej nawigacji
- ⚠️ Najwięcej możliwości = najwyższa złożoność

## Częste Pytania

**Q: Czy kod dla GO2 zadziała na B2?**  
A: Częściowo. Musisz zmienić parametry PD i tryb silnika. Sekwencja ruchów może wymagać dostosowania.

**Q: Czy mogę używać tego samego SDK dla wszystkich modeli?**  
A: Tak! unitree_sdk2 obsługuje wszystkie modele. Tylko parametry i konfiguracja się różnią.

**Q: Która wersja B2 jest lepsza - zwykła czy B2W?**  
A: Zależy od aplikacji. B2W lepszy na płaskim terenie (szybszy, bardziej efektywny). B2 lepszy jeśli nie potrzebujesz kół (prostszy, tańszy).

**Q: Czy H1 jest trudniejszy niż GO2?**  
A: Tak, znacznie. Dwunożna równowaga jest bardziej złożona. Zacznij od GO2, potem przejdź na H1.

**Q: Jakie są minimalne wymagania sprzętowe?**  
A: GO2/B2: komputer z Ubuntu 20.04, interfejs Ethernet  
H1/G1: potężniejszy komputer (więcej obliczeń)

## Zasoby Specyficzne dla Modelu

### GO2 / GO2W
- `example/go2/` - wszystkie przykłady
- Podstawowe sterowanie low-level

### B2 / B2W
- `example/b2/` - przykłady
- `b2_sport_client.cpp` - tryb sportowy

### H1
- `example/h1/` - przykłady
- `example/h1/README.md` - dokumentacja mechanizmu PR
- `high_level/` - łatwiejsze sterowanie
- `low_level/` - zaawansowane sterowanie

### G1
- `example/g1/` - przykłady
- `audio/` - sterowanie audio
- `dex3/` - sterowanie dłonią
- `agv/` - nawigacja autonomiczna

## Podsumowanie

Wybór modelu zależy od Twojego przypadku użycia:
- **Nauka** → GO2
- **Przemysł** → B2/B2W
- **Badania humanoidalne** → H1
- **Zaawansowane badania** → G1

Wszystkie modele używają tego samego SDK, ale wymagają różnych parametrów i podejść programowania.

---

**Powodzenia w wyborze i programowaniu odpowiedniego robota! 🤖**
