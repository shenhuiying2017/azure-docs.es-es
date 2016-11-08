---
title: Solución de problemas del control de acceso basado en roles | Microsoft Docs
description: Obtenga ayuda para los problemas o dudas que le surjan relativos a los recursos del control de acceso basado en roles.
services: azure-portal
documentationcenter: na
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: kgremban

---
# Solución de problemas del control de acceso basado en rol
## Introducción
[El control de acceso basado en rol](role-based-access-control-configure.md) es una función eficaz que le permite delegar accesos específicos a recursos en Azure. Esto significa que puede tener la seguridad de que concede a una determinada persona el acceso exclusivamente a lo que necesita y nada más. No obstante, algunas veces el modelo de recursos para los recursos de Azure puede ser complicado y puede costar entender a qué se están concediendo permisos exactamente.

En este documento se explica qué puede esperar al usar algunos de los nuevos roles del Portal de Azure. Estos tres roles abarcan todos los tipos de recursos:

* Propietario
* Colaborador
* Lector

Los propietarios y los colaboradores tienen acceso total a la experiencia de administración, pero un colaborador no puede dar acceso a otros usuarios o grupos. Nos centraremos en el rol del lector, ya que tiene más cosas que comentar. Consulte el artículo de [introducción de Control de acceso basado en rol](role-based-access-control-configure.md) para obtener más información sobre cómo conceder acceso.

## Cargas de trabajo del Servicio de aplicaciones
### Funcionalidades de acceso de escritura
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

Si no puede acceder a ninguno de estos iconos, deberá pedirle al administrador el acceso de colaborador a la aplicación web.

### Tratar con recursos relacionados
Las aplicaciones web pueden resultar complicadas si entran en juego distintos recursos. Este es un grupo de recursos típico con un par de sitios web:

![Grupo de recursos de aplicación web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Como consecuencia, si le concede a alguien acceso solo a la aplicación web, muchas de las funcionalidades de la hoja del sitio web del Portal de Azure estarán deshabilitadas.

Estos elementos requieren acceso de **lectura** al **plan del Servicio de aplicaciones** que se corresponde con su sitio web:

* Visualización del plan de tarifa de la aplicación web (gratis o estándar).
* Configuración de escala (número de instancias, tamaño de la máquina virtual y configuración de escala automática).
* Cuotas (almacenamiento, ancho de banda y CPU).

Estos elementos requieren acceso de **lectura** a todo el **grupo de recursos** que contiene su sitio web:

* Enlaces y certificados SSL (porque los certificados SSL se pueden compartir entre sitios en el mismo grupo de recursos y la misma ubicación geográfica)
* Reglas de alertas
* Opciones de escala automática
* Componentes de Application Insights
* Pruebas web

## Cargas de trabajo de máquina virtual
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

Si no puede acceder a ninguno de estos iconos, deberá pedirle al administrador el acceso de colaborador al grupo de recursos.

## Ver más
* [Control de acceso basado en roles de Azure](role-based-access-control-configure.md): inicio de RBAC en el Portal de Azure.
* [RBAC: Roles integrados](role-based-access-built-in-roles.md): obtenga información sobre los roles que se incluyen como estándar en RBAC.
* [Roles personalizados en Azure RBAC](role-based-access-control-custom-roles.md): aprenda a crear roles personalizados para satisfacer sus necesidades de acceso.
* [Creación de un informe de historial de cambios de acceso](role-based-access-control-access-change-history-report.md): realice un seguimiento del cambio de asignaciones de rol en el RBAC.

<!---HONumber=AcomDC_0713_2016-->