Criação de dados de frequência. Frequência de um campo:

```
select aircraft_code, count(*) as freq
from flights
group by 1;
```

![[Pasted image 20221202043903.png]]

**Dados para histogramas:**

"Preço médio por classe de serviço"

```
select fare_conditions, avg(amount)
from ticket_flights
group by fare_conditions;
```

![[Pasted image 20221202044621.png]]

**Distribuição de tickets por voos:**

A quantidade de X tickets foram vendidas para Y voos.
- primeiro conta a quantidade de tickets por voo
- depois usa o número de tickets como categoria e conta a quantidade de voos.

```
select tickets, count(*) as flights
from(
	select flight_id, count(ticket_no) as tickets
	from ticket_flights
	group by 1
) tickets_number_distribution_by_flights
group by 1;
```

![[Pasted image 20221202053554.png]]

# Discretização (Binning)

Utilizado para se trabalhar com valores contínuos. Os intervalos de valores são agrupados  (bin ou bucket).

Bins de tamanhos arbitrários:
Faixas de preço.

```
select
    case
        when amount <= 5000 then 'up to 5000'
        when amount <= 20000 then '5000 - 20000'
        when amount <= 50000 then '20000 - 50000'
        else '50K+'
    end as price_bin
    , count(ticket_no) tickets
from ticket_flights
group by 1
order by 2;
```

![[Pasted image 20221202055805.png]]

Bins de tamanho fixo, utilizando arredondamento, para a milhar mais próxima:

```
select
    round(amount, -3) as price_bin
    , count(ticket_no) as tickets
from ticket_flights
group by 1
order by 2;
```

![[Pasted image 20221202092637.png]]

Bins de tamanha fixo, utilizando log(), para números maior que zero:
```
select
    log(amount) as price_bin
    , count(ticket_no) as tickets
from ticket_flights
group by 1
```


Bins usando funções de janela ntile, fazendo binning pelo percentil:

```
select
	ntile
	,min(amount) as lower_bound
	,max(amount) as upper_bound
	,count(flight_id) as flights_no
from (
		select
			ntile(10) over (order by amount)
			, flight_id
			, fare_conditions
			, amount 
		from ticket_flights
) as a
group by 1 order by 1;
```
![[Pasted image 20221207042522.png]]

Distribuição de valores ntile(10), particionado por fare_conditions, número de voos que ofereceram as condições e o menor e maior valor do percentil:

```
select 
	ntile
	, fare_conditions
	,min(amount) as lower_bound
	,max(amount) as upper_bound
	,count(distinct flight_id) as flights_no
from (
	select 
		ntile(10) over (partition by fare_conditions order by amount)
		, flight_id
		, fare_conditions
		, amount from ticket_flights
) as a
group by
	ntile
	, fare_conditions
order by
	fare_conditions
	, ntile;
```

![[Pasted image 20221207043722.png]]

### Detectando duplicidade


