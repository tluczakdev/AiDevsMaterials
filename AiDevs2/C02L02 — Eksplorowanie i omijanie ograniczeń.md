# C02L02 — Eksplorowanie i omijanie ograniczeń

Mówiliśmy już sporo o ograniczeniach modeli. Część z nich widzieliśmy już w praktyce. Tym razem przyjrzymy się im z bliska i zobaczymy, jak fakt, że potrafimy programować (lub posługiwać się narzędziami no-code) pozwoli nam je naginać lub wprost omijać.

## Halucynacje i ograniczenia bazowej wiedzy

Głównym problemem halucynacji jest fakt, że nierzadko trudno ją wykryć. GPT-4, zapytany o link do Tailwind CDN, podaje go. Jednak aktualna wersja to v3.3.3, a nie v2.2.19. 

![](https://cloud.overment.com/limits-6d1f0ea0-a.png)

Uświadomienie modelu chociażby o bieżącej dacie do pewnego stopnia zmniejsza problem związany z posługiwaniem się nieaktualnymi informacjami. Jednak pomimo zasygnalizowania ograniczeń, sugestie modelu mogą zawierać sugestie niebędące dobrymi praktykami. 

![](https://cloud.overment.com/awareness-386aa97a-2.png)

Kolejne pytanie dotyczące jednej z nowszych funkcjonalności CSS jest częściowo poprawne, ale jest w stanie nakierować nas na rozwiązanie. Jeżeli jednak pytanie dotyczyłoby tematu, o którym model nie ma pojęcia, zostalibyśmy niejawnie wprowadzeni w błąd. 

![](https://cloud.overment.com/knowledge-2df13263-3.png)

- ⚡ [Zobacz przykład](https://platform.openai.com/playground/p/qn3EROLoaMQRcyJjgglgUWrG?model=gpt-4)

Temat ograniczeń bazowej wiedzy na potrzeby takich zadań skutecznie adresuje chociażby Phind, którego odpowiedź jest poprawna. Dodatkowe wyniki wyszukiwania lub źródła pozwalają je dodatkowo zweryfikować. Swoją drogą, jest to wskazówka dobrej praktyki, którą warto uwzględniać przy projektowaniu własnych integracji (mam na myśli podawanie źródeł).

![](https://cloud.overment.com/phind-8e5b27d1-b.png)

Poniżej przygotowałem przykładowy prompt, który **ogranicza wiedzę modelu do podanego kontekstu i niczego więcej**. Model posiada nadaną rolę, zasady, jeden przykład zachowania prezentujący odmowę udzielenia odpowiedzi (w celu podkreślenia tego faktu) oraz kontekst **z informacją o źródle**.

Pytanie na temat związany z informacjami zawartymi w kontekście, **skutkuje udzieleniem poprawnej odpowiedzi oraz podaniem źródła informacji.** Jednak w tym przypadku **jej jakość** jest proporcjonalna do jakości dostarczonego kontekstu. 

![](https://cloud.overment.com/context-294af44f-c.png)

Jeśli pytanie wykracza poza kontekst, model, zgodnie z oczekiwaniem, odmówi odpowiedzi. Jest to, w tym przypadku, oczekiwane zachowanie. Wówczas zyskujemy kontrolę, **jednak nie mamy 100% pewności, że model nie zmieni swojego zachowania**, np. w wyniku dostarczenia kontekstu zawierającego instrukcję lub nietypowego zapytania użytkownika. Teraz tylko to zaznaczam, a w praktyce zobaczymy to innym razem. 

![](https://cloud.overment.com/outside-1046956d-7.png)

- ⚡ [Zobacz przykład](https://platform.openai.com/playground/p/Xz4LwC6z8AGa3O9jwYqvWUWQ?model=gpt-4)

Zatem problem halucynacji modelu jest istotny i stanowi ogromne wyzwanie. W codziennej pracy możesz skorzystać z narzędzi, które adresują go poprzez świetne podłączenie modelu do Internetu. Jednak, gdy tworzysz system (lub nawet prosty mechanizm) pracujący na wyselekcjonowanych danych, to:

- Zadbaj o nadanie roli, kontekstu i zdefiniowanie zasad.
- Wyraźnie oddziel kontekst od reszty promptu.
- Wewnątrz kontekstu możesz wyodrębnić wyraźnie oddzielone od reszty fragmenty.
- Do załączonych fragmentów (jeśli masz taką możliwość), dołącz etykiety / metadane oraz źródła.
- Optymalizuj treści dołączane do kontekstu, **ale unikaj wypełniania go po brzegi**. Warto poświęcić czas na **precyzyjne wyszukiwania** lub umieszczanie w nim jedynie podsumowania wyników innych promptów (np. takich, odpowiedzialnych za podsumowanie).
- Stosuj wyrażenia regularne oraz placeholder'y w celu unikania dodawania do kontekstu np. długich linków, które zużywają tokeny. W trakcie generowania możesz je dynamicznie podmieniać lub doklejać jako źródła.
- Pamiętaj: **jakość odpowiedzi jest zwykle wprost proporcjonalna do jakości kontekstu**.

## Pierwsze kroki z zewnętrznym, dynamicznym kontekstem

To dobry moment, abyśmy zbudowali prosty mechanizm dynamicznego kontekstu, tworzonego na podstawie zewnętrznych źródeł (np. plików czy wyników wyszukiwania).

W przykładzie **[09_context](https://github.com/i-am-alice/2nd-devs/tree/main/09_context)** zapisałem plik Markdown z kilkoma informacjami na mój temat. Następnie mogę go wczytać za pomocą np. TextLoader (LangChain) i dołączyć do kontekstu. Oczywiście obowiązuje nas tutaj limit kontekstu, jednak widać, że model poprawnie potrafi wykorzystać jego treść. 

![](https://cloud.overment.com/context-45d0e692-4.png)

Powyższy kod stanowi przykład wspomnianego w poprzednich lekcjach systemu RAG (Retrieval-Augmented Generation), łączącego LLM z zewnętrznymi danymi. W tym przypadku możemy zadawać pytania na temat dostarczonej treści, natomiast do gry wchodzą także transformacje i integracje z zewnętrznymi usługami. Na przykład, źródłem danych mogłaby być notatka po spotkaniu, która mogłaby zostać zamieniona na obiekt JSON, a następnie przesłana przez API do systemu zarządzania klientami (CRM).

Na razie posługujemy się jeszcze bardzo małymi zestawami danych, więc z powodzeniem możemy w całości dodać je do kontekstu. Jednak w praktyce, rzadko coś takiego ma miejsce, dlatego konieczne jest sięgnięcie po przeróżne techniki pozwalające precyzyjnie wybrać istotny dla zapytania (lub konwersacji) kontekst. Tutaj naturalnie przychodzą do głowy silniki wyszukiwania oraz bazy wektorowe, jednak nimi zajmiemy się w późniejszych lekcjach.

W przykładzie **[10_switching](https://github.com/i-am-alice/2nd-devs/tree/main/10_switching)** możesz zobaczyć, jak wykorzystałem prompt do **wyboru źródła wiedzy** na podstawie zapytania użytkownika. Zadaniem promptu jest zwrócenie **wyłącznie nazwy pliku**, która może być wykorzystana w dalszej części logiki. 

![](https://cloud.overment.com/sources-60edd9e2-d.png)

Powyższy mechanizm, niemal **w takiej samej formie**, stosuję w swoich aplikacjach na potrzeby wyboru **zewnętrznych źródeł danych, np. adresów stron www**. Konkretnie wygląda to następująco: 

1. Na liście mam adresy oraz ich opisy. Np. "Definitions & General Knowledge": wikipedia.org.
2. Następnie na podstawie bieżącego zapytania (i ewentualnie krótkiego podsumowania konwersacji) wybierany jest adres strony **oraz zapytanie do wyszukiwarki**.
3. Adres strony przekazywany jest do SerpAPI w formacie: "site:adres_strony zapytanie".
4. Top 3 wyników trafia do kontekstu. Możliwe jest także **odczytanie i podsumowanie treści tych stron**.

Naturalnie należy pamiętać tutaj o **niedeterministycznej naturze modeli** oraz tym, że musimy zabezpieczyć się na wypadek zwrócenia wyniku, który **nie jest poprawny**. Możemy to zrobić poprzez zastosowanie wyrażeń regularnych czy promptów weryfikujących (elementy guardrails).

## Dokumenty

W poprzednich lekcjach mówiłem, że w pracy z zewnętrznymi danymi istotne jest **opisywanie ich metadanymi**, wśród których znajdą się np. informacje na temat ich pochodzenia. Są to tzw. "dokumenty", czyli obiekty składających się z **treści** oraz **metadanych**.

![](https://cloud.overment.com/document-a70178d0-1.png)

Posługując się danymi z ostatniego przykładu, **dokumentem nie będą całe pliki, lecz ich fragmenty (ang. chunk)**. Akurat w tym przypadku, zawartość dokumentu jest krótka, jednak zwykle **przechowujemy w nich możliwie małe zestawy danych, które zawierają istotne informacje.** Inaczej mówiąc, zależy nam na tym, aby dokumenty dzieliły nasze zestawy danych, ale nie robiły tego zbyt agresywnie, aby nie traciły swojego kontekstu. Zatem decyzja o długości treści dokumentu czy opisujących go metadanych **zależy od konkretnego przypadku**. Musimy jednak pamiętać, że ostatecznie zostaną umieszczone w prompcie, a to oznacza, że obowiązuje nas zarówno limit kontekstu, jak i dążenie do wybrania możliwie istotnych informacji dla danej interakcji z modelem. 

Połączmy więc teraz dotychczasową wiedzę, aby lekko zmodyfikować ostatnie przykłady. W katalogu **[11_docs](https://github.com/i-am-alice/2nd-devs/blob/main/11_docs/11.ts)** znajdziesz poniższy kod, którego zadanie polega na: 

1. Wczytaniu treści plików
2. Podziale ich na dokumenty według (w tym przypadku) podwójnego znaku nowej linii
3. Przetworzeniu dokumentów promptem **opisującym ich źródło** (w tym przypadku jest to wybór imienia osoby, z którą powiązany jest dany dokument)
4. Przetwarzanie odbywa się w pętli wykonującej zapytania **równolegle** w celu przyspieszenia procesu, z ograniczeniem do 5 zapytań jednocześnie w celu **uniknięcia limitów OpenAI API**

![](https://cloud.overment.com/processing-31c457b1-b.png)

Rezultatem działania skryptu jest **plik JSON** zawierający **listę dokumentów opisanych z pomocą słów kluczowych**. Naturalnie samo "wzbogacenie / opisanie" mogłoby uwzględniać także bardziej rozbudowany prompt. Widzisz jednak tutaj wyraźnie, jak możesz wykorzystać LLM do przygotowywania zestawów wiedzy na potrzeby dynamicznego kontekstu i systemów, np. RAG. 

![](https://cloud.overment.com/json-3dc9e646-6.png)

Powyższe dokumenty są istotne także ze względu na to, że niebawem będziemy wykorzystywać je w połączeniu z bazami wektorowymi, które będą przechowywać je jako embedding. Zaznaczę tylko, że **nie musisz wykorzystywać LangChain**, bo jak widzisz, mówimy o bardzo prostych obiektach JSON. LangChain po prostu narzuca nam tutaj pewną strukturę i daje kilka przydatnych narzędzi. 

## Dostęp do Internetu i czytanie stron www

Przedstawiając Ci niektóre z realizowanych przeze mnie projektów, mówiłem, że wskazane jest uzyskanie **bezpośredniego dostępu do ustrukturyzowanych danych** (np. baza danych / API). Może się jednak zdarzyć, że nie będziemy mieli wyjścia i konieczne będzie podłączenie się do źródeł o zróżnicowanej strukturze. Jednym z takich źródeł są **treści stron www**.

Zautomatyzowana praca z treściami stron wchodzi w obszar Web Scrapingu, który uznaje się za **szarą strefę** ze względu na prawa do zawartości stron. Zawsze należy mieć to na uwadze i w kontekście komercyjnym pracować ze swoimi własnymi źródłami, lub tych, których licencja na to pozwala. W przypadku zastosowań prywatnych sytuacja jest nieco inna, jednak i tak należy mieć na uwadze polityki prywatności oraz regulaminy serwisów i stron www.

Scrapowanie i parsowanie treści stron www jest **ogromnym wyzwaniem** ze względu na **zróżnicowanie ich struktury** oraz **dynamiczne wczytywanie ich elementów**. W poprzedniej edycji AI_Devs do odczytywania treści stron wykorzystywaliśmy bibliotekę [unfluff](https://www.npmjs.com/package/unfluff), jednak jej skuteczność nie jest stuprocentowa, choć sama w sobie jest bardzo wygodna. Obecnie możemy stosunkowo łatwo skorzystać z nieco bardziej zaawansowanych narzędzi, które dają nam większą kontrolę. Mowa o Cheerio, Puppeteer czy Playwright, do których możesz podłączyć się samodzielnie, lub przez dostępne klasy w LangChain. 

Cheerio pracuje bezpośrednio na pobranej zawartości HTML strony, co może się przydać w przypadku prostych struktur. Puppeteer i Playwright **wykorzystują Chromium do interakcji ze stronami**, co wymaga więcej zasobów, ale daje bardzo szerokie możliwości automatyzacji. Podstawowe zastosowanie **loadera** z Puppeteer przedstawiam w przykładzie **[12_web](https://github.com/i-am-alice/2nd-devs/tree/main/12_web)**.

![](https://cloud.overment.com/scrapper-7e1aeebb-e.png)

Zwróć uwagę na to, że **nie pobieram całej treści strony**, tylko skupiam się na jej fragmencie, a konkretnie na selektorze **.main**. W celu pozbycia się niepotrzebnych tagów HTML, a jednocześnie utrzymania struktury nagłówków, formatowania, obrazków i linków, **transformuję HTML do składni Markdown**.

Rezultat jest prawie zgodny z moim oczekiwaniem, jednak adresy podstron **niepotrzebnie zużywają tokeny**, a w dodatku istnieje pewne ryzyko, że model niepoprawnie by je przepisał w trakcie wypowiedzi. Dlatego w dalszej części tego przykładu, **wykorzystuję wyrażenia regularne** oraz **podmianę treści**, aby **adresy URL zostały zapisane jako metadane**, a **w treści pojawiły się placeholdery**.

![](https://cloud.overment.com/regex-c59fcdd9-a.png)

Wówczas otrzymujemy dokument, który może już z powodzeniem trafić do kontekstu LLM. Oczywiście dla dłuższych treści konieczne byłoby dalsze przetwarzanie, jednak w tym momencie możemy się zatrzymać. 

![](https://cloud.overment.com/md-ba2a09bd-0.png)

W związku z charakterystyką Web Scrapingu oraz **różnym poziomem merytorycznym** stron www, blogów itd., zauważyłem, że warto **doprecyzować to, z jakimi źródłami integruje się nasza aplikacja**. W przypadku mojej wersji Alice są to głównie **dokumentacje** oraz **blogi**, które czytam. Czasem stanowi to pewne ograniczenie, jednak daje nam to większą kontrolę oraz przestrzeń do optymalizacji, ponieważ nawet przykład powyżej pokazuje, że zamknięta lista stron **pozwala zawężać sposób przeszukiwania jej treści** i tym samym oszczędzać tokeny.

## Sytuacje w których LLM się nie sprawdzą i co możemy z tym zrobić?

Nie trudno się domyślić, że zaprezentowane powyżej przykłady nie są odpowiedzią na każde pytanie. Nawet praca ze stronami, których treści dostępne są dla zalogowanych użytkowników, może stanowić wyzwanie. Podobnie, jeśli strona wykorzystuje **dynamiczne klasy html**, to również dostęp do wybranych fragmentów treści jest utrudniony. Praca z obrazami i wideo, czy innymi formatami plików, także stawia przed nami kolejne wyzwania.

Dlatego, gdy spotykasz problem, którego obecna technologia nie może zaadresować, lub zwyczajnie jest to mało efektywne (np. kosztowo), to możesz pomyśleć o **optymalizacji obecnego procesu** lub jego **wsparciu**, zamiast pełnej automatyzacji. Niektórych rzeczy, po prostu, nie warto robić. 

Scenariusze, w których LLM mogą się nie sprawdzić, uwzględniają: 

- Pracę z trudno dostępnymi danymi w związku z ich formatem
- Przetwarzanie wymagające bardzo wysokiej precyzji (przypomnij sobie wykres z jednej z poprzednich lekcji, porównujący skuteczność człowieka i LLM)
- Przetwarzanie liczb (bez programistycznego wsparcia)
- Generowanie wysokiej jakości treści (bez fine-tuningu)
- Przetwarzanie ogromnych zestawów danych (ze względu na koszty)
- Akcje wymagające niskiego czasu reakcji i wysokiej wydajności
- Zadania związane z **bezpośrednią interakcją z człowiekiem**
- Zadania związane z przetwarzaniem i generowaniem obrazu
- Systemy autonomiczne (np. AutoGPT jest imponujące, ale aktualnie praktyczne zastosowanie jest ograniczone)
- Systemy pracujące na tajnych / bardzo wrażliwych danych (obecnie dostępne opcje obejmują plany Microsoft Azure OpenAI, ChatGPT Enterprise lub modele OpenSource)

Lista oczywiście mogłaby być dłuższa, jednak skupiłem się na **najczęstszych zapytaniach**, z którymi się spotkałem. Należy także podkreślić, że mówimy tutaj o **Dużych Modelach Językowych** dostępnych powszechnie (np. OpenAI). W przypadku Fine-Tuningu czy budowania wyspecjalizowanych modeli (co wykracza poza zakres naszego szkolenia), sytuacja wygląda inaczej, co potwierdza wiele usług na rynku. 

#aidevs_2