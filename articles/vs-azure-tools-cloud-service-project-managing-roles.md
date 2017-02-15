---
title: "Administración de roles en los proyectos de Azure Cloud Services con Visual Studio | Microsoft Docs"
description: "Obtenga información sobre cómo agregar nuevos roles al proyecto de servicio en la nube de Azure o quitarle roles existentes con Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8c805f9af3154e46f25a6d24c7df33f390189340


---
# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Administración de roles en los proyectos de servicios en la nube de Azure con Visual Studio
Una vez creado el proyecto de servicio en la nube de Azure, puede agregarle nuevos roles o quitarle roles existentes. También puede importar un proyecto existente y convertirlo en un rol. Por ejemplo, puede importar una aplicación web ASP.NET y designarla como rol web.

## <a name="adding-or-removing-roles"></a>Adición o eliminación de roles
**Para agregar un rol**

En el **Explorador de soluciones**, abra el menú contextual del nodo **Roles** del proyecto de servicio en la nube y elija **Agregar**. Puede seleccionar un rol web o un rol de trabajo existente en la solución actual o bien crear un nuevo proyecto de rol web o rol de trabajo. También puede seleccionar un proyecto adecuado, por ejemplo, un proyecto de aplicación web ASP.NET, y asociarlo a un proyecto de rol.

**Para quitar una asociación de rol**

En el nodo **Roles** del proyecto de servicio en la nube del Explorador de soluciones, abra el menú contextual del rol que quiere quitar y elija **Agregar**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Eliminación y adición de roles en el servicio en la nube
Si quita un rol del proyecto de servicio en la nube pero posteriormente decide volver a agregarlo al proyecto, solo se agregarán la declaración del rol y los atributos básicos como, por ejemplo, los extremos y la información de diagnóstico. No se agregan referencias o recursos adicionales al archivo ServiceDefinition.csdef ni al archivo ServiceConfiguration.cscfg. Si quiere agregar esta información, tendrá que volver a agregarla manualmente en estos archivos.

Por ejemplo, es posible que quite un rol de servicio web y más adelante decida agregarlo de nuevo a la solución. Si lo hace, se producirá un error. Para evitar este error, tiene que agregar de nuevo el elemento `<LocalResources>` que se muestra en el siguiente código XML al archivo ServiceDefinition.csdef. Use el nombre del rol de servicio web que volvió a agregar al proyecto como parte del nombre del atributo para el elemento **<LocalStorage>** . En este ejemplo el nombre del rol de servicio web es **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Pasos siguientes
Vea [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)para saber cómo configurar los roles en Visual Studio.




<!--HONumber=Nov16_HO3-->


