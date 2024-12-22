# C03L02 — LLM w kodzie aplikacji

Podłączenie prostych interakcji do kodu aplikacji nie jest szczególnie wymagające. Jednak dość szybko zaczynają być widoczne wyzwania, wynikające chociażby z niedeterministycznej natury modeli.

## Organizacja aplikacji

Aplikacja integrująca się z LLM do złudzenia przypomina taką, która tego nie robi. Myślę, że dość dobrze obrazuje to repozytorium projektu Quivr, który umożliwia "rozmawianie z danymi", pełniąc rolę "Second Brain". Wewnątrz projektu widać prawdopodobnie dobrze znane Ci elementy: autoryzacja i uwierzytelnienie połączenia, middleware, ścieżki, repozytoria czy serwisy. Nietrudno zauważyć także logikę odpowiadającą za parsowanie różnych formatów plików czy vector store, no i same testy. Zasadniczo z elementów związanych bezpośrednio z LLM można wyróżnić **połączenie z modelem** i **vector store** oraz ewentualnie **parsery**.

![](https://cloud.overment.com/quivr-046993a8-8.png)

Skala obecności elementów związanych z LLM może różnić się w zależności od projektu. W przypadku logiki Alice API z której korzystam na swoje potrzeby, udział LLM jest dość duży i uwzględnia: 

- Rozpoznawanie intencji
- Wybieranie zewnętrznych źródeł
- Korzystanie z zewnętrznych narzędzi
- Podsumowywanie zgromadzonych informacji
- Znacznie bardziej złożone niż w Quivr budowanie promptu
- Komunikaty potwierdzające wykonanie zadania
- Autorefleksję umożliwiającą zapisywanie wspomnień
- Umiejętności posługiwania się narzędziami
- Rozpoznawanie mowy
- Mówienie

> Info: (wszystkie wymienione wyżej elementy, których nie omówiliśmy, pojawią się w drugiej połowie AI_Devs).

Jednak nawet w tej sytuacji mówimy o dość **odseparowanej logice**, którą mogę rozwijać i testować, nie wpływając na resztę aplikacji. Przykładowo zmiana promptu związanego z autorefleksją zmienia wyłącznie format zapisywanych informacji, nie wpływając na sposób przepływu tych danych. 

Podobnie też narzędzia, którymi posługuje się Alice stanowią zupełnie niezależną część i są odseparowane tak bardzo, że mogę się nimi posługiwać bezpośrednio przez API, bez jakiegokolwiek zaangażowania pozostałej logiki aplikacji. 

Stopniowo zaczynają pojawiać się pierwsze wzorce projektowe. Mówimy jednak o dość wczesnym etapie i nie wszystkie z proponowanych technik są odpowiednio zweryfikowane lub dopasowane do scenariuszy, które spotkamy. Część z nich nawet już omówiliśmy a inne będziemy omawiać w dalszych lekcjach. Mowa tutaj o RAG (Retrieval Augmented Generation) czy ReAct (ReAct Prompting). Podobnie też na przykładzie LangChain widzimy, że jego niektóre elementy są niezwykle przydatne, podczas gdy inne bardzo komplikują rozwój aplikacji. 

## Organizacja promptów

LangChain oferuje zestaw narzędzi, których założeniem jest **ułatwienie zarządzania strukturą promptów** w kodzie aplikacji. W praktyce wypada to dość różnie i jeśli nie korzystamy z zaawansowanych narzędzi tego frameworka (np. Chain lub Agent), to nie widzę powodu, aby z nich korzystać. W zamian z powodzeniem można pozostać przy np. **Template Strings** i **Tag Functions** obecnych w JavaScript. 

Ogólne założenie narzędzi ułatwiających tworzenie szablonów promptów jest zasadne. Zależy na zachowaniu **spójności** i unikania błędów związanych z formatowaniem. Jednak razem z dokładnością w praktyce jest nam potrzebna także **kontrola**, na którą bardzo negatywny wpływ ma **‌(obecnie) mało elastyczna warstwa abstrakcji**.

Idąc dalej, samo zbudowanie działających promptów nie jest wystarczające, ponieważ musimy zadbać także o to, aby je utrzymywać. Ogólna rekomendacja z którą się spotkałem i która dobrze działa w praktyce polega na tym, aby utrzymywać prompty **możliwie krótkimi** lub stosować format łatwy w edycji (np. prezentowane wcześniej listy Facts & Rules). Wówczas łatwiej także o **wersjonowanie** i obserwowanie tego, w jaki sposób zmiany instrukcji wpływają na zachowanie modelu.

Projektowanie zwięzłych promptów nierzadko wymaga dużej kreatywności oraz nieszablonowego podejścia. Przykładowo zamiast  opisywać szczegółowo to, na czym mi zależy, mogę to pokazać. Podobną technikę stosowaliśmy już wcześniej, zmieniając narrację promptu z "Jesteś [xyz] (...)" na "Cześć! Jestem [xyz] (...)", która pozwalała na znacznie bardziej precyzyjne oraz zwięzłe układanie instrukcji. Całość obrazuje poniższy przykład. Zwróć uwagę, że praktycznie nie pozostawiam w nim miejsca na dodatkowe komentarze ze strony modelu. Co więcej, jeden z przykładów **zawiera treść która jest instrukcją / pytaniem**, dzięki czemu podkreślam oczekiwane zachowanie nawet w sytuacji gdy dane będą zawierać coś, co model może zinterpretować jako polecenie.

![](https://cloud.overment.com/short-38a17623-a.png)

Taki prompt składający się z bardzo obrazowych przykładów działa także świetnie w połączeniu z modelem GPT-3.5-Turbo. Jednak mając na uwadze fakt, że **ma on problemy z podążaniem za instrukcją systemową**, rozpoczynam konwersację dopiero od trzeciej wiadomości, przez co pierwszy fragment dodatkowo wpływa na zachowanie modelu.

![](https://cloud.overment.com/35-f810747e-d.png)

- ⚡ [Zobacz przykład](https://platform.openai.com/playground/p/3dKC6V4vu0RoMYER4NxwZSMR?model=gpt-3.5-turbo-16k)

Natomiast obecnie najlepszym wyborem w takiej sytuacji staje się GPT-3.5-Turbo-1106. Jest to wersja, która poradzi sobie z tak prostym generowaniem obiektu JSON i w przeciwieństwie do swojej starszej wersji, skuteczniej podąża za instrukcjami. Dodatkowo poza samym poleceniem mówiącym o generowaniu obiektu, warto ustawić dodatkowy parametr **‌response_format** ustawiony na **json_object** (należy **połączyć go z instrukcją i samo dodanie go, nie jest wystarczające**). 

Integrując LLM z kodem, praktycznie w każdym przypadku mówimy o jakimś rodzaju **dynamicznych danych** lub nawet **dynamicznego budowania struktury promptów** z mniejszych części. Wiemy już, że pomimo tego, że modele dobrze radzą sobie z nieustrukturyzowanymi danymi, to tam gdzie potrzebujemy precyzji, należy dbać o spójność. W przypadku dynamicznych promptów kluczowa jest ich **modularność** (możliwość łączenia ze sobą fragmentów w różnych konfiguracjach) oraz **separatory**. Np. stosowanie "###" nie sprawdzi się dla treści zapisanej jako treść Markdown, ponieważ taki ciąg znaków może wystąpić bezpośrednio w tekście jako nagłówek H3. Podobnie też jeśli mamy **zagnieżdżone konteksty**, to chcemy oddzielić je **różnymi rodzajami separatorów**, aby zredukować ryzyko pomylenia treści.

Niezależnie od tego, w jaki sposób zaprojektujesz i zorganizujesz prompty w swojej aplikacji, **krytyczne** jest zadbanie o **pełne monitorowanie**. Samo zapisanie **zapytania** oraz **odpowiedzi** jest niewystarczające, ponieważ nie da Ci pełnych informacji o tym, co się wydarzyło. Brak tych danych, **znacznie utrudni** debugowanie. Jeśli korzystasz z LangChain, to najlepszym sposobem będzie skorzystanie z LangSmith, ponieważ  podłączenie polega na utworzeniu projektu i dodania zmiennych środowiskowych (klucz API LangChain pobierzesz w **LangSmith** (jest to mylące)). Z kolei dostęp do LangSmith możesz uzyskać [tutaj](https://bravecourses.circle.so/c/informacje/dostep-do-langsmith-dla-ai_devs-2).

![](https://cloud.overment.com/langsmith-f0fe9d5d-b.png)

Podstawowe monitorowanie jednak nie wystarczy, ponieważ do LangSmith trafiają jedynie informacje związane z samą interakcją z LLM. Natomiast przepływ danych w Twojej aplikacji, niemal zawsze będzie uwzględniał kontakt z zewnętrznymi źródłami danych oraz różnymi obszarami jej logiki. 

Oczywiście nie traktuj tego, jak coś **absolutnie koniecznego** nawet dla prostych projektów, ponieważ mówimy teraz raczej o środowisku produkcyjnym. Gdy będziesz integrować LLM na własne potrzeby, to wystarczy Ci zapisanie **całej konwersacji**, co dzieje się w LangSmith. 

## Walidacja danych wejściowych i wyjściowych

Walidacja danych na potrzeby LLM uwzględnia wszystko to, **co już znasz z klasycznych aplikacji** i dodaje kilka dodatkowych aspektów. Jeden z nich dotyczy faktu, że gdy użytkownik ma **dowolność aplikacji**, musimy przygotować się na możliwie każdą sytuację. Wówczas najlepszym sposobem "obrony" jest zastosowanie tych samych narzędzi, czyli LLM.

W jednej z moich aplikacji stosuję zestaw **zasad**, których ma przestrzegać AI. Zasady te zestawiane są z **konwersacją** i jeśli jakaś z zasad jest naruszona, otrzymuję odpowiedź, która blokuje dalszą interakcję. Akurat w tym przypadku **pomijam najnowszą odpowiedź generowaną przez model**, jednak jeśli nie korzystasz z opcji streamingu, to ją także możesz walidować.

![](https://cloud.overment.com/filter-7ef75d58-c.png)

Sam korzystam z bardzo zbliżonego formatu (ale rozszerzonej listy zasad) do weryfikacji zapytań kierowanych do mojej aplikacji. Przykład poniżej pokazuje to w jaki sposób model przestrzega określonych przeze mnie zasad, odmawiając udzielania odpowiedzi na tematy spoza swojego zakresu.

![](https://cloud.overment.com/luke-baec746b-5.png)

Jednak czasem zdarzają się sytuacje w których poprawne, jednak **jestem w stanie to zauważyć, dzięki automatycznemu flagowaniu takich wątków**. Jedna z konwersacji uwzględniała pytanie, którego nie przewidziałem, ponieważ moje zasady informowały model o ewentualnej chęci naruszenia zabezpieczeń, w związku z tym zablokował pytanie o **prompt injection**. I fakt, Prompt Injection to technika ataku, jednak zapytanie o nią w kontekście kursu o Prompt Engineeringu powinno być dopuszczalne. 

![](https://cloud.overment.com/injection-c1379e24-4.png)

Pokazuję Ci to, aby zasygnalizować, że **obecnie trudno bronić się przed atakami**, ponieważ nawet jeśli uda nam się dobrze zabezpieczyć, to i przypadkowo możemy sprawić, że użytkownicy niemający na celu nic złego, zostaną zablokowani. 

Jednak z drugiej strony, przypadek o którym teraz mówię dotyczy ~1% użytkowników, więc nadal mówimy tutaj o wysokiej skuteczności, tym bardziej że system zablokował 100% niepożądanych zachowań. Oczywiście nie ma gwarancji, że tak pozostanie, jednak w kontekście mojego projektu, można uznać, że system spełnia swoje zadanie.

Przy takiej walidacji danych istotną rolę odgrywa także **słowo kluczowe**, które w tym przypadku zostało ustawione na **ALLOW** lub **BLOCK**. Nawet jeżeli w konwersacji znajdzie się prompt zaburzający działanie modelu, to i tak **dalsza logika aplikacji nie dopuści do dalszej interakcji**. Co więcej, mówimy tutaj o **oddzielnym prompcie**, więc przekazanie tych słów do oryginalnej konwersacji również nie wchodzi w grę. No i ostatecznie same słowa kluczowe są **krótkie**, przez co ich generowanie nie zajmuje zbyt wiele czasu.

## Parsowanie odpowiedzi modelu i obsługa błędów

Integracja aplikacji z LLM wymaga wymiany informacji pomiędzy modelem a kodem, a to sprawia, że konieczne jest utrzymywanie kontroli nad tym, w jaki sposób dane są przekazywane. W związku z tym, że model może nas zaskoczyć i np. zamiast wygenerować obiekt JSON, napisze: "Proszę, oto obiekt JSON {...}". Dlatego zawsze gdy generujesz obiekty JSON, ustaw wspomnianą już właściwość **response_format** na **json_object** czy Function Calling.

Podobnie jak w przypadku walidacji, parsowanie również może odbywać się poprzez interakcję z modelem. Naturalnie nie zawsze jest to wymagane, ponieważ czasem wystarczy nam zwykłe wyrażenie regularne lub jakiś rodzaj transformacji tekstu. 

W jednej z wersji Alice, gdy jeszcze nie miałem dostępu do Function Calling, w celu pobierania obiektu **prostego obiektu JSON** z odpowiedzi modelu, wykorzystywałem poniższą funkcję. Jej zadaniem było odnalezienie nawiasów klamrowych oraz podjęcie próby parsowania tekstu znajdującego się pomiędzy nimi. Oczywiście należy podkreślić, że ten kod działał tylko w przypadku gdy wiadomość **nie zawierała kilku obiektów**. To jednak w tym przypadku nigdy nie miało miejsca, a jeśli nawet by się wydarzyło, to informacja o błędzie w pełni mnie satysfakcjonowała.

![](https://cloud.overment.com/parsing-6763b49f-9.png)

Zakładając jednak, że **nie mam pewności** czy model zwróci mi pożądany format (bo niekoniecznie musi to być JSON) i jednocześnie **nie mam możliwości** wykryć go programistycznie to wówczas mogę skorzystać z modelu. Co więcej, nie muszę tego robić od razu, ponieważ mogę podjąć próbę parsowania go z pomocą kodu. Jeśli próba się nie powiedzie, zostanie wyrzucony wyjątek, który mogę przechwycić i zamiast informować o tym fakcie użytkownika, mogę przekazać zapytanie **do mocniejszego modelu** (lub innego promptu), aby poprawił działanie poprzedniego.

![](https://cloud.overment.com/fix-5fce59fb-0.png)

Przykład **bardzo prostej** implementacji takiego mechanizmu znajdziesz w **20_catch**. Domyślnie korzystam tam z modelu **gpt-3.5-turbo-1106**, dzięki czemu oszczędzam czas i pieniądze. Jednak mój prompt nie jest zbyt dobrze napisany (celowo) i w niektórych przypadkach nie potrafi podążać za początkową instrukcją. W rezultacie zwrócona odpowiedź **nie zawiera obiektu JSON** i psuje działanie mojego skryptu. 

![](https://cloud.overment.com/catch-dcf61e7d-b.png)

Gdy taka sytuacja ma miejsce, do gry wchodzi model GPT-4, który **naprawia ten błąd**. Oczywiście nie mam 100% pewności, że automatyczne naprawianie się uda, jednak jednocześnie zwiększam stabilność aplikacji. W produkcyjnym zastosowaniu musiałbym jeszcze obsłużyć sytuację w której GPT-4 także sobie nie poradzi, jednak na potrzeby tego przykładu, nie ma takiej potrzeby.

Alternatywnie mógłbym spróbować skorzystać **z tego samego modelu**, ale ze zmodyfikowanym promptem, którego zadaniem byłoby **ocenienie i naprawienie** wyniku, uwzględniając zarówno dane przekazane przez użytkownika, jak i **pierwotną odpowiedź modelu**. Rodzaj wybranej strategii będzie zależał od konkretnego przypadku i po prostu warto wziąć pod uwagę różne opcje. Ostatecznie jednak mechanizmy związane z próbą **automatycznego naprawienia** czy **parsowania danych z pomocą LLM** powinny być wykorzystywane awaryjnie, dlatego warto popracować nad optymalizacją promptów, by te mogły spełniać swoją rolę za pierwszym razem. 

## Powiadomienia o błędach

Z jakiegoś powodu dokumentacja OpenAI API nie zawiera informacji na temat statusów błędów. Co więcej, o problemach jesteśmy informowani zarówno przez statusy błędów, jak i wewnętrzne **status_code** a niekiedy **type**. Oznacza to, że brakuje tutaj spójności i bardzo trudno jest zgromadzić wszystkie informacje o błędach w jednym miejscu, tym bardziej, że w sieci znajdują się nieprawdziwe lub niepoprawne wyniki. 

Przykładowa informacja na temat błędu wygląda tak: 

![](https://cloud.overment.com/429-b39ddb34-a.png)

Natomiast pełna lista błędów z którymi sam się spotkałem, wygląda następująco: 

- **nieprawidłowe zapytanie** status: 400, type: invalid_request_error
- **przekroczony limit kontekstu** status: 400, type: invalid_request_error
- **przekroczony limit zapytań**: status: 429, code: rate_limit_error
- **błąd uwierzytelniania**: status: 401, code: invalid_api_key,  type: invalid_request_error
- **nie znaleziono**: status: 404, code: not_found_error
- **błąd uprawnień**: status: 403, code: permission_error
- **nieprawidłowy klucz API**: status: 400, code: invalid_api_key
- **model nie znaleziony (lub brak dostępu)**: status: 404, code: model_not_found
- **niewystarczający limit tokenów**: status: 403, code: insufficient_quota
- **niedozwolona metoda**: status 405, code: method_not_supported, type: invalid_request_error
- **błąd API OpenAI**: status: 500, code: api_error
- **błąd serwera**: status: 500, code: server_error

Warto zauważyć, że błąd 429 pojawia się także w sytuacji gdy **serwer jest obciążony zbyt dużą ilością zapytań**, niekoniecznie wykonywanych tylko przez nas. Choć taka sytuacja ma miejsce bardzo rzadko, warto mieć to na uwadze. Dodatkowo dobrą praktyką jest dodanie **generycznego błędu** informującego o statusie, którego nie ma na liście, ale może zostać dodany przez OpenAI w przyszłości. 

## Doświadczenia z LLM "na produkcji"

Podczas pracy nad własną wersją Alice, aplikacją desktopową, asystentem eduweb czy szeregiem różnych narzędzi, spotkałem różne sytuacje i wyciągałem z nich wnioski. Oto lista:

- **Błędy API:** Wykonując dowolne zadanie z LLM, **zakładam, że się nie uda**, głównie ze względu na błędy związane z API. Pomimo tego, że występują obecnie stosunkowo rzadko, to realizowanie **złożonych akcji** wymaga budowania mechanizmów umożliwiających ich wznawianie. W przeciwnym razie generujemy niepotrzebne koszty i marnujemy czas. Głównymi błędami są 429 (niezwiązane z moją aktywnością) oraz błąd 500. 
- **Limity:** Limit kontekstu, limit zapytań, limit przetwarzanych tokenów, twardy limit kosztów. Każdy z nich pojawia się co jakiś czas w moich aplikacjach i każdy wymaga dodatkowej uwagi, szczególnie w zastosowaniach produkcyjnych. Im większa skala, tym większa potrzeba wprowadzania systemu kolejek lub innych technik optymalizacji zarówno po stronie kodu jak i LLM. Same limity API ostatnio zostały powiększone i rozłożone na tzw. tiery. Ich aktualne wartości, znajdziesz [tutaj](https://platform.openai.com/docs/guides/rate-limits/usage-tiers?context=tier-free).
- **Niespodziewane koszty:** Estymacja kosztów bywa dość zdradliwa, szczególnie w systemach, które budują kontekst dynamicznie. Moje największe koszty były związane z tym, że prompt sprawdzał się na danych testowych, ale z różnych powodów generował zbyt dużo błędów na całej bazie. Inny przypadek uwzględniał **nieprawidłowe zapisywanie metadanych**, co pozornie wyglądało w porządku, ale całkowicie nie sprawdziło się w praktyce. Dobrze więc kilkukrotnie przekonać się na różnorodnych zestawach danych, czy nasze mechanizmy działają w porządku. 
- **Czas odpowiedzi:** Zwykle połączenie HTTP kończone jest po około 30-120s po braku odpowiedzi. W niektórych przypadkach możemy na to jakoś wpływać, a w innych nie. W sytuacji gdy zwiększanie limitu nie wchodzi w grę, należy rozważyć system kolejkowania, pollingu czy webhooków.
- **Nieoczekiwane zachowanie:** Raz na jakiś czas w moich systemach pojawiają się błędy, które również poprawiają się same. Niekiedy zidentyfikowanie ich źródła jest bardzo trudne, ponieważ nie można ich powtórzyć. Są to bardzo rzadkie sytuacje, ale jednak mają miejsce i przypominają mi o konieczności monitorowania zachowania mojego systemu oraz obsłudze błędów. 
- **Różnica językowa:** Obecnie w 100% pracuję z LLM w języku angielskim. Oczywiście można przygotować interakcję w języku polskim. Różnicy nauczyłem się w dość nietypowy sposób bo poprzez **różnice językowe embeddingu** i problemach z wyszukiwaniem anglojęzycznej bazy zapytaniami zapisanymi w języku polskim. Teoretycznie różnica w statystykach nie była zbyt duża, ale w przypadku koniecznej precyzji, okazała się znacząca.
- **Szybkość działania:** Na szybkość generowania odpowiedzi ma wpływ wiele czynników. W przypadku bardziej złożonych systemów wprost kluczowe staje się równoległe realizowanie zapytań oraz możliwie unikanie stosowania modeli dla zadań, które tego absolutnie nie wymagają. Przykładowo dla prostych wyszukiwań znacznie lepiej sprawdza się klasyczny silnik wyszukiwania, niż bazy wektorowe wymagające embeddingu, który wydłuża czas generowania odpowiedzi. Z kolei w innych sytuacjach embedding i bazy wektorowe sprawdzą się lepiej niż stosowanie LLM (np. gdy porównujesz jakieś dane, zamiast sięgać po LLM, możesz skorzystać z Similarity Search, co może okazać się bardziej wydajne) 
- **Skala pamięci:** Stworzenie systemu RAG (lub podobnego) na małej skali danych jest banalnie proste, szczególnie gdy mamy do dyspozycji obecne narzędzia oraz kontekst modeli na poziomie 128 tysięcy tokenów. Projektowanie **działającego** systemu pracującego na dużych zestawach informacji wymaga wiele pracy oraz dużego doświadczenia w przetwarzaniu danych. Dodatkowo niemal 90% aktywności związana jest z klasycznym programowaniem a nie interakcją z LLM, chociaż wiedza na temat modeli jest krytyczna do tego, aby zwyczajnie wiedzieć, co należy zrobić.
- **Cache/Szybszy dostęp do danych**: Stosowanie jakiejkolwiek techniki przyspieszającej dostęp do informacji jest zdecydowanie warte rozważenia. Pierwszym powodem jest szybkość działania systemu, drugą koszty. W przypadku nawet stosunkowo małych systemów ale pracujących na dużych zestawach danych, rachunki bardzo szybko rosną. Kojarzenie ze sobą podobnych zapytań lub stosowanie programistycznych technik dostępu do danych, zamiast ich przetwarzania przez model, jest zawsze bardzo wskazane. 

Prawdopodobnie mógłbym wymieniać w ten sposób jeszcze kilkanaście różnych punktów. Myślę jednak, że powyższe podkreślają najbardziej istotne wątki, które mogą przysporzyć Ci pracy lub wygenerować niepotrzebne koszty czy negatywnie wpłynąć na stabilność Twojej aplikacji.

#aidevs_2