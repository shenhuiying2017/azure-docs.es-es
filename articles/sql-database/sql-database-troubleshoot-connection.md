<properties
	pageTitle="Solución del problema de que la base de datos del servidor no está disponible actualmente para la Base de datos SQL de Azure"
	description="Pasos para identificar y resolver errores de conexión para la Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="daleche"/>

# Solución de problemas de "La base de datos del servidor no está disponible actualmente. Vuelva a intentar la conexión más tarde." y de otros errores de conexión
"La base de datos <dbname> en el servidor <servername> no está disponible actualmente." es el error de conexión transitorio más común para la Base de datos SQL de Azure. Los errores transitorios de conexión suelen deberse a una reconfiguración de plataforma, como la conmutación por error a un nuevo servidor o una interrupción temporal del sistema y son de corta duración. Si obtiene un error diferente, evalúe el [mensaje de error](sql-database-develop-error-messages.md) para pistas sobre la causa, determine si el problema es transitorio o persistente, y use las instrucciones de este tema.

## Pasos para resolver los problemas de conectividad transitorios
1.	Asegúrese de que su aplicación usa la lógica de reintento. Vea los [problemas de conectividad de](sql-database-connectivity-issues.md) y las [prácticas recomendadas y las directrices de diseño](sql-database-connect-central-recommendations.md) para estrategias de reintento generales. Luego vea [ejemplos de código](sql-database-develop-quick-start-client-code-samples.md) para información específica.
2.	Conforme una base de datos se acerca a sus límite de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
3.	Si los problemas de conectividad continúan, póngase en contacto con el soporte técnico para abrir un caso de soporte técnico.

## Pasos para resolver los problemas de conectividad persistentes
Si la aplicación no se puede conectar en absoluto, suele deberse a la configuración del IP y del firewall. Esto puede incluir la reconfiguración de la red en el lado cliente (por ejemplo, un nuevo proxy o dirección IP). Los parámetros de conexión mal escritos, como la cadena de conexión, también son comunes.

1.	Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente.
2.	En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes.
3.	Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de problemas de conectividad](sql-database-connectivity-issues.md).
4.	Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.
5.	Si los problemas de conectividad continúan, póngase en contacto con el soporte técnico para abrir un caso de soporte técnico.

<!---HONumber=AcomDC_1210_2015-->