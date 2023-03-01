[Powrót do strony głównej](https://github.com/piotrekDo/notatki/blob/main/SQL/SQL%20Podsumowanie.md)


<h2 style="color:green">USE</h2>
Polecenie `USE nazwa_tabeli`  zaznacza tabelę jako aktualnie używaną.

<h2 style="color:green">SELECT</h2>
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

<h3 style="color:lightgreen">WHERE</h3>
Klauzula pozwalająca na filtrowanie danych PRZED ich grupowaniem. Zapisywana po klauzuli `FROM`. 
Może zostać połączone z operatorami `AND`, `OR`, `NOT`.
```
WHERE birth_date > 1990-01-01 OR points > 1000
```
Operator `OR` przepuszcza dane spełniające jeden z warunków. `AND` wymaga spełnienia tych warunków. `NOT` jest negacją

**Kolejność zapiswania oparatorów `OR` oraz `AND` jest bardzo ważna!**
Podobnie jak działnia artmetyczne definiują pierwszeństwo dla mnożenia przed dodawaniem, w przypadku operatorów `AND` ma pierwszeństwo.
```
WHERE birth_date > 1990-01-01 OR points > 100 AND state = 'VA'
```

W powyższym przykładnie najpierw sprawdzany jest warunek `points > 100 AND state = 'VA'`. **ZAMIAST** ``birth_date > 1990-01-01 OR points > 100``
Kolejność można nadpisać stosując nawiasy okrągłe.

<h2 style="color:lightgreen">OPERATOR IN</h2>
Pozwala określić grupę warunków, w taki sposób jak zapis `OR` kilkakrotnie.
```
WHERE state = 'VA' OR state = 'GA' OR state = 'FL'
```
Możemy zastąpić:
```
WHERE state IN ('VA', 'GA', 'FL')
```
Kolejność nie ma tutaj znaczenia. Można stosować z operatorem `NOT` dającym odwrotny wynik. 

<h2 style="color:lightgreen">OPERATOR BETWEEN</h2>
Pozwala uprościć zapis w połąceniu z operatorem `AND`. Dane zawarte w klauzuli **wliczają** się w zakres.
```
SELECT *
FROM customers
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01';
```


<h2 style="color:lightgreen">OPERATOR LIKE</h2>
Uproszczony regex pozwalający definiować odpowiadający ciąg znaków. [Więcej na W3Schools](https://www.w3schools.com/sql/sql_like.asp)
Warunki zapisujemy w ' '
```
WHERE last_name LIKE '%y'
```
Zwróci nazwiska kończące się na 'y'.

<h2 style="color:lightgreen">OPERATOR REGEXP</h2>
Odpowiednik wyrażeń regularnych, dający szersze możliwości niż `LIKE`.

<h2 style="color:lightgreen">OPERATOR IS NULL</h2>
Pozwala wyszukać rekordy z pustymi wartościami
```
SELECT *
FROM customers
WHERE phone IS NULL
```
Zwróci wszystkich klientów nie mających przypisanego numeru telefonu. Można łączyć z `NOT` aby uzyskać wszystkich którzy mają przypisany numer. 

<h2 style="color:lightgreen">KLAUZULA ORDER BY</h2>
Pozwala na sortowanie wyników. Domyślnie wyniki sortowane są wg. kluczów głównych oraz rosnąco, co można zmienić słowem `DESC`. Sortowanie można łączyć, pozwalając stworzyć dalszą procedurę dla rekordów z takim samym wynikiem.
```
SELECT *
FROM customers
ORDER BY state DESC, last_name;
```
W przykładzie powyżej rekordy będą sortowane wg stanu **malejąco**, jeżeli stan jest taki sam, dalej będą sortowane wg nazwiska **rosnąco**.  Można sortować wg [aliasów](##ALIASY). Zamiast nazw kolumn można też użyć liczb całkowitych od 1 odpowiadających pierwszej, drugiej kolumnie itd.

<h2 style="color:lightgreen">KLAUZULA LIMIT</h2>
Pozwala ograniczyć ilość zwracanych rekordów, zapisywana **na końcu zapytania!**
```
SELECT *
FROM customers
LIMIT 3
```
Zwróci pierwsze trzy wyniki. Można stosować do paginacji z wykorzystaniem `OFFSET`. 
```
SELECT *
FROM customers
LIMIT 6, 3;
```
Zapis powyżej oznacza, że zostanie pominiętych pierwszych 6 rekordów i pobrane 3 kolejne. **Offset zapisujemy z początku**.


<h2 style="color:green">ALIASY</h2>
Aliasy pozwalają nadawać nazwy kolumną, dopisujemy je po słowie `AS`
```
SELECT last_name, 
	points * + 100 AS points 
```
Chcąc użyć znaku spacji, alias trzeba zapisać w ' ' lub " ".
