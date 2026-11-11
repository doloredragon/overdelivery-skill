# overdelivery

Skill do Claude Code, który sprawdza, czy Claude nie dał Ci więcej, niż prosiłeś. Działa na każdym efekcie pracy: planie, strategii, ofercie, briefie, prezentacji, dokumencie, skillu czy kodzie.

## Po co

Prosisz o plan pracy nad strategią, a dostajesz plan z dwunastoma etapami, trzema warsztatami i macierzą ryzyk. Prosisz o prosty skrypt, a dostajesz system z konfiguracją i pięcioma zabezpieczeniami. Claude lubi dokładać, a sam tego nie zauważy, bo ocenia swoją pracę tym samym tokiem myślenia, którym ją tworzył.

Ten skill oddaje ocenę świeżemu agentowi. Agent widzi tylko Twoją prośbę i to, co dostałeś. Wskazuje, co jest zbędne, co zrobiono za ciężko, a co tylko wygląda na zbędne i powinno zostać.

## Jak używać

Gdy Claude odda wynik, napisz na przykład:

- "czy to nie przekombinowane?"
- "czy nie dodałeś niepotrzebnych rzeczy?"
- "czy to nie za dużo jak na pierwszą wersję?"

Dostaniesz tabelę: każda uwaga, ocena (wyciąć / zostawić / Twoja decyzja) i jedno zdanie dlaczego. Odpowiadasz numerami. Nic nie znika bez Twojej zgody.

## Instalacja

```
git clone https://github.com/doloredragon/overdelivery-skill.git
cp -r overdelivery-skill/overdelivery ~/.claude/skills/
```

Skill jest po polsku.

## Autor

Piotr Rychel
