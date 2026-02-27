# BeeClear Smart Link Manager

**Automatyczne linkowanie wewnętrzne i zewnętrzne dla WordPress**

> Wtyczka WordPress automatycznie przekształca zdefiniowane frazy i wzorce w linki — poprawiając strukturę linkowania wewnętrznego, SEO i GEO na Twojej stronie.

---

## Zawartość repozytorium

```
beeclear-smart-link-manager-premium/
├── beeclear-smart-link-manager/          # Wersja FREE (WordPress.org)
│   ├── beeclear-smart-link-manager.php   # Główny plik wtyczki
│   ├── docs/
│   │   └── akcje-wtyczki.md              # Dokumentacja akcji wtyczki (PL)
│   ├── README.md
│   ├── readme.txt                         # Opis dla WordPress.org
│   └── LICENSE
├── beeclear-smart-link-manager-premium/  # Wersja PREMIUM
│   ├── beeclear-smart-link-manager-premium.php
│   ├── vendor/                            # Freemius SDK
│   ├── languages/                         # Pliki tłumaczeń
│   ├── readme.txt
│   └── LICENSE
└── .github/
    └── workflows/
        └── deploy_bc.yml                  # Automatyczny deployment FTP
```

---

## Wersje wtyczki

### Wersja FREE — `beeclear-smart-link-manager`

| Atrybut           | Wartość                       |
|-------------------|-------------------------------|
| Wersja            | 1.8                           |
| Licencja          | GPLv2 or later                |
| Wymaga WordPress  | 5.8+                          |
| Wymaga PHP        | 7.4+                          |
| Testowane do      | WordPress 6.9                 |
| Dostępna na       | WordPress.org                 |

#### Funkcje wersji FREE

- **Linkowanie wewnętrzne** oparte na regułach frazowych (token syntax) z obsługą wielkości liter
- **Atrybuty SEO**: `rel`, `title`, `aria-label`, `class` — pełna kontrola nad dostępnością i SEO
- **Automatyczne przebudowywanie indeksu** po aktualizacji wpisów + ręczna przebudowa jednym kliknięciem z podsumowaniem
- **Import/export reguł** — przenoszenie reguł między środowiskami bez ręcznego odtwarzania
- **Czytelny interfejs** administracyjny z sortowalnymi tabelami reguł
- **Wykluczanie wpisów** — możliwość wyłączenia linkowania dla wybranych typów postów lub pojedynczych stron
- **Limity linków na stronę** — kontrola liczby automatycznych linków na podstronie
- Działa dynamicznie — **nie modyfikuje treści wpisów** w bazie danych
- Kompatybilna z **edytorem Gutenberg** i **klasycznym edytorem**

---

### Wersja PREMIUM — `beeclear-smart-link-manager-premium`

| Atrybut           | Wartość                       |
|-------------------|-------------------------------|
| Wersja            | 1.7.5                         |
| Licencja          | GPLv2 or later                |
| Wymaga WordPress  | 5.8+                          |
| Wymaga PHP        | 7.4+                          |
| Testowane do      | WordPress 6.9                 |
| Monetyzacja       | Freemius (ID: 23044)          |
| Trial             | 3 dni (wymaga karty)          |

#### Dodatkowe funkcje wersji PREMIUM

Wszystkie funkcje FREE, a dodatkowo:

- **Semantic enhancement** — semantyczne wzbogacanie fraz i reguł
- **Zewnętrzne reguły linkowania** — zaawansowane zarządzanie linkami do zewnętrznych URL
- **Rozszerzone reguły linkowania** — obsługa wyrażeń regularnych (regex) z opcją czułości na wielkość liter
- **Priorytety stron** — ustawianie priorytetów dla konkurujących celów linkowania
- **Historia logów** — pełen podgląd aktywności i historii operacji
- **Zarządzanie licencją** przez platformę Freemius

---

## Instalacja

### Wersja FREE — z WordPress.org

1. W panelu WordPress przejdź do **Wtyczki > Dodaj nową**.
2. Wyszukaj **BeeClear Smart Link Manager**.
3. Kliknij **Zainstaluj** i **Aktywuj**.

### Wersja FREE — ręcznie

1. Wgraj folder `beeclear-smart-link-manager` do katalogu `/wp-content/plugins/`.
2. Przejdź do **Wtyczki** w panelu WordPress.
3. Aktywuj **BeeClear Smart Link Manager**.

### Wersja PREMIUM — ręcznie

1. Wgraj folder `beeclear-smart-link-manager-premium` do katalogu `/wp-content/plugins/`.
2. Przejdź do **Wtyczki** w panelu WordPress.
3. Aktywuj **Internal & External Link Manager – Premium (BeeClear)**.
4. Skonfiguruj ustawienia w panelu **Internal & External Link Manager (BeeClear)**.
5. Dodaj lub zaimportuj reguły linkowania wewnętrznego i zewnętrznego.

