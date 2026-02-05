# 📚 Dokumentacja Unitree SDK2 - Spis Treści

Witaj w polskiej dokumentacji Unitree SDK2! Ten dokument pomoże Ci znaleźć odpowiednie informacje.

## 🚀 Dla Początkujących - Zacznij Tutaj!

Jeśli pierwszy raz pracujesz z robotami Unitree, przeczytaj dokumenty w tej kolejności:

1. **[README.md](README.md)** - Zacznij tutaj!
   - Wymagania systemowe
   - Instalacja zależności
   - Pierwsza kompilacja
   - Podstawowe uruchomienie

2. **[SZYBKI_START_PL.md](SZYBKI_START_PL.md)** - Szybki start (5 minut)
   - Ściągawka komend
   - Typowe wartości parametrów
   - Mapowanie stawów
   - Szybkie rozwiązania problemów

3. **[PRZEWODNIK_PL.md](PRZEWODNIK_PL.md)** - Szczegółowy przewodnik
   - Podstawowe koncepcje (komunikacja, sterowanie)
   - Regulator PD
   - Interpolacja ruchu
   - Bezpieczeństwo
   - Anatomia robota

## 📖 Przewodniki Tematyczne

### Dla Programistów

**[PRZEWODNIK_PRZYKLADY_PL.md](PRZEWODNIK_PRZYKLADY_PL.md)** - Wyjaśnienie przykładów
- Szczegółowy opis wszystkich przykładów w `example/`
- Jak działają programy dla GO2, B2, H1, G1
- Jak modyfikować przykłady
- Wskazówki do eksperymentowania

### Wybór i Porównanie Robotów

**[ROZNICE_MIEDZY_MODELAMI_PL.md](ROZNICE_MIEDZY_MODELAMI_PL.md)** - Porównanie modeli
- GO2 vs B2 vs H1 vs G1
- Parametry sterowania dla każdego modelu
- Kiedy używać którego robota
- Jak adaptować kod między modelami

### Rozwiązywanie Problemów

**[ROZWIAZYWANIE_PROBLEMOW_PL.md](ROZWIAZYWANIE_PROBLEMOW_PL.md)** - Troubleshooting
- Problemy z kompilacją
- Problemy z uruchomieniem
- Problemy z zachowaniem robota
- Narzędzia diagnostyczne
- 20+ typowych przypadków z rozwiązaniami

## 🤖 Dokumentacja Specyficzna dla Modelu

### Robot H1
**[example/h1/README.md](example/h1/README.md)** - Mechanizm równoległy kostek
- Tryb PR dla stawów kostki
- Sterowanie stawami Pitch i Roll
- Eksperyment śledzenia trajektorii

## 📂 Struktura Dokumentacji

```
unitree_sdk2/
├── README.md                          # Główna dokumentacja (PL)
├── SZYBKI_START_PL.md                 # Szybki start
├── PRZEWODNIK_PL.md                   # Przewodnik szczegółowy
├── PRZEWODNIK_PRZYKLADY_PL.md        # Wyjaśnienie przykładów
├── ROZNICE_MIEDZY_MODELAMI_PL.md     # Porównanie robotów
├── ROZWIAZYWANIE_PROBLEMOW_PL.md     # Troubleshooting
│
├── example/                           # Przykłady kodu
│   ├── go2/                          # Przykłady dla GO2
│   ├── b2/                           # Przykłady dla B2
│   ├── h1/                           # Przykłady dla H1
│   │   └── README.md                 # Dokumentacja H1 (PL)
│   ├── g1/                           # Przykłady dla G1
│   └── ...
│
└── [pozostałe pliki SDK]
```

## 🎯 Szybka Nawigacja - Znajdź Co Potrzebujesz

### "Chcę się szybko nauczyć podstaw"
→ [SZYBKI_START_PL.md](SZYBKI_START_PL.md) + [PRZEWODNIK_PL.md](PRZEWODNIK_PL.md)

### "Chcę zrozumieć przykład go2_stand_example"
→ [PRZEWODNIK_PRZYKLADY_PL.md](PRZEWODNIK_PRZYKLADY_PL.md) (sekcja GO2)

### "Nie wiem który robot wybrać"
→ [ROZNICE_MIEDZY_MODELAMI_PL.md](ROZNICE_MIEDZY_MODELAMI_PL.md)

### "Mam problem z kompilacją/uruchomieniem"
→ [ROZWIAZYWANIE_PROBLEMOW_PL.md](ROZWIAZYWANIE_PROBLEMOW_PL.md)

### "Pracuję z robotem H1 i kostkami"
→ [example/h1/README.md](example/h1/README.md)

### "Chcę zmodyfikować parametry PD"
→ [PRZEWODNIK_PL.md](PRZEWODNIK_PL.md) (sekcja "Regulator PD")

### "Robot drga/oscyluje"
→ [ROZWIAZYWANIE_PROBLEMOW_PL.md](ROZWIAZYWANIE_PROBLEMOW_PL.md) (sekcja "Robot drga")

### "Jak zmienić pozycje docelowe?"
→ [PRZEWODNIK_PRZYKLADY_PL.md](PRZEWODNIK_PRZYKLADY_PL.md) (sekcja "Jak Modyfikować")

## 📊 Statystyki Dokumentacji

- **Łączna liczba stron**: ~40
- **Liczba przewodników**: 5 głównych + 2 README
- **Przykłady kodu wyjaśnione**: 10+
- **Rozwiązania problemów**: 20+
- **Języki**: Polski (główna dokumentacja)

## 🔍 Szczegółowy Spis Treści Wszystkich Dokumentów

### README.md
- Wprowadzenie dla studentów
- Środowisko wymagane przed kompilacją
- Przygotowanie środowiska programistycznego
- Kompilacja przykładów
- Instalacja SDK w systemie
- Struktura repozytorium

