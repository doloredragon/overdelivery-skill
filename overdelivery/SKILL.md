---
name: overdelivery
description: Zleca świeżemu agentowi przegląd gotowej pracy (kod, skill, dokument, plan) pod kątem nadmiaru wobec zamówienia (gold plating, over-engineering) i daje mapę decyzji, co wyciąć. Use when użytkownik pyta "czy nie doszło do overdelivery", "czy to nie przekombinowane", "czy nie dodałeś niepotrzebnych rzeczy", "czy to nie za dużo jak na ten etap". Nie do szukania błędów.
disable-model-invocation: false
allowed-tools: Read Glob Grep Agent Bash(wc *) Bash(ls *)
---

# overdelivery - czy zbudowano więcej, niż zamówiono, i czy to ma sens

Claude przy budowie dokłada: funkcje ponad zamówienie, zakres rosnący po kawałku, rozwiązania cięższe niż problem. Bramka poprawności (przegląd błędów) dokłada jeszcze więcej, bo pyta "co może pójść źle", a nie "czy to potrzebne" - w jednym z projektów dwie rundy takiej bramki dały ok. 25-30% kodu w przypadkach brzegowych. Ten skill zleca przegląd świeżemu agentowi i oddaje użytkownikowi decyzje.

## NAJWAŻNIEJSZE ZASADY

- **Nadmiar to nie błąd sam w sobie.** "Więcej teraz" jest w porządku, gdy ma konkretne uzasadnienie: upraszcza coś później, jest koniecznym przygotowaniem pod zaplanowany etap, zastępuje kilka rzeczy jedną, chroni przed sprawdzonym (nie wymyślonym) błędem. Błędem jest nadmiar bez uzasadnienia: spekulacja "na zapas", funkcja bez zamówienia, mechanizm na przypadek, który raczej nie wystąpi, praca późniejszego etapu zrobiona teraz bez potrzeby, złożoność większa niż problem. "Może się przydać" nie jest uzasadnieniem.
- **Autor nie ocenia sam siebie.** Przegląd robi świeży agent, który dostaje wyłącznie zamówienie i ścieżki wyniku: bez historii budowy, bez oceny autora, bez "dlaczego tak zrobiłem". Inaczej przegląd powtarza tok myślenia autora.
- **Bez zamówienia nie ma przeglądu.** Agent, który nie wie, co zamówiono, tnie na ślepo albo ocenia według własnego gustu. Brak jasnego punktu odniesienia = jedno pytanie do użytkownika (AskUserQuestion, propozycje, gdzie leży zamówienie), zanim cokolwiek ruszy.
- **"Wygląda na nadmiar, ale zostawić" jest obowiązkowe.** Krytyk prostoty bez tej listy wycina rzeczy, które wrócą jako problem (przykład: atrapy wykonawców wyglądały na zbędne, a były jedynym sposobem na przetestowanie wznawiania pracy po przerwaniu).
- **Zero cięć przed akceptem.** Do akceptu użytkownika skill tylko czyta i raportuje. Cięcia, uproszczenia i zmiany w plikach dopiero po jego numerach.
- **Wynik agenta to hipoteza.** Znaleziska z wagą "duża" i każdą rekomendację "wyciąć" sprawdzasz sam w plikach, zanim trafią do mapy jako "Przyjmuję".

## KIEDY UŻYWAĆ

- "sprawdź, czy nie doszło do overdelivery", "czy nie dodałeś niepotrzebnych rzeczy"
- "czy to nie jest niepotrzebnie skomplikowane / przekombinowane / przeinżynierowane"
- "skonsultuj z agentem, czy nie przesadziłem", "przegląd prostoty", "gold plating", "scope creep", "over-engineering"
- "czy to nie za dużo jak na [etap / wersję 1]"
- Propozycja od Claude'a jedną linijką po większej budowie (narzędzie, skill, wieloetapowy dokument), zwłaszcza po bramce poprawności: "Przegląd nadmiaru? świeży agent, kilka minut - odpalać?". Start dopiero po "tak".

## KIEDY NIE UŻYWAĆ

- Błędy i poprawność: przegląd błędów, weryfikator faktów, `/code-review`. Uproszczenia samego kodu bez pytania o zamówienie: `/simplify`.
- Styl i redakcja tekstu: osobny przegląd redakcyjny.
- Druga opinia o pomyśle, planie albo decyzji przed budową: rozmowa krytyczna albo skill do konsultacji z innym modelem, jeśli go masz.
- Użytkownik wprost chce opinii modelu spoza Claude'a: odpowiedni skill albo narzędzie do tego modelu; brief z `references/brief_agenta.md` możesz tam wykorzystać jako treść pytania.

## PRZEBIEG

Skopiuj checklistę do odpowiedzi i odznaczaj:

```
Przegląd nadmiaru:
- [ ] 1. Zamówienie i wynik ustalone (ścieżki, cytaty użytkownika); brak zamówienia -> jedno pytanie
- [ ] 2. Model agenta wybrany, brief z references/brief_agenta.md
- [ ] 3. Agent uruchomiony (general-purpose, jawny model)
- [ ] 4. Krytyczne znaleziska sprawdzone w plikach
- [ ] 5. Mapa decyzji wg references/mapa_decyzji.md - STOP, czekam na numery użytkownika
- [ ] 6. Po akcepcie: cięcia, testy, zamówienie punkt po punkcie
```

