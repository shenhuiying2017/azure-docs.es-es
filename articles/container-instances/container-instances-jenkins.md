---
title: Uso de Azure Container Instances como agente de compilación de Jenkins
description: Aprenda a usar Azure Container Instances como agente de compilación de Jenkins.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Uso de Azure Container Instances como agente de compilación de Jenkins

Azure Container Instances proporciona un entorno a petición, ampliable y aislado para ejecutar cargas de trabajo en contenedores. Gracias a estos atributos, Azure Container Instances es una plataforma excelente para ejecutar trabajos de compilación de Jenkins a gran escala. Este documento le guía con la implementación y el uso de un servidor Jenkins preconfigurado con ACI como destino de compilación.

Para más información sobre Azure Container Instances, consulte el artículo [acerca de Azure Container Instances][about-aci].

## <a name="deploy-jenkins-server"></a>Implementación de un servidor Jenkins

En Azure Portal, seleccione **Crear un recurso** y busque **Jenkins**. Seleccione la oferta de Jenkins con un editor de **Microsoft** y seleccione **Crear**.

Escriba la siguiente información en el formulario de conceptos básicos y haga clic en **Aceptar** cuando haya finalizado.

- **Nombre**: denominación de la implementación de Jenkins.
- **Nombre de usuario**: sirve de usuario administrador para la máquina virtual de Jenkins.
- **Tipo de autenticación**: se recomienda la clave pública de SSH. Si lo selecciona, copie la clave pública de SSH para usarla al iniciar sesión en la máquina virtual de Jenkins.
- **Suscripción**: seleccione una suscripción de Azure.
- **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente.
- **Ubicación**: seleccione una ubicación para el servidor Jenkins.

