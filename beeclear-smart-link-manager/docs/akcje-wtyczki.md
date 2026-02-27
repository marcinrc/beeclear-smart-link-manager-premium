# Czynności i ich wpływ na linkowanie

Poniższe punkty opisują, co dzieje się we wtyczce Internal & External Link Manager (BeeClear) w typowych scenariuszach administracyjnych i jak wpływa to na widoczność linków.

## 1. Zapisanie lub poprawa fraz w treści wpisu/podstrony
- Formularz meta w edycji wpisu zapisuje flagę wyłączenia linkowania, listę dozwolonych tagów, priorytety i limity dla danego wpisu, a także same frazy/regex wraz z kontekstem. Po każdej zmianie aktualizowana jest pamięć meta wpisu i **uruchamiana jest przebudowa indeksu** (`OPT_INDEX`).
- Jeżeli wpis jest publikowany (status `publish`) i jego typ jest obsługiwany, to po zapisaniu następuje dodatkowe, pełne przebudowanie indeksu oraz – gdy włączono `auto_scan_on_save` – startuje skan „overview”, który odświeża liczniki źródeł linków we wszystkich wpisach.
- Efekt: nowe lub zmienione frazy zaczynają być używane przy renderowaniu treści (autolinkowanie), a limity/priorytety są respektowane w kolejnych dopasowaniach.

## 2. Definiowanie nowych fraz do linkowania zewnętrznego
- Zapis w zakładce **External linking** filtruje i czyści wprowadzone reguły (fraza/regex, URL, rel/title/aria/class, limity na stronę, kontekst, dozwolone tagi, typy postów, wykluczenia) i zapisuje je w opcji `OPT_EXT_RULES`.
- Jeśli w globalnych ustawieniach włączono `auto_scan_on_external`, zapis reguł automatycznie uruchamia skan „overview” (zeruje mapy `OPT_LINKMAP` i `OPT_EXTERNAL_MAP`, buduje kolejkę do przeliczenia źródeł, loguje start).
- Efekt: nowe reguły zewnętrzne są brane pod uwagę przy autolinkowaniu treści, a skan – o ile uruchomiony – odświeża liczniki źródeł i podgląd w panelu overview.

## 3. Zmiany w **Global settings**
- Ustawienia są walidowane i zapisywane przez rejestrację opcji `OPT_SETTINGS` (m.in. atrybut `rel`, szablon linku, limity per strona/target, dozwolone typy postów, listy pomijanych tagów, logowanie czasu, auto-skany, preferencje czyszczenia).
- Formularz akcji na tej stronie oferuje natychmiastowe „Rebuild index now” oraz ręczne uruchomienie skanu „Scan site to refresh overview”.
- Efekt: zmienione ustawienia wpływają na sposób generowania linków (np. wygląd, miejsce wstrzyknięcia, limity) i na to, kiedy automatycznie wyzwalany jest skan lub czyszczenie danych.

## 4. „Rebuild index now”
- Przyciski na stronie ustawień lub Overview czyścą ewentualne błędy i **przeliczają od zera indeks reguł** na podstawie opublikowanych wpisów obsługiwanych typów. Wynik trafia do opcji `OPT_INDEX`, a w dzienniku aktywności zapisywana jest informacja o przebudowie.
- Efekt: natychmiastowe uwzględnienie bieżących reguł (wewnętrznych) w autolinkowaniu i w podsumowaniach.

## 5. „Scan site to refresh overview”
- Akcja AJAX tworzy kolejkę wszystkich publicznych wpisów/stron obsługiwanych typów, zeruje mapy `OPT_LINKMAP` i `OPT_EXTERNAL_MAP`, zapisuje stan (`OPT_OVERVIEW_SCAN`) i loguje start. Kolejne wywołania krokowe przetwarzają treść w małych porcjach, zbierając liczbę wygenerowanych linków (wewnętrznych i zewnętrznych) oraz konteksty.
- Po zakończeniu skanu stan jest usuwany, zapisywane jest podsumowanie (`OPT_OVERVIEW_SCAN_SUMMARY`) i wpis do logu. Widżet „Last scan summary” oraz tabele overview pokazują zaktualizowane liczniki.
- Efekt: podgląd liczby linków i ich źródeł odpowiada aktualnej treści oraz regułom; nie zmienia to samego indeksu fraz.

## 6. „Clear data & rebuild index”
- Żądanie w panelu ustawień/overview czyści bieżące dane runtime (`OPT_INDEX`, `OPT_LINKMAP`, `OPT_EXTERNAL_MAP`, stany/summary skanu), a następnie natychmiast przebudowuje indeks fraz.
- Efekt: reset liczników i wyników skanów; autolinkowanie po czyszczeniu korzysta z nowo zbudowanego indeksu.

## 7. „Purge database (ILM) & rebuild index”
- Rozszerzone czyszczenie usuwa mapy i indeksy runtime, **kasuje zapisane reguły zewnętrzne oraz metadane reguł wewnętrznych na wszystkich publicznych wpisach**. Po operacji uruchamiana jest przebudowa indeksu (z pustych danych) i logowany jest wpis.
- Efekt: pełne wyzerowanie konfiguracji linkowania; autolinkowanie zostaje de facto wyłączone, dopóki użytkownik nie zdefiniuje nowych fraz.
