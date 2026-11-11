# overdelivery - skill do Claude Code: czy nie zbudowano za dużo

Skill do [Claude Code](https://claude.com/claude-code), który po skończonej pracy zleca świeżemu agentowi jedno pytanie: **czy zbudowano więcej, niż zamówiono, i czy gdzieś nie jest niepotrzebnie skomplikowane?** Wynik to tabela decyzji: co wyciąć, co uprościć, co zostawić - i dlaczego. Niczego nie tnie, dopóki nie wybierzesz numerów.

## Po co powstał

Claude przy budowie ma skłonność do dokładania: funkcja ponad zamówienie, zakres rosnący po kawałku, mechanizm na przypadek, który raczej nie wystąpi, rozwiązanie cięższe niż problem (po angielsku: gold plating, scope creep, over-engineering).

Jeszcze więcej dokłada przegląd błędów. Audytor pyta "co może pójść źle", więc każda jego uwaga to kolejne zabezpieczenie, a nikt nie pyta "czy to w ogóle potrzebne". W projekcie, przy którym powstał ten skill, dwie rundy takiego przeglądu dały ok. 25-30% kodu w obsłudze przypadków brzegowych.

Autor sam tego nie wyłapie, bo ocenia swoją pracę własnym tokiem myślenia. Dlatego przegląd robi świeży agent, który widzi tylko zamówienie i wynik.

## Co robi

1. **Ustala punkt odniesienia** - co dokładnie zamówiono (brief, plan, specyfikacja, warunek "zrobione, gdy", słowa użytkownika z rozmowy) i gdzie leży wynik. Bez zamówienia nie rusza: pyta, gdzie ono jest.
2. **Zleca przegląd świeżemu agentowi** - bez historii budowy i bez tłumaczeń autora, najlepiej na innym modelu niż ten, który budował. Agent mierzy (linie, pola, mechanizmy) i odpowiada na pięć pytań: co jest ponad zamówienie, co zrobiono za ciężko, gdzie wynik odjechał od zamówienia, czy rozmiar jest proporcjonalny, i - obowiązkowo - **co wygląda na nadmiar, ale musi zostać**.
3. **Sprawdza wynik agenta** - każdą rekomendację "wyciąć" weryfikuje w plikach, zanim ją poleci (agent bywa za ostry i przeocza, że zamówienie czegoś wymaga).
4. **Daje mapę decyzji** - tabela: uwaga agenta, ocena (przyjmuję / odrzucam / Twoja decyzja), jedno zdanie dlaczego, rekomendowany zestaw numerów. Odpowiadasz numerami albo "wg rekomendacji".
5. **Wdraża tylko wybrane cięcia** - potem testy i sprawdzenie punkt po punkcie, czy zamówienie jest nadal spełnione i czy nic z listy "zostaje" nie wypadło przy okazji.

Kluczowa zasada: **nadmiar sam w sobie nie jest błędem.** "Więcej teraz" jest w porządku, gdy ma konkretne uzasadnienie: upraszcza coś później, przygotowuje zaplanowany dalszy etap, zastępuje kilka rzeczy jedną albo chroni przed błędem, który naprawdę wystąpił. "Może się przydać" uzasadnieniem nie jest.

## Do czego nie służy

- Do szukania błędów - od tego jest przegląd kodu (`/code-review`) albo weryfikator faktów.
- Do upraszczania kodu bez pytania o zamówienie - od tego jest `/simplify`.
- Do poprawiania stylu tekstu.

## Instalacja

Skopiuj folder `overdelivery` do folderu skilli Claude Code:

```
git clone https://github.com/doloredragon/overdelivery-skill.git
cp -r overdelivery-skill/overdelivery ~/.claude/skills/
```

Skill działa w każdym projekcie. Jeśli chcesz go tylko w jednym, skopiuj folder do `.claude/skills/` w tym projekcie.

Wymaga Claude Code z narzędziem Agent (podagenci). Skill jest po polsku.

## Jak używać

Po skończonej większej pracy (narzędzie, skill, wieloetapowy dokument, plan) napisz do Claude'a na przykład:

- "sprawdź, czy nie doszło do overdelivery"
- "czy to nie jest przekombinowane?"
- "czy nie dodałeś niepotrzebnych rzeczy?"
- "czy to nie za dużo jak na pierwszą wersję?"

Claude może też sam zaproponować przegląd jedną linijką po większej budowie - rusza dopiero po Twoim "tak".

Przegląd działa najlepiej, gdy zamówienie jest zapisane (plik z briefem, planem albo specyfikacją). Jeśli padło tylko w rozmowie, skill przekaże agentowi Twoje słowa dosłownie.

## Co jest w środku

- `overdelivery/SKILL.md` - zasady i przebieg krok po kroku.
- `overdelivery/references/brief_agenta.md` - wzór polecenia dla agenta przeglądu (rola, pięć pytań, kryterium uzasadnienia, format raportu, co mierzyć przy kodzie, skillu, dokumencie i planie).
- `overdelivery/references/mapa_decyzji.md` - wzór tabeli decyzji i raportu po cięciach, z przykładem z prawdziwego przeglądu.

W briefie agenta jest miejsce na zasady właściciela pracy (np. "zrobione lepsze niż perfekcyjne", "wersja 1 = to, co zamówione, dodatki do wersji 2"). Dopasuj je do siebie.

## Autor

Piotr Rychel
