# C02L05 — LLM posługujący się narzędziami

Możliwość generowania odpowiedzi przez model w dowolnym formacie, np. JSON, pozwala przygotowywać dane na potrzeby zapytań HTTP. Niestety nie zawsze mamy pewność, że odpowiedź będzie zawierała poprawnie sformatowany obiekt lub, że nie będzie zawierać dodatkowych komentarzy, które uniemożliwią jego parsowanie. Naturalnie moglibyśmy **przechwytywać wyjątki** i w razie problemów z parsowaniem obiektu wykorzystać LLM w celu poprawienia ewentualnych błędów. Jednak jest inna ścieżka.

## Wywoływanie Funkcji z pomocą Function Calling

Zastosowanie modeli związane z posługiwaniem się API (czy po prostu danych na potrzeby logiki aplikacji) stało się tak istotne, że OpenAI opublikowało specjalne wersje modeli, dostosowane pod tzw. **Function Calling** czy też **generowania ustrukturyzowanych danych**.

W praktyce polega to na tym, że:

- jako dodatkowy parametr zapytania przekazujemy listę funkcji w postaci **nazwy, opisu oraz zestaw parametrów**
- poza tym przesyłamy także **listę wiadomości**, podobnie jak w klasycznej interakcji z modelem
- model w odpowiedzi zwraca nam **nazwę funkcji** oraz **listę wartości jej parametrów**
- opcjonalnie (ale jest to zwykle potrzebne) wskazujemy, która funkcja ma być wybrana jako domyślna

Obrazek poniżej idealnie oddaje ideę Function Calling. Mamy w nim **rozmowę na temat AI_Devs** oraz **prośbę użytkownika o zapisanie notatki na temat tego kursu**. Do zapytania dodana jest także funkcja **quick_note** oraz definicja jej parametru (w tym przypadku jest to "**note**"). W opisach funkcji i parametrów znajdują się krótkie instrukcje wyjaśniające ich rolę. 