**1. Zamówienie i wynik.** Zbierz z rozmowy i plików: brief, plan (wiersz etapu, "zrobione, gdy"), specyfikację (rozdziały wiążące, z zakresem linii), rejestr dozwolonych odstępstw, zasady typu "wersja 1 = wierny wzorzec, dodatki do wersji 2". Zamówienie wypowiedziane tylko w rozmowie przekaż cytatem słów użytkownika, nie własnym streszczeniem. Wynik = lista ścieżek absolutnych (pliki, foldery) i, jeśli są, polecenie uruchamiające testy. Zapisz w briefie jednym zdaniem fakt, że praca przeszła bramkę poprawności (ile rund), bez listy poprawek.

**2. Model.** Inny kontekst niż autor zawsze, inny model niż autor, gdy się da. Kolejność:
1. Model innej rodziny niż autor, dostępny w narzędziu Agent (np. Fable jako "krytyk koncepcji", `effort: "high"`), gdy autor na nim nie pracował.
2. Opus w świeżym kontekście, gdy takiego modelu nie ma albo to on budował.
3. Ten sam model co autor, ale świeży kontekst - ostateczność; zaznacz to w raporcie ("ten sam model, inny kontekst").
Model spoza Claude'a tylko na wyraźne życzenie użytkownika. `model` podajesz zawsze jawnie, bo bez niego agent dziedziczy model sesji.

**3. Agent.** Narzędzie Agent, `subagent_type: "general-purpose"` (potrzebuje Read i Bash do pomiarów i testów). Prompt = brief wypełniony według wzoru. Jedna runda; dopytanie przez SendMessage tylko wtedy, gdy raport pomija któreś z pięciu pytań albo nie ma listy "zostawić, bo". Agent pracuje w tle - w tym czasie możesz rozmawiać z użytkownikiem o czymś innym, ale nie przewiduj wyniku.

**4. Sprawdzenie.** Dla każdego znaleziska z wagą "duża" i każdego "wyciąć": czy element naprawdę istnieje w tym miejscu, czy zamówienie go nie wymaga (szukasz w plikach zamówienia), czy prostsza wersja agenta nie łamie czegoś z "zrobione, gdy". Pomiary agenta (linie, liczba pól) sprawdź `wc`, gdy na nich opiera się werdykt.

**5. Mapa decyzji.** Wzór, kolumny i przykład: `references/mapa_decyzji.md` (wczytaj przed pisaniem mapy). Pierwsza linia "**Do Ciebie: ...**", numeracja uwag agenta bez zmian, ocena Przyjmuję / Odrzucam / Twoja decyzja z jednym zdaniem dlaczego, dodatki z bramki oznaczone, rekomendowany zestaw numerów. Użytkownik odpowiada numerami. Bez okienka AskUserQuestion w tej samej turze, bo zasłania tabelę. Tu się zatrzymujesz.

**6. Po akcepcie.** Wdróż tylko wybrane numery. Potem: testy (jeśli są), a następnie zamówienie punkt po punkcie - każdy punkt "zrobione, gdy" i każdy wymóg z planu: nadal spełniony? Sprawdź też, że nic z listy "zostawić, bo" nie wypadło przy okazji. Raport: "**Do Ciebie: nic**" (albo konkretna rzecz), co wycięte, ile ubyło (linie, pliki, mechanizmy), wynik testów, tabela zamówienie -> spełnione. Zmiany w repo - commit według zasad projektu.

## Gotchas

- Fork (`subagent_type: "fork"`) dziedziczy całą rozmowę i model sesji, więc przegląd przestaje być niezależny. Zawsze general-purpose z jawnym modelem.
- Autor streszcza zamówienie pod to, co zbudował ("chodziło o solidny szkielet"). Do briefu idą pliki zamówienia i cytaty użytkownika, nie Twoja interpretacja.
- Agent prostoty zaczyna szukać błędów albo poprawiać styl. Uwagi o poprawności idą jedną linią na koniec raportu, stylistyczne odpadają - nie wchodzą do mapy jako cięcia.
- Agent rekomenduje wyciąć coś, czego zamówienie wymaga (przeoczył rozdział specyfikacji). Przed "Przyjmuję" znajdź w zamówieniu, czy element jest wymagany.
- Dodatki z bramki poprawności: kryterium "czy poprawkę dało się zrobić bez nowego mechanizmu". Gdy przegląd pokazuje przyrost z bramki, zaproponuj użytkownikowi dopisanie tego kryterium do briefu bramki w tym projekcie (propozycja, nie zmiana).
- "Wariant pośredni" jest częsty i dobry: mechanizm zostaje, wypełnienie albo zakres na zapas wypada (przykład: pole na dane wejściowe etapów zostaje, ale wypełnianie go z góry dla etapów, które dopiero powstaną, wypada).
- Po cięciach łatwo zgubić coś z zamówienia albo z listy "zostawić". Dlatego krok 6 kończy się porównaniem punkt po punkcie, nie samym "testy przechodzą".

## PLIKI REFERENCYJNE

- `references/brief_agenta.md` - wzór briefu dla agenta przeglądu (rola, pięć pytań, kryterium uzasadnienia, format raportu, pomiary według typu pracy). Wczytaj w kroku 2.
- `references/mapa_decyzji.md` - wzór mapy decyzji dla użytkownika i raportu po cięciach, z przykładem. Wczytaj w kroku 5.
