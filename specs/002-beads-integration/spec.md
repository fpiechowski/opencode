# Feature Specification: Integracja OpenCode z Beads

**Feature Branch**: `002-beads-integration`

**Created**: 2026-08-30

**Status**: Draft

**Input**: User description: "Chcę zintegrować OpenCode z beads (https://github.com/gastownhall/beads). W ramach integracji chcę mieć w OpenCode UI, który umożliwi mi zarządzanie beads, w tym podawanie sesjom beada, nad którym mają pracować. Integracja ma umożliwić pracowanie z beadami bezpośrednio w OpenCode. Kiedy mam zamiar otworzyć nową sesję, chcę mieć możliwość wyboru beada. W otwartej sesji z powiązanym beadem chcę mieć możliwość przeglądania jego danych, aktualizowania ich przez UI, a nie tylko przez MCP w samej sesji. Wyobrażam to sobie jako 'floating panel' jak ma Codex (screenshot): Codex takich paneli używa do wyświetlania akcji takich jak Commit, Push itp., a my chcemy w takim panelu w konwersacji wyświetlać dane beada."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Wybór beada przy tworzeniu sesji (Priority: P1)

Użytkownik rozpoczyna tworzenie nowej sesji w projekcie, który ma zainicjalizowane beads. W przepływie tworzenia sesji widzi opcjonalny wybór beada: wyszukiwarkę po tytule/ID z listą beadów gotowych do pracy (bez otwartych blokerów) wyeksponowaną na górze. Wybiera beada i tworzy sesję — sesja od startu jest powiązana z wybranym elementem pracy. Użytkownik może też świadomie pominąć wybór i utworzyć sesję bez beada.

**Why this priority**: To jest serce feature'u — powiązanie pracy agenta z śledzonym elementem pracy w momencie startu sesji. Bez tego pozostałe historie nie mają sensu.

**Independent Test**: Można w pełni przetestować tworząc sesję z wybranym beadem i weryfikując, że sesja jest z nim powiązana po utworzeniu. Dostarcza wartość nawet bez panelu (powiązanie samo w sobie organizuje pracę).

**Acceptance Scenarios**:

1. **Given** projekt z zainicjalizowanymi beads zawierającymi co najmniej jednego otwartego beada, **When** użytkownik otwiera przepływ tworzenia nowej sesji, **Then** widzi opcjonalny wybór beada z możliwością wyszukiwania po tytule i ID.
2. **Given** otwarty wybór beada, **When** użytkownik wpisuje frazę pasującą do tytułu beada, **Then** lista zawęża się do pasujących beadów, a beady bez otwartych blokerów są oznaczone jako gotowe do pracy.
3. **Given** wybranego beada w przepływie tworzenia sesji, **When** użytkownik zatwierdza utworzenie sesji, **Then** nowa sesja jest trwale powiązana z tym beadem.
4. **Given** wybranego beada i tryb „powiąż + rozpocznij pracę" (domyślny), **When** użytkownik zatwierdza utworzenie sesji, **Then** bead zostaje oznaczony jako w pracy (in_progress + przypisanie), a agent w sesji otrzymuje dane beada jako kontekst pracy.
5. **Given** wybranego beada i tryb „tylko powiąż", **When** użytkownik zatwierdza utworzenie sesji, **Then** sesja jest powiązana, ale stan beada w beads pozostaje niezmieniony i agent nie otrzymuje automatycznie kontekstu beada.
6. **Given** przepływ tworzenia sesji, **When** użytkownik pomija wybór beada, **Then** sesja tworzy się bez powiązania i cały proces działa bez zakłóceń.

---

### User Story 2 - Pływający panel beada w konwersacji (Priority: P1)

Użytkownik otwiera sesję powiązaną z beadem. W widoku konwersacji widzi zwijalny pływający panel (na wzór paneli akcji w Codex), który pokazuje kluczowe dane beada w skrócie: ID, tytuł, status, priorytet, typ, przypisanie. Po rozwinięciu panel pokazuje pełne dane: opis, etykiety, zależności (blokuje / zablokowany przez / relacje rodzic-dziecko) oraz historię aktywności. Panel nie zasłania konwersacji i nie wymaga opuszczania widoku sesji.

**Why this priority**: Widoczność kontekstu pracy w trakcie konwersacji z agentem — użytkownik zawsze wie, nad czym pracuje sesja, bez przełączania do terminala czy innego widoku.

