---
title: 'Plantillas de ejemplo de Azure Resource Manager: App Service | Microsoft Docs'
description: Ejemplos de plantillas de Azure Resource Manager para la característica Web Apps de App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
ms.locfileid: "29933075"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Plantillas de Azure Resource Manager para Web Apps

En la tabla siguiente se incluyen vínculos a las plantillas de Azure Resource Manager para la característica Web Apps de Azure App Service. Para obtener recomendaciones sobre cómo evitar errores habituales al crear plantillas de aplicaciones web, consulte [Guía de implementación de aplicaciones web con plantillas de Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Implementación de una aplicación web**||
| [Aplicación web vinculada a un repositorio de GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implementa una aplicación web de Azure que extrae código de GitHub. |
| [Web app with custom deployment slots](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots) (Aplicación web con espacios de implementación personalizados)| Implementa una aplicación web de Azure con espacios o entornos de implementación personalizados. |
|**Configuración de una aplicación web**||
| [Web app certificate from Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) (Certificado de aplicación web de Key Vault)| Implementa un certificado de aplicación web de Azure desde un secreto de Azure Key Vault y lo utiliza para el enlace de SSL. |
| [Aplicación web con un dominio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implementa una aplicación web de Azure con un nombre de host personalizado. |
| [Web app with a custom domain and SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl) (Aplicación web con un dominio personalizado y SSL)| Implementa una aplicación web de Azure con un nombre de host personalizado y obtiene un certificado de aplicación web desde Key Vault para el enlace de SSL. |
| [Web app with a GoLang extension](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang) (Aplicación web con una extensión de GoLang)| Implementa una aplicación web de Azure con la extensión de sitio GoLang. A continuación, puede ejecutar aplicaciones web desarrolladas en GoLang en Azure. |
| [Web app with Java 8 and Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat) (Aplicación web con Java 8 y Tomcat 8)| Implementa una aplicación web de Azure con Java 8 y Tomcat 8 habilitados. A continuación, puede ejecutar aplicaciones Java en Azure. |
|**Aplicación web para Linux**||
| [Web app on Linux with MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) (Aplicación web en Linux con MySQL) | Implementa una aplicación web de Azure en Linux con Azure Database for MySQL. |
| [Web app on Linux with PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) (Aplicación web en Linux con PostgreSQL) | Implementa una aplicación web de Azure en Linux con Azure Database for PostgreSQL. |
|**Web app with connected resources** (Aplicación web con recursos conectados)||
| [Web app with MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql) (Aplicación web con MySQL)| Implementa una aplicación web de Azure en Windows con Azure Database for MySQL. |
| [Web app with PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql) (Aplicación web con PostgreSQL)| Implementa una aplicación web de Azure en Windows con Azure Database for PostgreSQL. |
| [Web app with a SQL database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) (Aplicación web con un base de datos SQL)| Implementa una aplicación web de Azure y una base de datos SQL en el nivel de servicio Básico. |
| [Web app with a Blob storage connection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection) (Aplicación web con una conexión a Azure Blob Storage)| Implementa una aplicación web de Azure con una cadena de conexión de Azure Blob Storage. A continuación, puede usar el almacenamiento de blobs desde la aplicación web. |
| [Web app with a Redis cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache) (Aplicación web con Azure Redis Cache)| Implementa una aplicación web de Azure con Azure Redis Cache. |
|**App Service Environment para PowerApps**||
| [Create an App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) (Creación de un entorno de App Service v2) | Crea un entorno de App Service v2 en la red virtual. |
| [Create an App Service environment v2 with an ILB address](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) (Creación de un entorno de App Service v2 con una dirección ILB) | Crea un entorno de App Service v2 en la red virtual con una dirección de equilibrador de carga interno privado. |
| [Configure the default SSL certificate for an ILB App Service environment or an ILB App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) (Configuración del certificado SSL predeterminado para un entorno de App Service con ILB o un entorno de App Service v2 con ILB) | Configura el certificado SSL predeterminado para un entorno de App Service con ILB o de App Service v2 con ILB. |
| | |
