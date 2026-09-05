# llm — Plac zabaw z parametrami Gemini

Interaktywne demo **edulab** do nauki parametrów wywołań API Gemini (Google Generative
Language API). Pokręć każdą gałką, włączaj/wyłączaj pojedyncze parametry i zobacz na żywo,
jak zmienia się ładunek zapytania oraz odpowiedź modelu.

## Co to robi

- **Jeden plik, zero zależności** — `index.html` (HTML + CSS + vanilla JS). Otwórz w przeglądarce
  albo opublikuj na GitHub Pages.
- **Twój klucz, Twoja przeglądarka** — wklejasz własny klucz API Gemini; trafia wyłącznie do
  `localStorage` i jest wysyłany bezpośrednio do Google. Brak backendu, brak sekretów w repo.
- **Wszystkie parametry generowania** z opisem dydaktycznym po polsku:
  - Próbkowanie: `temperature`, `topP`, `topK`, `seed`
  - Długość/zatrzymanie: `maxOutputTokens`, `stopSequences`, `candidateCount`
  - Kary: `presencePenalty`, `frequencyPenalty`
  - Myślenie: `thinkingLevel` (modele 3.x) / `thinkingBudget` (modele 2.5) + `includeThoughts`
  - Format: `responseMimeType` + `responseSchema` (tryb JSON)
  - Bezpieczeństwo: `safetySettings` dla 4 kategorii szkód
- **Podgląd zapytania na żywo** — dokładny JSON wysyłany metodą POST aktualizuje się przy każdej
  zmianie. Do zapytania trafiają tylko *włączone* parametry — to główny mechanizm dydaktyczny.
- **Trzy najnowsze modele Flash** — lista modeli pokazuje tylko trzy najnowsze tekstowe modele
  Gemini Flash (domyślnie `gemini-3.8-flash`, `gemini-3.7-flash`, `gemini-3.5-flash`). Przycisk
  **Odśwież** pobiera `models.list` z API, wybiera trzy najnowsze modele Flash obsługujące
  `generateContent` (bez wariantów image/live/tts) i zapamiętuje je w `localStorage`.
- **Presety** — Deterministyczny, Kreatywny, Ekstrakcja JSON.
- **Strumieniowanie (SSE)** — przełącznik między `generateContent` a `streamGenerateContent`.

## Użycie

1. Otwórz `index.html` w przeglądarce (lub wejdź na opublikowaną stronę GitHub Pages).
2. Wklej klucz API z [aistudio.google.com/apikey](https://aistudio.google.com/apikey).
3. Wybierz model, wpisz prompt, pokręć parametrami i kliknij **Wyślij zapytanie**.

## Endpoint

```
POST https://generativelanguage.googleapis.com/v1beta/models/{model}:generateContent
Header: x-goog-api-key: {KLUCZ}
```

Wersja strumieniowa: `:streamGenerateContent?alt=sse`.
Lista modeli (przycisk Odśwież): `GET https://generativelanguage.googleapis.com/v1beta/models?pageSize=1000`.

## Demo agenta (agent.html)

Drugie demo w tym repo: **`agent.html`** pokazuje krok po kroku, jak pracuje agent AI
uruchomiony w aplikacji na pulpicie (np. Claude Desktop) i podłączony do narzędzi firmy.
W odróżnieniu od `index.html` (surowe parametry wywołania) tu chodzi o samą pętlę:
zadanie → plan → prośba o narzędzie → wynik → (powtórz) → decyzja → wynik dla użytkownika.

- **Pięć scenariuszy z różnych dziedzin.** Każdy kończy się czymś, czego zwykły czat
  nie zrobi: faktura z rozbieżnością, której agent nie księguje (finanse); zastępstwo
  wpisane do kalendarza po sprawdzeniu trzech źródeł (HR); zamówienie papieru złożone
  czwartego dnia, gdy cena spadła poniżej progu (zakupy); korepetycje z ułamków, w których
  agent zmienia plan po błędzie ucznia (edukacja); naprawa serwera z weryfikacją i
  powiadomieniem dyżurnego (IT).
- **Wszystko jest symulowane.** Narzędzia i ich wyniki to dane w pliku, więc demo jest
  powtarzalne i bezpieczne na scenie. Karty narzędzi oznaczone „działanie” to te, które
  w prawdziwym wdrożeniu zmieniałyby coś w świecie.
- **„Jak zrobiłby to zwykły czat”.** Każdy scenariusz ma przycisk, który pokazuje obok
  siebie odpowiedź czatu bez narzędzi (brzmi pomocnie, ale zostawia pracę Tobie) i wynik
  agenta z liczbą okrążeń i szacunkiem tokenów.
- **Łatwe śledzenie.** Pasek faz (zadanie, plan, pętla, wynik), separatory okrążeń,
  panel podłączonych narzędzi z podświetleniem aktywnego i pamięć rozmowy z licznikiem.
  Każda karta ma zwijany „Protokół” z JSON-em wymiany aplikacja ↔ model w kształcie
  `tool_use` / `tool_result`.
- **Sterowanie prezentera:** „Krok ▸” lub <kbd>spacja</kbd>/<kbd>→</kbd>, „Auto” lub
  <kbd>a</kbd> z regulowaną pauzą, <kbd>c</kbd> porównanie z czatem, <kbd>n</kbd> notatki
  prowadzącego (niewidoczne dla widowni).

## Design

UI zbudowane w systemie projektowym **edulab „Editorial Color”** (paper/ink, marigold/teal/coral,
fonty Bricolage Grotesque / Hanken Grotesk / DM Mono).