**Independent Test**: Można przetestować otwierając sesję z powiązanym beadem i sprawdzając, że panel pokazuje aktualne dane beada, zwija się i rozwija. Dostarcza wartość samodzielnie (read-only podgląd).

**Acceptance Scenarios**:

1. **Given** otwarta sesja powiązana z beadem, **When** użytkownik przegląda widok konwersacji, **Then** widzi pływający panel z kluczowymi danymi beada (ID, tytuł, status, priorytet).
2. **Given** widoczny panel beada, **When** użytkownik rozwija panel, **Then** widzi pełny opis, etykiety, zależności i historię aktywności beada.
3. **Given** widoczny panel beada, **When** użytkownik zwija panel, **Then** panel redukuje się do kompaktowej formy i nie zasłania konwersacji.
4. **Given** otwarta sesja bez powiązanego beada, **When** użytkownik przegląda konwersację, **Then** panel beada nie jest wyświetlany.

---

### User Story 3 - Edycja danych beada z poziomu UI (Priority: P1)

Użytkownik w trakcie sesji chce zaktualizować beada — np. zmienić status, priorytet, doprecyzować opis albo dodać etykietę. Robi to bezpośrednio w panelu beada (lub w przeglądarce beadów), bez odwoływania się do narzędzi agenta w sesji (MCP) i bez terminala. Zmiana zapisuje się w źródle prawdy beads i jest natychmiast widoczna w panelu oraz dla agenta.

**Why this priority**: Użytkownik jawnie wymaga aktualizacji przez UI, „a nie tylko przez MCP w samej sesji". To odróżnia ten feature od istniejącej integracji agentowej.

**Independent Test**: Można przetestować zmieniając pole beada w panelu i weryfikując zmianę w źródle danych beads (np. niezależnym odczytem). Dostarcza wartość nawet bez picker'a przy tworzeniu sesji.

**Acceptance Scenarios**:

1. **Given** sesja z powiązanym beadem i widoczny panel, **When** użytkownik zmienia status beada w panelu, **Then** zmiana zapisuje się trwale w beads, a panel pokazuje nowy status.
2. **Given** panel beada, **When** użytkownik edytuje opis beada i zapisuje, **Then** nowy opis jest trwale zapisany i możliwy do odczytania niezależnie od OpenCode.
3. **Given** panel beada i brak aktywnego przebiegu agenta, **When** użytkownik edytuje dowolne dostępne pole, **Then** edycja działa niezależnie od stanu agenta (bez wymogu aktywnej sesji MCP).
4. **Given** przeglądarka beadów, **When** użytkownik tworzy nowego beada z tytułem, typem i priorytetem, **Then** nowy bead pojawia się na liście i jest trwale zapisany w źródle danych beads.
5. **Given** otwarte szczegóły beada, **When** użytkownik dodaje zależność „blokuje" wskazując innego beada, **Then** zależność jest trwale zapisana i widoczna u obu beadów; próba usunięcia beada wymaga potwierdzenia i informuje o powiązanych sesjach oraz zależnościach.

---

### User Story 4 - Przeglądarka beadów projektu (Priority: P2)

Użytkownik otwiera dedykowany widok listy beadów bieżącego projektu: tabela/lista z ID, tytułem, statusem, priorytetem, typem i przypisaniem. Może filtrować po statusie, wyszukiwać po tytule/ID, sortować po priorytecie i od razu widzi, które beady są gotowe do pracy. Z listy otwiera pełne szczegóły beada.

**Why this priority**: Zarządzanie wymaga przeglądu całości pracy, nie tylko pojedynczego beada w sesji. P2, bo sesje można wiązać z beadami już przez picker (US1), a przeglądarka buduje na tych samych danych.

**Independent Test**: Można przetestować otwierając przeglądarkę w projekcie z beadami, filtrując, wyszukując i otwierając szczegóły — bez tworzenia żadnej sesji.

**Acceptance Scenarios**:

1. **Given** projekt z beadami, **When** użytkownik otwiera przeglądarkę beadów, **Then** widzi listę z kluczowymi polami każdego beada.
2. **Given** otwarta przeglądarka, **When** użytkownik filtruje po statusie „otwarty", **Then** lista pokazuje wyłącznie otwarte beady.
3. **Given** otwarta przeglądarka, **When** użytkownik otwiera szczegóły beada, **Then** widzi pełne dane wraz z zależnościami i historią.

