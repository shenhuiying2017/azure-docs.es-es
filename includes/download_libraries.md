#Descarga del SDK de Azure para Java

##Bibliotecas de cliente de Azure para Java: descarga manual

Las bibliotecas de Azure para Java se distribuyen bajo la [licencia de Apache, versión 2.0][license]. Haga clic [Aquí][zip-download] para obtener un archivo ZIP de las bibliotecas y todas las dependencias.  Microsoft Open Technologies, Inc. es quien lo ofrece. Consulte los archivos license.txt y ThirdPartyNotices.txt que se encuentran dentro del ZIP para obtener información sobre la licencia y sobre otras cuestiones.

##Bibliotecas de Azure para Java: Maven

Si su proyecto ya se ha configurado para usar Maven para la compilación, agregue la siguiente dependencia al archivo pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



En el elemento `<version>`, reemplace  *n.n.n* por un número de versión válido, que puede obtenerse del [Repositorio de bibliotecas de Azure en Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[licencia]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-descarga]:  http://go.microsoft.com/fwlink/?LinkId=253887

<!--HONumber=42-->
