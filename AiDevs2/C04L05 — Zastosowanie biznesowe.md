# C04L05 — Zastosowanie biznesowe

Budowanie narzędzi na swoje potrzeby może mieć bardzo pozytywny wpływ na naszą efektywność i komfort pracy. Jednak prędzej czy później przyjdzie nam stosować AI w obszarach typowo zawodowych — mowa tutaj zarówno o narzędziach wewnętrznych, jak i produktach z których korzystają klienci. Zobaczmy więc, to już w zasadzie teraz możemy zrobić z LLM w kontekście typowo biznesowym.

## Przykłady zastosowań LLM w biznesie

Niemal dla każdego oczywisty jest wpływ dobrze dobranych narzędzi na efektywność działania biznesu. W niektórych sytuacjach rola ta staje się tak istotna, że uzasadnione jest **budowanie własnych rozwiązań**. Zastosowanie narzędzi no-code sprzyja dynamice ich tworzenia i tym samym zwiększa zwrot z inwestycji (eng. Return of Investment, ROI). Niemałą rolę w tej układance odgrywa również automatyzacja, której koncepcja również powinna być dla nas jasna. Dziś to wszystko zaczyna działać na zupełnie nowym poziomie, dzięki Generative AI oraz możliwości interakcji zarówno z modelami dostępnymi w chmurze, jak i modelami Open Source.

Lekcje C04L03 oraz C03L05 podkreśliły techniczne aspekty stosowania LLM produkcyjnie oraz w automatyzacjach działających w tle. W tym wszystkim istotny był także zestaw wykorzystywanych narzędzi, a w szczególności możliwość interakcji z nimi poprzez API.

**Interakcja z ChatGPT / Perplexity / Midjourney / Whisper**

Pierwszym, najbardziej powszechnym i oczywistym zastosowaniem LLM w biznesie, jest dostarczenie w organizacji wiedzy do tego, jak skutecznie pracować z AI wykorzystując gotowe narzędzia. Na rynku praktycznie każdego dnia pojawiają się produkty adresujące konkretne problemy lub oferujące rozwiązania, które wcześniej nie były dostępne (np. automatyczne notatki ze spotkań). Dziś zadania, które wcześniej zajmowały mnóstwo czasu, mogą być realizowane jednym kliknięciem lub poprzez znacznie uproszczony proces. Mowa o transformacji tekstu, obrazu, dźwięku, a nawet wideo.

