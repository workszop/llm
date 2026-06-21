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

## Design

UI zbudowane w systemie projektowym **edulab „Editorial Color”** (paper/ink, marigold/teal/coral,
fonty Bricolage Grotesque / Hanken Grotesk / DM Mono).
