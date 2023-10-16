# 🍕 Pizza Sales - Análisis
![logo](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/8b1db89a-a7fd-4489-a936-43b5e6d2dbb6)

## Tabla de Contenido
- [Introducción](#introducción)
- [Herramientas utilizadas](#herramientas-utilizadas)
- [Diagrama entidad relación](#diagrama-entidad-relación)
- [Preguntas y respuestas](#preguntas-y-respuestas)
- [Conclusiones](#conclusiones)
- [Visualización](#visualización)

## Introducción
El restaurante Plato's Pizza necesita de un consultor BI que analice y genere un reporte sobre los datos que recopilaron en todo un año, con el fin de encontrar oportunidades para generar más ventas y trabajar de manera más eficiente.

En el siguiente link se puede acceder a la base de datos: [aquí](https://www.kaggle.com/datasets/shilongzhuang/pizza-sales?datasetId=2566526&sortBy=voteCount)

## Herramientas utilizadas
- SQL Server
- Power BI
- Canva

## Diagrama entidad relación
![Sin título](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/568ef5f7-529d-42e5-93f3-0caf1cf5f768)

## Preguntas y respuestas
**1) ¿Qué meses, días y horarios tienden a ser las más ocupados?**

- Meses con más cantidad de pedidos:
````sql
select top 3
		datename(month,order_date) as Mes,
		count(distinct order_id) as Cant_Pedidos
from [dbo].[pizza_sales$]
group by datename(month,order_date)
order by Cant_Pedidos desc;
````
#### Respuesta:
| Mes     | Cant_Pedidos |
| ------- | ------------ |
| July    | 1.935        |
| May     | 1.853        |
| January | 1.845        |

- Día de la semana con más pedidos:
````sql
select top 3
	datename(weekday, convert(date, order_date, 105)) as 'Dia de la semana',
	count (distinct order_id) as Cant_Pedidos	
from [dbo].[pizza_sales$]
group by datename(weekday, convert(date, order_date, 105))
order by Cant_Pedidos desc;
````
#### Respuesta:
| Día de la semana | Cant_Pedidos |
| ---------------- | ------------ |
| Friday           | 3.538        |
| Thursday         | 3.239        |
| Saturday         | 3.158        |

- Horas con más pedidos:
````sql
select top 3
	dateadd(hour, datediff(hour, 0, order_time), 0) as Hora,
	count(distinct order_id) as Cant_Pedidos	
from [dbo].[pizza_sales$]
group by dateadd(hour, datediff(hour, 0, order_time), 0)
order by Cant_Pedidos desc;
````
#### Respuesta:
| Hora   | Cant_Pedidos |
| ------ | ------------ |
| 12:00  | 2.520        |
| 13:00  | 2.455        |
| 18:00  | 2.399        |

***

**2) ¿Cuántas pizzas se cocinan en los períodos pico?**

````sql
select top 3
	dateadd(hour, datediff(hour, 0, order_time), 0) as Hora,
	sum(quantity) as Cant_Pizzas
from [dbo].[pizza_sales$]
group by dateadd(hour, datediff(hour, 0, order_time), 0)
order by Cant_Pizzas desc;
````
#### Respuesta:
| Hora   | Cant_Pizzas  |
| ------ | ------------ |
| 12:00  | 6.776        |
| 13:00  | 6.413        |
| 18:00  | 5.417        |

De esos resultados me surgió la pregunta: **¿Cuántas pizzas se cocinan en promedio en el horario de las 12:00?**

````sql
select 
	round(avg(Cant_Pizzas),1) as Promedio_Pizzas
from (
	select 
		cast(order_date as date) as Dia,
		dateadd(hour, datediff(hour, 0, order_time), 0) as Hora,
		sum(quantity) as Cant_Pizzas
	from [dbo].[pizza_sales$]
	where datepart(hour, order_time) = 12
	group by cast(order_date as date), dateadd(hour, datediff(hour, 0, order_time), 0)
) as t1;
````
#### Respuesta:
| Promedio_Pizzas  |
| ---------------- |
| 19               |

_Lo mismo podría averiguar en el horario de las 13:00 y 18:00 simplemente reemplazando esos valores en el condicional "where"._

***

**3) ¿Cuál es la mejor y la peor venta de pizza?**

- En función de pizza
````sql
select top 1 
	pizza_name, 
	round(sum(total_price),1) as Mejor_Venta_Total	
from [dbo].[pizza_sales$]
group by pizza_name
order by Mejor_Venta_Total desc;
````
#### Respuesta:
| pizza_name              | Mejor_Venta_Total  |
| ----------------------- | ------------------ |
| The Thai Chicken Pizza  | 43.434,3           |

````sql
select top 1 
	pizza_name,
	round(sum(total_price),1) as Peor_Venta_Total	
from [dbo].[pizza_sales$]
group by pizza_name
order by Peor_Venta_Total asc;
````
#### Respuesta:
| pizza_name             | Peor_Venta_Total   |
| ---------------------- | ------------------ |
| The Brie Carrie Pizza  | 11.588,5           |

- En función de tamaño de pizza
````sql
select top 1 
	pizza_size,
	round(sum(total_price),1) as Mejor_Venta_Total		
from [dbo].[pizza_sales$]
group by pizza_size
order by Mejor_Venta_Total desc;
````
#### Respuesta:
| pizza_size  | Mejor_Venta_Total  |
| ----------- | ------------------ |
| L           | 375.318,7          |

````sql
select top 1 
	pizza_size,
	round(sum(total_price),1) as Peor_Venta_Total
from [dbo].[pizza_sales$]
group by pizza_size
order by Peor_Venta_Total asc;
````
#### Respuesta:
| pizza_size  | Peor_Venta_Total  |
| ----------- | ----------------- |
| XXL         | 1.006,6           |

- En función del tipo de pizza
````sql
select top 1 
	pizza_category,
	round(sum(total_price), 1) as Mejor_Venta_Total	 
from [dbo].[pizza_sales$]
group by pizza_category
order by Mejor_Venta_Total desc;
````
#### Respuesta:
| pizza_category | Mejor_Venta_Total  |
| -------------- | ------------------ |
| Classic        | 220.053,1          |

````sql
select top 1 
	pizza_category,
	round(sum(total_price), 1) as Peor_Venta_Total
from [dbo].[pizza_sales$]
group by pizza_category
order by Peor_Venta_Total asc;
````
#### Respuesta:
| pizza_category | Peor_Venta_Total  |
| -------------- | ----------------- |
| Veggie         | 193.690,5         |

***

**4) ¿Cuál es el valor promedio de una pizza?**

````sql
select	
	round(avg(total_price),2) as Promedio_valor_pizza
from [dbo].[pizza_sales$];
````
#### Respuesta:
| Promedio_valor_pizza  |
| --------------------- |
| 16,82                 |

***

**5) ¿Cuáles combinaciones (tamaño y pizza) se piden más?**

````sql
select top 3 
	pizza_size, 
	pizza_name,
	count(*) as Cant_Combinaciones
from [dbo].[pizza_sales$]
group by pizza_size, pizza_name
order by Cant_Combinaciones desc;
````
#### Respuesta:
| pizza_size | pizza_name             | Cant_Combinaciones |
| ---------- | ---------------------- |--------------------|
| S          | The Big Meat Pizza     | 1.811              |
| L          | The Thai Chicken Pizza | 1.365              |
| L          | The Five Cheese Pizza  | 1.359              |

## Conclusiones
- El mes con más pedidos es **Julio**.
- El día de la semana que más se pide es el **Viernes**.
- El horario con más pedidos es al mediodía **12:00**.
- En total se realizaron unas **6.776** pizzas en el horario pico de las 12:00, es decir, en promedio unas **19** pizzas por día.
- Las pizzas que más ganancias dejan son **The Thai Chicken Pizza**, el tamaño que más ganancia deja es el **L** y la categoría que más ganancia deja es la **Classic**.
- Una pizza cuesta, en promedio, **16,82** dólares.
- La combinación preferida de los clientes es tamaño **S** de la **The Big Meat Pizza**.

## Visualización
![Dashboard 1](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/89fcf8be-757d-4394-96d2-d1be30ad9daa)
***
![Dashboard 2](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/5c6a0fad-420b-42b9-a2ab-42120525e9c0)

Nota: _Dashboard con filtro puesto es el mes de Julio_

