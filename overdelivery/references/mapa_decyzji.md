# Mapa decyzji dla użytkownika i raport po cięciach

Wzór z prawdziwego przeglądu (etap szkieletu narzędzia do analizy danych), przyjęty przez właściciela bez dopytań. Mapa ma sprawić, że "wg rekomendacji" znaczy coś konkretnego: użytkownik widzi każdą uwagę agenta, Twoją ocenę i powód. Same numery bez tabeli nie wystarczą - użytkownik nie wie wtedy, co jest rekomendacją.

## Wzór mapy

```
**Do Ciebie: decyzja, które cięcia wdrażam - odpowiedz numerami z listy na dole.**

[Werdykt 2-3 zdania: ocena agenta + Twoja, pomiar (np. "1 445 linii kodu, nadwyżka ok. 300-400 w trzech miejscach"), czy wymaga przebudowy.]
Przegląd: [model agenta; jeśli ten sam co autor - "ten sam model, inny kontekst"]. Uwagi agenta: [N] - przyjmuję [a], odrzucam [b], do Twojej decyzji [c].

| # | Uwaga przeglądu | Moja ocena | Dlaczego |
|---|---|---|---|
| [nr agenta] | [element i co z nim nie tak, krótko; "(z bramki)" przy dodatkach z bramki poprawności] | Przyjmuję: wyciąć / uprościć [jak] | [jedno zdanie; co sprawdziłem w plikach] |
| ... | ... | Odrzucam | [dlaczego zostaje: wymóg zamówienia z miejscem / koszt cięcia większy niż zysk] |
| ... | ... | Twoja decyzja, rekomenduję [wariant] | [czego dotyczy wybór, co zyskasz, co ryzykujesz] |

Wygląda na nadmiar, ale zostaje: [element - powód, w jednej linii każdy; z listy agenta, po Twoim sprawdzeniu]

Rekomendacja: [numery, np. "1 (wariant pośredni), 2, 4a, 5"]. Napisz "wg rekomendacji" albo podaj własne numery.
```

Zasady wypełniania:
- **Numeracja agenta**, nie własna - użytkownik może zajrzeć do surowego raportu i znaleźć ten sam wiersz.
- **Język użytkownika:** zamiast nazw technicznych opis skutku ("program sprawdza, czy ręcznie zmieniłeś plik" zamiast "SHA-256 + `przyjmij`"); nazwę techniczną najwyżej w nawiasie.
- **"Przyjmuję" tylko po sprawdzeniu** w plikach (krok 4 SKILL.md); w "Dlaczego" zaznacz, co sprawdzone.
- **"Twoja decyzja"** wtedy, gdy wybór zależy od tego, czego nie wiesz (plany użytkownika, ważenie czasu teraz vs później) - nie jako unik.
- **Dodatki z bramki** oznacz "(z bramki)", gdy wiesz z rozmowy albo historii, że przyszły z poprawek audytora. Gdy jest ich kilka, dodaj pod tabelą jedno zdanie: propozycja dopisania do briefu bramki w tym projekcie kryterium "czy poprawkę da się zrobić bez nowego mechanizmu".
- Bez okienka AskUserQuestion w tej samej turze - zasłania tabelę.

## Przykład (skrót - wiersze z prawdziwej mapy, część pominięta)

```
**Do Ciebie: decyzja, które cięcia wdrażam - odpowiedz numerami z listy na dole.**

Umiarkowany nadmiar: rdzeń szkieletu jest proporcjonalny (1 445 linii kodu dla 74 etapów), ale narosły trzy rzeczy ponad zamówienie, a dwie rundy bramki dołożyły przypadków brzegowych. Nie wymaga przebudowy - dwa cięcia i zamrożenie zakresu.
Przegląd: Fable, świeży kontekst. Uwagi agenta: [N] - przyjmuję [a], odrzucam [b], do Twojej decyzji [c].

| # | Uwaga przeglądu | Moja ocena | Dlaczego |
|---|---|---|---|
| 1 | Dane wejściowe późniejszych etapów wypełnione z góry | Twoja decyzja, rekomenduję wariant pośredni | mechanizm musi zostać (inaczej wraca błąd z bramki), ale wypełnienie automatem to praca późniejszych etapów |
| 2 | Wykrywanie ręcznych edycji plików | Przyjmuję: wyciąć do wersji 2 | wyniki to JSON, użytkownik edytuje markdown - przypadek raczej nie wystąpi |
| 3 | Kontrole jakości przed etapem z danymi | Odrzucam cięcie, przyjmuję zasadę | specyfikacja tego wymaga; kolejne kontrole dopiero w swoim etapie |
| 4 | Limit przerwań, plik błędu diagnostyki, opcja --wymus (z bramki) | Przyjmuję: wyciąć konkretne (4a) | w bramce nie było kryterium prostoty; poprawki dało się zrobić bez nowych mechanizmów |
| 7 | Duplikat listy poleceń w skillu | Odrzucam | skill musi sam wystarczyć; koszt 10 linii |
| 8 | Próbka danych w repo | Odrzucam | wniosłaby dane osobowe do gita |

Wygląda na nadmiar, ale zostaje: zaślepki agentów - jedyny sposób na test wznawiania; zapis atomowy i stan per jednostka - wymagają tego równoległe agenty i test przerwania.

Rekomendacja: 1 (wariant pośredni), 2, 4a, ... Napisz "wg rekomendacji" albo podaj własne numery.
```

## Wzór raportu po cięciach (krok 6)

```
**Do Ciebie: nic** [albo konkretna rzecz]

Wdrożone: [numery i jednym zdaniem każde]. Ubyło: [linie / pliki / mechanizmy / kroki - przed i po].
Testy: [wynik; brak testów - jak sprawdziłem].

| Punkt zamówienia ("zrobione, gdy" / wymóg z planu) | Nadal spełniony? |
|---|---|
| [punkt] | tak / nie - [co i dlaczego] |

Lista "zostaje" nienaruszona: [tak / co się zmieniło].
```