---

### User Story 5 - Zmiana lub odpięcie beada w istniejącej sesji (Priority: P2)

Użytkownik w trakcie pracy orientuje się, że sesja powinna pracować nad innym beadem — albo że powiązanie już nie ma sensu. Z poziomu panelu lub ustawień sesji odpina obecnego beada, wybiera innego albo zostawia sesję bez powiązania.

**Why this priority**: Plany się zmieniają; bez tego błędne powiązanie wymuszałoby tworzenie nowej sesji. P2, bo obejściem jest utworzenie nowej sesji z właściwym beadem.

**Independent Test**: Można przetestować odpinając i przepinając beada w istniejącej sesji i weryfikując, że panel natychmiast odzwierciedla zmianę.

**Acceptance Scenarios**:

1. **Given** sesja z powiązanym beadem, **When** użytkownik odpina beada, **Then** panel znika, sesja pozostaje bez powiązania, a stan beada w beads (w tym ewentualny claim) pozostaje niezmieniony.
2. **Given** sesja z powiązanym beadem, **When** użytkownik wybiera innego beada z trybem „rozpocznij pracę", **Then** panel pokazuje dane nowego beada, nowy bead zostaje claimnięty, a poprzedni pozostaje w dotychczasowym stanie.

---

### User Story 6 - Łagodna degradacja bez beads (Priority: P3)

Użytkownik pracuje w projekcie bez zainicjalizowanych beads albo źródło danych beads jest chwilowo niedostępne. Wszystkie powierzchnie UI związane z beadami pokazują czytelny pusty stan z wskazówką (np. jak zainicjalizować beads) zamiast błędów lub uszkodzonego UI. Tworzenie i prowadzenie sesji działa normalnie.

**Why this priority**: OpenCode musi pozostać w pełni użyteczny bez beads; integracja jest opcjonalna. P3, bo dotyczy stanu brzegowego, ale krytycznego dla zaufania do feature'u.

**Independent Test**: Można przetestować otwierając projekt bez beads i weryfikując puste stany oraz niezakłócone tworzenie sesji.

**Acceptance Scenarios**:

1. **Given** projekt bez zainicjalizowanych beads, **When** użytkownik otwiera przeglądarkę beadów lub tworzenie sesji, **Then** widzi pusty stan z jasną wskazówką, a przepływ sesji działa normalnie.
2. **Given** sesja z powiązanym beadem i chwilowo niedostępne źródło danych beads, **When** użytkownik otwiera sesję, **Then** panel pokazuje ostatnie znane dane z oznaczeniem nieaktualności, a edycja jest zablokowana z czytelnym komunikatem.

---

### Edge Cases

