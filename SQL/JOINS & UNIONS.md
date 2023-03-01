[Powrót](https://github.com/piotrekDo/notatki/blob/main/SQL/SQL%20Podsumowanie.md)

Joiny pozwalają wybrać dane z kilku tabel i złączyć je. W SQL istnieją dwa rodzaje *join*. **INNER** oraz
**OUTER**. Słowo `INNER` jest opcjonalne, jest to domyślny rodzaj *join*. Join zapisujemy po klauzuli `FROM` w połączeniu ze słowem `ON` gdzie definiujemy warunek złączenia. 
```
SELECT *
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id;
```

_Column ambiguous_ - powszechny problem, jeżeli zaznaczamy kolumnę występującą w kilku tabelach:
```
SELECT order_id, customer_id, first_name
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id;
```
Wówczas należy zaznaczyć z której tabeli pochodzi kolumna, nawet jeżeli są one identyczne jako klucz główny / obcy. 
```
SELECT order_id, o.customer_id, first_name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;
```
**_UWAGA!_** stosując aliasy, musimy się do nich odnosić w klauzuli `ON`.


<h2 style="color:green">JOINY MIĘDZY BAZAMI DANYCH</h2>
W ramach naszego systemu bazodanowego możemy mieć zdefiniowanych wiele baz danych. Przełączamy się między nimi z pomocą słowa `USE`. 
```
USE sql_store
SELECT * 
FROM order_items oi
JOIN sql_inventory.products p
	ON oi.product_id = p.product_id;
```
Chcąc połączyć dane z innej bazy wystarczy dodać prefix z nazwą tej bazy `JOIN sql_inventory.products p`


<h2 style="color:green">SELF JOIN</h2>
W ramach bazy `sql_hr.employees` mamy zdefiniowaną poniższą tabelę  
![self_join_table](https://github.com/piotrekDo/notatki/blob/main/SQL/img/self_join_table.jpg)

kolumna `reports_to` wskazuje na klucz główny samej siebie, wskazując jednocześnie na przełożonego dla każdego pracownika.  Możemy wykorzystać *self join* aby uzyskać dane managera dla każdego pracownika. 
```
SELECT e.employee_id, e.first_name, e.last_name, e.job_title, m.last_name AS manager
FROM employees e
JOIN employees m
	ON e.reports_to = m.employee_id;
```
Wykonujemy join łącząc pole `reports_to` z `employee_id`
W rezultacie nie uyskamy wpisu dla Yovonnda, ponieważ w jej przypadku pole `reports_to` jest `NULL` i w tej sytuacji należy zapisać `LEFT JOIN`


<h2 style="color:green">JOIN NA WIELU TABELACH</h2>
Istnieje możliwość zapisu kolejnych Joinów, w celu złączenia większej ilości tabel
```
SELECT o.order_id, o.order_date, c.first_name, c.last_name, os.name AS status
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN order_statuses os ON o.status = os.order_status_id;
```


<h2 style="color:green">ZŁACZENIA Z KILKU KOLUMN</h2>
Czasami nie mamy możliwości złączenia tablel po kluczu obcym. Złączenia wymagają unikalnych wartośći.
`sql_store.order_items`
![compound order items](https://github.com/piotrekDo/notatki/blob/main/SQL/img/compound_order_items.jpg)
W tabeli `order_items` mamy wiele wpisów z `order_id` oraz `product_id`. Możemy stworzyć warunek z kombinacji tych 2 pól. W tabeli `order_items` mamy **klucz kompozytowy** stworzony z 2 kolumn właśnie. 
Dokonujemy złączenia z wykorzystaniem słówka `AND`
``
SELECT * 
FROM order_items oi
JOIN order_item_notes oin
	ON oi.order_id = oin.order_id
    AND oi.product_id = oin.product_id;
```


# OUTER JOINS
Inner join zwraca wyniki tylko dla rekordów mających parę w warunku złączeniowym. Przykładowo jeżeli zapiszemy zapytanie.:
```
SELECT c.customer_id, c.last_name, o.order_id
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;
```
W wyniku otrzymamy jedynie rekordy klientów, którzy złożyli zamównienie. W przypadku  jeżeli chcemy zobaczyć wszystkicch klientów musimy wykonać `LEFT JOIN`.
`LEFT JOIN orders o ON c.customer_id = o.customer_id;`
Dzięki temu zobaczymy listę **wszystkich kleintów** z wartością `NULL` dla `order_id`  w przypadku gdy nie ma zamównień dla danego klienta. `LEFT` dotyczy tabeli pochodzącej z klauzuli `FROM`, natomiast `RIGHT` dotyczy tabeli pochodzącej z `JOIN`. Zarówno `LEFT` oraz `RIGHT` są `OUTER JOIN` przy czym słowo `OUTER` jest opcjonalne. 

<h2 style="color:green">OUTER JOIN NA WIELU TABELACH</h2>
W przykładzie powyżej możemy dołączyć tabelę `shippers` aby uzyskać informacjie o wysyłce
```
SELECT c.customer_id, c.last_name, o.order_id, sh.name AS shipper
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
LEFT JOIN shippers sh ON o.shipper_id = sh.shipper_id;
```
W tej sytuacji inner join zawęziłby wyniki. 


<h2 style="color:green">CROSS JOIN</h2>
Cross join pozwala wyświetlić wszytkie wyniki z jednej tablei dla każdego z drugiej. Aby to osiągnąć **nie stosujemy** warunku złączenia. 
```
SELECT c.first_name AS customer, p.name AS product
FROM custoemrs c
CROSS JOIN products p
```
Zapis `CROSS JOIN products` nie jest konieczny, ale nadaje więcej  jasności na to co dostaniemy w wyniku zapytania.

<h2 style="color:lightgreen">KLAUZULA USING</h2>
Pozwala uprościć warunek złączenia, jeżeli nazwy kolumn są takie same dla obydu tabel. 
`JOIN orders o ON c.customer_id = o.customer_id;` możemy zastąpić
`JOIN orders o USING (customer_id)`
Może być również stosowana w przypadku kluczy kompozytowych. 


# UNIONS
Pozwalają łączyć **wiersze** zamiast kolumn. Polega na zapisaniu dwóch lub więcej selektów, złaczonych słowem `UNION`. **Liczba kolumn zwracanych w każdym `SELECT` musi być jednakowa!**.
```
SELECT order_id, order_date, 'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT order_id, order_date, 'Archive' AS status
FROM orders
WHERE order_date < '2019-01-01';
```

co pozwli osiągnąc wynik:  
![union](https://github.com/piotrekDo/notatki/blob/main/SQL/img/union.jpg)

**WAŻNE**- nazwa kolumn jest określana przez pierwszy SELECT. 
