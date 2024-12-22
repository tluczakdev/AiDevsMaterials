# C05L04 — Narzędzia asystenta

Na tym etapie mamy już praktycznie wszystko do zbudowania własnego asystenta, czy zastosowania jego mechanik w różnych sytuacjach. Jednak podobnie jak w przypadku pamięci długoterminowej, teoria znacznie różni się od praktyki. Podzielę się zatem swoimi doświadczeniami, oraz wskazówkami, które mogą pomóc uniknąć Ci wielu problemów z którymi sam mierzyłem się przez długi czas. Naturalnie niemożliwe jest zaadresowanie ich wszystkich, więc skupimy się na tych, które postrzegam jako najbardziej istotne. 

## Praca z asystentem, zdolnym do posługiwania się narzędziami

Nasz asystent będzie tak użyteczny, ile pracy włożymy w budowanie narzędzi, którymi będzie w stanie się posługiwać. **Nie mówimy tutaj jednak o zbudowaniu ich wszystkich na raz, ale raczej nieustannym rozwoju**. Nawet w przypadku Alice, ostatnie z umiejętności dodałem zaledwie kilka dni temu. I choć może brzmieć to jak bardzo trudne zadanie, to w praktyce na pewnym etapie zrozumienia AI, złożenie w całość nowej umiejętności, jest już formalnością. 

Po zbudowaniu pierwszych narzędzi, warto skierować swoją uwagę na to, aby asystent faktycznie potrafił po nie sięgnąć, gdy jest to potrzebne. Powinien być w stanie wywnioskować to z kontekstu konwersacji, a nawet **poprawić błędnie wykonane zadanie!** 

Poniżej znajduje się przykład rozmowy w której użytkownik pyta asystenta o zgromadzenie informacji na wybrany temat, a następnie zawęża swój wybór do wskazanych punktów, by w końcu wydać polecenie. Asystent przy jego wykonaniu bierze pod uwagę **całą dotychczasową konwersację** (lub jej podsumowanie). 

