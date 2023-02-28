[Powrót do strony głównej](obsidian://open?vault=lol&file=SQL%2FSQL%20Podsumowanie)

## USE
Polecenie `USE nazwa_tabeli`  zaznacza tabelę jako aktualnie używaną.

## SELECT
Podstawowe zapytanie służące pobieraniu dancyh z bazy.
```
SELECT *
FROM customers
WHERE custoemr_id = 1
ORDER BY first_name;
```
Kolejność jest istotna. W selectach można wykonywać działania artmetyczne
```
SELECT last_name, points * + 100
FROM costumers;
```

#### DISTINCT
Słowo kluczowe `DISTINCT` pozwala pobrać jedynie unikatowe wpisy bez duplikatów.  Zapisywane jest bezpośrednio przed nazwą kolumny
```
SELECT DISTINCT state
FROM customers;
```

#### WHERE
Klauzula pozwalająca na filtrowanie danych PRZED ich grupowaniem. Zapisywana po klauzuli `FROM`. 


### ALIASY
Aliasy pozwalają nadawać nazwy kolumną, dopisujemy je po słowie `AS`
```
SELECT last_name, 
	points * + 100 AS points 
```
Chcąc użyć znaku spacji, alias trzeba zapisać w ' ' lub " ".