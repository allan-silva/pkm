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

