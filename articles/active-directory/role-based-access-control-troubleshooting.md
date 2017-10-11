---
title: "Solución de problemas de Azure RBAC | Microsoft Docs"
description: Obtenga ayuda para los problemas o dudas que le surjan relativos a los recursos del control de acceso basado en roles.
services: azure-portal
documentationcenter: na
author: andredm7
manager: femila
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 407c030ea159915d4d7ac21760a3d17ec2204372
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="role-based-access-control-troubleshooting"></a>Solución de problemas del control de acceso basado en rol

En este artículo se responden preguntas comunes sobre de los derechos de acceso específicos que se conceden con roles, para que sepa qué esperar cuando usa los roles de Azure Portal y pueda solucionar problemas de acceso. Estos tres roles abarcan todos los tipos de recursos:

* Propietario  
* Colaborador  
* Lector  

Los propietarios y los colaboradores tienen acceso total a la experiencia de administración, pero un colaborador no puede dar acceso a otros usuarios o grupos. Nos centraremos en el rol del lector, ya que tiene más aspectos que comentar. Consulte el artículo de [introducción de Control de acceso basado en rol](role-based-access-control-configure.md) para obtener más información sobre cómo conceder acceso.

## <a name="app-service-workloads"></a>Cargas de trabajo del Servicio de aplicaciones
### <a name="write-access-capabilities"></a>Funcionalidades de acceso de escritura
Si concede a un usuario acceso de solo lectura a una única aplicación web, se deshabilitan algunas características que no cabría esperar. Las siguientes funcionalidades de administración requieren acceso de **escritura** a una aplicación web (bien como colaborador, bien como propietario) y no están disponibles en un escenario de solo lectura.

* Comandos (p. ej., iniciar, parar, etc.)
* Cambiar opciones, como la configuración general, opciones de escala, opciones de copia de seguridad y opciones de supervisión
* Acceder a las credenciales de publicación y otros secretos, como opciones de aplicaciones y cadenas de conexión
* Registros de streaming
* Configuración de registros de diagnóstico
* Consola (símbolo del sistema)
* Implementaciones activas y recientes (para implementaciones git continuas locales)
* Gasto estimado
* Pruebas web
* Red virtual (solo visible para un lector si un usuario con acceso de escritura ha configurado previamente una red virtual)

Si no puede acceder a ninguno de estos iconos, debe pedirle al administrador el acceso de colaborador a la aplicación web.

### <a name="dealing-with-related-resources"></a>Tratar con recursos relacionados
Las aplicaciones web pueden resultar complicadas si entran en juego distintos recursos. Este es un grupo de recursos típico con un par de sitios web:

![Grupo de recursos de aplicación web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Como consecuencia, si le concede a alguien acceso solo a la aplicación web, muchas de las funcionalidades de la hoja del sitio web de Azure Portal están deshabilitadas.

Estos elementos requieren acceso de **escritura** al **plan de App Service** que corresponde a su sitio web:  

* Visualización del plan de tarifa de la aplicación web (gratis o estándar).  
* Configuración de escala (número de instancias, tamaño de la máquina virtual y configuración de escala automática).  
* Cuotas (almacenamiento, ancho de banda y CPU).  

Estos elementos requieren acceso de **escritura** a todo el **grupo de recursos** que contiene su sitio web:  

* Enlaces y certificados SSL (los certificados SSL se pueden compartir entre sitios en el mismo grupo de recursos y la misma ubicación geográfica)  
* Las reglas de alertas  
* Opciones de escala automática  
* Componentes de Application Insights  
* Pruebas web  

## <a name="virtual-machine-workloads"></a>Cargas de trabajo de máquina virtual
Al igual que con las aplicaciones web, algunas funciones de la hoja de máquina virtual requieren acceso de escritura a la máquina virtual o a otros recursos del grupo de recursos.

Las máquinas virtuales están relacionadas con los nombres de dominio, las redes virtuales, las cuentas de almacenamiento y las reglas de alerta.

Estos elementos requieren acceso de **escritura** a la **máquina virtual**:

* Extremos  
* Direcciones IP  
* Discos  
* Extensiones  

Estos requieren acceso de **escritura** a la **máquina virtual** y al **grupo de recursos** (junto con el nombre de dominio) donde se encuentran:  

* El conjunto de disponibilidad  
* El conjunto de carga equilibrada  
* Las reglas de alertas  

Si no puede acceder a ninguno de estos iconos, debe pedirle al administrador el acceso de colaborador al grupo de recursos.

## <a name="see-more"></a>Ver más
* [Control de acceso basado en roles de Azure](role-based-access-control-configure.md): inicio de RBAC en el Portal de Azure.
* [RBAC: Roles integrados](role-based-access-built-in-roles.md): obtenga información sobre los roles que se incluyen como estándar en RBAC.
* [Roles personalizados en Azure RBAC](role-based-access-control-custom-roles.md): aprenda a crear roles personalizados para satisfacer sus necesidades de acceso.
* [Creación de un informe de historial de cambios de acceso](role-based-access-control-access-change-history-report.md): realice un seguimiento del cambio de asignaciones de rol en el RBAC.

