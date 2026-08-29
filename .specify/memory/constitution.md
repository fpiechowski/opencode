<!--
Sync Impact Report
==================
Version change: (nowa) → 1.0.0
Modified principles: brak (pierwotne przyjęcie)
Added sections: Core Principles, Zakres i granice zmian, SpecKit Workflow, Governance
Removed sections: brak
Deferred TODOs: brak
-->

# OpenCode Custom Constitution

## Core Principles

### I. Desktop-first

Customizacje forka kierują priorytet na aplikację desktopową OpenCode.

- Każda zmiana MUSI być oceniana pod kątem wpływu na desktop (Electron, okna, paski
  tytułowe, przewijanie, skalowanie).
- Zmiany dotykające interfejsu MUSZĄ respektować zachowanie RTL/LTR.
- Web i CLI nie są głównym celem forka; zmiany tam MUSZĄ mieć uzasadnienie.

Racjonalne: fork istnieje głównie po to, by wprowadzać własne customizacje desktopowe.

### II. SpecKit-driven

Wszystkie zmiany funkcjonalne są prowadzone przez SpecKit.

- Każda funkcja MUSI zaczynać się od specyfikacji (`/speckit.specify`), następnie planu
  (`/speckit.plan`), a dopiero potem implementacji.
- Artefakty SpecKit w `.specify/` SĄ katalogiem zmian poczynionych w OpenCode i MUSZĄ
  być aktualizowane przy każdej zmianie funkcjonalnej.
- NIE WOLNO implementować funkcji bez odpowiadającego artefaktu specyfikacji.

Racjonalne: SpecKit zapewnia ślad, plan i spójność wszystkich zmian w forku.

### III. Obserwowalność i logowanie

Zmiany MUSZĄ zachowywać debuggowalność.

- Nowe ścieżki kodu MUSZĄ używać logowania strukturalnego.
- NIE WOLNO usuwać ani degradować istniejących logów bez jawnego uzasadnienia.
- Błędy MUSZĄ być raportowane z wystarczającym kontekstem do diagnozy.

Racjonalne: łatwa diagnoza jest kluczowa w środowisku desktopowym.

### IV. Test-first / jakość

Testy i typy są bramką jakości każdej zmiany.

- Testy MUSZĄ być pisane przed implementacją lub razem z nią (TDD preferowane).
- `bun typecheck` MUSI przechodzić z katalogu pakietu (np. `packages/opencode`),
  nigdy `tsc` wywoływany bezpośrednio.
- Testy MUSZĄ być uruchamiane z katalogu pakietu, nie z roota repozytorium.
- Unikać mocków; testować faktyczną implementację, nie jej kopię.

Racjonalne: zmiany w forku muszą być bezpieczne i weryfikowalne.

### V. Minimalnie inwazyjny fork

Zmiany lokalne MUSZĄ być minimalne i łatwe do scalania z upstream OpenCode.

- NIE WOLNO edytować plików generowanych (`src/generated`, `src/generated-effect`);
  regeneracja przez `bun run generate` z `packages/client`.
- Zachować kierunek zależności runtime: Schema → Core i Protocol, Core i Protocol → Server.
- Trzymać się konwencji repozytorium (np. `dev` jako gałąź domyślna, styl opisany w
  AGENTS.md).

Racjonalne: fork musi dać się aktualizować o zmiany upstream bez nadmiernego wysiłku.

## Zakres i granice zmian

Konstytucja dotyczy forka OpenCode Custom, którego głównym celem są customizacje desktopowe.

- Zmiany wprowadzane są wyłącznie w obrębie forka OpenCode.
- Pliki generowane nie są edytowane ręcznie.
- Gałąź domyślna repozytorium to `dev`.

## SpecKit Workflow

Proces deweloperski każdej zmiany funkcjonalnej:

1. Specyfikacja: `/speckit.specify` — opis funkcji i kryteria akceptacji.
2. Plan: `/speckit.plan` — rozbicie na zadania implementacyjne.
3. Implementacja: realizacja zadań zgodnie z planem.
4. Weryfikacja: `bun typecheck` i testy z katalogu pakietu MUSZĄ przechodzić.

Artefakty SpecKit MUSZĄ być aktualizowane po zakończeniu zmiany, tak aby `.specify/`
pozostawało wiernym katalogiem zmian poczynionych w OpenCode.

## Governance

Konstytucja nadrzędna wobec innych praktyk i konwencji w forku.

- Poprawki: każda zmiana konstytucji MUSI być udokumentowana i zatwierdzona przez
  właściciela forka; przy zmianach zasad wymagany plan migracji.
- Wersjonowanie semver: MAJOR dla usunięć lub redefinicji zasad, MINOR dla nowych zasad
  lub materialnego rozszerzenia, PATCH dla doprecyzowań i poprawek redakcyjnych.
- Zgodność: każda zmiana w kodzie MUSI być sprawdzana pod kątem zgodności z konstytucją.

**Version**: 1.0.0 | **Ratified**: 2026-08-30 | **Last Amended**: 2026-08-30
