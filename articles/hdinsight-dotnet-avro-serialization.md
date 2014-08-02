<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Avro.NET Serialization" pageTitle="Serialize Data with Avro.NET | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro.NET to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize Data with Avro.NET " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Serialización de datos con Avro.NET
===================================

Información general
-------------------

Este tema muestra cómo utilizar Microsoft Avro Library (Avro.NET) para serializar objetos y otras estructuras de datos en secuencias de bytes para conservarlos en la memoria, en una base de datos o en un archivo. Implementa el sistema de serialización de datos de Apache Avro a través de JSON a fin de definir un esquema independiente del lenguaje que proporciona un formato de intercambio de datos binarios compacto que se puede procesar a través de varios lenguajes (actualmente, C, C++, C\#, Java, PHP, Python y Ruby) cuando los objetos deben recuperarse a través de la deserialización.

El formato de serialización de Apache Avro se usa ampliamente en entornos de Hadoop, incluido Microsoft HDInsight. Puede encontrar información detallada sobre el formato en la especificación de Apache Avro.

La biblioteca Avro.NET de Microsoft HDInsight es compatible con dos maneras de serializar los objetos: a través del uso de reflexión y a través de registros genéricos.

Requisitos previos
------------------

La biblioteca de Microsoft .NET para Avro se distribuye como paquete de NuGet. Instale desde Visual Studio: Seleccione la pestaña **Proyecto** -\> **Administrar paquetes de NuGet...**, busque "Avro" en el cuadro de **búsqueda en línea** y, a continuación, haga clic en el botón **Instalar** junto a **Microsoft .NET Library for Avro**. La dependencia de Newtonsoft.Json (\>= .5.0.5) también se descarga automática con Microsoft Avro Library

### Descripción general

Escenarios:

-   [Serialización mediante el uso de reflexión](#Scenario1)

-   [Serialización mediante el uso de contenedores de objetos de Avro](#Scenario2)

-   [Serialización mediante el uso de archivos contenedores de objetos y serialización con reflexión](#Scenario3)

-   [Serialización mediante el uso de archivos contenedores de objetos y serialización con registro genérico](#Scenario4)

-   [Serialización mediante el uso de archivos contenedores de objetos con códec de compresión personalizado](#Scenario5)

Serialización mediante el uso de reflexión
------------------------------------------

En el ejemplo que aparece a continuación, se serializan una clase y un struct, luego se deserializan y, finalmente, se comparan con las instancias iniciales para asegurar la identidad. Microsoft Avro Library crea automáticamente el esquema JSON para los tipos, considerando los atributos del contrato de datos. Microsoft Avro Library utiliza el [serializador de contratos de datos](http://msdn.microsoft.com/en-us/library/ms731072(v=vs.110).aspx) para identificar los campos que se están serializando.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializar y deserializar un conjunto de datos de ejemplo representado como un objeto mediante reflexión
    //No se requiere definición explícita de esquema; el esquema de objetos serializados se crea automáticamente
    public void SerializeDeserializeObjectUsingReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una nueva instancia de AvroSerializer y especificar un AvroDataContractResolver de estrategia de especialización personalizada
    //para serializar solo propiedades atribuidas con DataContract/DateMember
    var avroSerializer = AvroSerializer.Create<SensorData>();

    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    //Crear un conjunto de datos con clase y struct de ejemplo 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

    //Serializar los datos a la secuencia especificada
    avroSerializer.Serialize(buffer, expected);

      
    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Deserializar datos desde la secuencia y convertirlos al mismo tipo utilizado para serialización
    var actual = avroSerializer.Deserialize(buffer);

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    bool isEqual = this.Equal(expected, actual);

    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);

    }
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }


      
    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización a memoria mediante el uso de reflexión
    Sample.SerializeDeserializeObjectUsingReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida: 
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN
    //
    // Serializando conjunto de datos de ejemplo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // El resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.

Serialización mediante el uso de contenedores de objetos de Avro
----------------------------------------------------------------

No siempre es posible tener las clases .NET que representan los datos. El esquema de datos puede ser dinámico como, por ejemplo, cuando los datos son representados como archivos de valores separados por coma (CSV) con un esquema desconocido de antemano y requieren convertirse al formato de Avro. En tales casos, es necesario crear una instancia de la clase especial (AvroRecord) con un esquema JSON explícitamente especificado, completar los campos con los datos requeridos y serializarlos. Este ejemplo muestra cómo definir un esquema, crear el registro correspondiente, completarlo con los datos y, a continuación, serializar y deserializar. Finalmente, se compara la identidad de los objetos iniciales y los deserializados.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializar y deserializar el conjunto de datos de ejemplo mediante el uso de registro genérico.
    //Registro genérico es una clase especial con el esquema definido explícitamente en JSON.
    //Todos los datos serializados se deben asignar a los campos de Registro genérico,
    //los que, a su vez, serán serializados.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un búfer de secuencia de memoria
    using (var stream = new MemoryStream())
    {
    //Crear un registro genérico para representar los datos
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;

    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };

    Console.WriteLine("Serializing Sample Data Set...");

    //Serializar los datos
    serializer.Serialize(stream, expected);

    stream.Seek(0, SeekOrigin.Begin);

    Console.WriteLine("Deserializing Sample Data Set...");

    //Deserializar los datos a un registro genérico
    dynamic actual = serializer.Deserialize(stream);

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización a memoria mediante el uso de Registro genérico
    Sample.SerializeDeserializeObjectUsingGenericRecords();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida: 
    // SERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // El resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.

Serialización mediante el uso de archivos contenedores de objetos y serialización con reflexión
-----------------------------------------------------------------------------------------------

Este ejemplo es similar al ejemplo 1: una clase y un struct se serializan y almacenan en un archivo contenedor de objetos ubicado en el directorio actual de la aplicación de ejemplo. A continuación, se lee este archivo, los datos se deserializan y, finalmente, se comparan con las instancias iniciales para asegurar la identidad. Los datos en el archivo contenedor de objetos se comprimen con el códec de compresión Deflate (¿vínculo?).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Los datos se comprimirán con el códec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.

Serialización mediante el uso de archivos contenedores de objetos y serialización con registro genérico
-------------------------------------------------------------------------------------------------------

Este ejemplo es similar al ejemplo 2: un conjunto de datos de muestra se serializa con AvroRecord con un esquema JSON definido explícitamente. Los datos serializados se almacenan en un archivo contenedor de objetos nuevo ubicado en el directorio actual de la aplicación de ejemplo. A continuación, se lee este archivo, los datos se deserializan y, finalmente, se comparan con las instancias iniciales para asegurar la identidad.

Los datos del archivo contenedor de objetos no se comprimen (se usa el códec de compresión Null).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Esta clase contiene todos los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de Registro genérico y archivos contenedores de objetos de Avro
    //Los datos serializados no se comprimen
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Definir el esquema en JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";

    //Crear un serializador genérico basado en el esquema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Crear un registro genérico para representar los datos
    var testData = new List<AvroRecord>();

    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);

    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);

    //Serializando y guardando datos en archivo
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //No se comprimirán los datos (códec de compresión Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Guardar secuencia en archivo
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Leyendo y deserializando los datos
    //Crear un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Finalmente, comprobar los resultados
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }

    //
    //Métodos auxiliares
    //

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File does not exist", path);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de Registro genérico al archivo contenedor de objetos de Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // DESERIALIZACIÓN MEDIANTE EL USO DE REGISTRO GENÉRICO Y ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO
    //
    // Definiendo el esquema y creando el conjunto de datos de ejemplo...
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.

Serialización mediante el uso de archivos contenedores de objetos con códec de compresión personalizado
-------------------------------------------------------------------------------------------------------

La [especificación de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) (en inglés) permite el uso de los códecs opcionales de compresión (además de Null y Deflate). El ejemplo que aparece a continuación muestra cómo usar un códec de compresión personalizado para los archivos contenedores de objetos. No implementa un códec realmente distinto (como Snappy, mencionado como códec opcional compatible en la especificación de Avro), sino que utiliza la implementación de [Deflate](http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.110).aspx) .NET Framework 4.5 (que proporciona un mejor algoritmo de compresión basado en zlib), que muchos desarrolladores podrían encontrar útil.

    // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}// Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}// Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}// Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}// Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file // Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;{0}// Este código se debe compilar con el parámetro de marco de destino definido como ".NET Framework 4.5"
    // para asegurar que se utiliza la implementación deseada del algoritmo de compresión Deflate
    // Asegúrese de que su proyecto C# esté configurado como corresponde
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    #region Definiendo objetos para serialización
    //Clase de ejemplo usada en ejemplos de serialización
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //Struct de ejemplo usado en ejemplos de serialización
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }
    #endregion

    #region Definiendo códec personalizado basado en .NET Framework V.4.5 Deflate
    //La clase de códec Avro.NET contiene dos métodos 
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //que son las claves para la compresión de datos.
    //Para habilitar un códec personalizado, es necesario implementar estos métodos para el códec requerido

    #region Definición de secuencias de compresión y descompresión
    //DeflateStream (clase del espacio de nombres System.IO.Compression que implementa el algoritmo Deflate)
    //no se puede utilizar directamente para Avro, porque no es compatible con operaciones esenciales, como Búsqueda.
    //Por esta razón, es necesario implementar dos clases heredadas de secuencia
    //(una para secuencia comprimida y otra para secuencia descomprimida)
    //que utilizan compresión Deflate e implementar todas las características requeridas 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;

    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }

    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }

    public override void Flush()
    {
    this.compressionStream.Close();
    }

    public override long Length
    {
    get { return this.buffer.Length; }
    }

    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }

    set
    {
    this.buffer.Position = value;
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }

    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);

    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }

    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;

    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }

    public override bool CanRead
    {
    get { return true; }
    }

    public override bool CanSeek
    {
    get { return true; }
    }

    public override bool CanWrite
    {
    get { return false; }
    }

    public override void Flush()
    {
    this.decompressed.Close();
    }

    public override long Length
    {
    get { return this.decompressed.Length; }
    }

    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }

    set
    {
    throw new NotSupportedException();
    }
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }

    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }

    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);

    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion

    #region Definición de códec
    //Definir la clase de códec real que contiene los métodos requeridos para manipular secuencias:
    //GetCompressedStreamOver(secuencia no comprimida) y GetDecompressedStreamOver(secuencia comprimida)
    //La clase de códec utiliza clases para las secuencias comprimidas y descomprimidas descritas
    internal sealed class DeflateCodec45 : Codec
    {

    //Solamente utilizamos una IMPLEMENTACIÓN distinta de Deflate, por lo que el nombre del códec sigue siendo "Deflate"
    public static readonly string CodecName = "deflate";

    public DeflateCodec45()
    : base(CodecName)
    {
    }

    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }

    return new CompressionStreamDeflate45(decompressed);
    }

    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }

    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion

    #region Definición de Codec Factory
    //Definir Codec Factory modificada para usar en Reader
    //Capturará el intento de usar "deflate" y proporcionará códec personalizado 
    //Para todos los demás casos, se basará en la clase base (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {

    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion

    #endregion

    #region Clase de ejemplo con métodos de demostración
    //Esta clase contiene los métodos que demuestran
    //el uso de Microsoft Avro Library
    public class AvroSample
    {

    //Serializa y deserializa el conjunto de datos de ejemplo mediante el uso de reflexión y archivos contenedores de objetos de Avro
    //Los datos serializados se comprimen con el códec de compresión personalizado (Deflate de .NET Framework 4.5)
    //
    //Este ejemplo utiliza secuencia de memoria para todas las operaciones relacionadas con la serialización, deserialización y
    //manipulación de contenedor de objetos, a pesar de que podría utilizarse fácilmente Secuencia de archivo.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Ruta de acceso para el archivo contenedor de objetos de Avro
    string path = "AvroSampleReflectionDeflate45.avro";

    //Crear un conjunto de datos con clase y struct de ejemplo
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Serializando y guardando datos en archivo
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //Crear una instancia SequentialWriter para el tipo SensorData que puede serializar una secuencia de objetos SensorData a secuencia
    //Aquí se introduce el códec personalizado. Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado.
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serializar los datos a secuencia mediante el redactor secuencial
    testData.ForEach(writer.Write);
    }
    }

    //Guardar secuencia en archivo
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Lectura y deserialización de datos
    //Creación de un búfer de secuencia de memoria
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Leyendo datos desde el archivo contenedor de objetos
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Preparar la secuencia para deserializar los datos
    buffer.Seek(0, SeekOrigin.Begin);

    //Debido a la firma del constructor de SequentialReader<T>, se requiere una instancia AvroSerializerSettings
    //cuando Codec Factory se especifica explícitamente
    //Puede comentar la línea que aparece a continuación si desea utilizar el Deflate integrado (ver siguiente comentario)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //Crear un SequentialReader para tipo SensorData que deserializará todos los objetos serializados desde la secuencia determinada
    //Permite iteración sobre los objetos deserializados porque implementa la interfaz IEnumerable<T>
    //Aquí se introduce Codec Factory personalizado.
    //Para su conveniencia, la siguiente línea de código comentada muestra cómo usar Deflate integrado
    //(en este caso, no se requiere un parámetro explícito de Codec Factory).
    //Observe que, debido a que el ejemplo trata con distintas IMPLEMENTACIONES de Deflate, los códecs integrados y personalizados son intercambiables
    //en las operación de lectura y escritura
    //mediante el uso de (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Finalmente, comprobar que los datos deserializados coinciden con los originales
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }

    //Eliminar el archivo
    RemoveFile(path);
    }
    #endregion

    #region Métodos auxiliares

    //Comparación de dos objetos SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Guardando secuencia de memoria en un archivo nuevo con la ruta de acceso determinada
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;

    }
    }

    //Leyendo un contenido de archivo con la ruta de acceso determinada a una secuencia de memoria
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }

    //Eliminando el archivo con la ruta de acceso determinada
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
    quot;. File does not exist", path);
    }
    }
    #endregion

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Crear una instancia de clase AvroSample e invocar métodos
    //que ilustran distintos enfoques de serialización
    AvroSample Sample = new AvroSample();

    //Serialización mediante el uso de reflexión al archivo contenedor de objetos de Avro mediante el uso de códec personalizado
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // El ejemplo debería mostrar la siguiente salida:
    // SERIALIZACIÓN MEDIANTE EL USO DE REFLEXIÓN, ARCHIVOS CONTENEDORES DE OBJETOS DE AVRO Y CÓDEC PERSONALIZADO
    //
    // Serializando conjunto de datos de ejemplo...
    // Guardando datos serializados en archivo...
    // Leyendo datos desde archivo...
    // Deserializando conjunto de datos de ejemplo...
    // Comparando los conjuntos de datos inicial y deserializado...
    // Para el par 1, el resultado de la comparación de identidades de los conjuntos de datos es True
    // Para el par 2, el resultado de la comparación de identidades de los conjuntos de datos es True
    // ----------------------------------------
    // Presione cualquier tecla para salir.
