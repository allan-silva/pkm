Gerando distribuição simples na amostra através da função MOD.

Retorna todas as linhas que os últimos 3 digitos são 0006.

```
select 
	flight_id,
		flight_no,
		arrival_airport,
		departure_airport
from flights
where
	mod(flight_id, 10000) = 6
LIMIT 10;
```

![[Pasted image 20221201094544.png]]

Tags:
#sql #banco-de-dados 
