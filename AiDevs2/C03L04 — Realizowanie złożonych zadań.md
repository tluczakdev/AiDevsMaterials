# S03L04 — Realizowanie złożonych zadań

Widząc przykłady długoterminowej pamięci i systemów umożliwiających **porozmawianie z zapisanymi informacjami**, to pojawia się pytanie — **dlaczego mamy angażować tutaj AI, zamiast skorzystać z sensownej wyszukiwarki**? Jest ono szczególnie zasadne w sytuacji, gdy mówimy o bazie składającej się z naszych własnych notatek czy wiedzy, którą już posiadamy. Zobaczmy więc, jak to wypada w praktyce, przy uwzględnieniu zaawansowanych technik projektowania systemu zdolnego do realizowania złożonych zadań ... samodzielnie! 

## Strategie organizacji i przechowywania danych dla LLM

Poznaliśmy już różne zagadnienia związane z pracą z danymi na potrzeby LLM. Jednak po zakończeniu AI_Devs zderzysz się ze scenariuszami, które nawet trudno wymienić, ponieważ jest ich tak wiele. Co więcej, nierzadko są to **nowe problemy, na które niekiedy nie ma jeszcze jednoznacznych odpowiedzi**. Na szczęście, do ich rozwiązania możemy zastosować zarówno to, co już znamy z programowania, jak i nowe narzędzia i techniki dostępne dla nas dzięki LLM. Bardzo istotne jest zatem to, aby **wychodzić poza to, co już znamy**. 

