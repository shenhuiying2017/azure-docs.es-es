<properties urlDisplayName="" pageTitle="Role based access control troubleshooting" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Role based access control troubleshooting" authors="Stephen Siciliano"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Stephen="" Siciliano" />

# Solución de problemas del control de acceso basado en roles

## Introducción

El [control de acceso basado en roles](http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-configure/) es una característica muy útil que le permite delegar accesos específicos a recursos en Azure. Esto significa que puede tener la seguridad de que concede a una determinada persona el acceso exclusivamente a lo que necesita. No obstante, algunas veces el modelo de recursos para los recursos de Azure puede ser complicado y puede costar entender a qué se están concediendo permisos exactamente.

En este documento explicamos qué puede esperar al usar algunos de los roles del portal de vista previa. En esta versión se incluyen tres roles predefinidos:

-   Propietario
-   Colaborador
-   Lector

Los propietarios y los colaboradores tienen acceso total a la experiencia de administración, pero un colaborador no puede dar acceso a otros usuarios o grupos. Nos centraremos en el rol del lector, ya que tiene más cosas que comentar. [Vea este artículo](http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-configure/) si quiere detalles sobre cómo conceder acceso exactamente.

## Cargas de trabajo de sitio web

### Acceso de solo lectura

Cuando le conceda a un usuario acceso de lectura a un sitio web, o si es usted el que solo tiene este tipo de acceso, es posible que algunas características estén deshabilitadas sin esperarlo. Las siguientes capacidades de administración requieren acceso de **escritura** al sitio web (bien como colaborador, bien como propietario) y no están disponibles en un escenario de solo lectura.

1.  Comandos (p. ej., iniciar, parar, etc.)
2.  Cambiar opciones, como la configuración general, opciones de escala, opciones de copia de seguridad y opciones de supervisión
3.  Obtener acceso a las credenciales de publicación y otros secretos, como opciones de aplicaciones y cadenas de conexión
4.  Registros de streaming
5.  Configuración de registros de diagnóstico
6.  Consola de sitios web (símbolo del sistema)
7.  Implementaciones activas y recientes (para implementaciones git continuas locales)
8.  Gasto estimado
9.  Pruebas web
10. Red virtual (solo visible para un lector si un usuario con acceso de escritura ha configurado previamente una red virtual)

Si no puede obtener acceso a ninguno de estos elementos, necesitará acceso de colaborador en el sitio web.

### Tratar con recursos relacionados

Los sitios web pueden resultar complicados si entran en juego distintos recursos. Este es un grupo de recursos típico con un par de sitios web:

![Grupo de recursos de sitio web](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Como consecuencia, si le concede a alguien acceso solo al sitio web, muchas de las funciones de la hoja del sitio web estarán totalmente deshabilitadas.

1.  Estos elementos requieren acceso al **plan de hospedaje web** que corresponde a su sitio web:  
    * Ver el nivel de precios del sitio web (p. ej., gratis, compartido, b&aacute;sico o est&aacute;ndar)
    * Configuraci&oacute;n de escala (es decir, n&uacute;mero de instancias, tama&ntilde;o de la m&aacute;quina virtual, opciones de escala autom&aacute;tica).
    * Cuotas (p. ej., almacenamiento, ancho de banda, CPU).
2.  Estos elementos requieren acceso a todo el **grupo de recursos** que contiene su sitio web:
    * Enlaces y certificados SSL (porque los certificados SSL se pueden compartir entre sitios en el mismo grupo de recursos y la misma ubicaci&oacute;n geogr&aacute;fica)
    * Reglas de alertas
    * Opciones de escala autom&aacute;tica
    * Componentes de Application Insights
    * Pruebas web

## Cargas de trabajo de máquina virtual

Al igual que con los sitios web, algunas funciones de la hoja de máquina virtual requieren acceso de escritura a la máquina virtual o a otros recursos del grupo de recursos.

Las máquinas virtuales tienen estos recursos relacionados:

-   Nombres de dominio
-   Redes virtuales
-   Cuentas de almacenamiento
-   Reglas de alertas

1.  Estos elementos requieren acceso de **escritura** a la máquina virtual:
    * Extremos
    * Direcciones IP
    * Discos
    * Extensiones
2.  Estos requieren acceso de escritura en la máquina virtual y en el **grupo de recursos** (junto con el nombre de dominio) que hay en:
    * El conjunto de disponibilidad
    * El conjunto de carga equilibrada
    * Las reglas de alertas

Si no puede acceder a ninguno de estos elementos, deberá pedirle al administrador el acceso de colaborador al grupo de recursos.

