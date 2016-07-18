<properties
   pageTitle="Introducción al Cifrado de datos transparente (TDE) en Almacenamiento de datos SQL | Microsoft Azure"
   description="Introducción al cifrado de datos transparente (TDE) en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/07/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# Introducción al cifrado de datos transparente (TDE) en Almacenamiento de datos SQL

> [AZURE.SELECTOR]
- [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
- [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Introducción a la auditoría](sql-data-warehouse-auditing-overview.md)
- [Auditoría de los clientes de nivel inferior](sql-data-warehouse-auditing-downlevel-clients.md)


El Cifrado de datos transparente (TDE) del Almacenamiento de datos SQL facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin requerir cambios en la aplicación.

TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos. En Base de datos SQL la clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Base de datos SQL. Microsoft alterna automáticamente estos certificados al menos cada 90 días. El algoritmo de cifrado usado por Almacenamiento de datos SQL es AES-256. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)].

##Habilitar el cifrado

Para habilitar TDE para un Almacenamiento de datos SQL, siga estos pasos:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente** ![][1]
4. Seleccione el valor **Activado** ![][2]
5. Seleccione **Guardar** ![][3]

##Deshabilitar el cifrado

Para deshabilitar TDE para un Almacenamiento de datos SQL, siga estos pasos:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente** ![][1]
4. Seleccione el valor **Desactivado** ![][4]
5. Seleccione **Guardar** ![][5]

##DMV de cifrado

El cifrado se puede confirmar con las siguientes DMV:

- [Sys.Databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->