Odkrywanie nowych aplikacji i sposobów ich zastosowania nie jest oczywiste i wymaga stałego procesu oraz odpowiedniej kultury organizacji (np. wewnętrzne spotkania czy szkolenia) oraz indywidualnych działań poszczególnych osób. Dla mnie najważniejszą aktywnością jest obserwowanie **‌[producthunt.com](https://producthunt.com)** oraz aktywne testowanie nowych narzędzi w formie trial lub miesięcznego okresu próbnego (dla usług wymagających wpięcia karty stosuję jednorazowe karty Revolut).

Narzędzia z których sam korzystam w codziennej pracy, to:

- **Perplexity.ai**: To usługa o której już mówiłem i która obecnie zastępuje mi ChatGPT. Dostęp do wyników wyszukiwania z Internetu zmniejszają ryzyko halucynacji, a ogólne tempo działania jest zwyczajnie znacznie wyższe niż w przypadku ChatGPT Plus. 

![](https://cloud.overment.com/oauth-8adeec39-1.png)

- **Alice**: Niemal cały czas pracuję z aplikacją "Alice", którą rozwijamy z myślą o optymalizowaniu naszej pracy. Największą korzyścią jest jej **kontekstowe zastosowanie w dowolnym miejscu na komputerze**. Bardzo dobrą alternatywą jest Raycast AI (macOS) lub Microsoft 365 Copilot (Windows).

![](https://cloud.overment.com/aidevs_alice-1e1b4f90-1.png)

- **Midjourney**: W którym wspólnie z naszym graphic designerem zbudowaliśmy generyczne prompty ułatwiające generowanie grafik **spójnych z naszą marką eduweb.pl**, które osadzone są w Figmie oraz szablonach wykorzystujących komponenty (podmiana grafiki w jednym miejscu, zmienia ją na wszystkich formatach)

![](https://cloud.overment.com/aidevs_templates-aab3d6eb-c.png)

- **OpenAI API**: Wykorzystujemy do projektowania dopasowanych do naszych procesów wewnętrznych narzędzi, których rolą jest znaczne zoptymalizowanie wybranych procesów. Niemal nigdy nie mówimy jednak o pełnej automatyzacji, ponieważ zawsze w jakimś miejscu zaangażowany jest człowiek. Poniższy przykład prezentuje tłumaczenie tekstu, który w tym przypadku został przetłumaczony bezpośrednio w Notion, z pełnym zachowaniem oryginalnego formatowania. Tak przetłumaczony tekst przechodzi także dodatkową weryfikację i korektę ze strony osoby odpowiedzialnej za ten proces. Podobne mechaniki stosowane są także w obszarach SEO.

![](https://cloud.overment.com/aidevs_translations-de835d02-3.png)

- **Adobe Firefly / Photoshop Beta**: Kolejnym narzędziem związanym z obszarem edycji grafiki, jest najnowsza wersja Adobe Photoshop, oferująca szereg funkcjonalności, takich jak Generative Fill czy Generative Expand. Stanowi idealne uzupełnienie Midjourney i najnowszej wersji DALL·E.

![](https://cloud.overment.com/aidevs_firefly-8029aad4-e.png)

- **Whisper / HappyScribe**: Niemal wszystkie treści audio / wideo, które powstają w naszych firmach są zamieniane na tekst z pomocą Whisper lub HappyScribe. Generowanie transkrypcji jest przydatne nie tylko na potrzeby kursów, ale także w przypadku firmowej dokumentacji / bazy wiedzy, która może być łatwo przeszukana. 

![](https://cloud.overment.com/aidevs_happy-6c971257-b.png)

- **Własne rozwiązania**: Dla małych procesów i aktywności, które mają potencjał na automatyzację i wsparcie z pomocą AI, budujemy scenariusze make.com. Jedną z praktyk stosowanych przeze mnie i Grzegorza, jest Second Brain, czyli specjalny panel w Notion. Wewnątrz niego synchronizują się nam dane z różnych kont i usług, ułatwiając zarządzanie wieloma projektami. Zbudowanie takich narzędzi zwykle trwa 30-120 minut i pozwala oszczędzać czas i energię przez kolejne miesiące. 

![](https://cloud.overment.com/aidevs_calendar-f885fa33-2.png)

- **Ogólny UX**: Nie wszystkie zastosowania AI obejmują wymiar użyteczności i zwiększania skuteczności. W niektórych przypadkach do gry wchodzą także proste integracje. Poniżej widać jak wcześniej stały komunikat informujący przekazania $AHOY Coin w naszej społeczności, zostaje zamieniony na jego sparafrazowaną wersję. To drobny detal, jednak zwykle ich połączenie i zastosowanie w wielu miejscach, zaczyna robić różnicę.

![](https://cloud.overment.com/aidevs_comments-a8ae2a6e-1.png)

Poza wymienionymi narzędziami, stosujemy jeszcze mniejsze usługi i aplikacje, które są wyspecjalizowane w pojedynczych zadaniach (np. usuwania tła ze zdjęć czy generowaniu audio na podstawie tekstu). Chociaż warto wziąć pod uwagę wymienione przeze mnie rozwiązania, to zdecydowanie konieczne jest także samodzielne odkrywanie zastosowań dostępnych na rynku narzędzi i dopasowywanie ich do potrzeb naszej pracy. 

Gdybym miało to podsumować: 

- Warto zbudować proces eksploracji narzędzi AI, zwykle wymagający zarezerwowania czasu w kalendarzu na takie aktywności oraz wspieranie kultury wymiany wiedzy
- Warto korzystać z najlepszych na rynku narzędzi i mocno **dostosować je do swoich potrzeb**
- Warto odkrywać niszowe rozwiązania, wyspecjalizowane w pojedynczych zadaniach
- Warto budować w pełni dopasowane do wewnętrznych procesów narzędzia

Nadal jednak poruszamy się tutaj raczej w obszarze gotowych narzędzi i ich dopasowania. Jest to najprostszy sposób na to, aby niskim kosztem i nakładem pracy, zacząć wykorzystywać AI w swojej firmie. 

## Budowanie prototypów, MVP i modułów produktów

Po pracy z gotowymi narzędziami, oraz budowaniem mniejszych rozwiązań, kolejny etap zastosowania AI obejmuje bezpośrednio obszary produktowe. Co ważne, nie mówimy tutaj wyłącznie o produktach integrujących LLM, ponieważ rola AI może polegać na **wsparciu procesu developmentu**.

Wykorzystanie GPT-4 w procesie **kreatywnym**, **koncepcyjnym** czy **decyzyjnym** wnosi mnóstwo wartości, w szczególności gdy **włożymy pracę w dostarczenie własnego kontekstu** oraz poprowadzimy model przez nasze procesy myślowe. Robienie tego w połączeniu z wiedzą na temat technik interakcji z modelem, czy nawet zbudowanie prostych narzędzi, może przełożyć się nie tylko na oszczędność czasu, ale także na realne poszerzenie perspektywy i zidentyfikowanie błędów myślowych. **GPT-4 wprost genialnie sprawdza się w roli krytyka**, w szczególności gdy w kontekście pojawią się sugestie o stosowaniu [modeli mentalnych](https://fs.blog/mental-models) (których wykorzystanie szczególnie polecam).

![](https://cloud.overment.com/aidevs_fsblog-15bffcd1-6.png)

Drugim istotnym obszarem budowania prototypu (zarówno pierwszej wersji, jak i jej modyfikacji), jest sam proces developmentu. Nie mówimy jednak tutaj o **generowaniu kodu prototypu**, a wykorzystaniu modelu w procesie **szybkiego rozwiązywania problemów, debugowania czy generowania małych fragmentów logiki aplikacji**. 

Dobrym przykładem jest jedna z funkcjonalności aplikacji Alice, która pozornie wygląda dość łatwo — pole tekstowe w którym możemy "nagrać" kombinację klawiszy. Okazuje się, że zaimplementowanie takiej logiki (przynajmniej dla mnie) nie jest oczywiste, ponieważ uwzględnia wiele warunków. Ostatecznie przełożenie ich wszystkich na kod jest możliwe, ale zajmuje czas. Przygotowując pierwszą wersję aplikacji, nie chciałem zatrzymywać się na tak prostej funkcjonalności, dlatego dużą część logiki wygenerował dla mnie GPT-4, co **znacznie przyspieszyło pracę** nad pierwszą wersją aplikacji. 

![](https://cloud.overment.com/aidevs_keyboard-1696872114.gif)

Podkreślam tutaj określenie "przyspieszyło", a nie "wykonało za mnie", ponieważ sama funkcjonalność była zbyt złożona dla modelu, a jednocześnie doskonale radził sobie z jej elementami. 

Istotne jest tutaj również to, że aplikację przygotowywałem w **najnowszej wersji SvelteKit**, o której GPT-4 nie ma zbyt wiele pojęcia. Dlatego ważną rolę odegrało tutaj **przekazanie kontekstu oraz momentami także fragmentów dokumentacji** (wówczas Perplexity nie było dostępne).

Pracując z GPT-4 nad prototypem realizowanym w nowych technologiach warto brać pod uwagę: 

- **Izolowanie problemu** i pracę na możliwie małym fragmencie, nad którym mamy kontrolę
- **Nadawanie wyczerpującego kontekstu** w postaci opisu rozwiązywanego problemu, naszych założeń, wymagań oraz fragmentów kodu
- **Generowanie małych ilości kodu**, aby uniknąć sytuacji w której debugowanie zajmuje więcej czasu niż napisanie logiki od podstaw
- **Szybkie debugowanie** polegające na pracy z małymi fragmentami kodu w których potencjalnie może znajdować się problem. Model jest w stanie (zwykle) wychwycić problem szybciej niż człowiek, oraz zasugerować rozwiązanie lub **naprowadzić na nie** (regularnie zdarza mi się, że GPT-4 nie poda poprawnego rozwiązania, ale kieruje na niego moją uwagę)
- **Redukcję powtórzeń** w przypadku prostych zadań, których wdrożenie wymaga czasu. Co ciekawe, nie musi to uwzględniać wyłącznie prostych zadań. Kiedyś na potrzeby prototypu potrzebowałem utworzyć rozbudowany blok **switch** uwzględniający kilkanaście warunków (które łatwo mogłem weryfikować). Model z pewnością rozwiązał ten problem szybciej ode mnie

## Optymalizacja procesów

LLM sprawdzają się także w pozostałych etapach rozwijania aplikacji. Schemat wygląda wówczas dość podobnie, jednak do gry wchodzi już obecność użytkowników i w związku z tym należy uwzględnić zasady o których mówiłem we wcześniejszych lekcjach. Co więcej, zastosowanie modelu może wyjść także znacznie poza sam obszar developmentu i być stosowane w marketingu czy wsparciu obsługi klienta. 

Aktualnie pracuję nad kilkoma narzędziami, których przeznaczeniem jest wsparcie procesów związanych z wydarzeniami eduweb.pl oraz aktywnościami dotyczącymi ekspansji zagranicznej easy.tools. Poniżej znajduje się bardzo szczegółowy opis procesu realizowania tzw. Sprintów Technologicznych, który (jak widać) uwzględnia szereg aktywności, które muszą wydarzyć się według ustalonego harmonogramu, oraz jednocześnie nie wchodzić w drogę równoległym sprintom. 

![](https://cloud.overment.com/aidevs_sprint-1d95b186-c.png)

Koordynowanie kilkunastu równoległych projektów pracujących na tych samych zasobach (np. dostępności studio) wymagałoby mnóstwo pracy ze strony człowieka. W tym przypadku aplikacja będzie w stanie samodzielnie reagować na ewentualne konflikty czy opóźnienia. Co więcej, dzięki zastosowaniu LLM, możliwe będzie uwzględnienie funkcjonalności wykraczających poza zwykłe warunki "if" i generyczne komunikaty. Przykładowo model będzie mógł zestawić tematykę sprintu z treściami dostępnymi na eduweb.pl i tym samym skrócić czas ich przygotowania oraz wzbogacić w treści, które normalnie mogłyby zostać pominięte. Ostatecznie jednak kontrola nad procesem pozostanie w rękach Asi. 

Takie wewnętrzne narzędzie stanowi doskonały przykład zastosowania obecnego potencjału modeli językowych, ponieważ **nie wychodzi poza organizację**, **posługuje się publicznie dostępnymi treściami** oraz **jest wykorzystywana przez osoby posiadające ogólną wiedzę na temat pracy z modelami**. Nie mamy więc tutaj do czynienia z szeregiem produkcyjnych wyzwań, a jednocześnie otrzymujemy realną wartość. 

W momencie pisania tych słów, narzędzie o którym mowa jest jeszcze na etapie fazy koncepcyjnej oraz gromadzenia danych. Docelowo będzie integrować się zarówno z kalendarzem jak i listami zadań osób z zespołu. Widać więc tutaj wyraźnie, że mówimy o integracjach **które już zdążyliśmy omówić w AI_Devs**. Wystarczy je teraz tylko ze sobą połączyć, a zastosowanie narzędzi no-code z całą pewnością ułatwi wdrożenie tego rozwiązania.

I teraz bardzo ważny wątek: **Czy takie narzędzie będzie perfekcyjnie opracowane zgodnie ze sztuką programowania?** Oczywiście, że nie. Takie oczekiwanie zabiłoby ten projekt już na etapie koncepcji, ponieważ w dość małym zespole nie bylibyśmy go w stanie wdrożyć. Na szczęście jednak, zwyczajnie nie jest nam to potrzebne, ponieważ cel jest zupełnie inny. Piszę o tym, ponieważ projektowanie takich rozwiązać zwyczajnie zasadniczo różni się od budowania funkcjonalności produkcyjnych aplikacji. Jednocześnie wiem na swoim przykładzie, że nadal możemy w ten sposób budować narzędzia mające realny, pozytywny wpływ na procesy wewnętrzne. Wyjątek mogą stanowić jedynie rozbudowane organizacje, w których zwyczajnie nie możemy pozwolić sobie na pominięcie np. kwestii wydajności powiązanej ze skalą działania. Tutaj **mówimy o rozwiązaniu na którym będzie pracować kilka osób**, ale którego obecność **będzie miała wpływ na tysiące ludzi** biorących udział w naszych projektach. 

Zatem przekładając to na kontekst Twój, Twojej firmy, pracodawcy czy klientów, mówimy o sytuacji w której identyfikujemy wewnętrzny problem i definiujemy jego zakres. Na tej podstawie określamy **w jaki sposób AI może nam pomóc i czy w ogóle jest to wymagane**. Prawdopodobnie przytoczony przeze mnie przykład Sprintów Technologicznych na eduweb.pl podsunie Ci różne pomysły. Jeżeli tak by się nie stało, to polecam zwrócić uwagę na: 

- realizowane już aktywności i procesy, które są jasno zdefiniowane lub można je łatwo doprecyzować. Wymyślanie na siłę zastosowań AI, zwykle nie jest dobrym pomysłem
- wybór i skupienie się na konkretnym celu przy jednoczesnym pominięciu zbędnych elementów, które wydłużają czas developmentu, a ich rola może być marginalna
- elastyczność rozwiązania pod kątem modyfikacji i aktualizacji. Z pewnością nie chcemy budować narzędzi, które nie będą w stanie reagować na zmiany w procesach firmowych. Podobnie też aktualizacje modeli czy generalny rozwój AI również powinien być brany pod uwagę już na etapie wczesnego projektu
- optymalizacji mogą podlegać zarówno całe procesy, jak i ich pojedyncze elementy. Nierzadko wzięcie pod uwagę zbyt dużego zakresu (np. obieg dokumentów w firmie) jest zbyt wymagający i lepiej skupić się na jego małej części (np. gromadzenia ich w odpowiednich katalogach na końcowym etapie procesu)

Ostatecznie wszystko to, o czym teraz rozmawiamy, jest kwestią bardzo indywidualną dla każdej organizacji. Dlatego poruszamy się teraz na dość ogólnym poziomie obejmującym szeroką perspektywę, którą należy połączyć z rozwiązaniami, które omawialiśmy we wcześniejszych lekcjach. 

## Szanse i zagrożenia biznesowe

Istnieje szereg szans i zagrożeń biznesowych związanych z obecnością sztucznej inteligencji oraz jej dostępnością. Narracja w Internecie często uderza w miejsca, które wzbudzają najwięcej emocji, dlatego dobrze będzie spojrzeć na temat Generative AI możliwie chłodnym okiem.

- **Wczesny etap rozwoju** może być postrzegany na różne sposoby. Modele, które już dziś mamy do dyspozycji realnie przyczyniają się do zwiększenia efektywności zarówno jednostek, jak i całych organizacji. Bez wątpienia przewaga wynikająca ze stosowania tych narzędzi wobec osób, które tego nie robią jest znacząca. Z drugiej strony wysiłek który trzeba włożyć na tym etapie w niedoskonałe narzędzia czy typowe problemy wczesnego rozwoju technologii, jest znaczący. **Należy więc szukać dźwigni** umożliwiającej uzyskanie możliwie wysokiego zwrotu z inwestycji przy możliwie niskim wysiłku. Jedną ze ścieżek przedstawionych w AI_Devs są narzędzia no-code i automatyzacje, jednak równie dobrze może to być dopracowany proces zdobywania wiedzy, eksperymentowania i budowanie rozwiązań w oparciu o głębokie zrozumienie obszaru AI. 

- **Dynamiczne zmiany** sprawiają, że trudno mówić o jakiejkolwiek przewidywalności. Nawet realizowanie takiego projektu jak AI_Devs stanowi ogromne wyzwanie ze względu na to, że praktycznie z dnia na dzień możemy uzyskać dostęp do narzędzi oferujących zupełnie nowe możliwości (np. GPT-4V). Ważne jest jednak to, że trudno mi wymienić umiejętności, które zdobyłem rok temu (zaraz po premierze chatGPT), które dziś okazują się bezużyteczne. Wprost przeciwnie — to, co wcześniej było trudne, dziś jest zdecydowanie bardziej dostępne, bardziej wydajne i efektywne. Jednocześnie bez wątpienia koniecznym elementem było tutaj pozostawanie na bieżąco poprzez **mój system zdobywania wiedzy** (który wspierany jest przez AI), oraz **praktyczne doświadczenie**.

- **Ograniczenia modeli i narzędzi** są widoczne praktycznie na każdym kroku, szczególnie przy budowie rozwiązań produkcyjnych. Jednak chociażby powyżej pokazałem, że z powodzeniem możemy stosować AI wewnętrznie i mamy także przykłady produktów z których mogą korzystać użytkownicy. Adresowanie ograniczeń modeli w tej chwili odbywa się niemal zawsze poprzez **architekturę** systemu korzystającego np. z LLM. Już widzieliśmy, w jaki sposób możemy przenosić ciężar aplikacji z modelu na kod i rozdzielać odpowiedzialności w taki sposób, aby zachować stabilność przy jednoczesnym korzystaniu z możliwości AI. W kontekście ograniczeń prawdopodobnie najbardziej istotną rolę pełni **zrozumienie** ograniczeń z którymi się mierzymy, ponieważ to ono będzie nas prowadzić do możliwych rozwiązań.

- **Niewłaściwe zastosowanie** wynikające chociażby z chęci skorzystania z nowej technologii w projektach, które zwyczajnie jej nie wymagają. LLM, bazy wektorowe czy inne narzędzia z obszaru AI z pewnością nie są odpowiedzią na każdy problem. Niemal na każdym kroku spotykam pomysły, projekty i całe firmy, które zdecydowanie nie potrzebują AI, a na siłę próbują je wdrożyć. Generuje to koszty, odwraca uwagę od głównej działalności firmy i zamiast dawać wartość, może generować szereg problemów. Warto więc zachować rozsądek i dobrze zastanowić się nad tym czy "kolejny czatbot" rzeczywiście jest potrzebny w obszarach, w których wystarczy dobrze działająca wyszukiwarka. 

- **Zupełnie nowe możliwości** sprawiające, że całe produkty, których zbudowanie wcześniej było albo niemożliwe, albo bardzo kosztowne, dziś mogą powstawać relatywnie szybko. Niezależnie od tego, czy mówimy o wewnętrznych narzędziach czy w pełni publicznych rozwiązaniach, Generative AI z pewnością otworzyło zupełnie nowy rozdział w technologii. Tym bardziej, że należy pamietać o tym, że w AI_Devs poruszamy się na dość wysokim poziomie abstrakcji, korzystając głównie z gotowych modeli. Ma to oczywiście swoje zalety i ograniczenia, ale na koniec dnia fundamentalnie zgadzam się z [Mirą Murati z OpenAI](https://www.youtube.com/watch?v=KpWNCQnHg20) i jej słowami o tym, że **wszyscy skupiają się teraz na trenowaniu własnych modeli, podczas gdy ogromnym wyzwaniem pozostaje budowanie produktów w oparciu o modele, które już teraz mamy do dyspozycji**. Oczywiście nie neguję w tym roli rozwoju samych modeli, jednak bez wątpienia nie jest to jedyne wyzwanie (oraz szansa), którą mamy przed sobą.

#aidevs_2