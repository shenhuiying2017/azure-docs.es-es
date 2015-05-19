<properties 
	pageTitle="¿Qué es el Almacén de claves de Azure? | Información general" 
	description="El Almacén de claves de Azure ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Almacén de claves de Azure, los clientes pueden cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través de claves que están protegidas por módulos de seguridad de hardware (HSM)." 
	services="key-vault" 
	documentationCenter="" 
	authors="cabailey" 
	manager="mbaldwin"/>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="cabailey"/>



# ¿Qué es el Almacén de claves de Azure? 

## Introducción

Almacén de claves de Azure \(actualmente en versión de vista previa\), ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Almacén de claves, puede cifrar claves y secretos \(por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas\) a través del uso de claves que están protegidas por módulos de seguridad de hardware \(HSM\). Para obtener seguridad adicional, puede importar o generar claves en HSM, las cuales nunca salen del límite HSM. Los HSM están cuentan con la certificación FIPS 140-2 de nivel 2.

Almacén de claves agiliza el proceso de administración de claves y le permite mantener el control de claves que obtienen acceso a sus datos y los cifran. Los desarrolladores pueden crear claves para desarrollo y prueba en minutos y, a continuación, migrarlas sin problemas a claves de producción. Los administradores de seguridad pueden conceder \(y revocar\) permisos a las claves según sea necesario.

Utilice la tabla siguiente para comprender mejor cómo Almacén de claves puede ayudar a satisfacer las necesidades de los programadores y de los administradores de seguridad.





| Rol | Declaración del problema | Resuelto por Almacén de claves de Azure |
| ------------- |-------------|-----|
| Desarrollador para una aplicación de Azure | “Quiero escribir una aplicación para Azure que use claves para firma y cifrado, pero quiero que sean externas desde mi aplicación para que la solución sea adecuada para una aplicación que se distribuya geográficamente. <br/><br/>También quiero que estas claves y secretos estén protegidos, sin tener que escribir el código, y quiero que me resulten fáciles de usar desde mi aplicación". | √ Las claves se almacenan en un almacén y las invoca un URI cuando es necesario.<br/><br/> √ Las se protegen mediante Azure usando algoritmos estándar de la industria, longitudes de clave y módulos de seguridad de hardware \(HSM\).<br/><br/> √ Las claves se colocan en HSM dentro de los centros de datos de Azure, lo que proporciona mayor confiabilidad y menor latencia.|
| Desarrollador para software como servicio \(SaaS\): |“No quiero asumir la responsabilidad, ni tampoco la posible responsabilidad, de las claves y los secretos de inquilino de mis clientes. <br/><br/>Quiero que los clientes posean y administren sus claves de modo que pueda concentrarse en hacer lo que hago mejor, que es proporcionar las características de software principales.” | √ Los clientes pueden importar sus propias claves a Azure y administrarlas. Cuando se necesita una aplicación SaaS para realizar operaciones criptográficas usando claves de sus clientes, el Almacén de claves lo hace en nombre de la aplicación. La aplicación no ve las claves de los clientes.|
| Responsable principal de la seguridad \(CSO\) | “Quiero saber que nuestras aplicaciones cumplen con HSM FIPS 140-2 de nivel 2 para administración de claves segura. <br/><br/>Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de las mismas. <br/><br/>Y aunque usamos varios servicios y recursos de Azure, quiero administrar las claves desde una ubicación única en Azure.” |√ Los HSM tienen la certificación FIPS 140-2 de nivel 2.<br/><br/>√ El Almacén de claves está diseñado para que Microsoft no vea ni extraiga las claves.<br/><br/>√ Registro prácticamente en tiempo real del uso de claves \(no disponible actualmente\).<br/><br/>√ El almacén proporciona una sola interfaz, independientemente de cuántos almacenes tiene en Azure, qué regiones admiten y qué aplicaciones los utilizan. |


Cualquier persona que tenga una suscripción de Azure puede crear y usar almacenes de claves. Aunque el Almacén de claves beneficia a los desarrolladores y los administradores de seguridad, el administrador de una organización que administra otros servicios de Azure, podría implementarlo y administrarlo. Por ejemplo, este administrador iniciaría sesión con una suscripción de Azure, crearía un almacén para la organización en el que almacenar las claves y, a continuación, asumiría la responsabilidad de las tareas operativas, como:

+ Crear o importar una clave o un secreto 
+ Revocar o eliminar una clave o un secreto
+ Autorizar a usuarios o aplicaciones a administrar o usar claves y secretos
+ Configurar el uso de claves \(por ejemplo, para firmar o cifrar\)
+ Supervisar el uso de claves

Este administrador podría ofrecer después a los desarrolladores los URI para llamar desde sus aplicaciones y proporcionar a su administrador de seguridad la información de registro de uso de claves. **La información de registro de uso de claves no está disponible actualmente.**

   ![Información general del Almacén de claves de Azure][1]

Los desarrolladores también pueden administrar las claves directamente mediante API. Para obtener más información, vea [API de REST del Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx) y [Referencia de API de cliente de C\# del Almacén de claves](https://msdn.microsoft.com/library/azure/dn903628.aspx).

## Pasos siguientes

Para consultar un tutorial de introducción para que un administrador, vea [Introducción al Almacén de claves de Azure](key-vault-get-started.md).

Para obtener más información acerca del uso de claves y secretos con el Almacén de claves de Azure, vea [Acerca de claves y secretos](https://msdn.microsoft.com/library/azure/dn903623.aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png



<!--HONumber=54-->