![](https://cloud.overment.com/aidevs_context-e22b1377-3.png)

Takie podejście jest wprost wymagane do budowania możliwie naturalnych interakcji. Bezpośrednio wynika z niego również możliwość **naprawienia popełnionego błędu**. Poniżej mamy przykład w którym Alice wygenerowała dla mnie grafikę bez zachowania wskazanych przeze mnie przerw. Bez konieczności powtarzania całego polecenia, mogłem tylko wskazać, co jest nie tak i poprosić o spróbowanie ponownie. Pomimo tego, że moja ostatnia wiadomość nie nawiązywała do generowania grafiki, to w świetle całej rozmowy, było to oczywiste i faktycznie, kolejna grafika była już poprawna. Takie zachowanie nie jest oczywiste, ponieważ nie mówimy tutaj wyłącznie o poruszaniu się w obrębie treści wymienionych wiadomości, ale także serii akcji realizowanych w tle, uwzględniających interakcję z zewnętrznymi usługami. 

![](https://cloud.overment.com/aidevs_fix-c293bfba-4.png)

Ze względu na to, że każda interakcja z asystentem odbywa się tylko poprzez jedno pole tekstowe lub kanał głosowy, należy **zmniejszać ryzyko wystąpienia sytuacji, w której asystent wybierze niepoprawną akcję**. Co więcej, to do asystenta należy wykonanie całego zadania, bez konieczności przekierowania mnie do zewnętrznych usług, więc proces nierzadko jest złożony i tym samym, system potrzebuje czasu na jego realizację. Dlatego zamiast tworzyć odseparowane narzędzia, np. "dodaj zadanie", "wygeneruj grafikę", znacznie lepiej sprawdza się podział na narzędzia, w ramach których dostępne są wybrane funkcje. 

![](https://cloud.overment.com/aidevs_tools-3d31e3a1-f.png)

Można także rozważyć samo grupowanie narzędzi, jednak sam stosuję to wyłącznie na potrzeby wyszukiwania / filtrowania akcji, a nie faktycznego jej uruchomienia. Odgrywa to istotną rolę w sytuacji, gdy polecenia są dość podobne do siebie. Np. "dodaj do listy" w moim przypadku może być skojarzone z Todoist (zadania) lub Notion (szybkie notatki). Zawężając listę funkcji oraz tworząc dla nich precyzyjne opisy, możemy obniżyć ryzyko pomyłki do minimum. Co więcej, wszędzie tam, gdzie jest to możliwe, buduję interakcje w sposób, pozwalający na wykonanie szeregu zadań, z pomocą jednego polecenia. Np. mogę powiedzieć **zadanie X jest skończone, Y dopisz do mojej listy a Z przenieś na jutro** — jedno polecenie, skutkuje wykonaniem trzech aktywności związanych z jednym narzędziem. W przyszłości planuję wprowadzić możliwość wydania poleceń dla różnych usług jednocześnie, a nawet typowe dla agentów "planowanie działania".

![](https://cloud.overment.com/aidevs_filtering-30cde503-7.png)

Ostatnią rzeczą na którą chciałem zwrócić uwagę, jest samo rozpoznawanie intencji, którą powinniśmy szczególnie dopracować, aby interakcja z asystentem była wygodna. Mam tutaj na myśli fakt, że z pewnością nie chcesz przypadkowo wydać polecenia w trakcie zwykłej rozmowy z asystentem. Dlatego aktualny podział, z którego sam korzystam wygląda tak: 

![](https://cloud.overment.com/aidevs_logic-fb38b764-d.png)

Powyższy schemat obrazuje fakt, że do tej pory posługiwałem się znacznymi uproszczeniami, gdy prezentowałem logikę mojego asystenta. W praktyce, jest to trochę bardziej złożone, więc rozbijmy to raz jeszcze na mniejsze kroki.

1. Zapytanie: To wiadomość tekstowa i/lub głosowa
2. Rozpoznanie: Określenie czy konieczne jest podejmowanie akcji
3. (opcjonalnie) Wybór narzędzi: Wyszukanie, filtrowanie i wybór akcji
4. Zebranie wspomnień: Nawet wtedy, gdy konieczne jest podjęcie działań, korzystam z pamięci długoterminowej, co zwiększa skuteczność realizacji poleceń
5. Odpowiedź: W przypadku akcji, jest to parafraza, uwzględniająca także informacje z pamięci długoterminowej

Są sytuacje w których rozróżnienie zwykłego zapytania od polecenia, nie jest oczywiste (nawet dla człowieka). Przykładowo jeśli mówię "Odpowiedz na to pytanie", to bez wątpienia jest to polecenie. Jednak mój system nie posiada żadnej akcji związanej z odpowiadaniem na pytania, i taka wiadomość powinna być interpretowana jako zwykłe zapytanie. Nie chcę jednak zaburzać mojego promptu rozpoznawania intencji, więc jedną z akcji jest "wypowiedź", która nie robi nic konkretnego, poza wykonaniem logiki wykonywania akcji. Efekt jest taki sam, a system utrzymuje stabilność nawet w takich sytuacjach. 

## Posługiwanie się narzędziami w postaci skryptów i automatyzacji

Nie bez powodu tak wiele uwagi poświęciliśmy planowaniu i budowaniu własnego API, funkcji serverless czy scenariuszy automatyzacji. W połączeniu z wiedzą na temat modeli, kształtowania promptów oraz tworzenia rozbudowanych interakcji, mówimy tutaj o prawdopodobnie **najważniejszym, technicznym elemencie naszego asystenta** oraz innych systemów integrujących LLMy. 

Z lekcji C02L05 wiemy już, że narzędzia dla LLM mają postać funkcji, skryptów czy API. Rolą modelu jest wybór akcji, oraz wygenerowanie obiektu JSON zawierającego informacje, które do jej wykonania są potrzebne. Możemy jednak pójść krok dalej i połączyć ze sobą w całość wszystkie dotychczasowe koncepcje, czyli **rozpoznawanie intencji, budowanie dynamicznego kontekstu, wyszukiwanie hybrydowe oraz strukturyzowanie odpowiedzi**. Dzięki temu możemy wyposażyć naszego asystenta nie w jedno czy dwa narzędzia, ale po prostu zbudować dla niego pamięć oraz cały zestaw umiejętności, którymi będzie się posługiwać w zależności od swoich potrzeb. Wśród tych narzędzi mogą być także funkcje umożliwiające asystentowi modyfikowanie własnej pamięci, czy nawet **ulepszania własnych promptów**.

Skoro umiejętności asystenta to schematy Function Calling i/lub prompty generujące obiekty JSON, to obowiązuje nas tutaj limit kontekstu. Wiemy już, że możemy omijać go z pomocą technik wyszukiwania i zastosowania chociażby baz wektorowych. Rzecz w tym, że tutaj, potrzebujemy wyjątkowej precyzji zarówno na etapie wyboru funkcji, jak i generowania jej parametrów. Istotną rolę odgrywają także dodatkowe informacje z pamięci długoterminowej czy dane z otoczenia. 

Gdy do Alice dociera wiadomość zawierająca polecenie, uproszczony schemat wykonania działania, wygląda mniej więcej tak: 

![](https://cloud.overment.com/aidevs_actions-59471db0-a.png)

Powyższy schemat może się różnić w zależności od podejmowanej akcji. Dla niektórych z nich Alice dodatkowo pobiera wyniki wyszukiwania w Internecie czy czyta treści wybranych stron. Ogólne kroki uwzględniają tutaj: 

- Rozpoznanie intencji, embedding oraz wzbogacenie zapytania
- Przeszukanie pamięci w celu pozyskania istotnych z punktu widzenia konwersacji wspomnień
- Przeszukanie listy umiejętności i pobranie tych, które powinny być wzięte pod uwagę do wykonania aktualnego zadania
- Zastosowanie mechaniki re-rank, która w tym przypadku jest promptem wybierającym akcje, które mają być uwzględnione w Function Call (samo wyszukanie tych najbardziej prawdopodobnych nie jest wystarczające)
- Uruchomienie Function Call w celu wygenerowania parametrów funkcji. Tutaj **pod uwagę brany jest także kontekst w postaci wybranych wspomnień czy wyników wyszukiwania w Internecie**
- Faktyczne wykonanie wybranego zadania oraz odebranie odpowiedzi, która zostaje poddana parafrazie na potrzeby kontekstu zapytania
- Zebranie wszystkiego w całość oraz faktyczne wygenerowanie odpowiedzi

W celu optymalizacji czasu reakcji, część z akcji realizowana jest równolegle. Podobnie też tam, gdzie to możliwe, działa model GPT-3.5-Turbo. Ostatecznie szybkość działania w dużym stopniu zależy od rodzaju podejmowanej akcji i zdecydowanie w przypadku części z nich, dobrze jest rozważyć "działanie w tle" oraz wysłanie powiadomienia (np. maila lub notyfikacji push) informujących o zakończeniu. 

Do tej pory jedynie wspominałem o re-rank, który w tym przypadku polega na doprecyzowaniu wyników wyszukiwania i skupieniu się na tych, które faktycznie są istotne. Aktualnie sam stosuję raczej najprostszy ze znanych mi sposobów, polegający na skorzystaniu z GPT-3.5-Turbo w celu oceny poszczególnych fragmentów. Samo zagadnienie jest jednak zdecydowanie bardziej zaawansowane i można o nim poczytać między innymi w publikacji [Matching, Re-ranking and Scoring](https://www.inf.uni-hamburg.de/en/inst/ab/lt/publications/2017-kohail-biemann-cicling.pdf). Z doświadczenia wiem, że strategia na którą się zdecydujemy, w dużym stopniu będzie zależała od danych i celu. Dojście do niej w dużej mierze powinno opierać się o zestawy danych testowych i dążenie do optymalizacji systemu do możliwie wysokiej skuteczności. U mnie kluczowe było wyraźne rozpoznawanie akcji, nawet jeśli potencjalnie były do siebie podobne (np. lista zadań i kalendarz). 

![](https://cloud.overment.com/aidevs_rerank-a21bb023-b.png)

W ten sposób klasyfikuję wspomnienia Alice i robię to z pomocą taniego i szybkiego GPT-3.5-Turbo. Dlatego też struktura promptu jest nieco inna niż zwykle i kończy się bezpośrednim zachęceniem modelu do zwrócenia konkretnie 0 lub 1 (co również przyspiesza system, choć nie daje modelowi miejsca do "zastanawiania się", co w niektórych sytuacjach mogłoby być wskazane).

Podobnie też wygląda klasyfikacja akcji, która ma zostać wykonana, jednak tutaj prompt jest znacznie bardziej złożony, ponieważ uwzględnia dodatkowe elementy, takie jak aktualna data, informacje z Internetu czy pozostałe obszary Alice. Dodatkowo mówimy tutaj także o przekazaniu schematu funkcji, która ma zostać uruchomiona. W związku z dużym zakresem informacji, które muszą zostać wzięte tutaj pod uwagę, konieczne jest ograniczenie kontekstu do tylko tych najbardziej kluczowych informacji, no i tutaj do gry wchodzi wspomniany wcześniej re-rank. 

Gdy wiem już, w jaki sposób dana akcja ma zostać uruchomiona, to reszta logiki asystenta jest już formalnością, ponieważ zbieram wszystkie dotychczasowe informacje, które brane są pod uwagę przy generowaniu ostatecznej odpowiedzi. Rezultat jest taki, jakby asystent świadomie wykonał określone zadanie i o tym informował, lub wykorzystywał pobrane informacje w celu poprowadzenia dalszej konwersacji. 

## Schemat komunikacji z narzędziami

Niezależnie od formy narzędzi dla LLM, zawsze potrzebujemy sposobu na przesłanie do nich danych oraz odebranie. Za pierwszy etap odpowiada Function Calling i obecne w nim instrukcje. Otrzymujemy obiekt JSON, który zostaje przekazany do wybranych narzędzi. Niemal zawsze będzie nas interesowało odebranie informacji zwrotnej, która będzie mogła być wykorzystana w dalszej konwersacji. Sam dla uproszczenia stosuję odpowiedź w postaci obiektu z jedną właściwością "data". Nie jest to jednak konieczne, ponieważ asystent może odebrać **cały obiekt odpowiedzi** i poprawnie go zinterpretować. To na który ze scenariuszy się zdecydujemy, zależy tutaj od nas. Zdecydowanie bardziej istotna jest tutaj **treść**, która musi zostać poprawnie wykorzystana przez asystenta oraz nie może wprowadzać go w błąd, ponieważ to może mieć wpływ na dalsze interakcje. 

Całość obrazuje poniższy przykład: 

- **User:** Czy możesz dodać ukończenie systemu powiadomień aplikacji Alice do listy zadań? 
- [AI]: Wybór akcji
- [AI]: Wygenerowanie parametrów w odpowiedniej formie, np. "Ukończenie systemu powiadomień" oraz pozostałych informacji opisujących zadanie
- [AI]: Przesyła wygenerowany obiekt JSON do aplikacji do zadań i otrzymuje obiekt zawierający informację o statusie wykonanego zadania. Ma to jednak formę generyczną i nie nadaje się do umieszczenia bezpośrednio w treści konwersacji
- **AI:** Jasne, dopisałam ukończenie systemu powiadomień do projektu Alice. Zrób to dziś przed 18:00.

Zatem cała trudność polega na tym, aby asystent otrzymał informację, której wykorzystanie w dalszej dyskusji nie wpłynie negatywnie na jakość odpowiedzi. Problemy mogą obejmować chociażby podawanie nieistotnego z punktu widzenia użytkownika identyfikatora.

![](https://cloud.overment.com/aidevs_id-ac9ef0d9-d.png) 

I chociaż takie zachowanie w przypadku własnego asystenta można jeszcze przemilczeć, tak już istotną rolę odgrywa **zapisywanie informacji niezbędnych do dalszej dyskusji**. No bo łatwo wyobrazić sobie scenariusz, w którym podjęta przez asystenta akcja jest nie do końca zgodna z naszymi oczekiwaniami. Zależy nam wówczas na tym, aby móc powiedzieć, co konkretnie ma być poprawione, bez konieczności powtarzania całej instrukcji od początku. Część z potrzebnych informacji będzie dostępna bezpośrednio w historii konwersacji. Problem w tym, że **nie wszystko faktycznie trafia do jej treści** i musimy mieć to na uwadze. Obecnie w przypadku Alice, adresuję ten problem bezpośrednio na etapie konkretnego narzędzia, co ma negatywny wpływ na wydajność (ponieważ nierzadko te same informacje pobierane są ponownie). Znacznie mądrzej byłoby przechowywać coś w rodzaju pamięci podręcznej dla rozmowy, po którą asystent może sięgać. 

Wykonanie akcji niekiedy będzie wiązać się z utworzeniem jakiegoś wpisu (lista zadań, kalendarz, e-mail), który i tak będziemy chcieli otworzyć. Dlatego zawsze, gdy to tylko możliwe, warto **zbudować URL do tworzonego wpisu**. W przypadku niektórych API, taki URL jest domyślnie zwracany w odpowiedzi. W obu z przypadków, adres ten powinien trafić do wypowiedzi asystenta lub być dołączony jako źródło. 

## Fine-tuning modelu GPT-3.5-Turbo

Fine-tuning to proces umożliwiający wyspecjalizowanie modelu w realizowaniu konkretnych zadań w celu uzyskania wyższej skuteczności, oszczędności tokenów oraz uzyskania niższego czasu odpowiedzi. Aby jednak było to możliwe, konieczne jest opracowanie zestawu danych treningowych, prezentujących oczekiwane zachowanie. W rezultacie otrzymujemy nową wersję modelu, która nie potrzebuje rozbudowanych promptów, aby skutecznie realizować to, co odzwierciedlały opracowane przez nas przykłady. Pozwala to ominąć (w pewnym sensie) ominąć ograniczenia długości kontekstu. Z drugiej strony, fine-tuning nie uwzględnia pracy z dynamicznymi danymi. Dlatego, pomimo wielu zalet, fine-tuning w większości przypadków zwyczajnie się nie sprawdzi. Zatem sugerowana ścieżka polega na tym, aby w pierwszej kolejności zbudować i zoptymalizować prompty. Dopiero w sytuacji, gdy nie jest to wystarczające, warto rozważyć fine-tuning. 

Należy zaznaczyć, że opracowanie danych treningowych na potrzeby fine-tuningu, zwykle jest procesem czasochłonnym i nierzadko wymaga połączenia programowania, automatyzacji oraz pracy manualnej. Z kolei same wymagania dotyczące fine-tuningu mogą różnić się w zależności od modelu, oraz zadania, które chcemy osiągnąć. Z tego powodu, przejdziemy teraz przez przykład opracowany przez Jakuba, który zarysuje to, czym jest fine-tuning, bez konieczności wkładania ogromnej ilości pracy w przygotowanie danych. 

Dane treningowe, to w przypadku modelu GPT-3.5 Turbo, lista wiadomości znanych z API (chodzi o właściwość **messages**). Ich zadaniem jest **zaprezentowanie oczekiwanego zachowania**. Oznacza to, że w tej sytuacji nie wyjaśniamy tego, w jaki sposób odpowiedzi mają być generowane, lecz **pokazujemy to**. Zadaniem modelu jest samodzielne "zrozumienie" zasad obecnych w zestawie danych treningowych i dopasowanie się do wykonywania przyszłych zadań według tego samego schematu. Rekomendowana przez OpenAI liczba przykładów wynosi ~50 - 100 jakościowych zestawów, choć minimum ustawione jest na 10. Ostatecznie ich ilość będzie zależała od konkretnego przypadku. 

Poniższy p**rzykład wprowadzający w fine-tuning, opracował Jakub**, a ja dodałem do niego kilka słów swojego komentarza. 

**Cel:** Sprawienie by model odpowiadał po staropolsku z domieszką gwary krakowskiej.
**Zestaw danych treningowych:** [Pobierz](https://cloud.overment.com/aidevs_dane-1700397830.jsonl)

Uruchomienie fine-tuningu wymaga wgrania pliku danych treningowych na serwer OpenAI. W odpowiedzi otrzymamy nazwę w formacie "file-xxxxxxxxxxxxx", którą należy zapisać na potrzeby kolejnych kroków. 

![](https://cloud.overment.com/aidevs_start-a32a3f55-c.png)

Następnie można przejść do uruchomienia fine-tuningu na przekazanych wcześniej danych treningowych. Warto rozważyć dodanie parametru "suffix", którego wartość zostanie umieszczona w nazwie modelu i ułatwi jego rozpoznanie. 

![](https://cloud.overment.com/aidevs_job-620bf507-1.png)

Status fine-tuningu można obserwować na stronie: [platform.openai.com/finetune](https://platform.openai.com/finetune). Natomiast po zakończeniu procesu, na naszym koncie pojawi się nowy model, który będziemy mogli wykorzystywać zarówno w Playground, jak i bezpośrednio z poziomu API.

W przeciwieństwie do Jakuba, preferuję bardziej nieoficjalny i nowoczesny styl wypowiedzi. Dlatego to właśnie nim posługuje się Alice, co widać na obrazku poniżej.

![](https://cloud.overment.com/aidevs_tone-9e549c2c-d.png)

Jeśli zestawimy powyższą odpowiedź z domyślną wersją GPT-3.5-turbo, to wyraźnie zauważymy w niej nieco bardziej oficjalny ton, charakterystyczny dla treści generowanych przez model.

![](https://cloud.overment.com/aidevs_neutral-7e5078a6-3.png)

Przykład Kuby może sugerować, że fine-tuning to niezwykle proste zadanie. Jednak w większości przypadków, będzie zupełnie odwrotnie, ponieważ będziemy potrzebowali zdecydowanie większego zestawu danych treningowych, aby skutecznie zmienić zachowanie modelu. Co więcej, na ich opracowanie, możemy potrzebować zdecydowanie więcej czasu, w szczególności gdy będzie nam chodziło o **zwiększenie precyzji** realizowanych zadań, a nie proste nadpisanie tonu wypowiedzi. Poniżej znajduje się kilka spośród kilku tysięcy przykładów, które opracowałem na potrzeby wcześniejszych wersji modeli OpenAI, działających na wczesnej wersji Alice.

![](https://cloud.overment.com/aidevs_categorize-3516b6a8-a.png)

Czas potrzebny na przygotowanie przykładów, oraz koszt fine-tuningu, to nie jedyne rzeczy, które należy brać pod uwagę. Przede wszystkim jakiekolwiek zmiany w zachowaniu modelu wymagają albo opracowania kolejnych danych treningowych i kontynuowanie fine-tuningu, albo rozpoczynanie całego procesu od początku. Poza tym korzystanie z takiej wersji modelu **jest droższe od podstawowej wersji**, co w wielu przypadkach może okazać się zupełnie nieuzasadnione i pozostanie przy dobrze opracowanym prompcie, może okazać się zdecydowanie bardziej pożądane. Warto więc przeliczyć koszty różnych scenariuszy i uwzględnić w nich nie tylko finanse, ale także korzyści płynące ze skuteczności oraz z elastyczności danej ścieżki. 

Nie oznacza to jednak, że fine-tuning nie ma sensu, lecz jego rola polega na adresowaniu bardzo specyficznych przypadków, związanych z **nadpisaniem domyślnego tonu wypowiedzi modelu**, **strukturyzowaniem odpowiedzi** czy nawet **zwiększeniu precyzji Function Calling**. 

Dlatego jeśli dotrzesz do punktu w którym model nie osiąga wystarczającej skuteczności dla opracowanych promptów, to może rozważyć fine-tuning, zaczynając od stosunkowo małego zestawu danych (~100 przykładów) w celu sprawdzenia czy zauważalna jest jakakolwiek poprawa. Jeśli tak, znasz już kierunek, w który można się zaangażować. Dobrze też wcześniej zapoznać się z przykładami zestawów danych treningowych dostępnych na HuggingFace (przykład: [jamescalam/agent-conversations-retrieval-tool](https://huggingface.co/datasets/jamescalam/agent-conversations-retrieval-tool/viewer/default/train?p=2))




#aidevs_2