![Configuración básica de implementación del portal de Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

En el formulario de opciones de configuración adicionales, complete los siguientes elementos:

- **Tamaño**: seleccione la opción de tamaño correcta para la máquina virtual de Jenkins.
- **Tipo de disco de máquina virtual**: especifique la unidad HDD (unidad de disco duro) o SSD (unidad de estado sólido) para el servidor Jenkins.
- **Red virtual** (opcional): selecciónelo para modificar la configuración predeterminada.
- **Subredes**: selecciónelo, compruebe la información y seleccione **Aceptar**.
- **Dirección IP pública**: su selección permite asignarle un nombre personalizado, y configurar el SKU y el método de asignación.
- **Etiqueta de nombre de dominio**: especifique un valor para crear una dirección URL completa en la máquina virtual de Jenkins.
- **Jenkins release type** (Tipo de versión de Jenkins): seleccione el tipo de versión deseado entre las opciones: LTS, Weekly build (Compilación semanal) o Azure Verified (Verificado por Azure).

![Configuración adicional de implementación del portal de Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

Para la integración de la entidad de servicio, seleccione **Auto(MSI)** para que [Azure Managed Service Identity][managed-service-identity] cree automáticamente una identidad de autenticación para la instancia de Jenkins. Selección de Manual para proporcionar las credenciales de entidad de servicio propias.

Los agentes de la nube configuran una plataforma basada en la nube para los trabajos de compilación de Jenkins. Para este documento, seleccione ACI. Con el agente de la nube ACI, cada trabajo de compilación de Jenkins se ejecuta en una instancia de Azure Containter Instances.

![Configuración de la integración en la nube de la implementación del portal de Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

Una vez finalizada la configuración de la integración, haga clic en **Aceptar** y en **Aceptar** de nuevo en el resumen de validación. Haga clic en **Crear** en el resumen de los Términos de uso. El servidor Jenkins tarda unos minutos en implementarse.

## <a name="configure-jenkins"></a>Configuración de Jenkins

En Azure Portal, vaya al grupo de recursos de Jenkins, seleccione la máquina virtual de Jenkins y tome nota del nombre DNS.

![Instrucciones de inicio de sesión en Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Vaya al nombre DNS de la máquina virtual de Jenkins y copie la cadena de SSH devuelta.

![Instrucciones de inicio de sesión en Jenkins](./media/container-instances-jenkins/jenkins-portal-04.png)

Abra una sesión en el terminal con su sistema de desarrollo y pegue en la cadena de SSH del último paso. Actualice "username" con el nombre de usuario especificado al implementar el servidor Jenkins.

Una vez conectado, ejecute el siguiente comando para recuperar la contraseña de administrador inicial.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Deje la sesión de SSH con ejecución en túnel y vaya a http://localhost:8080 en un explorador. Pegue la contraseña de administrador inicial en el campo tal como se muestra en la siguiente imagen. Seleccione **Continuar** cuando haya finalizado.

![Desbloqueo de Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

Seleccione **Install suggested plugins** (Instalar complementos sugeridos) para instalar todos los complementos de Jenkins recomendados.

![Instalación del complemento de Jenkins](./media/container-instances-jenkins/jenkins-portal-06.png)

Cree una cuenta de usuario administrador. Esta cuenta se usa para iniciar sesión en la instancia de Jenkins y trabajar con ella.

![Creación del usuario de Jenkins](./media/container-instances-jenkins/jenkins-portal-07.png)

Seleccione **Save and Finish** (Guardar y finalizar) cuando haya finalizado y, **Start using Jenkins** (Empezar a usar Jenkins), para completar la configuración.

Jenkins ahora está configurado y listo para compilar e implementar código. En este ejemplo se usa una sencilla aplicación Java para mostrar una compilación de Jenkins en Azure Container Instances.

## <a name="create-build-job"></a>Creación de un trabajo de compilación

Al utilizar una imagen de contenedor como destino de compilación de Jenkins debe especificar una imagen que incluya todas las herramientas necesarias para la correcta compilación. Para especificar la imagen, seleccione **Manage Jenkins** (Administrar Jenkins)  > **Configure System** (Configurar sistema) y desplácese hacia abajo hasta la sección **Cloud** (Nube). En este ejemplo, actualice el valor de la imagen de Docker a `microsoft/java-on-azure-jenkins-slave`.

Cuando termine, haga clic en **Save** (Guardar) para volver al panel de Jenkins.

![Configuración de la nube de Jenkins](./media/container-instances-jenkins/jenkins-aci-image.png)

Ahora cree un trabajo de compilación de Jenkins. Seleccione **New Item** (Nuevo elemento), asigne un nombre al proyecto de compilación, como `aci-java-demo`, seleccione **Freestyle project** (Proyecto de estilo libre) y haga clic en **OK** (Aceptar).

![Creación de trabajos de Jenkins](./media/container-instances-jenkins/jenkins-new-job.png)

En **General** (General), asegúrese de que **Restrict where this project can be run** (Restringir el ámbito de ejecución del proyecto) está seleccionado. Escriba `linux` como expresión de la etiqueta. Esta configuración garantiza que este trabajo de compilación se ejecuta en la nube de ACI.

![Creación de trabajos de Jenkins](./media/container-instances-jenkins/jenkins-job-01.png)

En la administración de código fuente, seleccione `Git` y escriba `https://github.com/spring-projects/spring-petclinic.git` como dirección URL del repositorio. Este repositorio de GitHub contiene el código de la aplicación de ejemplo.

![Incorporación de código fuente al trabajo de Jenkins](./media/container-instances-jenkins/jenkins-job-02.png)

En la compilación, seleccione **Add build step** (Agregar paso de compilación) y seleccione `Invoke top-level Maven targets`. Escriba `package` como objetivo del paso de compilación.

![Incorporación del paso de compilación de Jenkins](./media/container-instances-jenkins/jenkins-job-03.png)

Seleccione **Guardar** cuando haya terminado.

## <a name="run-the-build-job"></a>Ejecución del trabajo de compilación

Para probar el trabajo de compilación y tener Azure Container Instances como plataforma de compilación, inicie una compilación manualmente.

Seleccione **Build Now** (Compilar ahora) para iniciar un trabajo de compilación. El trabajo tarda unos minutos en iniciarse; durante la ejecución, verá un estado similar al de las imágenes siguientes.

![Incorporación del paso de compilación de Jenkins](./media/container-instances-jenkins/jenkins-job-status.png)

Con el trabajo en ejecución, abra Azure Portal y observe el grupo de recursos de Jenkins. Debería ver que se ha creado una instancia Azure Container Instances. El trabajo Jenkins se ejecuta en esta instancia.

![Jenkins se basa en ACI](./media/container-instances-jenkins/jenkins-aci.png)

Como Jenkins ejecuta más trabajos que el número configurado de ejecutores de Jenkins (el valor predeterminado es 2), se crean varias instancias Azure Container Instances.

![Jenkins se basa en ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

Una vez completados todos los trabajos de compilación, las instancias de Azure Container Instances se eliminan.

![Jenkins se basa en ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Jenkins en Azure, consulte [Azure y Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md