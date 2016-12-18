---
title: Conceptos de DevTest Labs | Microsoft Docs
description: "Aprenda los conceptos básicos de DevTest Labs y cómo puede facilitar la creación, la administración y la supervisión de máquinas virtuales de Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e1b087b9e952d9045e8580d4074e7655d165a08


---
# <a name="devtest-labs-concepts"></a>Conceptos de DevTest Labs
> [!NOTE]
> Este artículo es la tercera parte de una serie de tres:
> 
> 1. [¿Qué es DevTest Labs?](devtest-lab-overview.md)
> 2. [¿Por qué usar DevTest Labs?](devtest-lab-why.md)
> 3. **[Conceptos de DevTest Labs](devtest-lab-concepts.md)**
> 
> 

## <a name="overview"></a>Información general
La lista siguiente contiene las definiciones y los conceptos clave de DevTest Labs:

## <a name="artifacts"></a>Artefactos
Los artefactos se utilizan para implementar y configurar la aplicación después de aprovisionar una máquina virtual. Los artefactos pueden ser:

* Herramientas que desea instalar en la máquina virtual, como agentes, Fiddler y Visual Studio.
* Acciones que desea ejecutar en la máquina virtual, como la clonación de un repositorio.
* Aplicaciones que desea probar.

Los artefactos son archivos JSON basados de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) que contienen instrucciones para realizar la implementación y aplicar la configuración. 

## <a name="artifact-repositories"></a>Repositorios de artefacto
Los repositorios de artefactos son repositorios de Git donde se insertan los artefactos. Es posible agregar los mismos repositorios de artefactos a varios laboratorios de su organización a fin de reutilizarlos y compartirlos.

## <a name="base-images"></a>Imágenes base
Las imágenes base son imágenes de máquina virtual con todas las herramientas y los valores de configuración preinstalados y configurados para crear rápidamente una máquina virtual. Puede aprovisionar una máquina virtual seleccionando una base existente y agregando un artefacto para instalar su agente de prueba. Luego puede guardar la máquina virtual aprovisionada como base para que esta pueda utilizarse sin que sea necesario volver a instalar al agente de prueba para cada aprovisionamiento de la máquina virtual.

## <a name="formulas"></a>Fórmulas
Las fórmulas, además de las imágenes base, proporcionan un mecanismo para el aprovisionamiento rápido de la máquina virtual. Una fórmula en DevTest Labs es una lista de valores de propiedad predeterminados que se usan para crear una máquina virtual de laboratorio. Mediante las fórmulas, las máquinas virtuales que tengan el mismo conjunto de propiedades, como la imagen base, el tamaño de máquina virtual, la red virtual y los artefactos, se pueden crear sin necesidad de especificar esas propiedades todas las veces. Al crear una máquina virtual a partir de una fórmula, los valores predeterminados pueden usarse tal como están o modificarlos.

## <a name="caps"></a>Límites
Los límites son un mecanismo que sirve para minimizar la pérdida en el laboratorio. Por ejemplo, puede establecer un límite para restringir el número de máquinas virtuales que se pueden crear por usuario o en un laboratorio.

## <a name="policies"></a>Directivas
Las directivas ayudan a controlar los costos en su laboratorio. Por ejemplo, puede crear una directiva para apagar automáticamente las máquinas virtuales según una programación definida.

## <a name="security-levels"></a>Niveles de seguridad
La seguridad del acceso viene determinada por el Control de acceso basado en roles (RBAC) de Azure. Para entender cómo funciona el acceso, es importante comprender las diferencias entre un permiso, un rol y un ámbito según se define en RBAC. 

* Permiso: un permiso es un acceso definido para una acción específica (por ejemplo, acceso de lectura a todas las máquinas virtuales). 
* Rol: un rol es un conjunto de permisos que se pueden agrupar y asignar a un usuario. Por ejemplo, el rol de *propietario de la suscripción* tiene acceso a todos los recursos dentro de una suscripción. 
* Ámbito: un ámbito es un nivel dentro de la jerarquía de recursos de Azure (por ejemplo, un grupo de recursos, un único laboratorio o toda la suscripción).

Dentro del ámbito de DevTest Labs, hay dos tipos de roles para definir los permisos de usuario: usuario de laboratorio y propietario de laboratorio.

* Propietario de laboratorio: el propietario de un laboratorio tiene acceso a todos los recursos del laboratorio. Por lo tanto, el propietario de un laboratorio puede modificar las directivas, leer y escribir en todas las máquinas virtuales, cambiar la red virtual, etc. 
* Usuario de laboratorio: un usuario de laboratorio puede ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales, pero no puede modificar las directivas ni las máquinas virtuales creadas por otros usuarios. 

Para ver cómo crear roles personalizados en DevTest Labs, consulte el artículo [Concesión de permisos de usuario a directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Puesto que los ámbitos son jerárquicos, cuando un usuario tiene permisos en un ámbito determinado, también se le conceden automáticamente en cada ámbito de nivel inferior que engloba. Por ejemplo, si un usuario está asignado al rol de propietario de la suscripción, tiene acceso a todos los recursos de una suscripción, lo que incluye todas las máquinas virtuales, todas las redes virtuales y todos los laboratorios. Por lo tanto, el propietario de una suscripción hereda automáticamente el rol de propietario de laboratorio. Sin embargo, lo contrario no es cierto. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior al del nivel de suscripción. Por lo tanto, el propietario de un laboratorio no podrá ver las máquinas virtuales, las redes virtuales o los recursos que se encuentren fuera del laboratorio.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes
[Creación de un laboratorio en DevTest Labs](devtest-lab-create-lab.md)




<!--HONumber=Nov16_HO3-->


