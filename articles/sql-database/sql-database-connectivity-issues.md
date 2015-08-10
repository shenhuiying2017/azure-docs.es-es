<properties 
	pageTitle="Problemas de conectividad de la base de datos SQL de Azure" 
	description="Identificación y determinación de errores de conexión de la base de datos SQL." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="07/24/2015" 
	ms.author="sstein"/>


# Problemas de conexión de la base de datos SQL

Este artículo proporciona información general sobre cómo usar varias soluciones de problemas cuando no se puede establecer conexión con la base de datos SQL de Azure.


## Determine si el problema de conectividad es específico de una aplicación individual o si simplemente no se puede conectar a la base de datos.

Use SQL Server Management Studio o SQLCMD.EXE para comprobar que puede conectarse a la base de datos.

- Para obtener instrucciones sobre la conexión a la base de datos SQL con SQL Server Management Studio (SSMS), vea [Cómo conectarse a una base de datos SQL de Azure con SSMS](sql-database-connect-to-database.md).
- Para obtener instrucciones sobre la conexión a la base de datos SQL con SQLCMD, vea [Cómo conectarse a una base de datos SQL de Azure mediante sqlcmd](https://msdn.microsoft.com/library/azure/ee336280.aspx).



## ¿Se está ejecutando en Azure la aplicación que está tratando de conectarse a la base de datos SQL de Azure (por ejemplo, es la aplicación que no puede conectarse a la base de datos un servicio en la nube o una aplicación web)?

Asegúrese de que la regla de firewall permita que todos los servicios de Azure estén habilitados para el servidor/la base de datos.

- Para obtener información sobre las reglas de firewall y sobre la habilitación de las conexiones de Azure, vea [Firewall de base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).



## ¿Está intentando conectarse la aplicación a la base de datos SQL desde una red privada?

Asegúrese de que las reglas de firewall que permiten el acceso desde las redes específicas estén habilitadas para el servidor/la base de datos.

- Para obtener información general acerca de las reglas de firewall, vea [Firewall de la base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
- Para obtener instrucciones acerca de cómo configurar las reglas de firewall, vea [Cómo configurar opciones de firewall (base de datos SQL de Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).


## Si las reglas de firewall están configuradas correctamente, pruebe con el tutorial interactivo [Solución de problemas de conectividad de la base de datos SQL de Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).

El artículo de soporte técnico anterior proporciona ayuda para los siguientes problemas de conectividad de la base de datos SQL:

- No se puede abrir la conexión al servidor debido a la configuración del firewall 
- El servidor no se encontró o no estaba accesible 
- No se puede iniciar sesión en el servidor 
- Errores de tiempo de espera de conexión 
- Errores transitorios 
- La conexión terminó porque se ha alcanzado un límite definido por el sistema 
- Errores de conexiones de SQL Server Management Studio (SSMS) 


## Información adicional

- Para obtener información adicional sobre cómo conectarse a la base de datos SQL, vea [Directrices para la conexión a la base de datos SQL de Azure de mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx).   

- Encontrará detalles sobre los errores de conexión específicos en la sección **Errores transitorios, errores de pérdida de conexión** en [Mensajes de error para programas de cliente de Base de datos SQL](sql-database-develop-error-messages.md#bkmk_connection_errors).

- Es posible tener acceso a los datos de eventos de conexión mediante el envío de consultas de eventos de conectividad con la vista [**sys.event\_log (base de datos SQL de Azure)**](https://msdn.microsoft.com/library/dn270018.aspx).

- Es posible tener acceso a las métricas de eventos de conectividad de la base de datos mediante el envío de consultas a la vista [**sys.database\_connection\_stats (base de datos SQL de Azure)**](https://msdn.microsoft.com/library/dn269986.aspx).

 

<!---HONumber=July15_HO5-->