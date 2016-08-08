<properties
   pageTitle="Conexión a la puerta de enlace de datos local de las Aplicaciones lógicas | Microsoft Azure"
   description="Información sobre cómo crear una conexión a la puerta de enlace de datos local desde una aplicación lógica."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# Conexión a la puerta de enlace de datos local para las Aplicaciones lógicas

Los conectores de las aplicaciones lógicas compatibles permiten configurar la conexión para acceder a los datos locales a través de la puerta de enlace de datos local. Los siguientes pasos le guiarán por el proceso de instalación y configuración de la puerta de enlace de datos local para trabajar con una aplicación lógica.

## Requisitos previos

* Debe utilizar una dirección de correo electrónico profesional o educativa en Azure para asociar la puerta de enlace de datos local a su cuenta (cuenta basada en Azure Active Directory).
    * Si usa una cuenta de Microsoft (por ejemplo, @outlook.com, @live.com), podrá usar su cuenta de Azure para crear una dirección de correo electrónico profesional o educativa [siguiendo estos pasos](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

> [AZURE.WARNING] Actualmente existe una limitación, y es que la instalación de la puerta de enlace local solo se realizará mediante una cuenta que se haya registrado con Power BI. Mientras tanto, registre cualquier cuenta con "Power BI (gratis)" para completar la instalación correctamente.

* Debe tener la puerta de enlace de datos local [instalada en una máquina local](app-service-logic-gateway-install.md).
* Ninguna otra puerta de enlace de datos local de Azure debe haber reclamado la puerta de enlace ([esto se produce en el proceso de creación del paso 2 que se indica a continuación](#2-create-an-azure-on-premises-data-gateway-resource)): una instalación solo puede estar asociada a un recurso de puerta de enlace.

## Instalación y configuración de la conexión

### 1\. Instalación de la puerta de enlace de datos local

Puede encontrar información sobre cómo instalar la puerta de enlace de datos local [en este artículo](app-service-logic-gateway-install.md). La puerta de enlace debe instalarse en una máquina local para poder continuar con el resto de los pasos.

### 2\. Creación de un recurso de puerta de enlace de datos local de Azure

Una vez instalada, debe asociar su suscripción de Azure a la puerta de enlace de datos local.

1. Inicie sesión en Azure con la misma dirección de correo electrónico profesional o académica utilizada durante la instalación de la puerta de enlace.
1. Haga clic en el botón de recurso **Nuevo**.
1. Busque y seleccione **Puerta de enlace de datos local**.
1. Complete la información para asociar la puerta de enlace a su cuenta (lo que incluye seleccionar el **nombre de la instalación** pertinente).

    ![Conexión a una puerta de enlace de datos local][1]
1. Haga clic en el botón **Crear** para crear el recurso.

### 3\. Creación de una conexión con la aplicación lógica del diseñador

Ahora que su suscripción de Azure está asociada a una instancia de la puerta de enlace de datos local, puede crear una conexión con ella desde dentro de una Aplicación lógica.

1. Abra una aplicación lógica y elija un conector que admita la conectividad local (en la fecha en la que se redactó este artículo, SQL Server).
1. Seleccione la casilla **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local).

    ![Creación de una puerta de enlace del diseñador de Aplicaciones lógicas][2]
1. Seleccione la **puerta de enlace** a la que desea conectarse y cumplimente cualquier otra información obligatoria sobre la conexión.
1. Haga clic en **Crear** para crear la conexión.

Llegado a este punto, la conexión ya debería estar configurada correctamente para que pueda utilizarla en la aplicación lógica.

## Pasos siguientes
- [Ejemplos y escenarios habituales de las aplicaciones lógicas](app-service-logic-examples-and-scenarios.md)
- [Características de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0727_2016-->