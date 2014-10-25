# Descarga del SDK de Azure para Java

## Bibliotecas de clientes de Azure para Java: Descarga manual

Las bibliotecas de Azure para Java se distribuyen bajo la licencia de [Apache License, Version 2.0][]. Haga clic [aquí][] para obtener un archivo ZIP de las bibliotecas y todas sus dependencias. Microsoft Open Technologies, Inc. es quien lo ofrece. Consulte los archivos license.txt y ThirdPartyNotices.txt que se encuentran dentro del ZIP para obtener información sobre la licencia y sobre otras cuestiones.

## Bibliotecas de Azure para Java: Maven

Si su proyecto ya se ha configurado para usar Maven para la compilación, agregue la siguiente dependencia al archivo pom.xml.

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

En el elemento `<version>`, reemplace *n.n.n* por un número de versión válido, que puede obtenerse del [repositorio de bibliotecas de Azure en Maven (en inglés)][].

  [Apache License, Version 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [aquí]: http://go.microsoft.com/fwlink/?LinkId=253887
  [repositorio de bibliotecas de Azure en Maven (en inglés)]: http://go.microsoft.com/fwlink/?LinkID=286274
