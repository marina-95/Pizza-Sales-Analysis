# 🍕 Pizza Sales - Análisis
![Red Illustration Pizza Shop Logo](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/b7c7e9a4-e2f5-4224-b694-a449b2105e27)

# Tabla de Contenido


# Introducción
El restaurante Plato's Pizza necesita de un consultor BI que analice y genere un reporte sobre los datos que recopilaron en todo un año, con el fin de encontrar oportunidades para generar más ventas y trabajar de manera más eficiente.

En el siguiente link se puede acceder a la base de datos: [aquí](https://www.kaggle.com/datasets/shilongzhuang/pizza-sales?datasetId=2566526&sortBy=voteCount)

# Herramientas utilizadas
- SQL Server
- Power BI

# Diagrama entidad - relación
![Sin título](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/568ef5f7-529d-42e5-93f3-0caf1cf5f768)

# Preguntas & Respuestas
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
| July    | 1935         |
| May     | 1853         |
| January | 1845         |

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
| Friday           | 3538         |
| Thursday         | 3239         |
| Saturday         | 3158         |

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
| 12:00  | 2520         |
| 13:00  | 2455         |
| 18:00  | 2399         |

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
| 12:00  | 6776         |
| 13:00  | 6413         |
| 18:00  | 5417         |

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
| The Thai Chicken Pizza  | 43434.3            |

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
| The Brie Carrie Pizza  | 11588.5            |

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
| L           | 375318.7           |

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
| XXL         | 1006.6            |

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
| Classic        | 220053.1           |

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
| Veggie         | 193690.5          |

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
| 16.82                 |

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
| S          | The Big Meat Pizza     | 1811               |
| L          | The Thai Chicken Pizza | 1365               |
| L          | The Five Cheese Pizza  | 1359               |

# Conclusiones
- El mes con más pedidos es **Julio**.
- El día de la semana que más se pide es el **Viernes**.
- El horario con más pedidos es al mediodía **12:00**.
- En total se realizaron unas **6776** pizzas en el horario pico de las 12:00, es decir, en promedio unas **19** pizzas por día.
- Las pizzas que más ganancias dejan son las **The Thai Chicken Pizza**, el tamaño que más ganancia deja es el **L** y la categoría que más ganancia deja es la **Classic**.
- Una pizza cuesta, en promedio, **16.82** dólares.
- La combinación preferida de los clientes es tamaño **S** de la **The Big Meat Pizza**.

# Visualización
