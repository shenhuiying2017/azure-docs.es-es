<properties
	pageTitle="Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure"
	description="Pasos para identificar y resolver errores de conexión comunes para la base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="daleche"/>

# Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure
Los problemas de conexión relacionados con la base de datos SQL de Azure se pueden clasificar según los siguientes niveles:

- [Errores transitorios (corta duración o intermitentes)](#troubleshoot-the-transient-errors)
- [Errores persistentes o no transitorios (errores que se repiten con frecuencia)](#troubleshoot-the-persistent-errors-non-transient-errors)

Si encuentra problemas de conexión, pruebe los pasos de solución de problemas que se describen en este artículo.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Solución de los errores transitorios
Si la aplicación está experimentando errores transitorios, revise los siguientes temas para obtener sugerencias acerca de cómo solucionar problemas y reducir la frecuencia de estos errores:

- [Troubleshooting Database &lt;x&gt; on Server &lt;y&gt; is unavailable (Error: 40613) (Solución de problemas del error La base de datos &lt;x&gt; del servidor &lt;y&gt; no está disponible (Error: 40613))](sql-database-troubleshoot-connection.md)
- [Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>
## Solución de los errores persistentes (errores no transitorios)
Si la aplicación no se puede conectar a la base de datos SQL Azure de forma persistente, normalmente indica un problema con uno de los siguientes elementos:

- Configuración del firewall. La base de datos SQL de Azure o el firewall del cliente están bloqueando las conexiones de la base de datos SQL de Azure.
- Reconfiguración de la red en el lado cliente: por ejemplo, un nuevo servidor de proxy o dirección IP.
- Error de usuario: por ejemplo, escribió incorrectamente los parámetros de conexión, como el nombre del servidor en la cadena de conexión.

### Pasos para resolver los problemas de conectividad persistentes
1.	Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente.
2.	En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes. Consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) para obtener indicaciones adicionales.
3.	Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de problemas de conectividad](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.	Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.

<!----HONumber=AcomDC_0511_2016-->