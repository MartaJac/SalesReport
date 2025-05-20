# 📊 Dokumentacja raportu Power BI – Analiza sprzedaży (2023–2025)

## 1. 📝 Opis raportu

Raport przedstawia kompleksową analizę danych sprzedażowych z lat 2023–2025, obejmując zarówno kanały online, jak i offline. Umożliwia ocenę efektywności sprzedaży w podziale na sklepy, regiony, produkty, kategorie oraz kampanie promocyjne. Dzięki interaktywnej strukturze raportu oraz zastosowanym wskaźnikom analitycznym (YoY, marża, zysk, udziały, zwroty) użytkownik może łatwo identyfikować obszary wzrostu i ryzyka oraz podejmować decyzje biznesowe oparte na danych.

---

## 2. 🧱 Struktura modelu danych

Model semantyczny raportu opiera się na relacyjnej strukturze, obejmującej następujące tabele:

### Fakty:

* `Sprzedaz` – sprzedaż offline
* `SprzedazOnline` – sprzedaż online

### Wymiary:

* `Kalendarz` – tabela dat
* `Produkt` – informacje o produktach
* `Podkategoria` – podkategorie produktów
* `Kategoria` – kategorie produktów
* `Sklep` – dane sklepów stacjonarnych
* `Klient` – dane klientów
* `Promocja` – szczegóły kampanii promocyjnych
* `Lokalizacja` – dane geograficzne (miasto, kraj, kontynent)
* `TypSprzedazy` – kanały sprzedaży
* `Kierownik` – dane menedżerów sklepów

---

## 3. 🔗 Relacje między tabelami

Model zawiera **15 relacji** typu *Many-to-One*, wszystkie z **kierunkiem filtrowania jednokierunkowym (Single direction)**:

| From Table              | From Column      | To Table     | To Column        | Typ relacji |
| ----------------------- | ---------------- | ------------ | ---------------- | ----------- |
| Sprzedaz                | Data sprzedaży   | Kalendarz    | Data             | Many-to-One |
| SprzedazOnline          | Data zamówienia  | Kalendarz    | Data             | Many-to-One |
| SprzedazOnline          | Data wysyłki     | Kalendarz    | Data             | Many-to-One |
| Sprzedaz/SprzedazOnline | Promocja ID      | Promocja     | Promocja ID      | Many-to-One |
| Sprzedaz/SprzedazOnline | Sklep ID         | Sklep        | Sklep ID         | Many-to-One |
| SprzedazOnline          | Klient ID        | Klient       | Klient ID        | Many-to-One |
| Sklep                   | Kierownik ID     | Kierownik    | Kierownik ID     | Many-to-One |
| Sprzedaz/SprzedazOnline | Produkt ID       | Produkt      | Produkt ID       | Many-to-One |
| Produkt                 | Kategoria ID     | Kategoria    | Kategoria ID     | Many-to-One |
| Podkategoria            | Kategoria ID     | Kategoria    | Kategoria ID     | Many-to-One |
| Sklep                   | Lokalizacja ID   | Lokalizacja  | Lokalizacja ID   | Many-to-One |
| Klient                  | Lokalizacja ID   | Lokalizacja  | Lokalizacja ID   | Many-to-One |
| Sprzedaz                | Typ sprzedaży ID | TypSprzedazy | Typ sprzedaży ID | Many-to-One |
| SprzedazOnline          | TypSprzedazy ID  | TypSprzedazy | Typ sprzedaży ID | Many-to-One |

Model obsługuje również **Row-Level Security (RLS)** na poziomie kontynentu (`Lokalizacja[Kontynent]`).

---

## 4. 📊 Miary w modelu

W modelu zaimplementowano **ponad 80 miar DAX**, zorganizowanych logicznie w folderach tematycznych. Poniżej przedstawiono najważniejsze z nich wraz z opisami:

### 📊 Miary główne:

