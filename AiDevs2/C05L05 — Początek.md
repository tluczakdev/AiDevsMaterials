# C05L05 — Początek

Właśnie dotarliśmy do miejsca, w którym **możemy zacząć**. Przez ostatnie 5 tygodni przeszliśmy przez różne tematy z pogranicza programowania, AI i automatyzacji. Do niektórych wątków wracałem kilkukrotnie, stopniowo odsłaniając ich kolejne detale. Zaprojektowałem cały materiał AI_devs 2 w taki sposób, aby możliwie **nie dało się podążać za konkretnym schematem.** Celem każdej z lekcji było przedstawienie koncepcji, które mogą działać w izolacji oraz być w przeróżne sposoby ze sobą łączone na potrzeby budowania nie tylko asystentów AI, ale praktycznie dowolnych systemów integrujących LLM. Wprost zależało mi na tym, aby **nie dostarczać rozwiązań, lecz rysować scenariusze, które pozwolą swobodnie posługiwać się LLM w kodzie i automatyzacji w sposób znacznie wykraczający to, co przedstawiłem**. 

Dziś właśnie to będzie tematem przewodnim ostatniej lekcji AI_devs 2. Za chwilę **połączymy wszystko**, czego się do tej pory nauczyliśmy, wychodząc momentami poza to, co wydawało się do tej pory w naszym zasięgu.

## Struktura aplikacji połączonej z Dużym Modelem Językowym

Aplikacje i automatyzacje w których obecne są LLMy nie różnią się za bardzo od tych, w których ich nie ma. Zatem ich fundament tworzymy posługując się tymi samymi umiejętnościami, które posiadaliśmy przed rozpoczęciem AI_devs. Szczególne znaczenie zyskują jednak: **projektowanie i posługiwanie się API**, **architektura baz danych**, **praca z plikami i różnymi formatami danych** oraz **wykorzystywanie silników wyszukiwania**. 

Do tego dochodzi **wiedza o Dużych Modelach Językowych**, **umiejętność pisania zaawansowanych promptów**, a nawet **znajomość zagadnień z obszaru psychologii**. Szczególnie istotne jest również posiadanie skutecznego systemu nauki dopasowanego do nas, który umożliwi nam pozostawanie na bieżąco z tym, co dzieje się obecnie w obszarze AI. 

Na obrazku poniżej znajduje się główny plik aplikacji z którą będziemy dziś pracować. Każdy kto ma jakiekolwiek doświadczenie na back-endzie widzi, że mamy tutaj bardzo prosty serwer udostępniający równie proste API. Na pierwszy rzut oka jest to typowa aplikacja nieprzejawiająca oznak tego, że **z jej pomocą będziemy w stanie porozmawiać z Alice** w wersji zdolnej do zapamiętywania informacji i posługiwania się narzędziami. 

