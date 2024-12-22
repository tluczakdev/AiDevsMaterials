# C04L04 — Własne API

Biorąc pod uwagę aktualny rozwój samych modeli oraz narzędzi umożliwiających ich wykorzystanie w kodzie, widzimy, że ich produkcyjne wdrożenie jest wymagające. Z drugiej strony budowanie narzędzi z których możemy korzystać na własne potrzeby, jest niezwykle proste, szczególnie gdy łączymy programowanie z elementami narzędzi no-code. W związku z tym, że dość szybko liczba skryptów i scenariuszy rośnie, warto rozważyć zbudowanie **prywatnego API**. 

## Koncepcja i zastosowania własnego API

Wszystkie złożone interakcje z GPT-4 odbywają się u mnie za pośrednictwem działającej na serwerze Digital Ocean aplikacji NestJS połączonej z bazą PostgreSQL, której schemat pokazywałem w **C02L04**. Mowa tutaj konkretnie o **Alice Private API**, które jest zaprojektowane z myślą o mnie. Podstawą interakcji jest tutaj proste zapytanie do API o następującej strukturze (oczywiście chronione kluczem API!): 

![](https://cloud.overment.com/query-9b787abe-4.png)

Wyjaśniając powyższe właściwości:

- **conversation_id**: jest opcjonalne i służy do utrzymywania wątku. Przy pierwszej interakcji to pole jest puste, a jego wartość zwracana jest podczas odpowiedzi z API. Posiadam także  endpoint **/assistant/current** z pomocą którego mogę pobrać UUID aktualnej rozmowy. Dodatkowo konwersacja wygasa **automatycznie po 15 minutach** i wówczas rozmowa zaczyna się od początku (o ile conversation_id nie jest podane w zapytaniu).
- **query**: to po prostu zapytanie kierowane do modelu. Jego treść jest dodawana do konwersacji jako UserMessage. 
- **stream**: w niektórych przypadkach zależy mi na strumieniowaniu odpowiedzi. Gdy ta właściwość ustawiona jest na **true**, tokeny są zwracane pojedynczo, a **conversation_id** jest przesyłane w nagłówku odpowiedzi. Strumieniowanie **nie działa w make.com**.
- **model**: to wersja modelu z którą chcę pracować przy realizacji głównej części zapytania. 

Prywatne API wykorzystuję w kilku miejscach, dzięki czemu łatwo mogę po nie sięgać niezależnie od tego czy jestem przy komputerze czy też nie. Istotną zaletą jest również fakt, że zapytania mogą być także przetwarzane bez mojego udziału (np. z poziomu działających w tle scenariuszy, o których już mówiliśmy).

**Integracja z moją wersją aplikacji Alice**

Domyślnie aplikacja Alice łączy się bezpośrednio z OpenAI. W mojej wersji posiadam **dodatkowy sposób interakcji** (przypisany do innego skrótu klawiszowego), który przesyła zapytanie do mojego API. To dzięki temu poniższa wiadomość faktycznie włączyła AC/DC w moich słuchawkach. Fraza "Play AC/DC" została zinterpretowana jako polecenie, skojarzona z jedną z umiejętności oraz scenariuszem make.com odpowiedzialnym za połączenie ze Spotify. Po kilku sekundach pojawiła się odpowiedź, a akcja została wykonana. 

![](https://cloud.overment.com/acdc-70ce68ad-c.png)

Prawdopodobnie największą, niewidoczną tutaj zaletą własnego API, jest fakt, że **dane są współdzielone**, ponieważ znajdują się dokładnie w tej samej bazie. Zatem mogę poprosić o zapisanie danych na jednym urządzeniu i nawet kilka miesięcy później zapytać o nie na innym. Dokładnie te same dane mogą być także wykorzystane przez automatyzacje działające w tle.

**Integracja z kanałem na Slacku**

Drugim miejscem w którym mogę swobodnie komunikować się z moim prywatnym API, jest kanał na Slacku. Dzięki rozbudowanym funkcjom tego komunikatora, mogę np. wysłać wiadomość głosową (w tym przypadku wypowiedziałem: "Alice, could you invite people to my AI Devs course?"), która została zamieniona na tekst z pomocą Whisper. Pomimo tego, że polecenie nie zawierało żadnych dodatkowych informacji na temat AI_Devs poza nazwą, Alice poprawnie odnalazła swoje wspomnienia na temat tego projektu, pisząc (swoimi słowami) rozbudowaną wiadomość. 

![](https://cloud.overment.com/slack-532020dd-4.png)

Myślę, że w świetle tej i ostatnich lekcji, bardziej zrozumiały stanie się przywoływany przeze mnie już schemat automatyzacji obsługującej taką integrację. Jego wyzwalaczem jest zdarzenie związane z nową wiadomością na wskazanym kanale. Ustawiony na początku filtr **pomija wiadomości wysyłane przez Alice, dzięki czemu odpowiada wyłącznie na moje zapytania** (porównanie odbywa się na podstawie User ID). W sytuacji gdy do wiadomości dołączone jest nagranie audio, jego treść zostaje zamieniona na tekst i zapisana w zmiennej.

Chwilę później oryginalne zapytanie lub treść wiadomości głosowej zostaje przekierowane na endpoint **/assistant/conversation**, ale wcześniej zostaje pobrane UUID aktualnej konwersacji. Ostatecznie odpowiedź ze strony prywatnego API zostaje przesłana jako wiadomość zwrotna z konta Alice (lub działającego na Slacku bota).

![](https://cloud.overment.com/dm-c22fce6a-f.png)

Jeśli chcesz podejrzeć scenariusz łączący Slacka z moim prywatnym API, możesz go pobrać [tutaj](https://cloud.overment.com/aidevs_slackdm-1696753963.json). 

Poza tym, że Slack posiada rozbudowane API (nierzadko trudne do zrozumienia i w większości płatne), to jego dodatkową zaletą jest dostępność na wielu platformach. Poniżej widać skróty na moim telefonie, wśród których znajduje się skrót uruchamiający kanał na Slacku przypisany do Alice.

![](https://cloud.overment.com/mobile-4e3590ee-9.jpg)

**Scenariusze Automatyzacji make.com**

Nawet w scenariuszu powyżej widać odwołania do prywatnego API z pomocą modułu HTTP. Oczywiste jest więc, że mogę sięgać po **dowolną informację zapisaną w bazie danych poprzez LLM** i korzystać z niej w dowolnym scenariuszu automatyzacji. Jest jednak tutaj jeden, dodatkowy element — możliwość zapisywania i aktualizowania tych danych. 

Omawialiśmy już rozpoznawanie intencji oraz wyposażenie modelu w narzędzia z których może korzystać. Nic nie stoi na przeszkodzie, aby jednym z narzędzi była **interakcja z własną pamięcią**. W efekcie jedyne co muszę zrobić, aby Alice zapamiętała lub zaktualizowała jakieś elementy swojej pamięci, to po prostu powiedzieć, aby to zrobiła. Np. "Zaktualizuj swoje wspomnienie o (...)" powoduje odnalezienie konkretnej informacji i nadpisanie jej bądź rozbudowanie. Ten fakt mogę wykorzystywać zarówno w scenariuszach automatyzacji, jak i automatyzacji Siri Shortcuts. 

Przykładowo poniższa automatyzacja uruchamia się co kilka minut, dzięki czemu Alice pobiera sobie informacje z czujników mojego iPhone (lokalizacja, lokalizacja samochodu itd.)

![](https://cloud.overment.com/update-47e2d6e7-7.jpg)

Analogiczne zastosowanie może dotyczyć obserwowanych w make.com zdarzeń lub danych pobieranych cyklicznie. Zamiast zapisywać je w Airtable, mogą zostać przekazane do prywatnego API jako polecenie "zapamiętania" wskazanej informacji. 

**Siri Shortcuts**

No i ostatnim miejscem w którym wykorzystuję połączenie z własnym API, jest Siri Shortcuts działające na moim telefonie i zegarku. Tam interakcja ma formę głosową zarówno jako wejście (nagranie audio), jak i wyjście (elevenLabs.io). Schemat funkcjonowania jest dokładnie taki sam jak w przypadku wcześniejszych automatyzacji, więc w tej chwili nie będę się nad tym dłużej zatrzymywał. 

## Zastosowania własnych skryptów realizujących pojedyncze zadania

Patrząc na potencjalne zastosowania prywatnego API, powinno być oczywiste to, jak istotne jest zbudowanie szeregu narzędzi w postaci (zwykle) prostych skryptów. W moim przypadku takie narzędzia również dostępne są jako endpoint prywatnego API, z którego może korzystać zarówno Alice, ale także mogę po nie sięgać z dowolnego innego miejsca.

Zatem w praktyce, mówimy tutaj cały czas o **jednej aplikacji** udostępniającej API integrujące się z LLM oraz bazą danych. Budując narzędzia umożliwiające np. wczytywanie treści strony www czy podział długiej treści na mniejsze fragmenty, zawsze warto mieć na uwadze ich reużywalność i możliwość konfiguracji. 

Nawet endpoint **/assistant/conversation**, który pokazałem wcześniej, zawiera szereg **opcjonalnych** ustawień wpływających na jego zachowanie. Dzięki temu jeden adres może być wykorzystywany zarówno przy integracji ze Slackiem, jak i aplikacją Alice. 

Listę narzędzi omawiałem w **C04L01** i **C04L02**, jednak teraz ponownie nawiążę do ich wykorzystania na potrzeby AI lub bezpośredniego uruchomienia. 

Przykładem mogą być "szybkie notatki", które wykorzystuję w codziennej pracy do gromadzenia tymczasowych informacji na różne tematy. Zamiast otwierać kilkadziesiąt stron w zakładkach, po prostu skrótem klawiszowym przesyłam zaznaczony tekst do Notion. W lekcji **C02L04** pokazywałem jak to działa w praktyce, jednak tym razem, wyjdziemy nieco dalej, poza zwykłe przekazywanie treści. Taki przykład powinien oddać **sens budowania własnych, małych narzędzi, które można ze sobą łączyć**, dzięki czemu nie tylko my możemy z nich korzystać, ale także budowane przez nas aplikacje AI (np. asystent).

Wyobraźmy sobie sytuację w której potrzebuję zebrać swoje notatki na temat wyznaczania celów. W związku z tym, ze sporo na ten temat pisałem w newsletterach eduweb, swoim blogu, społeczności Ahoy czy e-bookach, trudno byłoby to szybko zgromadzić w jednym miejscu. Ze względu na to, że Alice "przeczytała" niemal wszystkie te publikacje, może się swobodnie nimi posługiwać. Zatem mogę przeprowadzić proces myślowy przeprowadzający przez zagadnienia, które mnie interesują. W trakcie dyskusji Alice może korzystać z zewnętrznych źródeł z Internetu, aby uzupełniać kontekst konwersacji. W trakcie rozmowy mogę poprosić o zapisywanie wybranych fragmentów jako szybkie notatki Notion, do których mogę łatwo wrócić nawet wtedy, gdy proces takiego "researchu" rozciągnięty jest na kilka dni.

![](https://cloud.overment.com/note-4af545b8-7.png)

Przekładając to na nieco mniej specyficzną sytuację, wyobraź sobie, że Twój asystent ma dostęp do treści dokumentacji narzędzi z których korzystasz czy nawet aplikacji, którą rozwija Twoja firma. Mówimy tutaj nie tylko o "sprytnej wyszukiwarce", ale także rozszerzaniu posiadanej wiedzy o aktualne informacje z Internetu. 

Narzędzia, które to umożliwiły obejmują:

- Mechanikę organizowania i dostępu do pamięci długoterminowej, w tym przypadku Alice
- Możliwość posługiwania się wynikami wyszukiwania z Internetu
- Możliwość odczytywania treści stron www
- Integracja z Notion (no-code)
- Posługiwanie się narzędziami w trakcie konwersacji, uwzględniając kontekst **bieżącej rozmowy**, a nie tylko pojedynczego zapytania

Taką mechanikę można przekładać na różne obszary życia codziennego (treningi, naukę, hobby, zadania domowe) oraz biznesowe (sprzedaż, marketing, zarządzanie). W każdym z tych przypadków mówimy jednak raczej o **dużym dopasowaniu** logiki takiej aplikacji, a nie ogólnym narzędziu działającym wszędzie. Z drugiej strony to dopasowanie może odbywać się poprzez **rozmawiające ze sobą moduły**, a niekoniecznie wyspecjalizowany system. 

## Konfiguracja własnej domeny i VPS

Nasze prywatne API oczywiście może składać się z totalnie niezależnych od siebie funkcji uruchomionych np. na Netlify lub scenariuszy automatyzacji w make.com. Jeśli jednak mamy taką możliwość, to warto rozważyć zbudowanie aplikacji działającej na back-endzie, której rolą będzie udostępnienie API. W tym celu najlepiej dobrać technologię w której czujemy się najlepiej oraz takiej, która oferuje gotowe rozwiązania często spotykanych problemów związanych z integrowaniem LLM (część z nich mieliśmy już okazję spotkać).

Jeśli pracujesz na back-endzie lub jako full-stack dev, to bez problemu skonfigurujesz wszystko czego potrzebujesz dla swojego API. Jeśli jednak Twoje serwerowe doświadczenie nie jest duże, to zarysuję kroki, które należy uwzględnić oraz takie, które można rozważyć. 

Do stworzenia swojego API potrzebujesz: 

- back-endu aplikacji webowej, np. przygotowanej w NestJS
- bazy danych, np. mySQL lub postgreSQL
- domeny, np. [z porkbun](https://porkbun.com/)
- hostingu. Sam korzystam z Digital Ocean, jednak możesz wybrać także rozwijany przez Kubę [mikr.us](https://mikr.us) lub dowolny inny VPS (Virtual Private Server). Istotne jest tylko to, aby mieć swobodny dostęp do maszyny poprzez terminal

Do tego przydadzą Ci się umiejętności związane z: 

- obsługą git i github
- podstawy obsługi terminala
- [podstawy konfiguracji nginx i Let's encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)
- [podstawy pracy z bazami danych i SQL](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart)
- (opcjonalnie) [praca z CI&CD, np. Github Actions](https://youtu.be/NeaUvC5_25Y?t=617)

Domyślam się, że pierwsze dwa punkty są dla Ciebie jasne. Do kolejnych podłączyłem linki do materiałów, które przeprowadzą Cię przez proces konfiguracji swojego VPSa. Aby jednak wszystko było możliwie jasne, przejdziemy przez kolejne kroki na przykładzie Digital Ocean (możesz skorzystać z dowolnego innego dostawcy).

W Digital Ocean serwer określany jest mianem "Droplet". Tworząc go, potrzebujesz maszyny działającej możliwie blisko Twojej lokalizacji (zwykle jest to Frankfurt), 1-4 GB RAM (dla Github Actions warto mieć więcej pamięci) oraz ilość przestrzeni dyskowej dopasowanej do Twoich potrzeb. Jako system operacyjny sam zawsze wykorzystuję Ubuntu w wersji 22.04.

Połączenie z serwerem praktycznie zawsze będzie miało miejsce przez SSH w związku z tym musimy utworzyć dedykowany klucz SSH do połączenia z naszym serwerem.

![](https://cloud.overment.com/ssh-3ea2cfff-d.png)

Powyższe polecenie utworzy parę kluczy: prywatny i publiczny. Prywatny zachowujesz na swoim komputerze i nikomu nie udostępniasz, a publiczny dodajesz na etapie konfiguracji swojego dropletu. Klucz publiczny możesz skopiować poleceniem: **pbcopy < ~/.ssh/aidevs.pub**. 

Po kilku chwilach droplet powinien być gotowy. Połączenie z nim można nawiązać poprzez polecenie: **ssh root@165.227.154.7 -i ~/.ssh/aidevs**. Jeśli wszystko pójdzie w porządku, na ekranie, powinien pojawić się ekran powitalny Ubuntu.

![](https://cloud.overment.com/connect-aeadbbb4-1.png)

Na tym etapie z powodzeniem można przejść przez podlinkowane przeze mnie artykuły z bloga Digital Ocean, które bardzo precyzyjnie wyjaśniają wszystko, co należy zrobić. W skrócie możesz się spodziewać: 

- Utworzenia zdalnego repozytorium Github
- Zainstalowania node.js i postgreSQL na serwerze
- Zainstalowania i skonfigurowania serwera nginx
- Konfiguracji domeny i certyfikatu SLL
- Pobrania projektu ze zdalnego repozytorium na serwer
- Uruchomienia projektu
- (opcjonalnie/wskazane) konfiguracji Github Actions lub innego rozwiązania do automatycznej publikacji aplikacji na serwerze produkcyjnym

Jeżeli posługujesz się narzędziami no-code, tutaj sytuacja jest znacznie prostsza, ponieważ dzięki webhookom, w zasadzie natychmiast stworzone przez Ciebie akcje stają się dostępne publicznie. Warto jednak zadbać o to, aby w ustawieniach webhooka dodać własny klucz API, który ma być obecny w nagłówku zapytania kierowanego na webhook. Poza tym, pamiętaj, że scenariusze mogą wywoływać się wzajemnie, o ile zaczynają oraz kończą się webhookiem. Wówczas wystarczy moduł HTTP, aby nawiązać z nimi kontakt i odebrać informację zwrotną. 

## Projektowanie bazy danych dla prywatnego API

Niezależnie od tego, na jaką bazę danych się zdecydujesz, spotkasz dość uniwersalne problemy. Prawdopodobnie najlepszą sugestią jaką mogę tutaj dać, jest rozpisanie tego, co planujesz przechowywać w swojej bazie, oraz w jaki sposób dane te będą się ze sobą łączyć. Następnie należy wybrać konwencję nazewnictwa i zwyczajnie się jej trzymać.

W związku z tym, że jesteś jedyną osobą korzystającą z bazy połączonej z prywatnym API, obowiązują tutaj nieco luźniejsze zasady niż w przypadku bazy produkcyjnej w której znajdują się także dane innych użytkowników. Z drugiej strony zasoby jakimi dysponujemy do jej opracowania są zdecydowanie mniejsze (bo zwykle takie projekty realizujemy sami, po godzinach). Z tego powodu warto zacząć od czegoś prostego i potem stopniowo rozbudowywać strukturę w zależności od swoich potrzeb. 

W moim przypadku logika była dość prosta, ponieważ potrzebowałem tabel na: 

- **historię wiadomości**: wewnątrz niej przechowuję zarówno moje pytania, jak i odpowiedzi modelu. Poza tym wiadomości oznaczane są poprzez UUID w celu odróżnienia poszczególnych rozmów. W niektórych sytuacjach przechowuję tutaj także "refleksję" modelu nad konwersacją, która uwzględnia np. podsumowanie bieżącej rozmowy
- **tabele dla zasobów**: tutaj przechowuję wszystko, co może być przydatne dla mnie w przyszłości, lub może okazać się pomocne przy autonomicznej pracy asystenta. Poza samą treścią, przechowuję również listę źródeł, podsumowanie oraz generuję semantyczne tagi i kategorie. Dodatkowe opisywanie wpisów wykorzystywane jest głównie na potrzeby późniejszego wyszukiwania
- **tabele dla umiejętności**: oddzielna przestrzeń bazy dedykowana jest opisom umiejętności asystenta i instrukcje posługiwania się nimi, które podobnie jak zasoby posiadają dodatkowe pola ułatwiające ich odszukanie. To tutaj przechowywane są także obiekty JSON opisujące schemat parametrów, które model ma generować do uruchomienia funkcji

Nietrudno zauważyć, że prywatne API nie wymaga zbyt rozbudowanej bazy. Podobnie też nie potrzebujemy tutaj przejmować się wydajnością czy objętością danych, ponieważ zwykle mówimy o skali uwzględniającej nie więcej niż kilkanaście lub kilkadziesiąt tysięcy rekordów (co w skali baz danych jest małą liczbą).

Pomimo małej skali nadal warto zachować dobre praktyki związane z nazewnictwem, typami czy plikami migracji (widoczny poniżej). Dzięki temu nawet jeżeli konieczne będzie zresetowanie całej struktury, to odbędzie się to bez większych problemów (zakładając, że zadbamy o kopię przechowywanych w niej danych).

![](https://cloud.overment.com/schema-a924c9f7-8.png)

W kontekście aplikacji integrujących LLM, samo budowanie bazy nie różni się od projektów, które z AI nie mają nic wspólnego. Wyjątek stanowi większy nacisk położony na **opisywanie / tagowanie danych** oraz oczywiście synchronizacja z bazą wektorową i/lub silnikami wyszukiwania. Proces projektowania struktury bazy no-code (np. w Airtable) również wygląda bliźniaczo i nawet mieliśmy okazję go doświadczyć w poprzednich lekcjach. 

Istnieje jednak pewien bardzo istotny aspekt pracy z bazami danych aplikacji integrujących LLM i jest nią **spójność danych pomiędzy serwerem produkcyjnym a lokalnym**. W przypadku klasycznej aplikacji istotna była struktura oraz nierzadko generowane dane jedynie przypominające wpisy z bazy produkcyjnej (tzw. seed). Tutaj sytuacja jest inna, szczególnie dla danych, które są wykorzystywane bezpośrednio w działających promptach, ponieważ nawet najmniejsze różnice mogą prowadzić do znaczących rozbieżności. Poza tym jeżeli mamy obszerne dane indeksowane w bazie wektorowej (i tym samym zamieniane na embedding), to mało efektywne jest powtarzanie tego procesu wielokrotnie. Aby nie było wątpliwości **nie mówimy tutaj o połączeniu lokalnej aplikacji do produkcyjnej bazy (!)**, a jedynie korzystanie z kopii niezbędnych dla poprawnego działania promptu informacji. 

Jeśli nie posiadasz jakiegokolwiek doświadczenia w projektowaniu baz danych, myślę, że na początek Airtable może być dobrym wyborem. Niestety programistyczna integracja może sprawiać problemy ze względu na obecny w nim, bardzo agresywny rate limiting, który dyskwalifikuje zastosowanie Airtable "na produkcji". 




#aidevs_2