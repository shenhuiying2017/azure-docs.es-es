<properties
	pageTitle="Migración de aplicaciones lógicas a la versión de esquema 2015-08-01-preview | Servicio de aplicaciones de Microsoft Azure"
	description="Puede migrar fácilmente las aplicaciones lógicas a la última versión de esquema. Simplemente, siga estos pasos:"
	services="logic-apps"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="deonhe"/>

# Migración de aplicaciones lógicas a la versión de esquema 2015-08-01-preview

Para mover las aplicaciones lógicas existentes al nuevo esquema, realice lo siguiente:  
1. Abra la aplicación lógica en el Portal de Azure  
2. Haga clic en Actualizar esquema:

![Icono de API][step1]

La página Actualizar esquema aparece y ofrece un vínculo a un documento que proporciona más información acerca de las mejoras del nuevo esquema:

![Icono de API][step2]

>[AZURE.NOTE] Al seleccionar **Actualizar esquema**, automáticamente se ejecutan los pasos de migración y se le proporciona la salida de código. Con ello puede actualizar la definición, sin embargo, asegúrese de seguir las buenas prácticas de codificación descritas en la sección **Procedimientos recomendados** de más adelante.

## Procedimientos recomendados al migrar las aplicaciones lógicas a la última versión de esquema:  

- Copie el script migrado a una nueva aplicación lógica: no sobrescriba el antiguo hasta que haya completado las pruebas y confirmado que la aplicación migrada funciona según lo previsto.
- Pruebe la aplicación lógica **antes de** ponerla en producción
- Una vez finalizada la migración, comience la actualización de las aplicaciones lógicas para usar las [API administradas](./apis-list.md) siempre que sea posible. Por ejemplo, puede empezar a utilizar Dropbox v2, en cualquier ocasión que esté utilizando DropBox v1.


## Pasos siguientes
-  [Obtenga información sobre cómo migrar las aplicaciones lógicas de forma manual](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!----HONumber=AcomDC_0824_2016-->