### SZYBKI_START_PL.md
- Pierwsze kroki (5 minut)
- Najważniejsze pliki
- Parametry sterowania - ściągawka
- Typowe pozycje dla GO2
- Mapowanie stawów
- Komendy szybkiego dostępu
- Szybkie rozwiązania problemów
- Bezpieczeństwo - 5 złotych zasad
- Konwersje jednostek
- Workflow typowego projektu

### PRZEWODNIK_PL.md
- Podstawowe koncepcje
  - Komunikacja z robotem (DDS)
  - Sterowanie silnikami
  - Regulator PD
  - Interpolacja ruchu
- Struktura typowego programu
- Anatomia robota GO2
- Bezpieczeństwo (szczegółowo)
- Częste problemy i rozwiązania
- Przykładowe wartości pozycji
- Wskazówki dla początkujących

### PRZEWODNIK_PRZYKLADY_PL.md
- Przegląd przykładów
- Przykłady dla GO2
  - go2_stand_example.cpp (szczegółowo)
  - go2_low_level.cpp
  - go2_trajectory_follow.cpp
- Przykłady dla B2
  - b2_stand_example.cpp
  - b2_sport_client.cpp
- Przykłady dla H1 (high/low level)
- Przykłady dla G1 (audio, dex3, agv)
- Wspólne elementy wszystkich przykładów
- Jak modyfikować przykłady
- Debugowanie i testowanie
- Częste pytania
- Kolejne eksperymenty

### ROZNICE_MIEDZY_MODELAMI_PL.md
- Przegląd modeli (tabela)
- GO2 vs B2 - kluczowe różnice
  - Parametry sterowania
  - Tryb silnika
  - Pozycje i sekwencje
  - Bezpieczeństwo
- B2 vs B2W (koła)
- GO2 vs GO2W
- H1 - robot humanoidalny
- G1 - zaawansowany humanoid
- Tabele porównawcze
- Jak wybrać odpowiedni model
- Adaptacja kodu między modelami
- Porady specyficzne dla modelu

### ROZWIAZYWANIE_PROBLEMOW_PL.md
- Problemy z kompilacją (7 przypadków)
- Problemy z uruchomieniem (6 przypadków)
- Problemy z zachowaniem robota (6 przypadków)
- Problemy z danymi (2 przypadki)
- Problemy specyficzne dla modelu (3 przypadki)
- Problemy z uprawnieniami (2 przypadki)
- Narzędzia diagnostyczne
- Kiedy szukać pomocy
- Checklist przed pytaniem o pomoc
- Podsumowanie najczęstszych problemów

### example/h1/README.md
- Interfejs sterowania mechanizmem równoległym
- Budowa mechanizmu kostki
- Tryb PR
- Eksperyment śledzenia pozycji stawów
- Fragmenty kodu z wyjaśnieniami
- Jak uruchomić przykład
- Wyniki śledzenia pozycji
- Interpretacja wykresów
- Podsumowanie dla studenta

## 💡 Wskazówki do Nauki

### Dzień 1: Podstawy
1. Przeczytaj README.md
2. Zainstaluj zależności
3. Skompiluj SDK
4. Uruchom pierwszy przykład (go2_stand_example)

### Dzień 2: Zrozumienie
1. Przeczytaj PRZEWODNIK_PL.md
2. Zrozum koncepcje: DDS, PD, interpolacja
3. Przeanalizuj kod go2_stand_example
4. Zmodyfikuj parametry Kp/Kd i obserwuj efekty

### Dzień 3: Eksperymenty
1. Zmień pozycje docelowe
2. Dodaj własny etap ruchu
3. Eksperymentuj z czasami trwania
4. Zapisuj co działa dobrze

### Tydzień 2: Zaawansowane
1. Poznaj różnice między modelami
2. Spróbuj innych przykładów
3. Zacznij tworzyć własne sekwencje
4. Zaimplementuj prosty algorytm

## 🌐 Dodatkowe Zasoby

- **Centrum Dokumentacji Unitree**: https://support.unitree.com/home/zh/developer
- **GitHub Repository**: https://github.com/unitreerobotics/unitree_sdk2
- **Forum Unitree**: https://support.unitree.com/

## 🤝 Jak Korzystać z Tej Dokumentacji

1. **Czytaj po kolei** - dokumenty są powiązane
2. **Eksperymentuj** - teoria + praktyka = nauka
3. **Zapisuj notatki** - co działa, co nie
4. **Pytaj** - jeśli coś niejasne, sprawdź troubleshooting
5. **Dziel się wiedzą** - pomóż innym studentom

## ✅ Checklist Początkującego

- [ ] Przeczytałem README.md
- [ ] Zainstalowałem wszystkie zależności
- [ ] Skompilowałem SDK bez błędów
- [ ] Uruchomiłem pierwszy przykład
- [ ] Rozumiem podstawowe koncepcje (DDS, PD)
- [ ] Wiem jak zmienić parametry Kp/Kd
- [ ] Wiem jak zmienić pozycje docelowe
- [ ] Znam zasady bezpieczeństwa
- [ ] Wiem gdzie szukać pomocy przy problemach

## 📞 Potrzebujesz Pomocy?

1. Sprawdź [ROZWIAZYWANIE_PROBLEMOW_PL.md](ROZWIAZYWANIE_PROBLEMOW_PL.md)
2. Przeszukaj GitHub Issues
3. Zapytaj na forum Unitree
4. Utwórz nowy Issue (jeśli to bug w SDK)

---

**Powodzenia w nauce programowania robotów Unitree! 🤖🎓**

*Ostatnia aktualizacja: 2026-02-05*
