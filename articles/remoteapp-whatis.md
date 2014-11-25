<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo" />

# ¿Qué es Azure RemoteApp?

RemoteApp permite crear programas a los que se accede en modo remoto a través de Azure y que parecen estar ejecutándose en el equipo local del usuario final. A estos programas se les denomina programas RemoteApp. En lugar de mostrarse al usuario en el escritorio del servidor host de sesión de Escritorio remoto (RDSH), el programa RemoteApp se integra en el escritorio del cliente. Este programa se ejecuta en su propia ventana redimensionable, se puede arrastrar entre varios monitores y tiene su propia entrada en la barra de tareas. Si un usuario ejecuta más de un programa RemoteApp en el mismo servidor host de sesión de Escritorio remoto, el programa RemoteApp comparte la misma sesión que Servicios de Escritorio remoto.

RemoteApp puede reducir la complejidad y la sobrecarga administrativa en numerosas situaciones; por ejemplo, las siguientes:

-   Sucursales, donde el soporte técnico de TI local puede ser limitado, así como el ancho de banda.
-   Situaciones donde los usuarios necesitan acceder a programas en modo remoto.
-   Implementación de programas de línea de negocio (LOB), especialmente los programas LOB personalizados.
-   Entornos donde los usuarios no tienen equipos asignados y varios usuarios usan el mismo equipo de forma rotatoria, como "hot desk" o "hotelling".
-   Implementación de varias versiones de un programa, especialmente si la instalación de esas versiones en modo local causaría conflictos.

A diferencia de los Servicios de Escritorio remoto tradicionales, Azure RemoteApp se ejecuta en el Portal de administración de Azure. Los usuarios acceden a los programas a través del portal, mientras que toda la administración de programas y usuarios se realiza a través del Portal de administración.

Hay dos tipos de implementación de RemoteApp:

-   Una implementación en la nube se hospeda y almacena todos los datos de programas en la nube de Azure.
-   Una implementación híbrida se hospeda en la nube de Azure pero permite a los usuarios acceder a datos almacenados en la red local.

Independientemente del tipo de implementación que elija, después de crear el servicio, debe publicar los programas que desea compartir con los usuarios en la fuente para usuarios finales, que es la lista de programas disponibles a los que los usuarios obtienen acceso desde el portal de Azure. Tenga en cuenta que la fuente muestra todos los programas de todos los servicios asociados con la suscripción.