* `Sprzedaż` – łączna wartość sprzedaży (offline + online)
* `Zysk` – sprzedaż pomniejszona o koszty i zwroty
* `Koszt` – całkowite koszty transakcyjne
* `Marża` – zysk jako procent sprzedaży
* `Zwrot` – wartość zwrotów offline i online
* `Sprzedaż ilościowa` – suma sprzedanych jednostek

### 📈 Porównania czasowe:

* `Sprzedaż LY`, `Zysk LY`, `Koszt LY` – wartości z roku poprzedniego
* `Sprzedaż YoY`, `Zysk YoY`, `Koszt YoY` – zmiana rok do roku (%)
* `Sprzedaż MoM`, `Sprzedaż LM`, `Sprzedaż NM` – analiza miesiąc do miesiąca
* `Sprzedaż YTD`, `RTM` – sprzedaż narastająco i rolling 12 miesięcy

### 📊 Analizy szczegółowe:

* `Udział sprzedaży`, `Udział kosztów`, `Udział sprzedaż vs koszt` – udział w rynku wg marek
* `Sprzedaż wg kontynentu`, `Udział w totalu` – analiza geograficzna
* `Sprzedaż AGD/Audio`, `Największa/Najmniejsza sprzedaż` – analizy produktowe

### 🌟 KPI promocyjne:

* `Sprzedaż z promocją`, `Sprzedaż bez promocji`
* `Udział sprzedaży z promocją`
* `Średni rabat`

### 📌 Miary wizualne i pomocnicze:

* `txt Sprzedaż YoY`, `Kolor tekstu (Zysk)` – dynamiczne teksty z ikonami trendu ▲▼
* `Labels bar chart` – etykieta do wykresu z ikoną 🔴🟢
* `Sprzedaż sklepów w tym samym mieście` – porównanie lokalne

> 🔍 **Liczba miar:** 85
> ⚙️ Miary obsługują dynamiczne kolory, warunkowe ikony, porównania rok do roku, miary z aktywacją relacji oraz analizy toggle (parametry pól).

---

## 📌 Dodatkowe informacje

* Raport zawiera **drill-through** do szczegółów produktu, regionu i sklepu
* Wykorzystano **bookmarki, toggle, warunkowe tytuły, ikony trendu, dynamiczne kolory i tooltipy, Field Parameters**
* Dane walutowe są prezentowane w **USD**
* Dane filtrują się według: lat, miesięcy, kanałów sprzedaży, regionów, promocji, kategorii i produktów

---

## 🔢 Przykłady zaawansowanych miar DAX

### 🔹 1. Sprzedaż YoY (Year-over-Year)

```DAX
Sprzedaż YoY =
DIVIDE(
    [Sprzedaż] - [Sprzedaz LY],
    [Sprzedaz LY]
)
```

Oblicza procentową zmianę sprzedaży względem poprzedniego roku.

---

### 🔹 2. Sprzedaż sklepów w tym samym mieście

```DAX
Sprzedaż sklepów w tym samym mieście =
VAR _Miasto = SELECTEDVALUE(Sklep[Lokalizacja ID])
RETURN
CALCULATE(
    [Sprzedaż],
    FILTER(
        ALL(Sklep),
        Sklep[Lokalizacja ID] = _Miasto
    )
)
```

Pozwala porównać sprzedaż wybranego sklepu z innymi sklepami w tym samym mieście, z pominięciem aktywnego filtra sklepu.

---

### 🔹 3. Udział sprzedaż vs koszt

```DAX
Udział sprzedaż vs koszt =
([Udział sprzedaży] - [Udział kosztów]) * 100
```

Gdzie:

```DAX
Udział sprzedaży =
DIVIDE(
    [Sprzedaż],
    CALCULATE([Sprzedaż], ALL(Produkt[Marka]))
)

Udział kosztów =
DIVIDE(
    [Koszt],
    CALCULATE([Koszt], ALL(Produkt[Marka]))
)
```