---

## Konfiguracja

Po aktywacji wtyczki przejdź do menu **Internal & External Link Manager (BeeClear)** w panelu administracyjnym WordPress.

### Główne sekcje konfiguracji

| Sekcja                  | Opis                                                           |
|-------------------------|----------------------------------------------------------------|
| **Ustawienia globalne** | Typy postów, limity linków, automatyczne przebudowywanie      |
| **Reguły wewnętrzne**   | Definiowanie fraz linkowanych do wewnętrznych wpisów/stron    |
| **Reguły zewnętrzne**   | Definiowanie fraz linkowanych do zewnętrznych URL *(Premium)* |
| **Przebuduj indeks**    | Ręczna przebudowa z podglądem wyników                         |
| **Import/Export**       | Przenoszenie reguł między środowiskami                        |
| **Historia logów**      | Podgląd aktywności *(Premium)*                                |

### Metabox na poziomie wpisu

Każdy wpis i strona posiadają metabox umożliwiający:
- Definiowanie reguł linkowania specyficznych dla danej strony
- Wyłączanie automatycznego linkowania dla konkretnego wpisu
- Ustawianie priorytetów i limitów linków

---

## CI/CD — Automatyczny Deployment

Repozytorium skonfigurowane jest z automatycznym deploymentem przez **GitHub Actions** (`deploy_bc.yml`).

### Zasada działania

Workflow uruchamia się przy każdym pushu do gałęzi `main` lub `bc`:

1. **Wykrywanie zmian** — sprawdza, czy zmieniła się wersja FREE, PREMIUM lub obie
2. **Deploy FREE** — wgrywa przez FTP tylko jeśli zmieniły się pliki FREE
3. **Deploy PREMIUM** — wgrywa przez FTP tylko jeśli zmieniły się pliki PREMIUM

### Środowiska docelowe

Deployment trafia jednocześnie do trzech katalogów na serwerze:

| Środowisko     | Ścieżka                                            |
|----------------|----------------------------------------------------|
| Produkcja      | `/public_html/wp-content/plugins/`                 |
| Demo           | `/public_html/demo/wp-content/plugins/`            |
| Wersja EN      | `/public_html/en/wp-content/plugins/`              |

### Wymagane sekrety GitHub

| Sekret          | Opis                     |
|-----------------|--------------------------|
| `FTP_HOST`      | Adres serwera FTP        |
| `FTP_USERNAME`  | Nazwa użytkownika FTP    |
| `FTP_PASSWORD`  | Hasło FTP                |

---

## FAQ

**Czy wtyczka modyfikuje treść wpisów w bazie danych?**
Nie. Linki są zarządzane dynamicznie — treść wpisów w bazie pozostaje niezmieniona.

**Czy wtyczka działa z najnowszym WordPress?**
Tak. Wtyczka jest testowana z WordPress do wersji 6.9.

**Jak przebudować indeks po zmianie reguł?**
Przejdź do ustawień wtyczki i użyj przycisku ręcznej przebudowy, lub włącz automatyczne przebudowywanie po aktualizacji wpisów.

**Dlaczego niektóre frazy nie są linkowane?**
Sprawdź priorytety reguł, limity linków na stronę oraz ustawienia czułości na wielkość liter i dopasowania fraz.

**Czy mogę wykluczyć konkretne wpisy lub typy postów?**
Tak. W ustawieniach wtyczki możesz wyłączyć linkowanie dla wybranych typów postów lub pojedynczych wpisów.

**Jak przenieść reguły między witrynami?**
Użyj narzędzi import/export, aby przenosić reguły między środowiskami bez ręcznego odtwarzania.

---

## Dokumentacja techniczna

Szczegółowa dokumentacja akcji wtyczki dostępna jest w:
[`beeclear-smart-link-manager/docs/akcje-wtyczki.md`](beeclear-smart-link-manager/docs/akcje-wtyczki.md)

---

## Wsparcie i kontakt

- Strona: [https://beeclear.pl](https://beeclear.pl)
- E-mail: info@beeclear.pl
- Strona wtyczki: [https://beeclear.pl/en/internal-external-link-manager/](https://beeclear.pl/en/internal-external-link-manager/)

---

## Licencja

Obie wersje wtyczki są udostępnione na licencji **GNU General Public License v2.0 or later**.
Freemius SDK zawarty w wersji Premium jest objęty licencją **GPL-3.0**.

Szczegóły licencji:
- [GPLv2](https://www.gnu.org/licenses/gpl-2.0.html)
- [GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html)

---

© BeeClear