- Projekt bez zainicjalizowanych beads → puste stany z instrukcją inicjalizacji; przepływy sesji nietknięte.
- Źródło danych beads niedostępne w trakcie sesji (baza zajęta, błąd odczytu) → panel pokazuje ostatnie znane dane z wskaźnikiem nieaktualności; edycje odrzucane z czytelnym błędem.
- Powiązany bead zamknięty lub usunięty zewnętrznie (terminal, inna sesja) → panel pokazuje stan zamknięty albo „bead nie istnieje" z opcją odpięcia.
- Współbieżna edycja: agent aktualizuje beada przez narzędzia w sesji, gdy użytkownik edytuje go w UI → konflikt jest sygnalizowany użytkownikowi z możliwością przeładowania najnowszych danych; brak cichego nadpisywania.
- Bead już claimnięty (in_progress) w momencie wiązania w trybie „rozpocznij pracę" → powiązanie powoduje się, a UI sygnalizuje istniejący stan zamiast wykonywać drugiego claima.
- Usuwanie beada powiązanego z sesjami lub mającego zależności → wymaga potwierdzenia z jasną informacją o konsekwencjach; powiązane sesje tracą powiązanie, a ich panele pokazują „bead nie istnieje" z opcją odpięcia.
- Odpięcie lub przepięcie beada w trakcie aktywnego przebiegu agenta → powiązanie zmienia się natychmiast; agent odczytuje aktualne powiązanie najpóźniej przy kolejnym bezpiecznym punkcie przerwania pracy.
- Ten sam bead powiązany z wieloma sesjami → dozwolone; powiązanie jest właściwością sesji.
- Bardzo duża liczba beadów (1000+) → wyszukiwanie i lista pozostają responsywne.
- Zmiany wykonane poza OpenCode (terminal, inne narzędzia) → UI odzwierciedla je po odświeżeniu lub aktualizacji na żywo.
- Układ RTL → panel i przeglądarka respektują kierunek tekstu zgodnie z konstytucją forka.
- Przełączenie projektu → powierzchnie beads pokazują wyłącznie beady bieżącego projektu.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST umożliwiać przeglądanie listy beadów bieżącego projektu w dedykowanej przeglądarce, pokazując dla każdego beada: ID, tytuł, status, priorytet, typ i przypisanie.
- **FR-002**: Przeglądarka MUST wspierać wyszukiwanie tekstowe po tytule i ID, filtrowanie po statusie, sortowanie po priorytecie oraz oznaczanie beadów gotowych do pracy (bez otwartych blokerów).
- **FR-003**: System MUST umożliwiać otwarcie pełnych szczegółów beada: opis, etykiety, zależności (blokuje / zablokowany przez / rodzic-dziecko) oraz historia aktywności.
- **FR-004**: Przepływ tworzenia nowej sesji MUST oferować opcjonalny wybór beada przez przeszukiwalny picker z beada gotowymi do pracy wyeksponowanymi na górze; użytkownik może pominąć wybór bez konsekwencji.
- **FR-005**: Powiązanie sesji z beadem MUST być trwałe i przetrwać restart aplikacji.
- **FR-006**: System MUST umożliwiać odpięcie beada oraz przepięcie sesji na innego beada w dowolnym momencie życia sesji.
- **FR-007**: Sesja z powiązanym beadem MUST prezentować zwijalny pływający panel w widoku konwersacji (na wzór paneli akcji Codex), który nie blokuje pracy z konwersacją.
- **FR-008**: Panel MUST w formie zwiniętej pokazywać kluczowe dane beada (ID, tytuł, status, priorytet), a po rozwinięciu pełne dane: opis, etykiety, przypisanie, zależności i historię aktywności.
- **FR-009**: System MUST umożliwiać z UI pełny cykl życia beada: tworzenie nowych beadów (z tytułem, opisem, typem, priorytetem i etykietami), edycję pól istniejących (co najmniej status, priorytet, tytuł, opis, etykiety, przypisanie), zamykanie, usuwanie oraz zarządzanie zależnościami (blokuje / zablokowany przez / rodzic-dziecko) — wszystko z trwałym zapisem w źródle danych beads.
- **FR-010**: Wszystkie operacje UI na beadach MUST działać niezależnie od narzędzi agenta w sesji (MCP) i nie wymagać aktywnego przebiegu agenta.
- **FR-011**: UI MUST odzwierciedlać zewnętrzne zmiany w beads (terminal, narzędzia agenta, inne sesje) po odświeżeniu lub na żywo oraz sygnalizować nieaktualność danych przy niedostępności źródła.
- **FR-012**: Dla projektu bez zainicjalizowanych beads wszystkie powierzchnie beads MUST pokazywać pusty stan z wskazówką inicjalizacji; podstawowe przepływy sesji MUST działać bez zakłóceń.
- **FR-013**: Konflikty współbieżnej edycji MUST być sygnalizowane użytkownikowi z opcją przeładowania najnowszych danych; system nie wolno cicho nadpisywać cudzych zmian.
- **FR-014**: Wszystkie nowe powierzchnie UI MUST respektować układ RTL/LTR zgodnie z konstytucją forka.
- **FR-015**: Panel MUST pokazywać beada powiązanego z aktualnie przeglądaną sesją, w zakresie wyłącznie bieżącego projektu.
- **FR-016**: Przy wiązaniu beada z sesją użytkownik MUST mieć wybór jednego z dwóch trybów: (a) „tylko powiąż" — powiązanie jest wyłącznie metadaną sesji widoczną w UI, bez zmian w beads i bez kontekstu dla agenta; (b) „powiąż + rozpocznij pracę" — bead jest oznaczany jako w pracy (claim: status in_progress + przypisanie), a dane beada są dostarczane agentowi w sesji jako kontekst pracy. Tryb domyślny to „powiąż + rozpocznij pracę"; wybór użytkownika jest zapamiętywany jako preferencja.
- **FR-017**: Odpięcie beada MUST NIE cofać automatycznie stanu w beads (claim/status pozostają bez zmian); użytkownik może zmienić status ręcznie z UI. Przepięcie na innego beada w trybie „rozpocznij pracę" claimuje nowego beada, ale nie zwalnia automatycznie poprzedniego.

