<properties
	pageTitle="Tutorial: Activación de la actualización más reciente de Base de datos SQL V12"
	description="Describe los pasos que hay que seguir para probar la versión V12 de Base de datos SQL de Azure a través de la nueva interfaz de usuario del portal de Microsoft Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# Tutorial: Activación de la actualización más reciente de Base de datos SQL V12

En este tema, se describen los pasos que puede seguir para activar la opción de Base de datos SQL de Azure V12, tal como la publicó Microsoft en diciembre de 2014.

Para probar la versión más reciente de V12, primero tiene que suscribirse a Microsoft Azure o, al menos, a una suscripción de [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).

La versión V12 se puede activar desde el nuevo Portal de vista previa de Azure, [http://portal.azure.com/](http://portal.azure.com/), en lugar de desde el [Portal de Azure anterior](http://manage.windowsazure.com/). Después de activar la versión V12 para la suscripción, las opciones de creación y actualización de V12 se desbloquearán en el Portal de vista previa de Azure. A continuación, los usuarios pueden iniciar el flujo de trabajo de [creación](sql-database-create.md) desde la hoja del servidor o la de la base de datos.

> [AZURE.NOTE]Las bases de datos de prueba, copias de la base de datos o bases de datos nuevas son buenas candidatas para la actualización a la vista previa. Las bases de datos de producción de las que depende su negocio deben esperar hasta después del período de la versión de vista previa.

Para obtener más información sobre la actualización a V12, consulte [Planeación y preparación para actualizar a Base de datos SQL de Azure V12](sql-database-v12-plan-prepare-upgrade.md).


## R: Autorización de seguridad

El primer paso es asegurarse de que tiene autorización suficiente para activar la versión V12 en su suscripción. Al intentar activar la opción de V12, se realiza una comprobación de la autorización para comprobar que tiene autoridad suficiente en la suscripción.

 Para probar la V12, debe tener una de las autorizaciones siguientes:

- El propietario de la suscripción
- Un coadministrador de la suscripción

Para obtener más información acerca de las cuentas de Azure, consulte [Administrar cuentas, suscripciones y roles administrativos](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Pasos en el Portal de vista previa de Azure

En esta sección se describe una secuencia de clics que puede seguir una vez que esté en la interfaz de usuario del Portal de vista previa de Azure para activar la opción de V12. Después de activar la opción, permanecerá disponible a partir de entonces.

Todos los escenarios de activación siguen la misma idea básica. Cuando trate de [crear por primera vez un nuevo servidor de Base de datos SQL](sql-database-create.md), se mostrará una hoja denominada **Última actualización (vista previa)** que incluye una casilla que se puede seleccionar para activar el privilegio de usar la versión V12. Después de activar el privilegio, no se volverá a mostrar la casilla de nuevo. En su lugar, se ve un control **Sí|No** que se puede utilizar para especificar si se desea que el nuevo servidor use la versión V12. Si elige **No**, se crea un servidor de V11 (como indica SELECT @@VERSION;).

### B.1 Control Sí|No para la versión V12

Después de activar el privilegio de la opción de V12, verá el control **Sí|No**, que se muestra rodeado con un círculo en la siguiente captura de pantalla del Portal de vista previa de Azure.

![YesNoOptionForTheV12Preview][Image1]


## C. Pasos siguientes

A continuación, en los siguientes temas se explican las distintas formas de utilizar Base de datos SQL V12.

- [Creación de una base de datos en la actualización de Base de datos SQL V12](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

<!---HONumber=58-->