# 🍕 Pizza Sales - Análisis
![Red Illustration Pizza Shop Logo](https://github.com/marina-95/Pizza-Sales-Analysis/assets/144913530/b7c7e9a4-e2f5-4224-b694-a449b2105e27)

# Tabla de Contenido


# Introducción
La pizzería Plato's Pizza necesita de un consultor BI que analice y genere un reporte sobre los datos que recopilaron en todo un año, con el fin de encontrar oportunidades para generar más ventas y trabajar de manera más eficiente.

En el siguiente link se puede acceder a la base de datos: [aquí](https://www.kaggle.com/datasets/shilongzhuang/pizza-sales?datasetId=2566526&sortBy=voteCount)

# Herramientas utilizadas
- SQL Server
- Power BI

# Diagrama entidad - relación

# Preguntas & Soluciones
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