### Key Entities *(include if feature involves data)*

- **Bead**: element pracy w beads — ID (np. bd-a1b2, z hierarchią bd-a3f8.1 dla podzadań), tytuł, opis, status (open / in_progress / closed), priorytet (P0–P4), typ, etykiety, przypisanie, zależności (blokuje / zablokowany przez / rodzic-dziecko / powiązany), znaczniki czasu, historia aktywności. Źródłem prawdy jest instalacja beads w projekcie; OpenCode nie przechowuje kopii danych beada.
- **Session–Bead Link**: trwałe powiązanie sesji OpenCode z jednym beadem (ID sesji, ID beada, tryb powiązania: „tylko powiąż" / „rozpocznij pracę", znacznik czasu powiązania); zmienialne w czasie życia sesji; jedna sesja ma co najwyżej jednego aktywnego beada, ten sam bead może być powiązany z wieloma sesjami.
- **Project Beads Context**: stan dostępności beads w bieżącym projekcie (zainicjalizowane / niezainicjalizowane / chwilowo niedostępne), sterujący pustymi stanami UI.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Użytkownik wiąże beada z nową sesją w maksymalnie 3 interakcje i 15 sekund od rozpoczęcia przepływu tworzenia sesji.
- **SC-002**: Użytkownik przegląda pełne dane powiązanego beada bez opuszczania widoku konwersacji (0 przejść do innego widoku lub narzędzia).
- **SC-003**: 100% edycji wykonanych z UI jest trwale zapisanych w źródle danych beads w ciągu 5 sekund i możliwych do zweryfikowania niezależnie od OpenCode.
- **SC-004**: Lista i wyszukiwarka beadów pozostają responsywne (< 2 s na operację) dla projektu z 1000 beadów.
- **SC-005**: Użytkownik kończy pełny przepływ „wybierz beada → pracuj w sesji → zaktualizuj status beada" w całości z UI, bez terminala i bez narzędzi agenta (100% ukończenia w teście użyteczności).
- **SC-006**: W 100% przypadków niedostępności lub braku beads UI pozostaje funkcjonalne — brak awarii, czytelny pusty stan lub komunikat błędu.

## Assumptions

- Istniejąca integracja agent↔beads przez MCP pozostaje bez zmian; ten feature dodaje pierwszorzędne UI w OpenCode, a nie zastępuje kanał agentowy.
- Claim wykonany z UI identyfikuje użytkownika jako przypisanego; przypisanie jest prostym polem tekstowym (narzędzie lokalne, jednoużytkownikowe).
- Tryb „rozpocznij pracę" dostarcza agentowi kontekst beada w postaci danych beada (tytuł, opis, zależności) — sposób techniczny dostarczenia jest decyzją planu, nie specyfikacji.
- Usunięcie beada jest operacją niszczącą wymagającą potwierdzenia; szczegóły zachowania dla beadów z dziećmi (podzadaniami) doprecyzuje plan zgodnie z semantyką narzędzia beads.
- Beady są zakresem projektu: picker, panel i przeglądarka operują wyłącznie na beads bieżącego projektu.
- Jedna sesja ma co najwyżej jednego aktywnie powiązanego beada (zmienialnego); odwrotna relacja jest dowolna.
- Zgodnie z konstytucją forka (desktop-first) feature celuje w aplikację desktopową OpenCode; wsparcie TUI/CLI jest poza zakresem tej specyfikacji i wymagałoby osobnego uzasadnienia.
- Narzędzie jest jednoużytkownikowe i lokalne — brak wymagań autoryzacji i uprawnień.
- Użytkownik samodzielnie instaluje i inicjalizuje beads w projekcie; OpenCode może jedynie prowadzić do inicjalizacji z pustego stanu, nie instaluje beads automatycznie.
- Wizualizacja grafu zależności (grafowa reprezentacja) jest poza zakresem pierwszego wydania; zależności prezentowane są listowo w szczegółach beada.
- Panel pokazuje wyłącznie powiązanego beada sesji; przeglądanie całej listy odbywa się w dedykowanej przeglądarce (US4), nie w panelu.