OpenAI [na stronie z przykładami](https://platform.openai.com/examples) podaje kilkanaście różnych zastosowań. Prompty do korekty tekstu, klasyfikacji, wyjaśniania czy podsumowania, wydają się być mało użyteczne. Szczególnie gdy porównamy je z zaawansowanymi technikami, takimi jak omawiane już Tree of Thoughts. 

Jeśli jednak spojrzymy na te przykłady przez pryzmat programistycznego zastosowania, szczególnie w kontekście organizacji danych, to ich postrzeganie znacznie się zmienia. 

**Korekta tekstu**: Notatki, blogowe wpisy czy automatyczne transkrypcje charakteryzują się błędami i problemami z formatowaniem. Naprawienie ich programistycznie praktycznie nie wchodzi w grę. Ręczne poprawianie tekstu nawet przy wsparciu LLM jest bardzo czasochłonne. Dlatego spójrzmy na przykład **25_correct**

![](https://cloud.overment.com/correct-f5ee53de-4.png)

Wczytujemy w nim plik i dzielimy na fragmenty. Następnie przechodząc przez każdy z nich, model **usuwa literówki, błędy gramatyczne, ortograficzne oraz zwiększa czytelność, jednocześnie zachowując oryginalne formatowanie tekstu**. Zachowanie formatowania możliwe jest praktycznie wyłącznie dzięki temu, że stosujemy składnię Markdown, którą GPT-4 potrafi się posługiwać. 

Prompt realizujący to zadanie, nawiązuje do roli copywritera, którego rolą jest przepisanie poprawionej wersji tekstu, według zdefiniowanych zasad. W związku z tym, że fragmenty mogą zawierać instrukcje dla modelu, również bardzo wyraźnie to podkreślam, posługując się nawet kilkoma przykładami dokładnie prezentującymi oczekiwane zachowanie w obliczu takich sytuacji. 

![](https://cloud.overment.com/copywriter-9af9d326-4.png)

Takie przetwarzanie dokumentów zwykle wymaga dość dużej precyzji i w związku z tym warto korzystać w tym celu z modelu GPT-4. W przypadku prostej korekty, wersja GPT-3.5-Turbo może okazać się wystarczająca. Wyniki działania tego skryptu możesz zobaczyć poprzez porównanie plików **draft.md** i **reviewed.md**, które znajdziesz w katalogu przykładu **25_correct**.

**Tłumaczenia:** Analogicznie jak w przypadku korekty, model może przetłumaczyć dla nas dokument. Przewagą nad Deepl czy Google Translate jest **możliwość zachowania formatowania**, a nawet zmiana tonu.

**Słowa kluczowe**: Opisywanie tekstu z pomocą słów kluczowych może okazać się przydatne na potrzeby wyszukiwania, filtrów, czy budowania sieci linkowania wewnętrznego na potrzeby SEO. Podobnie jak w przypadku korekty tekstu, schemat jest tutaj podobny, aczkolwiek zamiast generować poprawiony tekst, generujemy listę słów kluczowych. Programistycznie lub z pomocą innego promptu możemy usunąć duplikaty. Słowa kluczowe mogą także posłużyć do opisania pojedynczych dokumentów w celu ich późniejszego odnalezienia. 

**Kategoryzowanie**: Kategoryzowanie również odbywa się na podobnych zadaniach. Podobnie jak w przypadku słów kluczowych, tutaj głównie mówimy o organizacji danych. Możliwe scenariusze zastosowania to także **filtrowanie** poprzez np. przypisanie zapytania użytkownika do konkretnej kategorii, powiązanej z obszarem naszej bazy wiedzy. 

**Upraszczanie / Podsumowania**: Podsumowania i streszczenia to jedna z najważniejszych technik, przydatnej na potrzeby prowadzenia długich interakcji lub w kontekście czatu, lub w na potrzeby systemu RAG / HSRAG. W związku z tym, że mówimy tutaj o **kompresji**, część danych zostanie utracona i to, które z nich zostaną uwzględnione w podsumowaniu, w dużym stopniu zależy od modelu, ale my także mamy na to pewien wpływ. 

Przykład **26_summarize** zawiera kod, który **łączy ze sobą** wiele zagadnień, które do tej pory omawialiśmy. Konkretnie są to:

- wczytywanie treści pliku i ich podział uwzględniający estymację tokenów (dokładnie jak w przykładzie **04_tiktoken**)
- wykorzystanie Function Calling, które w tym przypadku pozwala nam na **ustrukturyzowanie danych**, dzięki czemu możemy łatwiej wykorzystywać odpowiedzi w dalszej logice aplikacji
- wykonanie promptu odpowiedzialnego za streszczenia fragmentów
- zapisywanie wyników w oddzielnym pliku w trakcie realizacji zadania

![](https://cloud.overment.com/summarize-87c2d3c6-d.png)

Sam prompt uwzględnia nadanie roli, oraz instrukcję uwzględniającą przygotowanie notatki na podstawie treści fragmentu dokumentu. Dodatkowo informuję w niej model o tym, że dostarczony tekst może zawierać instrukcję, pytanie lub polecenie, albo nawet wyglądać tak, jakby miał zostać dokończony. Dzięki temu zmniejszam ryzyko wystąpienia sytuacji, w której wygenerowana odpowiedź będzie "realizację instrukcji", a nie oczekiwanym streszczeniem. Poza tym dodałem kilka "zasad", które uznałem za pomocne w przypadku treści z którą pracuję (czyli konkretnie szkicu lekcji AI_Devs). 

![](https://cloud.overment.com/summary-bfaf68c8-3.png)

Jeśli przetestujesz działanie tego skryptu na swoim tekście, to prawdopodobnie zauważysz, że **konieczne będzie zmodyfikowanie** albo sposobu podziału na mniejsze fragmenty czy nawet samego promptu. Oczywiście możliwe jest opracowanie systemu tworzącego "ogólne podsumowania", jednak zwykle warto wprowadzić możliwość dostosowania oraz **nadania kontekstu** zwiększającego jakość odpowiedzi modelu. 

**Weryfikacja:** Korzystając z modyfikacji przykładu podsumowania długich dokumentów, można połączyć go z omawianymi już technikami związanymi z Vector Store i budowaniem dynamicznego kontekstu, oraz pobieraniem danych z zewnętrznych źródeł. Wówczas możliwe jest przygotowanie skryptu, zdolnego do **zweryfikowania zagadnień** omówionych w treści. Ogólna mechanika opiera się wówczas o wypisanie zagadnień lub pytań z dostarczonych fragmentów, a następnie zweryfikowanie ich poprawności poprzez porównanie z informacjami pobranymi np. z Wikipedii. Na tym etapie AI_Devs nie będziemy podejmować się realizacji takiego skryptu, ponieważ kilka brakujących elementów poruszymy w kolejnych lekcjach. 

## Bazy wektorowe

Projektując aplikacje wykorzystujące LLM, prawdopodobnie będziemy pracować także z bazami wektorowymi. Piszę "także", ponieważ zwykle chodzi o połączenie i synchronizację baz SQL/noSQL w których nadal będziemy przechowywać większość danych z których nasza aplikacja będzie korzystać. Jeśli posiadasz już doświadczenie w pracy z silnikami wyszukiwania, np. ElasticSearch czy Algolia, to w przypadku baz wektorowych mówimy o podobnej konfiguracji. Konkretnie **dane pomiędzy naszą główną bazą, a bazą wektorową muszą być synchronizowane** ze sobą. W formie wektorowej będziemy jednak wyłącznie te dane, które będziemy chcieli wykorzystywać jako dynamiczny kontekst dla LLM, aczkolwiek **pamiętaj, że bazy wektorowe mogą być wykorzystywane także w innych sytuacjach, np. wykrywaniu anomalii lub podobieństw w zbiorach danych**.

Mieliśmy okazję tworzyć już dokumenty, opisywać je metadanymi, a nawet zamieniać na embedding (listę wektorów) czy wyszukiwać informacje z pomocą Similarity Search. Zatem mamy niemal komplet informacji do tego, aby zacząć pracę z bazami danych. Jest jednak jeszcze kilka pojedynczych informacji, które warto posiadać: 

- Bazy wektorowe przechowują dane w formie **embeddingu** oraz opisujących je **metadanych**, zwykle zorganizowanych w ramach **kolekcji**
- W naszym przypadku będziemy zamieniać dane na embedding z pomocą modeli OpenAI, jednak przypominam, że istnieją także modele Open Source, z pomocą których możemy generować embedding za darmo, na swojej maszynie. 
- W zależności od modelu embedding może posiadać różne liczby wymiarów. W przypadku modelu z którego będziemy korzystać wartość ta wynosi **1536** (należy to zapamiętać)
- W przypadku text-embedding-ada-002 możemy pracować zarówno z językiem angielskim, jak i polskim. Jednak **nie polecam mieszać ze sobą tych języków** i zdecydować się na jeden. W moim przypadku zawsze jest to język angielski.
- Teoretycznie nie potrzebujesz bazy SQL/noSQL aby korzystać z bazy wektorowej, ponieważ treści dokumentów możesz przechowywać w metadanych. Nie polecam jednak tego robić, ponieważ ogranicza to dostęp do nich.
- Przechowując dane w bazie SQL/noSQL oraz bazie wektorowej, zadbaj o to, aby **ich identyfikatory były dokładnie takie same**. Sam stosuję w tym celu UUID (np. 0da77082-3d95-416a-93e7-bd09df09ebfa)
- Niewykluczone, że na pewnym etapie będzie możliwe przechowywanie wektorów bezpośrednio w bazie postgreSQL ze względu na projekt [pgvector](https://github.com/pgvector/pgvector). Obecnie jest on na wczesnym etapie rozwoju, ale warto go obserwować.
- Interakcja z bazami wektorowymi odbywa się poprzez API. W naszym przypadku skorzystamy z połączenia LangChain z SDK Qdrant oraz natywnego modułu Make.com. 

## Baza wektorowa: Qdrant 

Qdrant.tech jest jedną z najpopularniejszych baz wektorowych. Już teraz oferuje najważniejsze funkcjonalności. Można ją także skonfigurować **lokalnie na swoim komputerze** oraz własnym serwerze. Najprostszym sposobem jej uruchomienia, jest skorzystanie z Dockera. Jeśli z niego nie korzystasz, to w pierwszej kolejności [zainstaluj go](https://docs.docker.com/desktop/install/mac-install/) na swoim komputerze.

Po instalacji, uruchom dwa poniższe polecenia, dzięki którym pobierzesz tzw. obraz i uruchomisz tzw. kontener. Zaraz potem Qdrant będzie dostępny pod adresem: **http://localhost:6333/dashboard**, a powiązane z nim pliki trafią do **./qdrant_storage**. 

![](https://cloud.overment.com/aidevs_docker-ad561e2d-8.png)

Po uruchomieniu, przejdź do przykładu **27_qdrant**. Znajdziesz w nim kod **tworzący kolekcję, indeksujący dokumenty oraz wyszukujące powiązany z zapytaniem dokument**.

Ważne: w przypadku tworzenia kolekcji **upewnij się, że właściwość on_disk jest ustawiona na true** oraz, że Docker faktycznie mapuje katalog **qdrant_storage**. Aby mieć pewność, że wszystko działa poprawnie, utwórz kolekcję i zrestartuj Dockera. Jeśli zostanie wczytana poprawnie, a nie tworzona na nowo, to wszystko jest w porządku. Dodatkowe potwierdzenie możesz uzyskać poprzez sprawdzenie katalogu **/qdrant_storage/collections** (pełna ścieżka będzie zależała od miejsca w którym uruchamiasz Dockera. W moim przypadku jest to katalog domowy).

W tym miejscu zaznaczę jednak, że celowo pomijam natywną integrację LangChain z Qdrant, ponieważ w chwili pisania tej lekcji, umożliwia ona bardzo podstawowe indeksowanie, które zwyczajnie nie sprawdza się w praktyce. 

W przykładzie przechodzimy przez następujące kroki: 

1. Nawiązanie połączenia z Qdrant (konieczne jest dodanie wartości **QDRANT_URL=http://localhost:6333** do pliku .env)
2. Sprawdzenie, czy kolekcja o ustalonej nazwie istnieje
3. Jeżeli kolekcja nie istnieje, tworzymy ją, przygotowując na indeksowanie embeddingu z text-embedding-ada-002 (wartość **size** ustawiona na 1536)
4. Sprawdzenie czy dokumenty były już indeksowane
5. Jeśli kolekcja jest pusta, wczytujemy treść pliku, dzielimy na fragmenty, dodajemy metadane, generujemy embedding, i wgrywamy do Qdrant
6. Wyszukujemy dokumenty zbliżone znaczeniem do zapytania, na podstawie którego wcześniej wygenerowaliśmy embedding

Jednym z najważniejszych elementów procesu indeksowania, jest zadbanie o nadanie własnych identyfikatorów, do których będziemy mieć dostęp i będziemy mogli zapisać w bazie danych. Identyfikatory zostały wygenerowane przez bibliotekę **uuid** i pojawiają się **zarówno w metadanych**, jak i samych punktach. 

Poza identyfikatorami, zapisałem także źródło dokumentów. Ponieważ, jak już kilkukrotnie podkreślałem, indeksując dokumenty, które są zwykle fragmentami dłuższych treści, musimy mieć możliwość ich swobodnego przywołania, filtrowania oraz aktualizacji.

![](https://cloud.overment.com/index-05ad9862-2.png)

Samo wyszukiwanie uwzględnia nie tylko odnalezienie podobnych wpisów, ale także zawężenie wyszukiwania w celu pominięcia niektórych dokumentów. Oczywiście w tym przypadku nic takiego nie będzie miało miejsca, ponieważ wszystkie dokumenty mają dokładnie to samo źródło.

![](https://cloud.overment.com/search-8d98a44c-4.png)

Ograniczanie wyszukiwań może odbywać się poprzez wartości generowane przez sam model. Przykładowo sam stosuję mechaniki pozwalające na **przeszukiwanie tylko wybranych wspomnień Alice**. Np. jeśli zadaję pytanie związane **wyłącznie** z moimi notatkami, to pomija pozostałe obszary swojej długoterminowej pamięci. 

## Baza wektorowa z no-code: Pinecone

W przypadku make.com i Pinecone sytuacja jest dość prosta, ponieważ po założeniu konta na pinecone.io musimy pobrać jedynie  założyć nowy indeks oraz **adres https** i **klucz API do konta.** Należy pamiętać o ustawieniu **dimensions** na 1536 oraz **metric** na cosine.

![](https://cloud.overment.com/dashboard-456e46c2-b.png)

Jeżeli chodzi o samą automatyzację w make.com, to uwzględnia ona trzy kroki:

1. Wygenerowanie obiektu JSON z pomocą modułu Create JSON. Po zaimportowaniu poniższego scenariusza **musisz skonfigurować ten moduł poprzez utworzenie struktury danych** na podstawie następującego obiektu JSON: **{"model": "text-embedding-ada-002", "input": "..."}**. Po prostu skopiuj go i w ustawieniach modułu wciśnij przycisk "Add", a następnie "Generate". Po zapisaniu zmian, upewnij się, że w polu input, znajduje się zmienna **query** pochodząca z webhooka
2. Następnym krokiem jest zapytanie do API OpenAI w celu wygenerowania embeddingu przekazanego zapytania.
3. No i ostatnim krokiem jest **albo** wyszukiwanie, albo tzw. upsert czyli "utwórz lub zaktualizuj" rekord.

Poniższy scenariusz jest na tym etapie jedynie pokazowy i nie pełni żadnej konkretnej funkcji. Do jego praktycznego zastosowania, będziemy jeszcze wracać. 

![](https://cloud.overment.com/pinecone-19a734ae-2.png)

- ⚡ [Pobierz blueprint](https://cloud.overment.com/aidevs_pinecone_similarity_search-1696114708.json)

## Podsumowanie

W tej lekcji połączyliśmy wiele zagadnień, przez które przechodziliśmy w ostatnich tygodniach. Na tym etapie konieczna jest jeszcze praktyka z nadchodzących lekcji. Chciałbym jednak podkreślić kilka istotnych wątków, które mogą pomóc w poukładaniu dotychczasowej wiedzy. 

**Organizacja wiedzy:** Wyraźnie wybrzmiały już wątki dotyczące roli dostępu do bezpośredniego źródła danych (np. przez API), otwartych formatów tekstowych, oczyszczania danych, ich podziału czy opisywania nawet z pomocą LLM. Na tym etapie mamy już wiedzę i narzędzia umożliwiające pracę z obszernymi zestawami informacji, co otwiera drogę do różnych modyfikacji wiedzy, w celu jej późniejszego wykorzystania przez AI i/lub człowieka. Poznaliśmy także różnice wynikające z tego, jak wiedza ma być prezentowana dla modelu, ale faktyczne mechaniki będziemy jeszcze budować. Ostatecznie znamy także rolę metadanych oraz łatwego dostępu do danych poprzez zapisywanie ich źródeł, oraz identyfikatorów. 

**Wyszukiwanie hybrydowe:** Ten temat nie wybrzmiał jeszcze wystarczająco, biorąc pod uwagę jego rolę oraz związane z nim wyzwania. Jednak widzimy już, że opracowanie mechanik wyszukiwania zaczyna się już na etapie przygotowania struktur źródeł wiedzy. Później, w zależności od budowanej aplikacji, możemy brać pod uwagę wyszukiwanie z pomocą np. Algolia Search, połączone z wyszukiwaniem poprzez bazy wektorowe czy złożone rozpoznawanie / wzbogacanie zapytania i filtrowanie obszarów wyszukiwania lub zwróconych wyników. 

**Dostosowanie:** Niemal wszystko, o czym do tej pory mówiliśmy opiera się o uniwersalne koncepcje, które można spotkać na przestrzeni całkowicie różnych aplikacji i systemów. Jednak szybko można się przekonać, że nawet stosunkowo proste wdrożenia będą wymagały dużego dopasowania do zestawu danych, realizowanego procesu czy wykorzystywanych narzędzi, a nawet polityki prywatności. Warto mieć to na uwadze, ponieważ (przynajmniej na tym etapie) trudno jest mówić o uniwersalnych rozwiązaniach czy promptach. Znacznie wyższą jakość można osiągać poprzez staranne opracowanie mechanik, składających się na większy, stabilny system. 

 


#aidevs_2