![](https://cloud.overment.com/call-141125e3-8.png)

Wykonanie takiego zapytania generuje odpowiedź zawierającą obiekt JSON (argumenty) oraz nazwę funkcji, którą model wybrał w związku z konwersacją. Można więc powiedzieć, że model **zdecydował o tym, którą funkcję wybrać oraz o tym, jak ją uruchomić**.

Zwróć uwagę także na **liczbę tokenów**, która uwzględnia nie tylko wiadomości, ale także definicje funkcji. Z technicznego punktu widzenia są one **wstrzykiwane są do instrukcji systemowej**. W praktyce oznacza to mniej więcej tyle, że **jesteśmy ograniczeni kontekstem** i że budując interakcje wykorzystujące Function Calling **zwykle będziemy musieli posługiwać się krótką listą** aby nie przekroczyć dostępnego limitu. Dodatkowo dla samego opisu pojedynczej funkcji czy jej parametrów obowiązuje limit tokenów, ale dokumentacja ani komunikaty błędów nie mówią dokładnie ile on wynosi. 

Na potrzeby Function Calling, parametr **functions** zawierający listę dostępnych funkcji i ich parametrów, można opisać następująco:

![](https://cloud.overment.com/ray-so-export-a0b25404-5.png)

- [Zobacz gist](https://gist.github.com/iceener/2b6f763c0cc1603955920ffd7e77fe52)

Zatem dostępne typy danych to **number, integer, string, object, array, boolean, null**. Dodatkowo przy obiektach i tablicach musimy określić jeszcze typ elementów. Możliwe jest także zdefiniowanie właściwości **enum** pozwalającej na ograniczenie wartości to określonej listy. Daj sobie chwilę czasu na przeanalizowanie powyższego obiektu. Niestety w dniu pisania tego tekstu, dokumentacja OpenAI nie opisuje dokładnie powyższej struktury i dochodziłem do niej metodą prób i błędów. Możliwe więc, że są jeszcze nieudokumentowane właściwości, które pominąłem.

Przy pracy z **Function Calling** należy pamiętać, że nie mamy tutaj do czynienia z jakimś niesamowicie magicznym mechanizmem, a jedynie pewną warstwą abstrakcji oraz dostosowanym modelem. Z tego powodu **nadal obowiązują tutaj wyzwania związane z halucynacją**, **utrzymywaniem uwagi modelu** czy **błędy logiczne**. Cały czas możesz myśleć o tym jak o prompcie (bo tym jest), lecz w nieco innej strukturze niż zazwyczaj.

Jeśli korzystasz z make.com do interakcji z modelami, to możesz wykorzystać akcję "Transform Text to Structured Data". Nie jest ona tak elastyczna jak w przypadku bezpośredniego połączenia z API (które w make.com także możesz wykonać), ale jednocześnie jest dość wygodna w użyciu. Po prostu w ramach odpowiedzi otrzymujesz **sformatowany tekst.**

![](https://cloud.overment.com/structured-8ed84134-d.png)

Zobaczmy teraz, jak możemy przenieść nasz przykład na kod.

## Function Calling w kodzie aplikacji oraz automatyzacji

Przykład **13_functions** przedstawia ogólny schemat pracy z Function Calling w LangChain, aczkolwiek w bardzo podobny sposób wyglądałoby to w przypadku bezpośredniej interakcji z OpenAI API. Zasadniczo musimy zadbać o to, aby upewnić się, że odpowiedź faktycznie zawiera informację o funkcji, którą mamy wykonać, oraz że jej argumenty są zgodne z tym, czego oczekujemy.

![](https://cloud.overment.com/call-7a3cdb2d-f.png)

Kolejnym krokiem jest **faktyczne wykonanie wskazanej akcji**, co również można zrealizować na różne sposoby, w zależności od tego, czy chcemy wywoływać funkcje **lokalne** (zapisane w kodzie) czy **zdalne** (poprzez zapytania HTTP).

W przypadku tych pierwszych potrzebujemy rodzaju interfejsu (np. obiekt), który pozwoli nam wskazać funkcję, którą chcemy wykonać. Przykład implementacji znajduje się poniżej. Obiekt **functions** zawiera listę dostępnych akcji. Gdy odpowiedź zwrócona przez model wskazuje na jedną z nich, możemy ją uruchomić, przekazując wygenerowane argumenty.

![](https://cloud.overment.com/execute-d7e902ec-9.png)

Dla zapytań HTTP możemy zastosować podobne rozwiązanie, aczkolwiek sam zwykle korzystam z **akcji zdefiniowanych w bazie danych (np. postgreSQL lub Airtable)**. Wówczas sytuacja jest jeszcze prostsza, ponieważ **wskazana nazwa służy do znalezienia wpisu w bazie danych**, a argumenty przekazywane są bezpośrednio jako payload zapytania HTTP. Obrazuje to poniższy przykład, który pochodzi bezpośrednio z kodu źródłowego mojej wersji Alice. 

![](https://cloud.overment.com/http-1962637e-b.png)

Ostatecznie do dyspozycji mamy jeszcze platformę make.com, w przypadku której opcja strukturyzowania danych może nam się przydać na potrzeby **narzędzi**, którymi będzie posługiwać się np. GPT-4. 

![](https://cloud.overment.com/make-76c1049e-e.png)

- [⚡ Pobierz Blueprint Scenariusza](https://cloud.overment.com/aidevs_structured-1695368295.json)

## Przykłady narzędzi z których może korzystać GPT-4

Wybór funkcji oraz przygotowanie danych do jej wywołania to koncepcja zmieniająca oblicze praktycznego zastosowania dużych modeli językowych. Prawdopodobnie widzisz już, że możesz wykorzystać to do **rozszerzania możliwości modelu** oraz **adresowania jego ograniczeń**.

Niewykluczone, że zastanawiasz się, na czym polega różnica pomiędzy zastosowaniem Function Calling w zwykłym generowaniem obiektu JSON, co mieliśmy już okazję robić. Podobnie też udało nam się podłączyć model do zewnętrznych źródeł wiedzy, a nawet stron www.

Otóż poza samym zwiększeniem kontroli nad strukturą, zyskujemy także możliwość **automatycznego wyboru funkcji, która ma zostać wykonana**. To otwiera przed nami koncepcję **Agentów** oraz coś, co określam mianem **Single Entry Point**, czyli jednego punktu wejścia. Inaczej mówiąc, zyskujemy tutaj przestrzeń do częściowej autonomiczności, którą zarysowuje przykład **14_agent**.

Mamy tutaj do dyspozycji trzy narzędzia: **dodawanie, odejmowanie i mnożenie**, którymi może posługiwać się model. Obecnie GPT-4 świetnie radzi sobie z większością obliczeń związanych z dodawaniem czy odejmowaniem, jednak mnożenie, szczególnie większych liczb, nadal wypada różnie. Ostatecznie najbardziej istotny jest tutaj mechanizm **samodzielnego wybierania narzędzia oraz sposobu posługiwania się nim**.

![](https://cloud.overment.com/math-d9d5196c-6.png)

W moim przypadku obecna lista narzędzi uwzględnia: 

- Połączenie z listą zadań (Notion / Todoist)
- Połączenie z kalendarzem (Google Calendar)
- Połączenie z aplikacją do notatek (Notion / Obsidian)
- Połączenie z feed readerem (Feedly)
- Połączenie z usługami streamingowymi (Spotify)
- Połączenie z systemem faktur (Fakturownia)
- Połączenie z systemami sprzedaży (Stripe)
- Połączenie z generatorami grafik (mój własny)
- Połączenie z własnymi narzędziami (np. do wyszukiwania w DuckDuckGo czy odczytywaniem treści stron i dokumentów)
- Połączenie bezpośrednio z API Alice (zapamiętywanie / aktualizowanie wspomnień / nauka nowych umiejętności)
- Połączenie z modelem Wolfram Alfa (Conversational API) na potrzeby prywatnych zastosowań związanych z obliczeniami i ogólną wiedzą, np. pogodą
- Połączenie z Google Maps API w kontekście zadań związanych z lokalizacją i odległością pomiędzy nimi
- Dodatkowe integracje do powiadomień w systemie urządzeń Apple

Powyższa lista może na ten moment pełnić dla Ciebie rolę inspiracji, ponieważ bliżej przyjrzymy się jej później. Aby jednak nie zostawiać Cię tylko z nią, zaprojektujemy narzędzie umożliwiające zarządzanie **prywatną listą zadań**. 

## Projektowanie własnych narzędzi dla LLM

Pierwsze narzędzia dla modelu text-davinci-002 projektowałem już w połowie grudnia, czyli zaraz po premierze ChatGPT. Przez ten czas wykorzystywałem je w różnych kontekstach: **bezpośredniej rozmowie, interakcji głosowej czy automatyzacjach.** Spotkałem po drodze zarówno problemy, jak i interesujące techniki, które zwiększają ich użyteczność. 

Przede wszystkim, ogromną rolę odgrywa wspomniana koncepcja "single entry point", czyli "jednego punktu wejścia". Polega ona na tym, aby interakcja z modelem wyposażonym w narzędzia odbywała się możliwie **z poziomu jednego okna czatu, bez konieczności wciskania dodatkowych przycisków czy określenia ustawień**. Oznacza to, że model powinien samodzielnie podejmować decyzję o sposobie wykonania zadania, co już zresztą zobaczyliśmy. Naturalnie odbywa się to kosztem **zmniejszenia kontroli** z naszej strony, więc mogą zdarzyć się sytuacje w których konieczne będzie **bezpośrednie sugerowanie oczekiwanej ścieżki**.

Zdolność do wybierania narzędzi w dużym stopniu zależy od **jakości promptu**, który za to odpowiada. Z tego powodu nie możemy oczekiwać działania takiej mechaniki na poziomie człowieka i musimy określić sobie pewne zasady, które zwiększą prawdopodobieństwo tego, że model **będzie zachowywał się zgodnie z naszymi oczekiwaniami**. Cześć z zachowań możemy także kontrolować programistycznie (np. poprzez walidację niektórych pól czy dodawanie wartości domyślnych).

Wdrożeniem takich mechanik zajmiemy się w kolejnych modułach AI_Devs. Rysuję Ci jednak kontekst już teraz, aby jasne były dla Ciebie niektóre fakty, które uwzględnimy za chwilę. Naszym celem będzie zbudowanie narzędzia **zdolnego do zarządzania zadaniami** na podstawie przesłanej wiadomości. Rolą modelu będzie jej zinterpretowanie i podjęcie opisanych działań. 

Nie będziemy jednak skupiać się na bardzo prostej, bezpośredniej interakcji i zaprojektujemy mechanizm zdolny do **faktycznego zarządzania** zadaniami. Konkretnie mowa tutaj o:

- Interpretowaniu naszego zapytania i pobraniu z niego danych
- Przygotowaniu integracji do faktycznego wykonania operacji
- Wsparciu dla akcji CRUD - Dodaj, Odczytaj, Zaktualizuj, (usuń pominiemy, bo w tym przypadku usunięcie zadania to zmiana jego statusu na zakończony)
- Możliwości dodania wielu wpisów jednym poleceniem
- Opracowaniu informacji zwrotnej, potwierdzającej wykonanie zadania

Podkreślam tutaj **pracę z prywatną listą zadań** ze względu na politykę prywatności. Warto także dopracować całą mechanikę na "testowym koncie" lub początkowo wykluczyć z niej akcje nieodwracalne (np. modyfikowanie i usuwanie). 

**Interpretowanie zapytania**

Ogólna koncepcja jest niezwykle prosta i polega na wygenerowaniu obiektu JSON opisującego akcję, na której nam zależy. Następnie z pomocą skryptu (lub automatyzacji) możemy go wykorzystać do wprowadzenia faktycznych zmian w aplikacji do zadań. Mówimy więc tutaj o:

- akcji dodawania zadań
- akcji aktualizacji zadań
- akcji oznaczania zadań jako zakończone
- akcji umożliwiającej pobranie listy nieskończonych zadań

Pełne definicje tych struktur znajdziesz w przykładzie **15_todoist**, a jedną z nich, odpowiedzialną za dodawanie zadań, na obrazku poniżej. Mamy więc tutaj **listę zadań** z właściwościami **content** oraz **due_string** (czas realizacji). Naturalnie możesz swobodnie rozbudować ten obiekt mając na uwadze limity kontekstu. 

![](https://cloud.overment.com/add-596a9cbf-c.png)

**Podejmowanie działania**

Druga część integracji obejmuje **przygotowanie logiki realizującej działanie dodania zadań**. Aby uczynić tę akcję nieco bardziej elastyczną, zadbałem o to, aby **móc dodawać wiele zadań jednocześnie**. Z kolei w celu **optymalizacji czasu potrzebnego na jego realizację**, zapytania realizowane są **równolegle**. 

Naturalnie implementacja będzie różnić się w zależności od technologii, na jaką się zdecydujesz, jednak schemat pozostaje zawsze taki sam, nawet w przypadku narzędzi no-code, czyli:

- Funkcja lub scenariusz automatyzacji, przyjmujące listę zadań wygenerowaną przez Function Calling na podstawie zapytania użytkownika
- Faktyczny kontakt z API w celu przesłania wszystkich wpisów
- Zwrócenie potwierdzenia, które w kolejnym kroku może być wykorzystane w celu **parafrazy wiadomości** informującej użytkownika o wykonanym zadaniu

![](https://cloud.overment.com/new-644b0155-f.png)

Przykład prezentujący połączenie wszystkich wymienionych elementów znajdziesz w katalogu **15_tasks**. Ze względu na jego złożoność, przyjrzymy się mu dość szczegółowo, jednak i tak polecam uruchomienie go i przetestowanie na swoim **testowym koncie Todoist**.

**Implementacja**

W pliku 15.ts zdefiniowałem funkcję **act** odpowiadającą za **interakcję z modelem**. Przekazana do niej wiadomość użytkownika, zostaje przesłana do OpenAI **i jeśli jest poleceniem skojarzonym z jedną z dostępnych akcji**, to dalsza część kodu **uruchamia funkcję łączącą się z Todoist**. Jeśli dojdzie do wykonania akcji, to odpowiedź jest **parafrazowana** lub w przeciwnym razie, zwracam bezpośrednią odpowiedź modelu.

![](https://cloud.overment.com/act-a106e9c8-3.png)

Wygląda to tak:

- User: Cześć!
- AI: Jak mogę Ci pomóc?
- User: Muszę napisać newsletter na jutro, dodaj to do listy
- (AI wykonuje akcję i otrzymuje odpowiedź z API)
- AI: (parafrazowane) Dodałem do Twojej listy napisanie newslettera na jutro

Mamy więc do czynienia ze swobodną konwersacją pomiędzy użytkownikiem, a GPT-4 zdolnym do posługiwania się listą zadań!

W pliku **todoist.ts** zdefiniowałem wszystkie funkcje, którymi posługuje się model (jako narzędziami). Mówimy tutaj o prostych interakcjach z API (aby nie komplikować przykładów, pominąłem obsługę błędów).

![](https://cloud.overment.com/todoist-6e39bd1a-0.png)

No i ostatecznie, w pliku **schema.ts** zapisałem **schematy opisujące funkcje** na potrzeby OpenAI. Możesz je potraktować jako referencję do tworzenia własnych funkcji, posługujących się np. narzędziami do zadań z których korzystasz lub innymi aplikacjami, usługami, czy nawet urządzeniami (podłączonymi do sieci).

![](https://cloud.overment.com/schema-3fe751e1-0.png)

**Rezultat**

W głównym pliku **15.ts** możemy zdefiniować listę poleceń, które nasz asystent ma zrealizować. Oczywiście, aby faktycznie było to możliwe, konieczne jest podanie swojego klucza API w pliku **.env** znajdującym się w głównym katalogu projektu. Efekt działania skryptu, można zobaczyć na animacji poniżej, lub pod [tym adresem](https://cloud.overment.com/tasks-1695500849.gif).

![](https://cloud.overment.com/tasks-1695500849.gif)

## Własne narzędzia no-code dla LLM

Pomimo tego, że potrafię programować, często sięgam po rozwiązania no-code w celu **ułatwienia sobie pracy**. Odgrywa to szczególną rolę w przypadku usług, których API nie jest tak dobre, jak Todoist. Poza tym stworzenie scenariusza jest dla mnie zdecydowanie szybsze, niż implementowanie całej logiki samodzielnie. Ostatecznie, narzędzia no-code można także **wykorzystywać równolegle**, ponieważ przykładowo, zamiast definiować funkcje wchodzące w interakcję z API, mogłem stworzyć scenariusz make.com, który realizowałby ich zadanie.

W przykładzie **16_nocode** znajdziesz kod będący alternatywną wersją **15_tasks**, jednak uwzględnia kilka zmian. (Pamiętaj, że aby go uruchomić, potrzebujesz zaimportować scenariusz Make.com i podłączyć do niego swoje konta. Powiązany z nim adres webhooka umieść kodzie pliku 16.ts w miejscu w którym aktualnie znajduje się pusty adres webhooka).

- Zamiast rozbicia na pojedyncze funkcje, mamy definicję **tylko jednej, ogólnej, której celem jest zarządzanie zadaniami**. Takie podejście pozwala nam np. na **zrealizowanie wielu zadań jednym zapytaniem**. Możemy więc za jednym razem poprosić zarówno o dopisanie zadań, jak i zakończenie innych, które wymienimy.
- Logika funkcji **w całości przeniesiona jest do make.com**, przez co **natychmiast mamy do niej zdalny dostęp**.

![](https://cloud.overment.com/nocode-f95fb8b3-f.png)

- ⚡ [Pobierz Blueprint](https://cloud.overment.com/manage_tasks-1695501872.json)

Powyższy scenariusz może zostać naturalnie rozbity na pojedyncze akcje, jednak w tym przypadku zdecydowałem się uwzględnić je wszystkie i **sterować jego wykonaniem w zależności od wyniku Function Calling**. Gdy zaimportujesz go do Make, dokładnie jak we wcześniejszych przykładach, potrzebujesz podłączyć swoje konta Todoist i OpenAI oraz ewentualnie dostosować prompty.

Zaznaczam w tym miejscu, że scenariusz może być **znacznie prostszy** i odpowiadać wyłącznie za pojedyncze zadanie. Wówczas nawet jeśli Twoje doświadczenie z make.com jest małe, to stosunkowo łatwo będziesz w stanie połączyć się np. ze Slackiem w celu zintegrowania swojego asystenta (pokażę jak to dokładnie zrobić w dalszej części kursu).

## Podsumowanie narzędzi dla LLM

Tworzenie narzędzi dla LLM polega w dużym stopniu na opracowaniu **logiki** oraz **instrukcji**, którymi model będzie posługiwać się w trakcie konwersacji. Zawsze jednak trzeba mieć na uwadze wszystkie ograniczenia, takie jak limity tokenów czy możliwość halucynacji. Z tego powodu warto unikać **nieodwracalnego wprowadzania zmian** bez ich wcześniejszej weryfikacji.

Na przykładzie naszej listy zadań, moglibyśmy zrobić tak, że model **ma dostęp wyłącznie do jednego projektu**, a pozostałe zadania może np. jedynie odczytywać. Co więcej, moglibyśmy w rękach modelu pozostawić wyłącznie **wybór akcji**, natomiast za resztę operacji odpowiadałaby logika działająca po stronie kodu.

W dalszej części kursu, pójdziemy krok dalej i znacznie rozszerzymy wachlarz narzędzi, którymi będzie mógł posługiwać się asystent. Do tego czasu, warto zaprojektować przynajmniej jedną umiejętność AI, która może pomóc nam w codzienności (np. lista zakupów czy zarządzanie domowym budżetem).

#aidevs_2