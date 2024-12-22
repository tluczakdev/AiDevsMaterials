![](https://cloud.overment.com/1-1697475372.png)

# C01L01 — Wprowadzenie do Generative AI

Generatywna Sztuczna Inteligencja to obszar Sztucznej Inteligencji, skupiający się na generowaniu np. tekstu, kodu, obrazów czy filmów. Trenowana na dużych zestawach danych, jest zdolna do tworzenia nowych treści. W rezultacie, Generatywna AI (GenAI) może odgrywać ogromną rolę w procesach kreatywnych, w tym także w rozwijaniu oprogramowania. Obecnie jednym z najpopularniejszych przykładów takich narzędzi są Duże Modele Językowe (Large Language Models, LLM), takie jak rodzina modeli GPT od firmy OpenAI, na której skupimy naszą uwagę w nadchodzących tygodniach.

W związku z tym, że rozwój umiejętności związanych z wykorzystaniem LLM jest znacznie prostszy niż nauka programowania, już na starcie masz ogromną przewagę. Co podkreśla [cytat Grega Brockmana, współzałożyciela OpenAI](https://twitter.com/gdb/status/1692699977628242279).

![](https://cloud.overment.com/greg-d17870c5-8.png)

Jego stwierdzenie, że to osoby potrafiące programować mogą mieć największy wpływ na AI, jest bardzo widoczny w praktyce. Jak niebawem się przekonasz, **połączenie GPT-3.5-Turbo oraz GPT-4 z kodem pozwoli Ci adresować obecne ograniczenia modeli oraz połączyć je z zewnętrznymi usługami, znacznie rozszerzając dostępne możliwości.** Aby to jednak było możliwe, konieczne jest zrozumienie technologii, narzędzi oraz technik pracy, które pozwolą Ci wyjść znacznie dalej niż rozmowa z ChatGPT. Z tego powodu niemal całkowicie pominiemy zarówno ChatGPT, jak i narzędzia wspierające kodowanie (np. Github Copilot). W zamian **skupimy się na bezpośredniej integracji OpenAI API z kodem aplikacji oraz dodatkiem w postaci automatyzacji**.

## Obecne możliwości Dużych Modeli Językowych

Modele Językowe projektowane są z myślą o tworzeniu treści na podstawie wejściowych danych oraz podążaniem za instrukcjami w kontekście czatu. W wyniku (prawdopodobnie) ogromnej skali danych wykorzystywanych do trenowania dużych modeli językowych, mamy do czynienia ze zjawiskiem tzw. **emergencji** związanym z [pojawianiem się zachowań nieobecnych w przypadku mniejszych modeli](https://arxiv.org/abs/2206.07682). Przykładem może być zdolność do tłumaczeń z jednego języka na inny, pomimo tego, że model nie był dokładnie do tego trenowany (ale oczywiście miał kontakt z tymi językami). 

Obecność emergencji w LLMach może wyraźnie sugerować, że **wszystkie możliwości modeli, z którymi obecnie mamy do czynienia, nie są nam jeszcze znane**. Tym bardziej, że mówimy tutaj o zachowaniach, które mogą zaskakiwać także twórców OpenAI. Przykładem jest fragment [GPT-4 Technical Report](https://cdn.openai.com/papers/gpt-4.pdf), omawiający **nieoczekiwany wzrost skuteczności w zadaniach "Hindsight Neglect"** związanych z odróżnianiem przewidywania od faktycznej odpowiedzi w obliczu znajomości rezultatu.

![](https://cloud.overment.com/gpt-4-paper-e0f2c1a3-0.png)

To jednak nie oznacza, że nie znamy zastosowań dużych modeli językowych oraz scenariuszy, w których sprawdzają się one wprost genialnie. Oto kilka przykładów:

- **Wcielanie się w różne role niczym kameleon**, co jednocześnie nadaje kontekst interakcji, dzięki któremu uwaga modelu skupia się na wybranym zagadnieniu (np. definicje występujące w różnych dziedzinach są dzięki określeniu roli asystenta postrzegane jednoznacznie).
- **Transformacje dostarczonych treści**, np. tłumaczenia, korekty, analizy i podsumowania, uwzględniające kontekst przetwarzanych dokumentów.
- **Parsowanie danych**, uwzględniając zadania, które są bardzo trudne do zrealizowania programistycznie, np. za pomocą wyrażeń regularnych.
- **Odpowiadanie na pytania i generowanie treści** na podstawie danych przekazanych jako kontekst zapytania.
- **Zadania związane z programowaniem** uwzględniają tworzenie, modyfikowanie, wyjaśnianie, oraz debugowanie kodu.
- **Integracja z kodem aplikacji** i zastosowanie biznesowe czynią LLMi użytecznymi narzędziami, które pozwalają realizować zadania związane z przetwarzaniem języka naturalnego (eng. Natural Language Processing, NLP).
- **Posługiwanie się API**, w szczególności w kontekście [Function Calling](https://openai.com/blog/function-calling-and-other-api-updates), oraz wersji modeli OpenAI wyspecjalizowanych w wyborze funkcji i generowaniu do nich parametrów.

Obecnie, ze względu na ograniczenia modeli, o których powiemy za chwilę, znacznie lepsze rezultaty otrzymamy w przypadku zadań realizowanych **na podstawie dostarczonego kontekstu** i istniejących danych. Przykładowo, zamiast oczekiwać, że GPT-4 napisze dla mnie tekst, który właśnie czytasz, wykorzystuję go jedynie w celu korekty tego, co już napisałem.

Podobnie wygląda to także w przypadku odpowiedzi na pytania, których jakość jest **nieporównywalnie lepsza** gdy dotyczą one treści dołączonych do zapytania, niż gdy odpowiedź generowana jest wyłącznie na podstawie bazowej wiedzy modelu, która obecnie często bywa nieaktualna.

Ostatecznie prawdopodobnie najważniejszą z umiejętności Dużych Modeli Językowych jest posługiwanie się zewnętrznymi usługami, narzędziami czy urządzeniami. W praktyce mówimy tutaj o **generowaniu obiektów JSON**, które programistycznie są przesyłane jako payload zapytania HTTP. Niemałą rolę pełni także zdolność do **podejmowania decyzji na temat tego, którą z funkcji należy uruchomić**, a nawet **planowania realizacji zadania, z uwzględnieniem dostępnych narzędzi**. Wówczas dochodzimy do koncepcji tzw. "Agenta" zdolnego do **autonomicznego (lub częściowo autonomicznego) realizowania zadań**, czego przykładem jest chociażby projekt [Aider](https://github.com/paul-gauthier/aider).

![źródło: Github](https://cloud.overment.com/screencast-1693383991.svg)

Pełne wykorzystanie tego, co oferują LLM-y, wymaga **połączenia ich z kodem aplikacji**. Nie mówimy tutaj jedynie o połączeniu z API OpenAI czy modelami OpenSource (np. LLaMA2). Do gry wchodzi tutaj projektowanie całej interakcji, uwzględniającej m.in.:

- **przetwarzanie różnych formatów danych** i ich organizację na potrzeby LLM (np. podział na mniejsze fragmenty, wzbogacanie, opisywanie)
- **wykorzystywanie dynamicznego kontekstu** (eng. [Retrieval Augmented Generation](https://research.ibm.com/blog/retrieval-augmented-generation-RAG), RAG) oraz długoterminowej pamięci dzięki połączeniu baz wektorowych (Pinecone / Qdrant / Supabase) oraz klasycznych silników wyszukiwania (Algolia / ElasticSearch). Przykładem może być [Quivr](https://github.com/stangirard/quivr).
- **projektowanie złożonych interakcji** wykraczających poza pojedyncze zapytania do modelu (np. w modelu [ReAct](https://react-lm.github.io/)), uwzględniających korzystanie z różnych narzędzi.
- **łączenie ze sobą wielu modeli** wyspecjalizowanych w realizowaniu konkretnych zadań (np. przetwarzaniu lub generowaniu grafik, audio czy wideo). Przykładem mogą być usługi takie jak [ElevenLabs](https://www.elevenlabs.io) czy [Replicate](https://replicate.com).
- **trenowanie i fine-tuning modeli** w celu zaadresowania konkretnych rodzajów zadań i sposobów ich wykonania, oraz ogólnego zwiększenia skuteczności modelu w wybranych obszarach.

Projektowanie takich aplikacji wymaga także podejmowania szeregu dodatkowych zadań związanych z **monitorowaniem, moderacją, testowaniem i optymalizacją** systemu. Mając na uwadze to wszystko, zrozumiała powinna być teraz perspektywa Grega Brockmana, mówiąca o roli osób potrafiących programować. Zdobycie programistycznych umiejętności umożliwiających wykorzystywanie dużych modeli językowych jest nieporównywalnie trudniejsze od jedynie rozszerzenia swojej wiedzy o techniki pracy z nimi.

## Obecne ograniczenia Dużych Modeli Językowych

Trenowanie LLM jest czasochłonne i wymaga przygotowania dużych zestawów danych. To z tego powodu bazowa wiedza modeli OpenAI kończy się w połowie 2021 roku i nie zawiera całej dostępnej wiedzy, a jedynie wybrane fragmenty (chociaż według promptu ChatGPT, wiedza uwzględnia już dane do Stycznia 2022). Dodatkowo, sama charakterystyka modeli wiąże się z różnego rodzaju ograniczeniami, które po części możemy adresować lub omijać, a na inne nie mamy odpowiedzi.

> ⚠️ Poniższe przykłady pochodzą z narzędzia [Playground](https://platform.openai.com/playground), które omówimy bardziej szczegółowo w kolejnych lekcjach. Jeśli chcesz skorzystać z niego już teraz, upewnij się, że jesteś w trybie "Chat" i aktywny model to GPT-4.

**Ograniczenie bazowej wiedzy i halucynacje**

Brak dostępu do dowolnej wiedzy jest jednym z największych ograniczeń LLM, które wydaje się wykluczać je z zastosowania np. podczas pracy z nowymi narzędziami, na temat których model nie ma pojęcia. Jest to jedna z głównych przyczyn tzw. **halucynacji modelu**, w wyniku której odpowiedzi nie tylko nie zawierają poprawnych informacji, ale wyglądają tak, jakby były poprawne, przez co trudno na pierwszy rzut oka je zauważyć. Przykładowo, według GPT-4 najnowsza wersja macOS to Monterey (aczkolwiek taka odpowiedź nie zawsze się pojawi, o czym powiem za chwilę, omawiając niedeterministyczną naturę modeli).

![](https://cloud.overment.com/lie-de197701-9.png)

🔗 [Zobacz przykład](https://platform.openai.com/playground/p/ygwvpyvpS61L6l7qdFvxqdBa?model=gpt-4)

Istnieją różne sposoby na zmniejszenie ryzyka takich sytuacji. Jednym z nich jest modyfikacja promptu (instrukcji dla modelu), aby podkreślała prawdomówność oraz bezpośrednie informowanie o braku dostępnej wiedzy.

![](https://cloud.overment.com/truthful-80bfaa92-a.png)

🔗 [Zobacz przykład](https://platform.openai.com/playground/p/EbzMyCreijOAwU6WpXKVkoHH?model=gpt-4)

Do realizacji niektórych zadań mamy możliwość **dostarczenia dodatkowej wiedzy do modelu w postaci kontekstu zapytania.** Jeżeli tylko jest to możliwe, warto to zrobić oraz dodatkowo podkreślić, że generowane odpowiedzi muszą wykorzystywać wiedzę z kontekstu. Wówczas np. nasze pytanie o wersję systemu jest poprawne, ponieważ powiązana z nim informacja została dołączona do zapytania. 

![](https://cloud.overment.com/context-7e3f6047-d.png)

🔗 [Zobacz przykład](https://platform.openai.com/playground/p/TktWqRIyaqDdhqKM2kN2VjGV?model=gpt-4)

Poza informacją o najnowszej wersji systemu, dołączyłem także aktualną datę. Zrobiłem to nie bez powodu, ponieważ podstawowa wersja modelu GPT-4 nie wie nawet "kiedy jest dziś", o ile nie dołączymy tej informacji do kontekstu. Jednocześnie widzisz już, że model świetnie radzi sobie z **wykorzystywaniem informacji zawartych w kontekście**, co otwiera przed nami różne możliwości oraz jasno sugeruje, że **poza bazową wiedzą modelu, szczególnie ważne są także jego umiejętności związane z rozumowaniem, przetwarzaniem oraz transformacją treści**.

**Ograniczenie długości kontekstu**

Obecne LLM-y opierają się na architekturze modelu Transformer, zaprezentowanej po raz pierwszy przez Google w 2017 roku w publikacji [Attention Is All You Need](https://arxiv.org/abs/1706.03762). Wiąże się z nią zarówno szereg możliwości, jak i ograniczeń. Jednym z nich jest limit długości treści przetwarzanej w danej chwili, obejmujący zarówno **dane wejściowe, jak i te generowane przez model**, który określa się jako tzw. "Token Window".

![](https://cloud.overment.com/limit-fc93254f-3.png)

Limity różnią się w zależności od modeli. Obecnie mówimy o zakresie pomiędzy około 4 a 16 tys. tokenów (fragmentów słów). Są jednak wyjątki w postaci np. modelu [Claude](https://claude.ai/), który jest zdolny do pracy na 100 tys. tokenów w ramach pojedynczego zapytania.

Z długością kontekstu wiążą się jeszcze trzy dodatkowe wyzwania, z którymi będziesz się mierzyć podczas pracy z dużymi modelami językowymi. Są to:

- **Koszty** związane z przetwarzaniem i generowaniem tokenów, które szybko rosną, nawet na stosunkowo małej skali.
- **Wydajność**, która w dużym stopniu jest uzależniona od liczby tokenów w ramach zapytania.
- **Skuteczność**, która według publikacji [Lost In The Middle](https://arxiv.org/pdf/2307.03172.pdf), spada w przypadku jednorazowego przetwarzania dłuższych treści.

Wszystko to prowadzi nas do prostego wniosku, mówiącego o tym, że warto projektować swoje systemy tak, aby pracowały na możliwie małym zestawie informacji **istotnych dla bieżącego zadania**. Przykład poniżej pokazuje jak nieprawidłowe dobranie kontekstu sprawiło, że model nie był w stanie udzielić poprawnej odpowiedzi na pytanie (ale jego zachowanie jest zgodne z instrukcją). 

![](https://cloud.overment.com/wrong-e07d12c4-5.png)

🔗[Zobacz przykład](https://platform.openai.com/playground/p/csXdIoK1BetYGtvljtEAtFr7?model=gpt-4)

Wątek pracy z tokenami i długim kontekstem (np. prowadzeniem długich rozmów czy przetwarzaniem długich dokumentów) będziemy jeszcze poruszać wielokrotnie w kolejnych lekcjach. Na razie zapamiętaj jedynie, że **warto utrzymywać kontekst tak krótkim, jak to możliwe**, o ile nie tracimy jego sensu.

**Niedeterministyczna natura**

Do prezentowanych przykładów zadań realizowanych przez GPT-4 dołączam linki kierujące do Playground. Jeśli z nich skorzystasz to prawdopodobnie zauważysz, że przy **ponownych próbach wykonywania dokładnie tych samych instrukcji, wynik może ulegać zmianie**. Powodem jest **niedeterministyczna natura modeli**, która w przeciwieństwie do **Pure Function** znanych z programowania funkcyjnego, nie daje nam pewności, że dla tych samych danych uzyskamy dokładnie taką samą odpowiedź.

W kolejnej lekcji wyjaśnię Ci, skąd dokładnie to wynika. Tymczasem spróbuj przejść do poniższego przykładu i wykonać go kilkukrotnie (kasując wiadomość wygenerowaną przez asystenta). Wówczas zauważysz, że co jakiś czas generowana odpowiedź będzie różnić się od wcześniejszych. 

![](https://cloud.overment.com/nondeterministic-50751271-b.png)

🔗 [Zobacz przykład](https://platform.openai.com/playground/p/NIf99Z2wfcCGFDnqdgsXK6Av?model=gpt-4)

Takie zachowanie jest bardzo niepożądane w przypadku logiki aplikacji realizującej określone zadania. Dodatkowe wyzwania pojawiają się także na etapie wprowadzania modyfikacji do promptów oraz trudnych do przewidzenia danych wejściowych (np. w przypadku czatbotów nie możemy przewidzieć tego, co wpisze użytkownik). Strategie radzenia sobie z tym problemem omówimy w późniejszej części AI Devs. Do tego czasu zapamiętaj, że **możesz jedynie sterować zachowaniem modelu, ale nie masz pewności, że wygenerowany rezultat zawsze będzie zgodny z Twoimi założeniami**.

**Obliczenia i zadania logiczne**

Nowsze wersje modeli (np. GPT-4) [są coraz lepsze w zadaniach związanych z obliczeniami oraz złożonymi zadaniami logicznymi](https://openai.com/research/improving-mathematical-reasoning-with-process-supervision). Mając jednak na uwadze ich niedeterministyczną naturę oraz problemy związane z halucynacją, raczej kiepskim pomysłem jest wykorzystywanie ich do przeprowadzania istotnych obliczeń, szczególnie na dużych liczbach. Dlatego, jak widać, GPT-4 podaje całkowicie błędny wynik dla zapytania: "821^5". 

![](https://cloud.overment.com/count-0dd0311b-7.png)

🔗 [Zobacz przykład](https://platform.openai.com/playground/p/5eCXCJCRSLxeWP9wdbmiYN8z?model=gpt-4)

To jest przykład zadania, dla którego modele językowe po prostu **nie zostały stworzone**. Podobne problemy zaobserwujemy także przy przeliczaniu dat (np. GPT-4 nie zawsze wie, kiedy jest "za dwa tygodnie w środę"), co nierzadko przydaje się w praktycznych zastosowaniach LLM. Najlepszym sposobem na radzenie sobie w takich sytuacjach jest skorzystanie z narzędzi, które zostały stworzone z myślą o takich zadaniach. Przykładem może być model WolframAlpha, który bez najmniejszego problemu udziela poprawnej odpowiedzi na praktycznie dowolne obliczenia.

![](https://cloud.overment.com/wolfram-38154ac8-8.png)

Wspomniałem już, że GPT-4 jest zdolne do posługiwania się API poprzez generowanie obiektów JSON. W przypadku Wolfram Alpha bezpłatny plan umożliwia wykonanie 2000 zapytań miesięcznie (co nierzadko wystarcza do prywatnych zastosowań) w ramach dostępu do API. Możemy więc stworzyć prompt, którego zadaniem będzie **przeanalizowanie zapytania użytkownika i pobranie z niego informacji na temat zadania, które zostanie zrealizowane przez inny model**. Zwrócona odpowiedź może albo zostać bezpośrednio zwrócona użytkownikowi albo **sparafrazowana w czytelny sposób**. Dokładnie na tej samej zasadzie możemy wyposażyć GPT-4 w inne narzędzia, które będą w stanie adresować jego słabe strony. O konkretach pomówimy jednak przy okazji lekcji na temat Function Calling.

## Praktyczne zastosowanie LLM w kontekście prywatnym

Wymieniałem ogólne zastosowania Dużych Modeli Językowych, jednak może nie być jasne, jak przekładają się one na faktyczne zastosowanie w codziennym życiu. Pominę jednak te najbardziej oczywiste przykłady, które na co dzień widzimy w Internecie, i skupię się na tych, które jednoznacznie wynikają z **połączenia programowania z AI**.

### Bezpośrednia integracja komputera i API

Stały dostęp do możliwości oferowanych przez LLMy, a także przypisanie skrótów klawiszowych do wybranych zadań, pozwala znacznie przyspieszyć pracę. Zintegrowanie, na przykład GPT-4, opiera się na połączeniu z OpenAI API, które może być realizowane poprzez aplikacje takie jak: **Keyboard Maestro lub Shortcuts (macOS), AutoHotkey (macOS / Windows) rozwijana przeze mnie publiczna wersja aplikacji Alice (macOS / Windows).** Alternatywnie, można skorzystać z ulubionego komunikatora (np. Slack / Telegram / Discord) i zbudować prostego bota umożliwiającego rozmowę z modelami GPT. 

![](https://cloud.overment.com/translate-1691593854.gif)

Wartość takiej integracji charakteryzuje się tym, że rośnie z czasem. Nie mówimy tutaj wyłącznie o prostych zapytaniach kierowanych do OpenAI, ale o uwzględnieniu własnych rozszerzeń, które zwiększają możliwości modelu oraz dopasowują go do naszych potrzeb. Widoczny na powyższej animacji prosty przykład tłumaczeń, może zostać dopasowany tak, aby utrzymywał ton charakterystyczny dla naszego stylu wypowiedzi. Daje to modelowi znacznie większą przewagę nad korzystaniem z narzędzi takich jak Deepl, a także pozwala na uzupełnianie naszych wypowiedzi, wzbogacając je lub upraszczając. 

Drugim zastosowaniem własnej integracji z LLM jest API, dzięki któremu możemy na przykład dodawać różne informacje do pamięci długoterminowej. Jeden z przykładów umożliwia czytanie i zapamiętywanie treści stron dodanych do tablicy Feedly, które następnie mogą być wykorzystane w trakcie rozmowy z asystentem AI.

![](https://cloud.overment.com/feedly-3ca144b8-2.png)

### Połączenie z Internetem i Wyszukiwarkami

GPT-4 domyślnie nie posiada dostępu do Internetu. Narzędzia takie jak Bing Chat czy Perplexity to oferują, jednak nie mamy większego wpływu na sposób przetwarzania treści oraz dostępu do informacji. Stworzenie prostej własnej integracji, która pozwala na wyszukiwanie oraz czytanie treści stron, jest stosunkowo łatwe dzięki LangChain oraz połączeniu z Puppeteer czy Playwright. Dostęp do wyników wyszukiwania możemy uzyskać poprzez [SerpAPI](https://serpapi.com) lub prosty skrypt działający na przykład w połączeniu z DuckDuckGo.  

![](https://cloud.overment.com/reading-b5cf5865-8.png)

Efekt pozornie niewiele różni się od tego, który możemy osiągnąć na przykład w ChatBing. Jednak w praktyce daje nam to szereg przewag, takich jak: 

- Całkowita kontrola nad promptami odpowiedzialnymi za przetwarzanie całej treści strony lub jej wybranych fragmentów
- Możliwość zapisania historii konwersacji w celu jej wyszukiwania podczas przyszłych rozmów
- Zestawianie tych informacji z własną bazą wiedzy, co prezentuje przykład poniżej

![](https://cloud.overment.com/extending-d0ccc003-8.png)

Podobne rezultaty osiągniemy w ostatnim module naszego programu, w którym zaprojektujemy mechaniki osobistego asystenta. Jeżeli nie planujesz budować takich integracji, to wspomniane Perplexity jest obecnie najlepszym narzędziem oferującym dostęp do Internetu oraz niespotykanie szybkie generowanie wyników. Stworzenie własnego API pozwala jednak na znacznie większą personalizację oraz planowanie działań wykonywanych w tle (na przykład obserwowanie wybranych stron www). 

### Hiper-personalizacja dzięki długoterminowej pamięci

W związku z tym, że do kontekstu zapytania GPT-4 możemy dołączyć różne informacje, naturalnym wydaje się połączenie ich z naszymi bazami wiedzy oraz rozmawianie z ich treścią. Kluczowym aspektem stają się tutaj **organizacja oraz wyszukiwanie informacji**, które mogą być dynamicznie wstrzykiwane do kontekstu i wykorzystane w trakcie generowania odpowiedzi. 

Złożoność takich systemów rośnie wraz z rozbudową bazy wiedzy. Jak już pokazałem, umieszczenie w kontekście zbyt wielu informacji lub ich błędne dopasowanie negatywnie wpłynie na zachowanie modelu i tym samym na wartość udzielonej odpowiedzi. Poniższe pytanie o link do narzędzia podobnego do Ray.so sprawiło, że przeszukana została baza zawierająca **listę zasobów/linków**. Dzięki dopasowaniu słów kluczowych oraz zastosowaniu bazy wektorowej i similarity search, możliwe było odnalezienie rekordu, który najbardziej pasował do przesłanego zapytania.

![](https://cloud.overment.com/integration-150734ca-8.png)

Możesz jednak uznać, że takie wyszukiwanie może zostać zrealizowane prostym dopasowaniem słów kluczowych i nie potrzebujemy tutaj AI. Sytuację zmienia jednak kolejny przykład, w którym wyjaśnienie cytatu zostało **zestawione z wiedzą na mój temat**, czyniąc wiadomość bardziej personalną i rezonującą.

![](https://cloud.overment.com/explain-574b3532-b.png)

Interakcja z GPT-4, który posiada dostęp do wiedzy na nasz temat (na przykład na podstawie wcześniejszych interakcji), pokazuje prawdziwy potencjał tej technologii. Odpowiednio zaprojektowany system może nawet prowadzić nas przez proces zdobywania konkretnych umiejętności czy nawyków, opierając się na naszym dotychczasowym postępie. 

### Połączenie z usługami i urządzeniami

Ostatnim z ważniejszych zastosowań prywatnych jest połączenie z różnymi usługami, na przykład listą zadań, kalendarzem, aplikacją do notatek, pocztą e-mail, komunikatorami czy własnymi skryptami realizującymi różne zadania. W rozmowie poniżej, **rozpoznano wydanie polecenia**, które następnie zostało skojarzone z **akcją dodawania zdarzeń do kalendarza**. W związku z tym, model wygenerował obiekt JSON opisujący wprowadzane zmiany oraz odpowiedź potwierdzającą wykonanie zadania. 

![](https://cloud.overment.com/calendar-ce767b2d-e.png)

Wyposażenie modeli językowych w narzędzia pozwala na zwiększenie ich użyteczności oraz zredukowanie halucynacji. Doskonałym potwierdzeniem tych słów jest [publikacja na temat ToolLM](https://arxiv.org/abs/2307.16789) — modelu wyspecjalizowanego w posługiwaniu się API na podstawie dokumentacji. 

Realizacja takich integracji jest możliwa poprzez połączenie wspomnianego już Function Calling oraz logiki aplikacji odpowiadającej za faktyczne połączenie z usługami lub urządzeniami. Co ciekawe, w tym ostatnim obszarze, genialnie sprawdzają się rozwiązania no-code/low-code, które pozwalają szybko iterować oraz łączyć się z różnymi usługami, [co szczegółowo omawia ta publikacja](https://arxiv.org/abs/2304.08103).

## Praktyczne zastosowanie LLM w kontekście zawodowym i biznesowym

Duże Modele Językowe sprawdzają się nie tylko w wymiarze prywatnym, ale przede wszystkim biznesowym. Należy jednak nadal mieć na uwadze fakt, że obecnie na rynku nie ma jeszcze dojrzałych narzędzi, frameworków czy wzorców projektowych pozwalających na swobodne rozwijanie aplikacji łączących kod z LLM. Doskonale obrazuje to slajd z prezentacji [State of GPT](https://youtu.be/bZQun8Y4L2A?t=2244), mówiący m.in. o:

- Wykorzystywaniu w **niekrytycznych** obszarach pod nadzorem człowieka
- Zastosowaniu jako źródło inspiracji lub sugestii
- Preferowaniu copilotów / asystentów niż autonomicznych rozwiązań

![](https://cloud.overment.com/state-a10c5ebd-4.png)

Mówiąc o tym bardziej obrazowo, GPT-4 **nie jest jeszcze w pełni gotowy do zastosowań produkcyjnych** i obecnie sprawdzi się jako ich **uzupełnienie** lub do prywatnych zastosowań. Główne argumenty, które potwierdzają takie nastawienie to: 

- Brak narzędzi do sterowania zachowaniem modelu, na których można polegać. Wynikają z tego różnego rodzaju problemy, sięgające nawet w obszar bezpieczeństwa, ponieważ model może podejmować działania niezgodne z założeniami. Przykładem może być wygenerowanie przez chatbota nieprawdziwej odpowiedzi.
- Dostępność API nadal jest niewystarczająca do biznesowych zastosowań wymagających wysokiego SLA. Istnieje jednak (trudno dostępna w EU) opcja dostępu do modeli OpenAI w ramach Microsoft Azure lub poprzez [ChatGPT Enterprise](https://openai.com/blog/introducing-chatgpt-enterprise) (wymagają skali powyżej $10 000 według mojej rozmowy z OpenAI. Nie wiem jednak, czy jest to indywidualna oferta czy ogólna reguła).

Sam posiadam różne narzędzia wykorzystujące modele OpenAI, które pomagają mi w pracy, ale absolutnie nie nadają się do udostępnienia innym, nietechnicznym użytkownikom. Jako programista, dysponuję wiedzą na temat pracy z modelami oraz w razie potrzeby mogę swobodnie wprowadzać potrzebne zmiany w promptach oraz w kodzie. W przypadku produkcyjnych zastosowań, nie byłoby to już tak proste i przykładowo, utrzymanie stabilności aplikacji stanowiłoby dość duże wyzwanie (aczkolwiek, w praktyce zależy to od realizowanego projektu).

Naturalnie, **nie oznacza to, że zastosowanie GPT-4 w niektórych obszarach produkcyjnych nie jest możliwe**. W praktyce jednak, niemal wszystkie obecnie dostępne na rynku produkty, przy bliższym poznaniu, okazują się niedopracowane i, co gorsza, nie realizują oferowanej wartości. Ostatecznie, nie powinno stanowić to dużego zaskoczenia, ponieważ mówimy o zastosowaniu technologii, która zaczęła zdobywać popularność kilka miesięcy temu. Więcej na temat zastosowań produkcyjnych w kontekście biznesowym powiemy w lekcjach związanych z automatyzacją połączoną z AI oraz podczas projektowania własnego asystenta AI.

### AI oraz automatyzacja procesów, organizacja i dostęp do wiedzy, rozwój produktów i ich funkcjonalności

LLM mają duży potencjał w kontekście zastosowania ich w obszarze automatyzacji procesów biznesowych oraz rozwoju produktów. W praktyce jednak, **bardzo łatwo jest zbudować prototyp**, a dojście do działającego produktu zajmuje dużo czasu. Poza wymienionymi przed chwilą wyzwaniami, wdrożenie rozwiązań AI do istniejących procesów wiąże się z podjęciem wymagających działań. Mowa tutaj między innymi o:

- Poznanie samej technologii przez zespół. Zdobywanie wiedzy przez top-level management oraz osoby odpowiedzialne za faktyczne wdrożenie AI zajmuje czas. Obecnie na rynku brakuje wiedzy i jakościowych szkoleń kierowanych nie tylko dla programistów. Poza dostępnością wiedzy, do gry wchodzi jeszcze czas potrzebny na faktyczne zdobycie umiejętności oraz późniejsze praktyczne doświadczenie.
- Zazwyczaj wdrożenie rozwiązań AI wymaga zgromadzenia i przetworzenia różnego rodzaju danych (bazy wiedzy, dokumentacje, opisy procesów, standardy itd.), co jest żmudnym i czasochłonnym zajęciem. Zwykle wyzwanie stanowi tutaj **rozproszenie danych w różnych usługach oraz formatach**. Np. odczytanie danych z dokumentów PDF, nawet w połączeniu z AI, nie jest proste. Po zgromadzeniu danych konieczne jest ich przetworzenie i przygotowanie na potrzeby modeli językowych (kategoryzacja, tagowanie, wzbogacanie, dzielenie na mniejsze fragmenty) w sposób umożliwiający ich aktualizację. Nierzadko mówi się, że wdrożenie AI to przede wszystkim praca związana z organizacją danych. 
- Już na etapie developmentu pojawiają się wyzwania związane z kosztami usług dostawców zarówno modeli (np. OpenAI) jak i zewnętrznych API (np. Pinecone). Rozliczanie w modelu uzależnionym od zużycia generuje dodatkowe koszty dla każdej osoby zaangażowanej w development. Optymalizacja zajmuje czas i wymaga wiedzy związanej nie tylko z projektowaniem promptów, ale także optymalizacji mechanizmów wyszukiwania czy przetwarzania treści.
- Aplikacja działająca na produkcji wymaga stałego monitorowania oraz podjęcia dodatkowych kroków związanych z moderowaniem danych wejściowych pod kątem zgodności np. z polityką OpenAI oraz założeniami naszego oprogramowania (np. nie chcemy, aby czatbot przyjmujący zamówienia był w stanie rozmawiać na inne tematy). Do tego dochodzi także możliwość minimalizowania ryzyka wygenerowania niepoprawnych odpowiedzi (halucynacji modelu) oraz obsługa błędów i przypadków brzegowych.
- Rozwój aplikacji wykorzystujących LLM generuje także problemy w związku z wprowadzaniem modyfikacji i budowaniem nowych funkcjonalności. W przeciwieństwie do kodu, nie mamy tutaj jeszcze sensownych narzędzi umożliwiających testowanie promptów w celu upewnienia się, że aktualizacja nie wprowadza regresji w aplikacji. Problem ten zaczyna być adresowany poprzez narzędzia takie jak [LangSmith](https://smith.langchain.com/) (jest jeszcze na bardzo wczesnym etapie rozwoju).

Podstawowe źródła na temat zastosowań produkcyjnych można znaleźć [w dokumentacji OpenAI](https://platform.openai.com/docs/guides/safety-best-practices). Jest to jednak wierzchołek góry lodowej, dlatego w miarę możliwości podzielę się własnymi doświadczeniami "z produkcji" na przestrzeni całego kursu AI Devs. Chciałbym jednak podkreślić, że nie na wszystkie pytania mamy odpowiedzi (nie tylko jako twórcy kursu, ale nawet OpenAI nie ma rozwiązań na np. prompt injection).

Powyższe punkty mogą pozostawić Cię z wrażeniem, że jakiekolwiek zastosowanie LLM w kodzie produkcyjnym jest niemożliwe. Nie jest to prawda i z powodzeniem LLM sprawdzą się do: 

- Zastosowań wewnętrznych, np. narzędzi obsługiwanych przez osoby posiadające wiedzę na temat pracy z nimi.
- Systemach ograniczających dowolność wprowadzanych danych i sposobu prezentowania odpowiedzi. Często wiąże się to z zastosowaniem AI "w tle". Przykładem może być przycisk "dopasuj kolor z AI", który przeanalizuje obraz i wygeneruje dla niego paletę kolorów zgodnie ze zdefiniowaną instrukcją.
- Funkcjonalnościach stanowiących wsparcie, niepełniących krytycznej roli oraz realizujących jasno zdefiniowany proces, który łatwo monitorować. Przykładem może być wzbogacanie bądź klasyfikowanie treści lub zaawansowane mechanizmy sugerujące treści na podstawie dopasowań niemożliwych (bądź trudnych) do zrealizowania z pomocą kodu.
- Wdrożeniach uwzględniających trenowanie i/lub fine-tuning modeli, w celu wyspecjalizowania ich w bardzo konkretnych zadaniach.

Ostatecznie nikt nie zabrania pełnego, produkcyjnego zastosowania LLM. Scenariusze zarysowane powyżej można do pewnego stopnia adresować lub zgodzić się na kompromisy. Tym bardziej, że wiele problemów, które widzimy dzisiaj, niebawem mogą całkowicie zniknąć. Przykładem może być kwestia prywatności danych, którą teraz można zaadresować poprzez plany Enterprise czy modele OpenSource. Zamiast czekać na gotowe rozwiązania, można już teraz zdobywać doświadczenie, które okaże się przydatne w przyszłości.

## Zadanie praktyczne

#aidevs_2 