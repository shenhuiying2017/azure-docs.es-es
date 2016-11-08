
### Límites de grupo elástico básico
|  |  |
| --- |:---:|
| Número máximo de eDTU por grupo |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Almacenamiento máximo por grupo (GB)* |&nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| Cantidad máxima de bases de datos por grupo |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Almacenamiento máximo de OLTP en memoria (GB) por grupo |N/D |
| Cantidad máxima de trabajos simultáneos por grupo |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Cantidad máxima de inicios de sesión simultáneos por grupo |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Cantidad máxima de sesiones simultáneas por grupo |4800 &nbsp;9600 &nbsp; 19 200 &nbsp; 28 800 &nbsp; 28 800 |
| Cantidad máxima de eDTU por base de datos* |5 |
| Cantidad mínima de eDTU por base de datos* |0,5 |
| Almacenamiento máximo por base de datos (GB)** |2 |
| Restauración a un momento dado |Cualquier momento en los 7 últimos días |
| Recuperación ante desastres |Replicación geográfica activa |
|  | |

* Se puede establecer el número máximo y mínimo de eDTU por base de datos en cualquiera de los valores que aparecen siempre que el tamaño seleccionado de DTU del grupo sea al menos tan grande como el número máximo de eDTU por base de datos

** Las bases de datos elásticas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita al almacenamiento de grupo restante o al almacenamiento máximo por base de datos, si este es menor.

### Límites de grupo elástico estándar
|  |  |
| --- |:---:|
| Número máximo de eDTU por grupo |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Almacenamiento máximo por grupo (GB)* |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Cantidad máxima de bases de datos por grupo |&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Almacenamiento máximo de OLTP en memoria (GB) por grupo |N/D |
| Cantidad máxima de trabajos simultáneos por grupo |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Cantidad máxima de inicios de sesión simultáneos por grupo |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Cantidad máxima de sesiones simultáneas por grupo |4800 &nbsp; 9600 &nbsp;19 200 &nbsp;28 800 &nbsp;&nbsp; 28 800 |
| Cantidad máxima de eDTU por base de datos* |10, 20, 50, 100 |
| Cantidad mínima de eDTU por base de datos* |0, 10, 20, 50, 100 |
| Almacenamiento máximo por base de datos (GB)** |250 |
| Restauración a un momento dado |Cualquier momento en los últimos 35 días |
| Recuperación ante desastres |Replicación geográfica activa |
|  | |

* Se puede establecer el número máximo y mínimo de eDTU por base de datos en cualquiera de los valores que aparecen siempre que el tamaño seleccionado de DTU del grupo sea al menos tan grande como el número máximo de eDTU por base de datos

** Las bases de datos elásticas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita al almacenamiento de grupo restante o al almacenamiento máximo por base de datos, si este es menor.

### Límites de grupo elástico premium
|  |  |
| --- |:---:|
| Número máximo de eDTU por grupo |125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| Almacenamiento máximo por grupo (GB)* |250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| Cantidad máxima de bases de datos por grupo |50 |
| Almacenamiento máximo de OLTP en memoria (GB) por grupo |N/D |
| Cantidad máxima de trabajos simultáneos por grupo |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Cantidad máxima de inicios de sesión simultáneos por grupo |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Cantidad máxima de sesiones simultáneas por grupo |4800 &nbsp; 9600 &nbsp;19 200 &nbsp;28 800 &nbsp;&nbsp; 28 800 |
| Cantidad máxima de eDTU por base de datos* |125, 250, 500, 1000 |
| Cantidad mínima de eDTU por base de datos* |0, 125, 250, 500, 1000 |
| Almacenamiento máximo por base de datos (GB)** |500 |
| Restauración a un momento dado |Cualquier momento en los últimos 35 días |
| Recuperación ante desastres |Replicación geográfica activa |
|  | |

* Se puede establecer el número máximo y mínimo de eDTU por base de datos en cualquiera de los valores que aparecen siempre que el tamaño seleccionado de DTU del grupo sea al menos tan grande como el número máximo de eDTU por base de datos

** Las bases de datos elásticas comparten almacenamiento de grupo, por lo que el almacenamiento de base de datos se limita al almacenamiento de grupo restante o al almacenamiento máximo por base de datos, si este es menor.

<!---HONumber=AcomDC_0914_2016-->