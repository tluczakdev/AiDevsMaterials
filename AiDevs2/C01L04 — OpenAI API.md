# C01L04 — OpenAI API i LangChain

Przechodzimy już do bezpośredniego połączenia z modelem poprzez OpenAI API oraz wprowadzamy **koncepcję** frameworka Langchain (JavaScript). Ze względu na to, że możesz programować w innych technologiach lub posługiwać się narzędziami no-code, skupimy się na praktykach, które możesz połączyć ze swoimi umiejętnościami.

## Ustawienia konta OpenAI

API pozwala nam nawiązywać bezpośredni kontakt z m.in. modelami GPT. W przeciwieństwie do ChatGPT Plus, tutaj model rozliczeń opiera się na **liczbie przetworzonych tokenów**. Z tego powodu **KRYTYCZNIE WAŻNE** jest ustawienie **twardych limitów** bezpośrednio po zarejestrowaniu konta na stronie [platform.openai.com/account/billing/limits](https://platform.openai.com/account/billing/limits).

Domyślnie dopuszczalny limit wynosi $120. Choć na moim prywatnym koncie jest większy, zawsze utrzymuję go na poziomie ~+$15 na wypadek jakichś błędów w trakcie developmentu czy projektowania automatyzacji.

![](https://cloud.overment.com/limits-7be6a2af-c.png)

Jeśli dopiero zakładasz konto, nie będziesz mieć dostępu do GPT-4, a jedynie do modeli GPT-3.5-Turbo czy GPT-3.5-Turbo-16k. Dostęp pojawi się po pomyślnej płatności o wysokości minimum $1.

![](https://cloud.overment.com/access-2d83a388-3.png)

**Gdy upewnisz się, że twardy limit jest ustawiony**, pobierz klucz API z zakładki [API keys](https://platform.openai.com/account/api-keys) i możemy przejść dalej. 

## Nawiązanie połączenia z OpenAI

Zakładam, że z pomocą znanych Ci narzędzi jesteś w stanie wykonać proste połączenie HTTP autoryzowane kluczem API. Warto jednak rozważyć **skorzystanie z SDK**, a w przypadku platform no-code, np. make.com z **natywnych modułów**. 

![](https://cloud.overment.com/openai-7941b46c-e.png)

Minimalny kontakt z modelem wygląda tak, jak poniżej. Mówimy więc o zapytaniu typu POST z właściwościami **message** oraz **model**. Jako odpowiedź otrzymujemy rozbudowany obiekt, który najbardziej interesuje nas ze względu na listę **choices**, zawierającą odpowiedź modelu. Istotna może być także właściwość **usage**, zawierająca informacje o wykorzystanych tokenach, które możemy zapisać po swojej stronie.

![](https://cloud.overment.com/connection-f40c5367-0.png)

API, poza parametrami, które widzieliśmy przy okazji Playground, oferuje kilka dodatkowych ustawień. Dwa z nich są bezpośrednio związane z **Function Calling**, którym nie będziemy się teraz zajmować, i wrócimy do nich przy okazji jednej z kolejnych lekcji. Pozostałe prezentują się następująco: 

- **n** — w związku z niedeterministyczną naturą modeli, możliwe jest **wygenerowanie kilku odpowiedzi dla tego samego promptu**. Jest to przydatne na potrzeby zastosowania "[self-consistency](https://arxiv.org/abs/2307.06857)", polegającym na sklasyfikowaniu i wyborze najlepszej odpowiedzi. Trzeba jednak pamiętać, że generowanie wariantów **wpływa na zużycie tokenów i tym samym na większe koszty**.
- **stream** — generowanie odpowiedzi zajmuje czas, dlatego OpenAI oferuje opcję strumieniowania, umożliwiając nam odczytywanie pojedynczych tokenów, co nierzadko ma **szczególnie pozytywny wpływ na doświadczenia użytkowników**.
- **logit_bias** — osobiście nie miałem jeszcze okazji wykorzystywać tej opcji. Jej zadanie polega na **obniżeniu prawdopodobieństwa wyboru wskazanych tokenów**. np. {11088: -100} zniechęca model do wykorzystywania słowa "kill". Wartość tokenu można uzyskać za pomocą encodera, który omówimy za moment.
- **user** — w przypadku zastosowania produkcyjnego, [dobre praktyki bezpieczeństwa](https://platform.openai.com/docs/guides/safety-best-practices) uwzględniają KYC (know your customer). Powodem jest fakt, że jeśli przykładowo udostępniasz na stronie chatbota, a jeden z użytkowników zapyta go o rzeczy, które naruszają politykę OpenAI, **Twoje całe konto może zostać zablokowane**. Korzystając z ID użytkownika, możesz wykorzystać tę informację na potrzeby takich sytuacji.

W dalszych częściach kursu, będziemy korzystać (opcjonalnie) z platformy make.com, która znacząco ułatwia tworzenie różnych integracji, a nawet narzędzi którymi model może się posługiwać. Aby nawiązać w niej połączenie z OpenAI możesz skorzystać z wbudowanego modułu HTTP lub (rekomendowane) z natywnego modułu OpenAI. Wystarczy, że utworzysz nowy scenariusz i z listy dostępnych integracji wybierzesz OpenAI, a następnie zapiszesz swój klucz API. 

![](https://cloud.overment.com/open-8573b501-7.png)

Na ten moment, to wszystko.

## Langchain

Połączenie LLM z logiką aplikacji lub scenariusza automatyzacji wydaje się proste. W praktyce jednak mamy tutaj do czynienia z połączeniem **naturalnego języka** i niedeterministycznej natury modeli z **kodem precyzyjnie opisującym przepływ danych**. Sytuacja zaczyna się komplikować w momencie, gdy projektujemy system składający się z większej liczby promptów, które mogą (ale nie muszą) się ze sobą łączyć.

Z tego powodu powstają różne narzędzia, których celem jest **ułatwienie nam tego zadania**. Każde z nich znajduje się jednak obecnie na stosunkowo wczesnym etapie rozwoju. Nie wszystkie ich elementy są dopracowane, a część z nich bardziej przeszkadza niż pomaga. Przykładami rozwiązań, na które warto zwrócić uwagę, są — Langchain ([Python](https://langchain.readthedocs.io/en/latest/) / [JavaScript](https://js.langchain.com/docs/)), na którym skupi się nasza uwaga, oraz [LLaMA Index](https://www.llamaindex.ai/).

Langchain jest obecnie dość rozbudowany, jednak w wielu miejscach **narzuca zbyt dużą warstwę abstrakcji** i/lub **posiada nieprecyzyjną dokumentację**. Jednocześnie przedstawia różne koncepcje, które możemy implementować samodzielnie lub korzystać tylko z wybranych narzędzi, całkowicie ignorując pozostałe.

**Połączenie z modelem**

Langchain oferuje rozbudowany interfejs dla różnych modeli (OpenAI/PaLM/Anthropic/Ollama), **co ułatwia ich ewentualne łączenie.** Sama interakcja do złudzenia przypomina tę, znaną z SDK. Mamy tu zatem zainicjowanie połączenia z modelem oraz faktyczne wysłanie zapytania, w tym przypadku w formacie ChatML (podział system/user/assistant).

![](https://cloud.overment.com/initial-58ed5a96-8.png)

Istotna różnica pojawia się na etapie **integrowania promptów** oraz **odpowiedzi modelu** z kodem aplikacji, np. poprzez możliwość **zweryfikowania formatu odpowiedzi**, **szablony promptów** i **ich kompozycję**. Pomimo tego, że w przypadku niektórych języków, np. JavaScript, z powodzeniem możemy skorzystać np. z [Tag Function / Tagged Templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates), to warto rozważyć strukturyzowanie promptów z pomocą wbudowanych w LangChain metod.

Poniżej odwzorowałem jeden z promptów, który omawialiśmy w poprzednich lekcjach, wykorzystując Prompt Templates. Dokładnie **taki sam efekt** mógłbym osiągnąć za pomocą Tag Function lub nawet prostym łączeniem ciągów znaków. Jednak tutaj różnica pojawia się w chwili, gdy kod naszej aplikacji staje się bardziej złożony. W związku z podatnością promptów na nawet najmniejsze zmiany, stosowanie mechanizmów umożliwiających **utrzymanie struktury** jest pomocne. 

![](https://cloud.overment.com/template-17ad822a-2.png)

Podobne mechaniki można stosować także bezpośrednio w automatyzacjach make.com poprzez obecne w nim funkcje i zmienne. Poniżej znajduje się jedno z narzędzi, którymi posługuje się model w celu zarządzania moją listą zadań. Jeśli nie korzystasz z takich narzędzi, to powiem tylko, że sam nie wyobrażam sobie budowania aplikacji łączących się z kilkoma usługami (nierzadko przez OAuth2.0) tylko na swoje potrzeby. Zwyczajnie nie starczyłoby mi na to wszystko czasu, dlatego właśnie wspominam o make.com, który świetnie sprawdza się jako uzupełnienie (i nierzadko jako fundament) interakcji z LLM. 

![](https://cloud.overment.com/templates-96c257e3-8.png)

Myślę, że widzisz tutaj wyraźnie zmienne a nawet całe sekcje promptów, które **dynamicznie pojawiają się w jego treści**. To właśnie w tym miejscu znaczenia nabiera **formatowanie promptu** czy **wyraźne wyróżnianie jego poszczególnych sekcji**.

**Streaming**

W przeciwieństwie do ChatGPT, bezpośrednia integracja z OpenAI API przerzuca na nas ciężar obsługi **strumieniowania** i **kontroli limitu tokenów**. Jeszcze kilka miesięcy temu, strumieniowanie wymagało ręcznej implementacji. Dziś mamy do dyspozycji funkcje uruchamiane [podczas generowania tokenów](https://js.langchain.com/docs/modules/model_io/models/chat/how_to/streaming) czy [w chwili wywołania zdarzenia](https://js.langchain.com/docs/modules/model_io/models/chat/how_to/subscribing_events) (np. wystąpienia błędu). 

![](https://cloud.overment.com/streaming-c3a64c73-9.png)

Jednym z ciekawszych zastosowań strumieniowania z jakim miałem styczność, było połączenie usługi ElevenLabs generującej głos na podstawie tekstu z GPT-4. Normalnie takie zadanie wymagałoby wygenerowania **pełnej odpowiedzi GPT-4**, następnie **pełnego audio ElevenLabs** i ostatecznie **odtworzenia go**. Strumieniowanie sprawia, że z kilkunastu sekund, czas reakcji spadł do ~1.75s

![](https://cloud.overment.com/speak-2f5e66fd-e.png)

Wskazówka: "na produkcji" prawdopodobnie będziesz streamować odpowiedzi modelu do klienta, np. przeglądarki. Jeśli będziesz mieć potrzebę przekazania dodatkowych informacji wraz z zapytaniem (np. identyfikatora konwersacji), skorzystaj z **nagłówków HTTP zapytania, np. x-conversation-id.**

**Kontrola Token Window**

Poza strumieniowaniem, połączenie przez API wymaga od nas przejęcia kontroli nad **limitami tokenów**, który łatwo przekroczyć np. podczas rozmowy. Istotnym elementem pracy z dopuszczalnym limitem jest **liczenie tokenów** z pomocą **tiktoken'a**. Jednak [zgodnie z OpenAI Cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/How_to_count_tokens_with_tiktoken.ipynb), ze względu na aktualizację modeli, mówimy tutaj o **przybliżonej estymacji**.

Dodatkowo, dla modeli GPT-3.5-Turbo oraz GPT-4, musimy uwzględnić także fakt, że mamy do czynienia z ChatML. Przykładowo, pomimo tego, że fraza "Hey, you!" to 4 tokeny, estymowany rezultat wynosi 11, ponieważ bierzemy pod uwagę metadane wynikające ze struktury ChatML. Zwróć także uwagę na sposób pobierania **token ID**, który możesz wykorzystać w połączeniu z parametrem **logit_bias**.

![](https://cloud.overment.com/tiktoken-2b3d1dd2-a.png)

Uzyskany wynik, w tym przypadku, zgadza się z informacją z właściwości **usage** dla wykonanego zapytania. Wygląda więc na to, że nasz licznik działa. 

![](https://cloud.overment.com/usage-48bb1699-5.png)

Estymacja tokenów przydaje się praktycznie na każdym kroku podczas integracji z LLM, jednak sama informacja o ich liczbie nie jest wystarczająca i musimy podjąć faktyczne działania związane z kontrolą liczby tokenów w prompcie. Opcji jest kilka: 

- Skorzystanie z modelu wspierającego większą liczbę tokenów, np. GPT-3.5-Turbo-16k (o ile to możliwe)
- Wybór różnych wersji promptu lub jego fragmentów
- Zmniejszenie kontekstu lub ucięcie wcześniejszych wiadomości konwersacji
- Zastosowanie kompresji (w postaci np. podsumowania) dla informacji zawartych w kontekście / konwersacji

Zasadniczo dążymy tutaj do utrzymania promptu w ramach "token window", **mając także na uwadze pozostawienie przestrzeni na odpowiedź modelu**. Poniższy obrazek obrazuje tzw. "floating window", czyli przesuwające się wraz z rozmową okno, które ucina wcześniejsze fragmenty rozmowy.

![](https://cloud.overment.com/floating-window-542f8b90-0.png)

LangChain daje nam do dyspozycji gotowe mechanizmy wykorzystujące tzw. **ConversationChain** (jest to łańcuch akcji ułatwiających prowadzenie konwersacji) do którego możemy podłączyć **pamięć** krótkoterminową lub długoterminową. Można to porównać do **stanu aplikacji** przechowywanego w pamięci lub pobieranego z bazy danych. O jej konkretnych rodzajach będziemy jeszcze mówić. Tymczasem, spójrzmy na przykład poniżej. 

**BufferWindowMemory** jest dokładnie wspomnianym przed sekundą "pływającym oknem". Ustawienie jego parametru "k" na wartość 1 sprawia, że model pamięta **wyłącznie poprzednią interakcję**. Oznacza to, że gdy się przedstawię, poproszę aby poczekał, a następnie zapytam o moje imię, **to przy założeniu, że w drugiej interakcji go nie powtórzył, nie będzie w stanie go podać.**

![](https://cloud.overment.com/topk-43027090-9.png)

Ucząc się na temat zarządzania kontekstem, **możesz pomyśleć, że nie ma to większego sensu**, ponieważ już teraz [istnieją modele zdolne do przetwarzania 100 tys. tokenów](https://www.anthropic.com/index/100k-context-windows) w jednym zapytaniu. Jednak długi kontekst nadal przekłada się na koszty, oraz na potencjalne ryzyko **odwrócenia uwagi modelu** od tego, co jest istotne. 

![](https://cloud.overment.com/anthropic-897643e0-4.png)

Zatem **kontrolowanie limitu tokenów to szukanie balansu** pomiędzy dostarczeniem **istotnych dla bieżącej konwersacji informacji** oraz ich ilością. 

Na ten moment, to wszystko na temat LangChain, choć do jego dalszych możliwości wrócimy w kolejnych lekcjach. 

## Moderowanie wejścia i wyjścia

Wspomniałem już o KYC oraz [dobrych praktykach od OpenAI](https://platform.openai.com/docs/guides/safety-best-practices) związanych z bezpieczeństwem. Jest to jednak na tyle istotny temat, że musimy zatrzymać się przy nim nieco dłużej. 

Istnieje kilka problemów związanych z **dawaniem użytkownikom dowolności pod kątem wprowadzania danych do modelu** oraz **dostarczaniem odpowiedzi generowanych przez model bezpośrednio do użytkowników**. Są to wyzwania związane z bezpieczeństwem aplikacji, bezpieczeństwem samych użytkowników, wyzwaniami prawnymi, zagrożeniami związanymi z wizerunkiem oraz z samą stabilnością aplikacji.

Pierwszym elementem, który warto stosować, jest **Moderation API** od OpenAI, czyli specjalny adres na który możemy przesłać **dowolny tekst**, aby uzyskać informację o tym, **czy narusza politykę OpenAI**. Dla odmiany, poniższe zapytanie zrealizowałem w make.com, wprowadzając tekst "You're id**t!", który został **oflagowany**, a więc nie powinien być przekazany do OpenAI. 

Sugestia: Moderację OpenAI stosuj także w przypadku swoich własnych danych. Wśród przetwarzanych przez Ciebie treści mogą przypadkowo znajdować się fragmenty niezgodne z polityką OpenAI. Prostym przykładem jest **transkrypcja**, ponieważ tam mogą wystąpić błędy takie jak odczytanie słowa "focus" jako "f**k you" — doświadczyłem tego osobiście.

![](https://cloud.overment.com/moderation-970f68fa-7.png)

Drugim elementem, jest **walidacja danych pod kątem Twoich zasad** o czym mówi [Constitutional AI](https://arxiv.org/abs/2212.08073). Chodzi tutaj o **dodatkowy prompt**, którego zadaniem jest **sklasyfikowanie dostarczonych danych**. Jeżeli dane wejściowe lub wygenerowana odpowiedź złamie przewidziane przez Ciebie zasady, **zostanie odrzucona** i najlepiej także **oznaczona.** Niestety nie mogę zaprezentować Ci promptów, które sam stosuję, natomiast ich ogólna struktura uwzględnia: 

- Klasyfikację pod kątem "adversarial prompt / prompt injection"
- Wykrycie próby nadpisania zachowania asystenta
- Wykrycie próby przechwycenia instrukcji systemowej
- Wykrycie próby oszukania asystenta
- Wykrycie potencjalnego naruszenia jednej ze zdefiniowanych przeze mnie zasad, dotyczących mojego systemu oraz niepożądanych przeze mnie zachowań, które nierzadko są charakterystyczne dla modelu

Taki prompt zwraca 0 lub 1 w zależności od tego, czy wykrył jakieś problemy. Naturalnie taki mechanizm można rozbudować lub przebudować w zależności od swoich potrzeb. **Nie daje on gwarancji bezpieczeństwa**, lecz obniża ryzyko problemów. Dokładnie taki sam prompt może weryfikować także **wypowiedź asystenta**. Wówczas oczywiście niemożliwe jest jej strumieniowanie, co obniża szybkość działania aplikacji. 

Trzecim elementem jest **sam prompt**, który powinien zawierać **precyzyjne instrukcje** oraz **sterować zachowaniem modelu tak, aby unikał niepożądanych aktywności oraz wypowiedzi**. Oczywiście nadal jego głównym zadaniem będzie zrealizowanie założonego celu, jednak musi się to odbyć w ramach wcześniej zdefiniowanych założeń. Np. nie chcemy aby system analizujący dokumenty pod kątem nieścisłości, zaczął odpowiadać na pytania zawarte we fragmentach takich plików.

Ostatecznie uzyskanie większej kontroli nad zachowaniem promptu wykorzystuje mechanizmy określane jako **Guardrails**, np. [NeMo](https://github.com/NVIDIA/NeMo-Guardrails). W przypadku LangChain, koncepcja ta występuje w ramach łańcuchów (Chains). Oczywiście też nic nie stoi na przeszkodzie aby samodzielnie projektować "tory po których porusza się LLM" zintegrowany z naszą aplikacją. 

## Integracja z OpenAI API

Pracę z OpenAI API oraz eksplorowanie tego, co oferuje, najlepiej jest zacząć od tworzenia rozwiązań **na swoje potrzeby**. Część z nich użytecznością będzie przypominać "aplikacje to-do" realizowane w ramach nauki. Z kolei inne mogą pozostać z Tobą przez długi czas, oszczędzając Ci energię lub ułatwiając pracę czy naukę. 

Techniczne doświadczenie otwiera Ci możliwość **zintegrowania usług i urządzeń z API OpenAI**. GPT-3.5/GPT-4 może być dla Ciebie dostępne praktycznie w dowolnym miejscu na Twoim komputerze poprzez **skróty klawiszowe**, oraz realizować różne zadania w tle z pomocą harmonogramu Twojej własnej aplikacji lub scenariuszy automatyzacji. 

W każdym przypadku, poza korzyściami dla siebie, stwarzasz sobie przestrzeń do bliższego poznawania Dużych Modeli Językowych. W związku z tym, że chcemy pracować już niemal wyłącznie z API, możemy sięgnąć po gotowe narzędzia lub zbudować własne. Mowa tutaj o: 

- Makrach Siri Shortcuts lub Keyboard Maestro (MacOS)
- Aplikacji "Alice" przygotowanej przeze mnie (macOS / Windows)
- Szablonie aplikacji Tauri przygotowanym przeze mnie (macOS / Windows / Linux) do której masz dostęp Open Source
- Własne skrypty, automatyzacji, mikroserwisy

Główną idea opiera się tutaj o **połączenie AI niewymagające zmiany kontekstu** lub **działania AI niewymagające Twojego zaangażowania**. Projektując takie rozwiązania dla siebie, początkowo nie będą stabilne, ale samodzielnie je naprawisz. Zdobędziesz w ten sposób wiedzę, którą z czasem możesz zastosować tworząc projekty dla klientów, pracodawcy, czy rozwijając własną firmę. 

**Konfiguracja Siri Shortcuts**

Shortcuts to aplikacja działająca w ekosystemie urządzeń Apple (macOS / ipadOS / iOS / watchOS). Poniższe makra działają na każdym z tych urządzeń i umożliwiają Ci kontakt z modelami OpenAI. Proste ale przydatne akcje (przetłumacz w luźnym stylu / popraw tekst / podaj definicję / wyjaśnij błąd) możesz przypisać do skrótów klawiszowych. 

![](https://cloud.overment.com/shortcuts-61195353-7.png)

Pobierz dwa makra:
- ⚡ [GPT-4 API Connector](https://www.icloud.com/shortcuts/b8223541849b4e94bf90a24d6722226e) — zaimportuj, otwórz i wklej klucz API
- ⚡ [GPT-4 Hello](https://www.icloud.com/shortcuts/d0cf09cf855a4903a0ce45a134427db9) — zaimportuj, **zduplikuj**, zaktualizuj prompt, przypisz do skrótu klawiszowego. Domyślnie do makra trafi **zawartość Twojego schowka**, a chwilę później odpowiedź modelu zostanie do niego skopiowana, zatem możesz ją po prostu wkleić. 

**Aplikacja Alice**

Alice to aplikacja desktopowa, **łącząca się bezpośrednio z OpenAI** i przechowująca **ustawienia oraz historię rozmów** na Twoim komputerze. Po zainstalowaniu musisz podać swój klucz API (przechowywany lokalnie) i możesz korzystać z integracji z GPT-3.5/GPT-4. W ramach aplikacji możesz definiować snippety, czyli prompty, które możesz przypisać do skrótów klawiszowych. 

- Skrót ⌘D / Control D ukrywa i odkrywa okno aplikacji
- Pozostałe skróty i snippety można ustawić samodzielnie

![](https://cloud.overment.com/alice-229a1eb1-0.png)

- ⚡ [Pobierz Alice](https://heyalice.app)

**Szablon aplikacji Tauri**

Na potrzeby AI_Devs przygotowałem szablon aplikacji Tauri, który można dostosować do swoich potrzeb, a następnie **wygenerować na systemy MacOS / Windows / Linux**. Wcześniej należy:

- Przejść przez instrukcję Tauri: https://tauri.app/v1/guides/getting-started/prerequisites
- Ustawić skróty klawiszowe w pliku main.ts (linia 39) oraz powiązane z nimi instrukcje systemowe
- Zbudować aplikację
- Podać swój klucz API

![](https://cloud.overment.com/tauri-103f36a5-a.png)

Szablon wykorzystuje framework Tauri (Rust / Svelte), jednak do skorzystania z aplikacji **nie trzeba znać żadnej z wymienionych technologii**. 

Aplikacja posiada następujące funkcjonalności:
- podłączenie własnego klucza API
- połączenie z GPT-3.5/4
- interakcja ze schowkiem systemowym
- wyświetlanie odpowiedzi w czacie z obsługą markdown
- możliwość przypisywania skrótów klawiszowych do akcji, które można ustalić w kodzie, przed zbudowaniem aplikacji

Dodatkowe informacje: 
- Domyślnie wykorzystywany jest model gpt-3.5-turbo. Można to jednak zmienić w plikach
- Do skorzystania z aplikacji należy wygenerować nowy klucz API oraz ustawić limity (podczas developmentu będziecie dużo testować) 
- Klucz API zapisywany jest jako plain text w pliku aidevs.dat. Jego lokalizacja zależy od systemu operacyjnego. W macOS jest to ~/Library/Application Support/com.aidevs.dev. Warto tutaj dodać jakiś rodzaj szyfrowania, aby nasz klucz nie był przechowywany w ten sposób.
- API obsługuje zarówno streaming odpowiedzi (zwracanie fragmentami), jak i generowanie całości
- Skróty klawiszowe rejestrują się automatycznie, w chwili uruchomienia aplikacji

- ⚡ [Pobierz szablon](https://cloud.overment.com/aidevs_2-1694857817.zip)

**Własne skrypty / mikroserwisy / automatyzacje**

W kolejnych lekcjach i późniejszej części kursu będziemy projektować i wdrażać różne skrypty, małe aplikacje oraz scenariusze automatyzacji. Jeśli chcesz, już teraz możesz stworzyć swoje proste narzędzia korzystając z ulubionych technologii oraz tego, czego się nauczyliśmy do tej pory. Wówczas kolejne tygodnie dadzą Ci nowe pomysły i przykłady rozszerzeń, które możesz zastosować w przypadku **własnego asystenta AI**



#aidevs_2


