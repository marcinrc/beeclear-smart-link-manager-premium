# CLAUDE.md

## Opis projektu

**BeeClear Smart Link Manager** to wtyczka WordPress do automatycznego zarządzania linkami wewnętrznymi i zewnętrznymi. Projekt zawiera dwie wersje:

- **Wersja darmowa** (`beeclear-smart-link-manager/`) – publikowana na WordPress.org
- **Wersja premium** (`beeclear-smart-link-manager-premium/`) – dystrybuowana przez Freemius SDK

Obie wersje są aktualnie w **wersji 2.0**.

## Struktura projektu

```
beeclear-smart-link-manager-premium/         # Repozytorium
├── beeclear-smart-link-manager/             # Wtyczka DARMOWA (v2.0)
│   ├── beeclear-smart-link-manager.php     # Główny plik wtyczki (~5400 linii)
│   ├── docs/akcje-wtyczki.md               # Dokumentacja hooków WordPress
│   ├── LICENSE
│   └── readme.txt                           # Format WordPress.org
├── beeclear-smart-link-manager-premium/    # Wtyczka PREMIUM (v2.0)
│   ├── beeclear-smart-link-manager-premium.php  # Główny plik (~6130 linii)
│   ├── languages/                           # Pliki tłumaczeń
│   ├── readme.txt
│   ├── LICENSE
│   └── vendor/                              # Freemius SDK (v2.x)
├── assets/                                  # Zrzuty ekranu (WordPress.org)
├── .github/workflows/deploy_bc.yml          # Deployment FTP
├── README.md                                # Dokumentacja repozytorium
└── CLAUDE.md                                # Ten plik
```

## Architektura

### Monolityczna struktura klas

Obie wersje używają tej **samej nazwy klasy** `BeeClear_ILM` – kolizja jest obsługiwana przez flagę inicjalizacji:

```php
private static $initialized = false;
```

Premium wykrywa obecność wersji darmowej i przejmuje kontrolę nad hookami. Wersja darmowa ustępuje premium, jeśli wykryje jej obecność.

### Kluczowe stałe (opcje bazy danych WordPress)

| Stała | Opis |
|---|---|
| `OPT_SETTINGS` | Globalne ustawienia wtyczki |
| `OPT_INDEX` | Skompilowany indeks reguł wewnętrznych |
| `OPT_EXT_RULES` | Reguły linków zewnętrznych (tylko premium) |
| `OPT_LINKMAP` | Liczniki linków w czasie rzeczywistym |
| `OPT_ACTIVITY_LOG` | Logi operacji (tylko premium) |
| `META_RULES` | Reguły per-post (metadane) |

### Jak działa linkowanie

Linki **NIE są zapisywane** bezpośrednio w treści postów. Są wstrzykiwane dynamicznie w czasie renderowania strony przez filtry WordPress (`the_content`). Reguły są kompilowane do indeksu, który jest odczytywany przy każdym żądaniu.

### Skanowanie i budowanie indeksu – model wewnętrzny (bez HTTP)

Cały proces skanowania i budowania indeksu odbywa się **wyłącznie po stronie serwera** za pomocą wewnętrznych API WordPressa — **nie generuje zewnętrznych requestów HTTP**, co eliminuje ryzyko blokad przez WAF/firewall.

#### Przepływ danych

1. **Rebuild Index** — wywoływany ręcznie lub automatycznie:
   - `WP_Query` pobiera posty z bazy danych (wewnętrzne zapytania SQL)
   - `get_post_meta()` odczytuje reguły per-post (`META_RULES`)
   - Kompiluje indeks i zapisuje do `OPT_INDEX` przez `update_option()`
   - **0 requestów HTTP** — cała operacja to PHP ↔ MySQL

2. **Autolink (`the_content`)** — dynamiczne wstrzykiwanie linków:
   - Filtr WordPress czyta skompilowany `OPT_INDEX` z pamięci/object cache
   - Podmienia frazy na linki w renderowanym HTML
   - **0 requestów HTTP** — operacja w pamięci PHP

3. **Skan w tle (WP-Cron)** — przetwarzanie dużych ilości postów w paczkach:
   - WordPress `spawn_cron()` generuje **1 loopback request** na `wp-cron.php` per tick
   - Cała logika skanowania wykonuje się wewnętrznie (WP_Query + get_post_meta)
   - **Minimalne ryzyko WAF** — jedyny ruch sieciowy to sporadyczny loopback

4. **Polling statusu z przeglądarki (AJAX)** — monitorowanie postępu skanu:
   - Przeglądarka wysyła **1 lekki AJAX request co ~3 sekundy** (read-only, odczyt statusu)
   - Endpoint tylko odczytuje flagę postępu — brak ciężkiego przetwarzania
   - **Minimalne ryzyko WAF** — niski rate, brak modyfikacji danych