![](https://cloud.overment.com/aidevs_structure-14238efe-d.png)

Aplikacja udostępnia trzy endpointy: 

- **Główny**, którego rolą jest wyświetlenie strony index.html umożliwiającej interakcję głosową oraz proste wyświetlenie odpowiedzi
- **Rozmowa** na który trafiają wszystkie wiadomości przesyłane do Alice. Odpowiedź może być przesłana w formie obiektu JSON lub strumieniowana pojedynczymi fragmentami
- **Nauka**, który stanowi przykład wewnętrznego API Alice i jednocześnie narzędzie, którym potrafi się posługiwać. 

Mówimy więc tutaj o minimalnej wersji asystenta AI z którym można dość swobodnie porozmawiać. Jednak w tym przypadku nie będziemy posługiwać się wyłącznie tekstem, ale także naszym głosem, poprzez wykorzystanie modeli Whisper oraz TTS (lub ElevenLabs). Uproszczony schemat tej interakcji widoczny jest poniżej. 

![](https://cloud.overment.com/aidevs_speech-b7240e5d-d.png)

Logika aplikacji jest dość złożona i wygenerowanie odpowiedzi jest czasochłonne, szczególnie gdy weźmiemy pod uwagę transkrypcję i generowanie audio, oraz późniejsze odtworzenie. Z tego powodu wykorzystamy strumieniowanie zarówno na etapie generowania odpowiedzi z OpenAI, jak i generowania audio, oraz odtwarzania poszczególnych fragmentów. Ten ostatni etap momentami będzie zauważalny i może być jeszcze optymalizowany, ale jednocześnie odda ogólną ideę takiego rodzaju interakcji. 

## Logika asystenta umożliwiająca swobodną rozmowę

Bez względu na to czy projektujemy asystenta czy wyspecjalizowane w jednym zadaniu narzędzie, zależy nam na tym, aby przekazywane dane były dobrze "zrozumiane" przez model. Naturalna konwersacja wydaje się jednym z najbardziej złożonych przykładów, który możemy podjąć, ale dokładnie na tym nam teraz zależy. Musimy tutaj zadbać nie tylko o to, aby wygenerować odpowiedź na podane zapytanie, ale także umożliwić przeprowadzenie rozmowy składającej się z przynajmniej kilku wiadomości. 

W pierwszej części głównej akcji asystenta odbieramy wiadomość, identyfikator konwersacji (UUID) oraz parametr umożliwiający strumieniowanie odpowiedzi. W zależności od ich wartości rozpoczynamy nową interakcję, lub kontynuujemy istniejącą. Jednak niezależnie od tego, zależy nam na kilku dodatkowych krokach obejmujących: 

- Rozpoznanie intencji użytkownika uwzględniającej wybranie obszaru pamięci oraz czy konieczne jest podejmowanie jakichś akcji
- Embedding zapytania, który wykorzystamy na potrzeby późniejszego odnalezienia wspomnień z obszaru wskazanego przez rozpoznanie intencji
- Zainicjowanie bazy danych oraz vector store (o ile jeszcze nie istnieją)

![](https://cloud.overment.com/aidevs_talk-716d8e0b-f.png)

Powyższe akcje wykonują się **równolegle**, ponieważ każda z nich zajmuje czas i w ten sposób możemy dość znacznie zmniejszyć czas reakcji asystenta. Rozpoznanie intencji oraz embedding w dość bezpośredni sposób przekłada się na ogólną trafność generowanej odpowiedzi, ponieważ są to elementy stanowiące pierwszy krok do budowania **dynamicznego kontekstu dla głównego promptu**.

Rozpoznawanie intencji może przybierać różne formy i sam eksperymentuję z różnymi technikami. Obecnie korzystam z Function Calling i poniższego schematu. Jak widać dążę tutaj do wygenerowania dwóch właściwości — **type** oraz **category**. Od pierwszej zależy podjęcie działania, a od drugiej obszar przeszukiwanej pamięci. Dla uproszczenia, na potrzeby naszego projektu będziemy jednak korzystać **wyłącznie z głównej pamięci asystenta** i nie będziemy dzielić je na mniejsze fragmenty. Pozostawiam jednak poniższy prompt do wglądu. 

![](https://cloud.overment.com/aidevs_intent-ffc75ea9-7.png)

Na tym etapie poza identyfikowaniem polecenia i wybieraniem obszarów pamięci, możemy także rozważyć **wzbogacenie zapytania** oraz **self-querying**, o czym mówiliśmy we wcześniejszych lekcjach. Tym razem pominiemy ten krok.

## Pamięć asystenta i baza SQLite oraz Faiss

Wspomnienia oraz historię wiadomości przechowuję w bazie SQLite. Skala projektu w pełni uzasadnia ten wybór, tym bardziej, że zachowujemy tutaj dużą wygodę oraz wystarczającą elastyczność. Do łatwiejszej interakcji z bazą wybrałem wielokrotnie sprawdzony przez siebie ORM — objection.js oraz knex.

Schemat bazy danych jest bardzo prosty i uwzględnia wyłącznie trzy tabele — **wspomnienia, umiejętności oraz wiadomości** z których **pierwsze dwie są synchronizowane z bazą wektorową / vector store**. 

- Wspomnienia zawierają swoją nazwę (wygenerowaną przez LLM), treść (parafrazowaną informację pochodzącą od użytkownika), tagi oraz opcjonalną refleksję i źródło
- Umiejętności zawierają nazwę, opis, adres webhooka oraz schemat function calling
- Wiadomości składają się z treści przesłanej przez użytkownika, odpowiedzi wygenerowanej przez AI oraz opcjonalnej nazwy wygenerowanej przez LLM. Dodatkowo mamy jeszcze **conversation_id** dzięki któremu możliwe jest grupowanie wiadomości z tego samego wątku

![](https://cloud.overment.com/aidevs_db-826f7840-d.png)

W związku z tym, że tylko wspomnienia i umiejętności mogą być wykorzystywane przez asystenta, tylko one synchronizowane są z bazą wektorową. Synchronizowanie odbywa się w tej sytuacji poprzez upewnienie się, że podczas zapisywania informacji w bazie SQLite, dodajemy do indeksu dokument **zawierający ten sam identyfikator (UUID)**. 

![](https://cloud.overment.com/aidevs_sync-d73049c7-f.png)

W przypadku tego projektu pominąłem aktualizowanie oraz usuwanie wspomnień, jednak schemat jest tutaj podobny. Po prostu przy jakiejkolwiek akcji podejmowanej na bazie danych, odzwierciedlamy ją w indeksie bazy wektorowej. Zwykle takie akcje możemy realizować przez ORM, który daje nam możliwość podłączenia się pod zdarzenie związane z wykonywaniem operacji na bazie. Dla ułatwienia pominąłem tutaj kroki związane ze wzbogacaniem wspomnienia przed jego zapisaniem, jednak to właśnie w tym miejscu moglibyśmy je zrealizować.  

Jeśli chodzi o bibliotekę Faiss to wybrałem ją wyłącznie na potrzeby tego projektu. I tak korzystam z niej w połączeniu z LangChain, więc ewentualna zmiana na Qdrant czy inną bazę, nie powinna stanowić większego problemu. Jednocześnie nie ma potrzeby instalowania dodatkowych zależności, ponieważ cały indeks przechowywany jest w pliku, w katalogu /memory. Niestety taka decyzja w pewien sposób nas ogranicza, ponieważ Faiss nie daje nam możliwości filtrowania rekordów na poziomie wyszukiwania, co jest przydatne przy złożonych strukturach pamięci. Uznajmy, że na ten moment nie ma to dla nas większego znaczenia, ale takie detale są istotne z punktu widzenia dobierania narzędzi. Tutaj moim celem było utrzymanie możliwie prostej logiki. 

## Przypominanie informacji istotnych dla bieżącej konwersacji

Poza Similarity Search wykorzystuję także wspominany już re-rank w celu zawężenia listy wspomnień, które faktycznie mogą być istotne z punktu widzenia aktualnej rozmowy. Takie działanie realizuję zwykle poprzez model GPT-3.5-turbo, ponieważ jest wystarczający do prostej klasyfikacji. Sam prompt bierze pod uwagę aktualne zapytanie oraz treść dokumentu. W przypadku swojej wersji Alice, pod uwagę biorę także **podsumowanie aktualnej konwersacji** w celu zwiększenia szansy na wybranie dokumentów, które mogą pozornie nie być powiązane z tym, o co pytam, ale istotne z punktu widzenia wcześniejszych wiadomości.

![](https://cloud.overment.com/aidevs_rerank-9af5975d-0.png)

Wyszukiwanie dąży do tego, aby **skupić się na wybranych obszarach pamięci** (dzięki rozpoznaniu intencji) oraz dobraniu takich wspomnień, które zmniejszą ryzyko wystąpienia halucynacji. Działania związane z klasyfikowaniem wspomnień mogą potencjalnie generować dość duże koszty i zajmują dodatkowy czas. Z drugiej strony wykonanie ich równolegle pozwala zoptymalizować cały proces i w zamian otrzymamy znacznie lepszą jakość końcowej odpowiedzi. 

Proces wyszukiwania może uwzględniać także zaangażowanie silników wyszukiwania, które również mogą wykonać **dokładnie to samo zadanie**, ale zwrócą nieco inne rezultaty. Wyniki obu rodzajów wyszukiwań możemy ze sobą zestawić i programistycznie lub z pomocą modelu wybrać te, które chcemy uwzględnić w generowaniu odpowiedzi.

Należy wyraźnie zaznaczyć, że wyszukiwanie nie musi opierać się wyłącznie o znalezienie fragmentów pasujących do wypowiedzi, ale także podjęcie dodatkowych działań związanych z **odbudowywaniem wspomnień**. Mam tutaj na myśli przypadek, gdy dłuższa treść dzielona jest na mniejsze fragmenty, a na etapie odzyskiwania wspomnień, łączymy je ponownie ze sobą. Niekonicznie chodzi tutaj o faktyczne połączenie, ale nawet o **wygenerowanie podsumowania dla większej liczby fragmentów, które normalnie nie zmieściłyby się w kontekście**. Warto więc pamiętać o tym, że mając do dyspozycji LLM, możemy wyjść znacznie poza prosty schemat wyszukiwania i wykorzystać transformację treści w celu uzyskania najlepszych rezultatów. 

Rezultatem procesu wyszukiwania zawsze będzie zestaw informacji, który zostanie przekazany do głównego promptu. W związku z tym, że zwykle mówimy o fragmentach pochodzących z różnych miejsc, to poza ich treścią należy uwzględnić także dodatkowe informacje na temat ich pochodzenia. Połączenie tego z instrukcją systemową pozwoli asystentowi wykorzystywać dostarczone treści w sposób przypominający to, jak człowiek posługuje się swoją pamięcią. 

Cały proces generowania odpowiedzi widoczny jest poniżej. Zaznaczyłem w nim także czas reakcji liczony od momentu odebrania żądania do czasu wygenerowania odpowiedzi. Warto zwrócić uwagę na strumieniowanie, które rozpoczęło się w tym przypadku po 2.9s, natomiast pełna odpowiedź została wygenerowana sekundę później. Im dłuższa treść byłaby generowana, tym większą oszczędność czasu generujemy na tym etapie. Docelowo do tego czasu reakcji należy doliczyć także samą transkrypcję oraz późniejsze wygenerowanie audio. 

![](https://cloud.overment.com/aidevs_timer-d2b28923-e.png)

W poprzednich lekcjach mówiłem o tym, że Alice posiada różne tryby funkcjonowania, dzięki którym wtedy, gdy nie jest to konieczne, pomija niektóre działania. Poniżej mamy przykład dla dokładnie takiego samego zapytania, jednak tym razem czas reakcji wyniósł 2.1s. 

![](https://cloud.overment.com/aidevs_memory-aae0aa23-b.png)

Jako ciekawostkę dodam, że w chwili pisania tego tekstu, przełączenie się z modelu gpt-4-1106-preview na gpt-3.5-turbo-1106 **zwiększyło czas reakcji**, ale pomimo tego odpowiedź została wygenerowana w tym samym czasie. Oczywiście obecnie wydajność modeli OpenAI bywa różna nawet w zależności od pory dnia, jednak wyraźnie widzimy, że nie zawsze przełączenie się na słabszy model da nam wszystkie oczekiwane korzyści. Tutaj  trochę tracimy na jakości, lecz zmniejszamy koszty. 

![](https://cloud.overment.com/aidevs_faster-544ef4c3-a.png)

## Podejmowanie działania i posługiwanie się narzędziami

Wiemy już, że w przypadku wydania polecenia nawiązującego do czegoś innego niż wypowiedź czy pisanie, spowoduje zidentyfikowanie intencji jako "akcji", którą Alice ma wykonać. Z punktu widzenia logiki aplikacji, jest to zwykły warunek powodujący podjęcie kilku dodatkowych kroków. Nie oznacza to jednak, że sam proces jest równie oczywisty.

![](https://cloud.overment.com/aidevs_action-82d5434a-c.png)

Obrazuje to funkcja performAction, wewnątrz której dochodzi do szeregu działać, mających na celu połączenie się z zewnętrzną usługą (lub własnym API), przesłanie danych oraz wygenerowanie odpowiedzi. Konkretnie mówimy tutaj o:

- Przeszukaniu listy umiejętności z pomocą Similarity Search w celu wybrania akcji, których nazwy oraz opis są najbardziej zbliżone do aktualnego kontekstu
- Planowania (re-rank), uwzględniającego w tej sytuacji wybór **jednej** akcji, która będzie zrealizowana
- Wczytanie akcji zapisanej w SQLite na podstawie UUID zwróconego w wyniku wyszukiwania
- Wykorzystanie Function Call w celu wygenerowania parametrów na podstawie schematu wybranej akcji
- Przesłanie wygenerowanych parametrów na adres powiązany z wybraną akcją i odebranie odpowiedzi

![](https://cloud.overment.com/aidevs_perform-205cdb6a-4.png)

Odpowiedź ze strony zdalnej akcji zostaje zwrócona z tej funkcji i zostaje dołączona do kontekstu. Poza jej treścią pojawia się także dodatkowa instrukcja, mówiąca o tym, aby Alice potwierdziła wykonanie zadania. 

![](https://cloud.overment.com/aidevs_context-6b97925c-b.png)

Rezultat dobrze widać poniżej, gdzie dla wiadomości z prośbą o zapamiętanie informacji na temat tego, abym dodał link umożliwiający dopisanie się do naszej Ściany Miłości, Alice faktycznie to zrobiła. W pierwszej kolejności rozpoznała akcję, skojarzyła ją ze swoją umiejętnością, a następnie odpowiedziała potwierdzając zrealizowanie prośby. 

![](https://cloud.overment.com/aidevs_memorize-b255a1cc-f.png)

Gdy zajrzymy do bazy danych, to zobaczymy w niej wpis, który przypomina treść o której zapamiętanie prosiłem. Jednak tam, gdzie używałem określenia wskazującego na siebie (np. "I need"), Alice zmieniła je na "user". Dzięki takiej transformacji wpis wygląda jak wspomnienie, które może zostać wykorzystane w późniejszej interakcji. 

![](https://cloud.overment.com/aidevs_memories-30b80ea7-e.png)

Jeśli więc w nowej konwersacji zapytam o to, co powinienem zrobić z lekcją AI_devs, to Alice przypomni mi dokładnie to, o czym mówiłem wcześniej. Naturalnie w celu zwiększenia użyteczności takiej mechaniki powinienem wyraźnie oznaczyć wspomnienia związane z moją listą zadań i ewentualnie móc je usuwać oraz przypisywać do nich daty. Widoczne są tutaj jednak ogólne zasady, którymi powinniśmy się kierować przy zapisywaniu informacji na potrzeby późniejszych konwersacji. 

![](https://cloud.overment.com/aidevs_recall-b3b92016-0.png)

Warto także pamiętać o tym, że transformacja widoczna powyżej nie powinna mieć miejsca w sytuacji, gdy wspomnienia nie dotyczą nas. Przykładowo gdy asystent czyta jakiś artykuł, to tam zwroty w pierwszej osobie, raczej powinny zostać zachowane w oryginale.

Patrząc na te złożoności, łatwo dochodzimy do wniosku o którym mówiłem wcześniej. Projektując asystenta, raczej będzie nam zależało na zachowaniu precyzji oraz niskiego poziomu złożoności poszczególnych elementów. Oczywiście całość może układać się w zaawansowany i rozbudowany system, jednak jego poszczególne elementy, dobrze jest utrzymywać możliwie prostymi. Przynajmniej na ten moment, bo nie wiadomo co przyniesie nam przyszłość i dalszy rozwój modeli oraz narzędzi do pracy z nimi. 

## Uniwersalne generowanie odpowiedzi i dynamiczny prompt

Budowanie dynamicznych promptów pojawiało się wielokrotnie na przestrzeni AI_devs. Jednak prawdziwy sprawdzian umiejętności czeka nas w miejscu, w którym dochodzi do generowania ostatecznych odpowiedzi poprzez prompt, którego elementy mogą zawierać dynamiczne treści. Czasem warto rozważyć tutaj wprost stosowanie kilku promptów i dobieranie ich automatycznie na podstawie wcześniejszej logiki. W moim przypadku zdecydowałem się jednak na ten pierwszy scenariusz w którym uwzględniam ogólne zasady komunikacji i jedynie biorę pod uwagę pojedyncze fragmenty, które mogą okazać się istotne z punktu widzenia skuteczności odpowiedzi. 

![](https://cloud.overment.com/aidevs_prompt-514310fa-9.png)

Końcowy prompt może bardzo szybko zwiększać swoją objętość, co z różnych powodów nie jest wskazane. Mowa nie tylko o limitach zapytania, ale także zdolności utrzymania uwagi na rzeczach, które są istotne dla aktualnej wypowiedzi. Gdy w takim prompcie umieścimy zbyt wiele informacji, to nawet jeśli wśród nich znajdą się właściwe, to i tak mogą zostać one pominięte przez model. W niektórych sytuacjach może to także zwiększyć ryzyko halucynacji, ponieważ np. asystent pomiesza różne fakty, generując odpowiedź, która nie ma większego sensu. 

Z punktu widzenia interakcji z asystentem, do gry wchodzi tutaj nie tylko sam prompt, ale także pozostałe wiadomości obejmujące aktualną konwersację. To właśnie z tego powodu **zapisuję ich treść w bazie danych**, ponieważ pozwala mi to na ich późniejsze wczytanie tylko poprzez podanie identyfikatora wybranej rozmowy w nagłówku zapytania.

![](https://cloud.overment.com/aidevs_messages-055f62d1-1.png)

Poniżej widać, jak moja pierwsza wiadomość otworzyła konwersację (wskazuje na to fragment "no messages"). Przy wysyłaniu drugiej wiadomości, do nagłówka zapytania (x-conversation-id) dodałem UUID zwrócony w poprzedniej wiadomości. Dzięki temu Alice pobrała wcześniejsze wiadomości i dzięki temu była zdolna do tego, aby powtórzyć to, co powiedziała chwilę wcześniej. Jak wiemy w interakcji z modelem, w przypadku konwersacji przesyłamy całą ich treść i możemy to robić tak długo, aż nie ograniczy nas limit zapytania. 

![](https://cloud.overment.com/aidevs_thread-7d30f9c3-4.png)

Na potrzeby tej aplikacji pominąłem już obsługę limitów i liczenia tokenów, tym bardziej, że mamy już narzędzia umożliwiające ich liczenie. 

Znacznie trudniejszym wątkiem do zaadresowania jest tutaj fakt, że jeśli jakaś treść **nie pojawi się w treści wiadomości**, to w późniejszej interakcji nie będzie już dostępna. O tym problemie również już mówiłem we wcześniejszych lekcjach sugerując, aby **zawsze brać pod uwagę nie tylko bieżące zapytanie, ale kontekst aktualnej konwersacji w postaci chociażby podsumowania**, którego treść może być wykorzystana przy wyszukiwaniu wspomnień.

## Interakcja głosowa

Whisper to prawdopodobnie najlepszy model do zamiany audio na tekst, posiadający także wsparcie dla języka polskiego. Z kolei zamianę tekstu na głos (TTS) świetnie realizują zarówno OpenAI, jak i ElevenLabs. Sam korzystam z tego drugiego, ponieważ obecnie barwy głosu oraz tempo działania (szczególnie dla modelu eleven_turbo_v2) są zdecydowanie lepsze. W praktyce dość łatwo zamienić te usługi, ponieważ wspierają dokładnie te same formaty danych i różni je tylko lista parametrów dla zapytania. 

Nasze zadanie polega więc na tym, aby za pomocą uprawnień przeglądarki przechwycić nagranie audio, zamienić je na tekst i przesłać do API. W związku z tym, że zadbałem o strumieniowanie odpowiedzi ze strony modelu, mogę wykorzystać ten fakt, aby rozpocząć generowanie nagrania audio, jeszcze na długo przed zakończeniem odpowiedzi API.

Przygotowałem więc bardzo prosty interfejs, umożliwiający zrealizowanie tych wszystkich kroków. Oznacza to, że mamy teraz możliwość głosowej interakcji z Alice. 

![](https://cloud.overment.com/aidevs_ui-121fd600-6.png)

Bardzo zależało mi na tym, aby przechwycić audio bezpośrednio w przeglądarce i nie angażować w tym celu serwera czy zewnętrznych usług. Niemal cały kod, który znajdziesz na front-endzie został wygenerowany przez GPT-4, aczkolwiek odbyło się to fragmentami i przypomina to klasyczny spaghetti-code. Przymknijmy jednak na to oko i w zamian skupmy się na funkcjonalności, która: 

- Po wciśnięciu "Speak to me" rozpoczyna nagranie
- Po zakończeniu nagrania, do gry wchodzi Whisper, który zamienia je na tekst
- Wiadomość zostaje przesłana do naszego API
- API strumieniuje odpowiedź ze strony OpenAI i robi to tokenami, które są ze sobą łączone i gdy pojawi się w nich znak zapytania, wykrzyknij, przecinek czy kropka, łączymy je w fragment. Fragment przesyłany jest do ElevenLabs bądź modelu TTS od OpenAI
- Odpowiedź zawierająca nagranie audio z wyżej wymienionych usług, zostaje odtworzona fragmentami w przeglądarce. Wykorzystuję w tym celu prostą kolejkę, aby całość była możliwie płynna

Logika budowania fragmentów na potrzeby wygenerowania audio jest stosunkowo prosta, jednak największą rolę odgrywa w niej tempo strumieniowania materiału audio, co praktycznie w całości zależy od wybranej przez nas usługi. 

![](https://cloud.overment.com/aidevs_el-7987527f-b.png)

Gdy tylko ElevenLabs lub TTS zwraca audio, dodaję je do kolejki, która uruchamiana jest natychmiast, ale następne fragmenty oczekują na zakończenie odtwarzania wcześniejszych. W ten sposób zwykle tylko na początku słychać przerwy pomiędzy nimi, co i tak wypada całkiem naturalnie. 

![](https://cloud.overment.com/aidevs_stream-6c23c436-a.png)

## Posługiwanie się długoterminową pamięcią

W trakcie AI_devs udostępniłem pliki markdown zawierające główną treść lekcji. Można je pobrać [tutaj](https://bravecourses.circle.so/c/informacje/tresci-lekcji-w-formacie-tekstowym-markdown) w wersji polskiej i angielskiej. Następnie wykorzystałem ten fakt, aby zbudować prosty system RAG umożliwiający interakcję z tą treścią. Jego kod udostępniłem [tutaj](https://bravecourses.circle.so/c/informacje/porozmawiaj-z-trescia-ai_devs-2-czyli-system-rag-dzialajacy-na-lekcjach-naszego-szkolenia). 

Naturalnie zdecydowałem się wykorzystać powyższe fakty, aby wyposażyć Alice w pamięć długoterminową w postaci treści lekcji AI_devs 2. To właśnie z tego powodu wybrałem Faiss i SQLite jako bazę wektorową i relacyjną, które można zapisać w pliku. Dzięki temu mogę teraz udostępnić zarówno jedno, jak i drugie w stosunkowo prosty sposób. Niestety, ta decyzja wiąże się także z ograniczeniami, w postaci chociażby braku możliwości filtrowania wyników na etapie wyszukiwania, a to negatywnie odbija się na skutecznym odzyskiwaniu wspomnień. Jednocześnie bardzo widoczne są najważniejsze koncepcje, które poruszaliśmy w AI_devs i których możemy doświadczyć w praktyce bez konieczności indeksowania ~500 000 znaków treści lekcji. Uruchomienie aplikacji będzie generować wyłącznie koszty związane z przetwarzaniem kolejnych zapytań oraz przetwarzaniem audio. 

![](https://cloud.overment.com/aidevs_content-9aa6845b-d.png)

Przykład głosowej interakcji można zobaczyć poniżej. Alice z powodzeniem była w stanie wskazać lekcję w której omówiliśmy konfigurację własnej domeny oraz VPS. Naturalnie mógłbym wykorzystać w tym celu również język polski lub przynajmniej zamienić anglojęzyczną wersję lekcji, jednak i tak widzimy całkiem przyzwoity rezultat. 

https://vimeo.com/887751701

Należy jednak pamiętać, że w tej chwili polegamy niemal wyłącznie na Similarity Search, co z pewnością będzie niewystarczające do bardziej złożonych zapytań. Powinno jednak wystarczyć do przeprowadzenia całkiem interesującej dyskusji na tematy poruszane w ramach szkolenia. Oczywiście nic też nie stoi na przeszkodzie, aby poruszyć z Alice inne wątki, ponieważ może skorzystać z bazowej wiedzy modelu GPT-4.

## Uruchomienie aplikacji

Kod źródłowy aplikacji, którą dziś omawiamy, dołączyłem do repozytorium [2nd-devs](https://github.com/i-am-alice/2nd-devs) do katalogu 31_alice. Jednak do uruchomienia aplikacji, konieczne jest podjęcie kilku dodatkowych kroków: 

- pobierz najnowszą wersję repozytorium i zainstaluj zależności
- w katalogu 31_alice/memory umieść indeksy z [tego archiwum](https://cloud.overment.com/indexes-1700764977.zip)
- w katalogu 31_alice/aidevs umieść plik memories.json [z tego archiwum](https://cloud.overment.com/aidevs_memories-1700765094.zip)
- w pliku 31_alice/views/index.js dodaj klucze API do OpenAI oraz ElevenLabs (tylko klucz OpenAI jest wymagany)
- uruchom aplikację poleceniem **bun 31 -- watch** i przejdź na localhost:3000

Uwaga: Kod nie jest produkcyjny i służy wyłącznie celom edukacyjnym. Uruchomienie aplikacji będzie generować koszty (przy obecnych modelach i cenach nie powinny być duże, ale to zależy od tego, jak intensywnie będziemy pracować). Dodatkowo praktycznie brak typów TypeScript również jest uzasadniony tym, że nie widziałem w tej chwili potrzeby ich pisania. 

Napisany przeze mnie kod, prawdopodobnie momentami można przenieść do swoich projektów. W takiej sytuacji zdecydowanie warto rozważyć przeniesienie się do postgreSQL czy MongoDB (według preferencji) oraz np. Qdrant. Niewykluczone, że obecne w kodzie prompty nie są doskonałe, ponieważ są znaczną modyfikacją tych, które stosuję w Alice i nie miałem możliwości ich w pełni przetestować. Ponownie jednak mogą stanowić pewien kierunek, który pomoże w kształtowaniu własnych promptów. Zasadniczo cały projekt może stanowić bardzo solidną podstawę do rozwijania swojego własnego asystenta, lub narzędzi wykorzystujących LLM. 

## To dopiero początek

Nie bez powodu dzisiejsza lekcja nosi tytuł "Początek". Jeśli czytasz te słowa, to z dużym prawdopodobieństwem AI i Duże Modele Językowe odegrają w Twoim życiu istotną rolę. Niezależnie od kierunku na jaki się zdecydujesz, jest kilka kroków, które możesz podjąć już teraz. 

Ostatnie tygodnie, a nawet dni, wyraźnie pokazują, że technologia zmienia się bardzo szybko. Choć fundamenty przez ostatni rok pozostawały takie same, tak nieustannie pojawiają się nowe narzędzia i możliwości, po które warto sięgać. Z tego powodu szczerze polecam Ci zbudowanie własnego systemu wiedzy wykorzystującego AI. Powinien być on opracowany z myślą o Tobie, Twoich zainteresowaniach, nawykach i stylu działania. W ten sposób zbudujesz (lub wzmocnisz) zwinny fundament, który pozwoli Ci, podobnie jak mi, swobodnie poruszać się w Erze Sztucznej Inteligencji. 

Na każdym kroku powtarzam jeden z cytatów, mówiących o tym, że **sekret tajemnicy nie leży w tym, co wiesz, lecz w tym, co z tym robisz**. W tym przypadku mówimy o praktycznym zastosowaniu zdobytej wiedzy, budowaniu i popełnianiu wielu błędów. Dlatego jeśli chcesz, to możesz: 

- Przeanalizować kod aplikacji, którą dziś udostępniam
- Zrozumieć jej najważniejsze elementy, które były szczegółowo poruszane we wcześniejszych lekcjach
- Uruchomić aplikację zgodnie z instrukcją i porozmawiać z Alice testując jej obecne możliwości oraz wyraźne ograniczenia. Być może niektóre z nich jesteś w stanie zaadresować
- Skorzystać z fragmentów lub samych koncepcji do zbudowania JEDNEGO narzędzia, które pomoże Ci w życiu prywatnym bądź zawodowym
- Rozbudować to, co przygotowałem, dopasowując cały system do swoich potrzeb lub na jego podstawie zbudować coś od podstaw

Prawdopodobnie już teraz w firmie w której pracujesz, temat AI został poruszony lub niebawem będzie. Jeśli masz taką możliwość oraz chęć, to potencjalnie tam znajdziesz przestrzeń do rozwijania umiejętności i być może nawet eksplorowania AI w znacznie bardziej zaawansowanym zakresie.

## Podsumowanie AI_devs

Serdecznie dziękuję Ci, że wspólnie dotarliśmy od tego miejsca. Dziękuję Ci za Twój czas, zaangażowanie i obecność przez ostatnie tygodnie. 

Dla mnie ten projekt to ponad 1000 godzin pracy nad materiałami i wiele tygodni w których to zaangażowanie łączyło się z moją codzienną pracą i rozwojem moich firm. Jako współtwórca tego szkolenia oraz wielu innych wiem, że dzielenie się wiedzą w takiej formie pozwala mi eksplorować wybrane tematy na poziomie większym, niż kiedykolwiek. Z drugiej strony konieczne jest posiadanie praktycznego doświadczenia oraz samodzielne spotykanie możliwości i zderzanie się z problemami, którymi również mogłem podzielić się w naszych lekcjach. 

Prawdopodobnie nietrudno wyobrazić sobie to, jak wiele pracy włożyliśmy w opracowanie materiałów i poprowadzenie AI_devs. Z drugiej strony wprost niewyobrażalne jest to, jak bardzo jesteśmy wdzięczni Tobie za aktywny udział w szkoleniu oraz każdemu, kto pomógł nam w jego realizacji. 

Szczególne podziękowania kieruję również do: 

- Jakub Mrugalski
- Mateusz Chrobok
- Alice
- Bartłomiej Rozkrut
- Kacper Łukawski
- Bartek Pucek
- Bartek Rycharski
- Kasia Marcyniuk
- Grzegorz Róg
- Michał Wedlechowicz

Za świetną zabawę i zrealizowanie projektu, który zapamiętam do końca życia. Dziękuję! 🫶

## Zadanie praktyczne (z gwiazdką, dla chętnych)

To zadanie kierujemy **wyłącznie do osób chętnych**, chcących podzielić się swoimi wrażeniami na temat AI_devs publicznie poprzez dopisanie się do naszej Ściany Miłości. Wystarczy kilka zdań, w których możesz poruszyć swoje wrażenia przed dołączeniem, te które towarzyszyły Ci w trakcie oraz te, które są z Tobą teraz. Każdy kolejny wpis stanowi dla nas ogromną wartość zarówno personalną, jak i marketingową. Obie z tych rzeczy mają wpływ na nasze kolejne inicjatywy oraz, mam nadzieję, nasze kolejne spotkania. Nie czuj jednak presji i jeśli chcesz, napisz coś od siebie na EasyLove — [dopisuję się do Ściany Miłości AI_devs](https://app.easy.tools/love/ask/68a7fc7cc8444718ab3f0469f3eb9d8c). 

Dodam także, że jeśli chcesz podzielić się opinią o AI_devs anonimowo, to w najbliższych dniach wyślemy także formularz w którym będzie można to zrobić.


#aidevs_2