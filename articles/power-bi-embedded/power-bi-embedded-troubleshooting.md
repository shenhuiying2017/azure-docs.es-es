<properties
   pageTitle="Solución de problemas de la versión preliminar de Microsoft Power BI Embedded"
   description="Solución de problemas de la versión preliminar de Microsoft Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/29/2016"
   ms.author="owend"/>

# Solución de problemas de la versión preliminar de Microsoft Power BI Embedded
Este artículo ofrece respuestas sobre cómo solucionar problemas de **Power BI Embedded**.

<a name="connection-string"/>
## Configuración de cadenas de conexión de SQL Server
Para establecer una cadena de conexión de SQL Server, debe seguir un formato específico. A continuación se muestra una cadena de conexión de ejemplo para SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para obtener más información sobre las cadenas de conexión de SQL Server, consulte los artículos siguientes:

-	[SQL Server Connection Strings](https://msdn.microsoft.com/library/jj653752.aspx) (Cadenas de conexión de SQL Server)
-	[SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## Configuración de credenciales
En el caso en el que tiene credenciales para un entorno de desarrollo o ensayo, como un nombre de usuario y una contraseña, podría tener que actualizar las credenciales que coincidan con una solución de producción.

## Otras referencias
- [Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)
- [What is Microsoft Power BI Embedded (¿Qué es Microsoft Power BI Embedded?)](power-bi-embedded-what-is-power-bi-embedded.md)

<!---HONumber=AcomDC_0713_2016-->