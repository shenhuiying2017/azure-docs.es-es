---
title: 'Plantillas de ejemplo de Azure Resource Manager: App Service | Microsoft Docs'
description: 'Plantillas de ejemplo de Azure Resource Manager: App Service'
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
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Plantillas de Azure Resource Manager para Azure Web Apps

En la tabla siguiente se incluyen vínculos a las plantillas de Azure Resource Manager. Para obtener recomendaciones sobre cómo evitar errores habituales al crear plantillas de aplicaciones web, consulte [Guidance on deploying web apps with Azure Resource Manager templates](web-sites-rm-template-guidance.md) (Guía para implementar aplicaciones web con plantillas de Azure Resource Manager).

| | |
|-|-|
|**Implementar una aplicación web**||
| [Aplicación web vinculada a un repositorio de GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implementa una aplicación web de Azure que extrae código de GitHub. |
| [Aplicación web con espacios de implementación personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implementa una aplicación web de Azure con espacios o entornos de implementación personalizados. |
|**Configurar una aplicación web**||
| [Certificado de aplicación web de Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implementa un certificado de aplicación web de Azure desde el secreto de Key Vault y lo utiliza para el enlace de SSL. |
| [Aplicación web con dominio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implementa una aplicación web de Azure con un nombre de host personalizado. |
| [Aplicación web con dominio personalizado y SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implementa una aplicación web de Azure con un nombre de host personalizado y obtiene el certificado de aplicación web desde Key Vault para el enlace de SSL. |
| [Aplicación web con la extensión GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implementa una aplicación web de Azure con la extensión de sitio Golang, lo que permite ejecutar en Azure aplicaciones web desarrolladas en Golang. |
| [Aplicación web con Java 8 y Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implementa una aplicación web de Azure con Java 8 y Tomcat 8 habilitados, lo que permite ejecutar aplicaciones Java en Azure. |
|**Aplicación web para Linux**||
| [Web App on Linux con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implementa una aplicación web de Azure en Linux con Azure Database for MySQL. |
| [Web App on Linux con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implementa una aplicación web de Azure en Linux con Azure Database for PostgreSQL. |
|**Aplicación web con recursos conectados**||
| [Aplicación web con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implementa una aplicación web de Azure en Windows con Azure Database for MySQL. |
| [Web App con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implementa una aplicación web de Azure en Windows con Azure Database for PostgreSQL. |
| [Aplicación web con SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implementa una aplicación web de Azure y SQL Database en el nivel de servicio "Básico". |
| [Aplicación web con conexión a Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implementa una aplicación web de Azure con una cadena de conexión de Blob Storage, lo que permite usar Azure Blob Storage desde la aplicación web. |
| [Aplicación web con Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implementa una aplicación web de Azure con Redis Cache. |
|**Entorno de App Service**||
| [Crear un entorno de App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crea un entorno de App Service Environment v2 en la red virtual. |
| [Crear un entorno de App Service Environment v2 con una dirección de ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crea un entorno de App Service Environment V2 en la red virtual con una dirección de equilibrador de carga interno privado. |
| [Configurar el certificado SSL predeterminado para un entorno ASE con ILB o un entorno ASE v2 con ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura el certificado SSL predeterminado para un entorno de App Service Environment con ILB o un entorno App Service Environment v2 con ILB. |
| | |