#### Podsumowanie requestów HTTP

| Akcja | Requesty HTTP | Ryzyko WAF |
|---|---|---|
| Rebuild index | 0 (wewnętrzne WP_Query + get_post_meta) | Brak |
| Autolink (the_content) | 0 (czyta OPT_INDEX z pamięci/cache) | Brak |
| Skan w tle (WP-Cron) | 1 loopback na `wp-cron.php` per tick | Minimalny |
| Polling statusu z przeglądarki | 1 AJAX co ~3s (read-only) | Minimalny |

> **Dlaczego to ważne:** Wcześniejsze podejście (przeglądarka wysyłała dziesiątki AJAX requestów na sekundę, każdy z przetwarzaniem) mogło być interpretowane przez WAF jako atak brute-force/DDoS. Obecny model przenosi całe przetwarzanie na stronę serwera (WP-Cron + batch processing), a przeglądarka jedynie odpytuje o status.

## Wymagania techniczne

- **PHP:** >= 7.4
- **WordPress:** >= 5.8
- **Przetestowane z:** WordPress 6.9 (free: 6.9.4, premium: 6.9)
- **Licencja:** GPLv2+

## Konwencje kodowania

Projekt przestrzega **WordPress Coding Standards (WPCS)**. Sprawdzanie kodu odbywa się przez narzędzia z `vendor/composer.json`:

```bash
composer phpcs         # WordPress CodeSniffer
composer phpcs:ci      # PHP Compatibility check
composer phpstan       # Statyczna analiza kodu
```

> Uwaga: `composer.json` jest częścią Freemius SDK (`vendor/`), nie korzenia projektu.

## Bezpieczeństwo

Przy każdej modyfikacji kodu należy pamiętać o:

- **Sanityzacja danych wejściowych:** `sanitize_text_field()`, `absint()`, `wp_kses_post()`, itp.
- **Escapowanie wyjścia:** `esc_html()`, `esc_url()`, `esc_attr()` – zawsze przed wyświetleniem
- **Nonce verification:** dla wszystkich formularzy i akcji AJAX (`wp_verify_nonce()`)
- **Capability checks:** `current_user_can('manage_options')` przed każdą akcją administracyjną
- **Przygotowane zapytania SQL:** `$wpdb->prepare()` jeśli konieczne zapytania bezpośrednie

## CI/CD i deployment

Wdrożenie odbywa się przez GitHub Actions (`.github/workflows/deploy_bc.yml`):

- **Trigger:** push do gałęzi `main` lub `bc`
- **Mechanizm:** FTP upload do 3 środowisk (produkcja, demo, wersja angielska)
- **Automatyczne wykrywanie:** tylko zmieniony plugin jest deployowany
- **Wykluczenia z deploymentu:** pliki `.md`, katalog `.git`, `node_modules`, `vendor/` (premium)

Wymagane sekrety GitHub Actions: `FTP_HOST`, `FTP_USERNAME`, `FTP_PASSWORD`

## Praca z kodem

### Dodawanie funkcjonalności

1. Edytuj odpowiedni plik główny (darmowy lub premium) – to monolity, cały kod jest w jednym pliku
2. Hookuj funkcje do istniejących akcji/filtrów WordPress lub dodaj nowe
3. Opcje konfiguracyjne przechowuj w WordPress options (`get_option()` / `update_option()`)
4. Dla ustawień per-post używaj metadanych (`get_post_meta()` / `update_post_meta()`)

### Testowanie manualne

Brak zautomatyzowanych testów jednostkowych w tym projekcie. Testowanie odbywa się manualnie na środowisku WordPress. Weryfikuj:

- Poprawność linkowania w `the_content`
- Działanie panelu administracyjnego
- Import/eksport reguł
- Kompatybilność z free/premium jednocześnie aktywowanymi

### Dodawanie hooków

Dokumentacja dostępnych akcji i filtrów wtyczki: `beeclear-smart-link-manager/docs/akcje-wtyczki.md`

## Ważne zasady pracy

- **Nie zmieniaj logiki inicjalizacji klasy** – flaga `$initialized` jest kluczowa dla koegzystencji wersji
- **Synchronizuj zmiany bugfixów** między wersją darmową i premium, gdy dotyczą wspólnej logiki
- **Zachowaj kompatybilność wsteczną** z PHP 7.4 – unikaj składni PHP 8.x bez sprawdzenia
- **Unikaj bezpośrednich zapytań do bazy danych** – korzystaj z WordPress API (options, meta, WP_Query)
- **Text domain:** `beeclear-smart-link-manager` (free) / `beeclear-smart-link-manager-premium` (premium)
