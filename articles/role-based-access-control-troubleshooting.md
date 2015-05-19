<properties 
	pageTitle="Solución de problemas del control de acceso basado en roles" 
	description="Trabajar con diferentes tipos de recursos para el control de acceso basado en rol." 
	services="azure-portal"
	documentationCenter="na" 
	authors="stepsic-microsoft-com" 
	writer="" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Solución de problemas del control de acceso basado en roles

## Introducción

[El control de acceso basado en rol](role-based-access-control-configure.md) es una función potente que le permite delegar accesos específicos a recursos en Azure. Esto significa que puede tener la seguridad de que concede a una determinada persona el acceso exclusivamente a lo que necesita. No obstante, algunas veces el modelo de recursos para los recursos de Azure puede ser complicado y puede costar entender a qué se están concediendo permisos exactamente.

En este documento explicamos qué puede esperar al usar algunos de los nuevos roles del portal de Azure. En esta versión se incluyen tres roles predefinidos: \* Propietario \* Colaborador \* Lector

Los propietarios y los colaboradores tienen acceso total a la experiencia de administración, pero un colaborador no puede dar acceso a otros usuarios o grupos. Nos centraremos en el rol del lector, ya que tiene más cosas que comentar. [Consulte este artículo](role-based-access-control-configure.md) para obtener información detallada sobre cómo conceder acceso exactamente.

## Cargas de trabajo del Servicio de la aplicaciones

### Acceso de solo lectura 

Cuando le conceda a un usuario acceso de lectura a un sitio web, o si es usted el que solo tiene este tipo de acceso, es posible que algunas funciones estén deshabilitadas sin esperarlo. Las siguientes capacidades de administración requieren acceso de **escritura** a una aplicación web \(bien como colaborador, bien como propietario\) y no están disponibles en un escenario de solo lectura.
 
1. Comandos \(p. ej., iniciar, parar, etc.\)
2. Cambiar opciones, como la configuración general, opciones de escala, opciones de copia de seguridad y opciones de supervisión
3. Obtener acceso a las credenciales de publicación y otros secretos, como opciones de aplicaciones y cadenas de conexión
4. Registros de streaming
5. Configuración de registros de diagnóstico
6. Consola \(símbolo del sistema\)
7. Implementaciones activas y recientes \(para implementaciones git continuas locales\)
8. Gasto estimado
9. Pruebas web
10. Red virtual \(solo visible para un lector si un usuario con acceso de escritura ha configurado previamente una red virtual\)
 
Si no puede obtener acceso a ninguno de estos iconos, necesitará acceso de colaborador a la aplicación web.

### Tratar con recursos relacionados
 
Las aplicaciones web pueden resultar complicadas si entran en juego distintos recursos. Este es un grupo de recursos típico con un par de sitios web:

![Grupo de recursos de aplicación web](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Como consecuencia, si le concede a alguien acceso solo al sitio web, muchas de las funciones de la hoja del sitio web estarán totalmente deshabilitadas.
 
1. Estos elementos requieren acceso al **plan del Servicio de aplicaciones** que corresponde a su sitio web:  
    * Visualización del nivel de precios de la aplicación web \(p. ej., gratis o estándar\).
    * Configuración de escala \(es decir, número de instancias, tamaño de la máquina virtual, configuración de escala automática\).
    * Cuotas \(p. ej., almacenamiento, ancho de banda, CPU\).
2. Estos elementos requieren acceso a todo el **grupo de recursos** que contiene su sitio web:  
    * Enlaces y certificados SSL \(porque los certificados SSL se pueden compartir entre sitios en el mismo grupo de recursos y la misma ubicación geográfica\)
    * Las reglas de alertas
    * Opciones de escala automática
    * Componentes de Application Insights
    * Pruebas web

## Cargas de trabajo de máquina virtual

Al igual que con las aplicaciones web, algunas funciones de la hoja de máquina virtual requieren acceso de escritura a la máquina virtual o a otros recursos del grupo de recursos.

Las máquinas virtuales tienen estos recursos relacionados: \* nombres de dominio \* redes virtuales \* cuentas de almacenamiento \* reglas de alerta

1. Estos elementos requieren acceso de **escritura** a la máquina virtual:  
    * Extremos
    * Direcciones IP
    * Discos
    * Extensiones
2. Estos requieren acceso de escritura a la máquina virtual y al **grupo de recursos** \(junto con el nombre de dominio\) donde se encuentran:  
    * El conjunto de disponibilidad
    * El conjunto de carga equilibrada
    * Las reglas de alertas
    
Si no puede acceder a ninguno de estos iconos, deberá pedirle al administrador el acceso de colaborador al grupo de recursos.
<!--HONumber=54-->