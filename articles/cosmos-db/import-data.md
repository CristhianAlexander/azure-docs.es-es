---
title: "Herramienta de migración de base de datos para Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo usar las herramientas de migración de datos de código abierto de Azure Cosmos DB para importar datos a Azure Cosmos DB desde varios orígenes, incluidos archivos MongoDB, SQL Server, Table Storage, Amazon DynamoDB, CSV y JSON. Conversión de CSV a JSON."
keywords: "csv a json, herramientas de migración de base de datos, convertir csv a json"
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 23a4a82dbdb611f4da90562af936fca28da9b24d
ms.contentlocale: es-es
ms.lasthandoff: 06/13/2017

---
# <a name="how-to-import-data-into-azure-cosmos-db-for-the-documentdb-api"></a>Importación de datos en Azure Cosmos DB para la API de DocumentDB

En este tutorial se muestra cómo utilizar la herramienta DocumentDB API Data Migration de Azure Cosmos DB, que puede importar datos desde diversos orígenes, incluidos archivos JSON, archivos CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB y colecciones de la API de DocumentDB de Azure Cosmos DB en colecciones para utilizarlos con Azure Cosmos DB y la API de DocumentDB. También se puede utilizar la herramienta de migración de datos al migrar de una colección de una recolección de una sola partición a una colección de varias particiones de la API de DocumentDB.

La herramienta de migración de datos solo funciona al importar datos a Azure Cosmos DB para usar con la API de DocumentDB. En este momento, no se admite la importación de datos para su uso con la API de Table o la API Graph. 

Para importar datos para su uso con la API de MongoDB, vea [Azure Cosmos DB: migración de datos para la API de MongoDB](mongodb-migrate.md).

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Instalación de la herramienta de migración de datos
> * Importación de datos de orígenes de datos diferentes
> * Exportación desde Azure Cosmos DB a JSON

## <a id="Prerequisites"></a>Requisitos previos
Antes de seguir las instrucciones del presente artículo, asegúrese de tener instalados los siguientes elementos:

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) o superior.

## <a id="Overviewl"></a>Información general sobre la herramienta de migración de datos
La herramienta de migración de datos es una solución de código abierto que importa los datos a Azure Cosmos DB desde una variedad de orígenes, como:

* Archivos JSON
* MongoDB
* SQL Server
* Archivos CSV
* Almacenamiento de tablas de Azure
* Amazon DynamoDB
* HBase
* Colecciones de Azure Cosmos DB

Aunque la herramienta de importación incluye una interfaz gráfica de usuario (dtui.exe), también se pueden controlar desde la línea de comandos (dt.exe). De hecho, hay una opción para mostrar el comando asociado después de configurar una importación a través de la interfaz de usuario. Se pueden transformar datos tabulares de origen (por ejemplo, archivos de SQL Server o CSV) de tal forma que se pueden crear relaciones jerárquicas (subdocumentos) durante la importación. Siga leyendo para obtener más información acerca de las opciones de origen, las líneas de comandos de muestra para importar desde cada origen, las opciones de destino y la visualización de los resultados de importación.

## <a id="Install"></a>Instalación de la herramienta de migración de datos
El código fuente de la herramienta de migración está disponible en GitHub en [este repositorio](https://github.com/azure/azure-documentdb-datamigrationtool) y una versión compilada se encuentra disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Puede compilar la solución o simplemente descargar y extraer la versión compilada en un directorio de su elección. A continuación, ejecute:

* **Dtui.exe**: versión de interfaz gráfica de la herramienta
* **Dt.exe**: versión de línea de comandos de la herramienta

## <a name="import-data"></a>Importar datos

Una vez que haya instalado la herramienta, es el momento de importar los datos. ¿Qué tipo de datos desea importar?

* [Archivos JSON](#JSON)
* [MongoDB](#MongoDB)
* [Archivos de exportación de MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Archivos CSV](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Colecciones de Azure Cosmos DB](#DocumentDBSource)
* [HBase](#HBaseSource)
* [Importación masiva de Azure Cosmos DB](#DocumentDBBulkImport)
* [Importación de registros secuenciales de Azure Cosmos DB](#DocumentDSeqTarget)


## <a id="JSON"></a>Para importar archivos JSON
La opción del importador de origen de archivos JSON permite importar uno o varios archivos JSON de un único documento o archivos JSON que contengan cada uno una matriz de documentos JSON. Cuando se agregan carpetas que contienen archivos JSON que se van a importar, existe la opción de buscar archivos en subcarpetas de forma recursiva.

![Captura de pantalla de opciones de origen de archivos JSON: herramientas de migración de base de datos](./media/import-data/jsonsource.png)

Estos son algunos ejemplos de línea de comandos para importar archivos JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Para importar desde MongoDB

> [!IMPORTANT]
> Si va a importar a una cuenta de Azure Cosmos DB compatible con MongoDB, siga estas [instrucciones](mongodb-migrate.md).
> 
> 

La opción del importador de origen de MongoDB permite importar desde una colección de MongoDB individual y, opcionalmente, filtrar documentos mediante una consulta o modificar la estructura del documento con una proyección.  

![Captura de pantalla de las opciones de origen de MongoDB](./media/import-data/mongodbsource.png)

La cadena de conexión tiene el formato estándar de MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de MongoDB especificada en el campo de la cadena de conexión.
> 
> 

Escriba el nombre de la colección desde la que se importarán los datos. Opcionalmente, puede especificar o facilitar un archivo para una consulta (por ejemplo, {pop: {$gt:5000}}) o proyección (por ejemplo, {loc:0}) para filtrar los datos que se van a importar y darles forma.

Estos son algunos ejemplos de línea de comandos para importar desde MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Para importar archivos de exportación de MongoDB

> [!IMPORTANT]
> Si va a importar a una cuenta de Azure Cosmos DB compatible con MongoDB, siga estas [instrucciones](mongodb-migrate.md).
> 
> 

La opción del importador de origen de archivos JSON de exportación de MongoDB permite importar uno o más archivos JSON generados a partir de la utilidad mongoexport.  

![Captura de pantalla de las opciones de origen de exportación de MongoDB](./media/import-data/mongodbexportsource.png)

Cuando se agregan carpetas que contienen archivos JSON de exportación de MongoDB para importarlos, tiene la opción de buscar archivos en subcarpetas de forma recursiva:

A continuación se muestra un ejemplo de línea de comandos para importar desde archivos JSON de exportación de MongoDB:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Para importar desde SQL Server
La opción del importador de origen SQL permite importar desde una base de datos SQL Server individual y, opcionalmente, filtrar los registros que se pueden importar utilizando una consulta. Además, puede modificar la estructura del documento especificando un separador de anidamiento (más en un momento determinado).  

![Captura de pantalla de opciones de origen de SQL: herramientas de migración de base de datos](./media/import-data/sqlexportsource.png)

El formato de la cadena de conexión es el formato de la cadena de conexión SQL estándar.

> [!NOTE]
> Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de SQL Server especificada en el campo de la cadena de conexión.
> 
> 

La propiedad de separador de anidamiento se utiliza para crear relaciones jerárquicas (documentos secundarios) durante la importación. Considere la siguiente consulta SQL:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as Address.AddressType, AddressLine1 as Address.AddressLine1, City as Address.Location.City, StateProvinceName as Address.Location.StateProvinceName, PostalCode as Address.PostalCode, CountryRegionName as Address.CountryRegionName from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Que devuelve los resultados siguientes (parciales):

![Screenshot of SQL query results](./media/import-data/sqlqueryresults.png)

Tenga en cuenta los alias como Address.AddressType y Address.Location.StateProvinceName. Al especificar un separador de anidamiento de “.”, la herramienta de importación crea subdocumentos Address y Address.Location durante la importación. Este es un ejemplo de un documento resultante en Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Estos son algunos ejemplos de línea de comandos para importar desde SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Para importar archivos CSV y convertirlos a JSON
La opción del importador de origen de archivos CSV le permite importar uno o varios archivos CSV. Cuando se agregan carpetas que contienen archivos CSV que se van a importar, existe la opción de buscar archivos en subcarpetas de forma recursiva.

![Captura de pantalla de opciones de origen de CSV](media/import-data/csvsource.png)

De forma similar a lo que sucede con el origen SQL, la propiedad de separador de anidamiento se utiliza para crear relaciones jerárquicas (documentos secundarios) durante la importación. Tenga en cuenta las siguientes filas de datos y la fila de encabezado CSV:

![Captura de pantalla de registros de ejemplo de CSV: CSV a JSON](./media/import-data/csvsample.png)

Tenga en cuenta los alias como DomainInfo.Domain_Name y RedirectInfo.Redirecting. Al especificar un separador de anidamiento de “.”, la herramienta de importación crea subdocumentos DomainInfo y RedirectInfo durante la importación. Este es un ejemplo de un documento resultante en Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

La herramienta de importación intentará inferir la información de tipo de los valores sin comillas de los archivos CSV (los valores entre comillas se tratan siempre como cadenas).  Los tipos se identifican en el siguiente orden: número, fecha y hora, booleano.  

Hay otras dos cosas que debe saber sobre la importación de archivos CSV:

1. De forma predeterminada, los valores sin comillas se recortan siempre en tabuladores y espacios, mientras que los valores entre comillas se mantienen como son. Este comportamiento se puede invalidar con la casilla Recortar valores entre comillas o la opción de línea de comandos /s.TrimQuoted.
2. De forma predeterminada, un valor null sin comillas se trata como un valor null. Este comportamiento se puede invalidar (es decir, tratar a los valores null sin comillas como si fueran cadenas "null") con la casilla Tratar NULL sin comillas como cadena o la opción de línea de comandos /s.NoUnquotedNulls.

Este es un ejemplo de línea de comandos para la importación CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Para importar desde Azure Table Storage
La opción del importador de código fuente de almacenamiento de tabla de Azure permite importar desde una tabla de almacenamiento de Azure individual y, opcionalmente, filtrar las entidades de tabla que se van a importar. Tenga en cuenta que no puede usar la herramienta de migración de datos para importar datos de Azure Table Storage a Azure Cosmos DB para su uso con la API de Table. En este momento, se admite solo la importación de Azure Cosmos DB para su uso con la API de DocumentDB.

![Screenshot of Azure Table storage source options](./media/import-data/azuretablesource.png)

El formato de la cadena de conexión de almacenamiento de tablas de Azure es:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de almacenamiento de tablas de Azure especificada en el campo de la cadena de conexión.
> 
> 

Escriba el nombre de la tabla de Azure desde la que se importarán los datos. Opcionalmente, puede especificar un [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

La opción del importador de origen de almacenamiento de tablas de Azure tiene las siguientes opciones adicionales:

1. Incluir campos internos
   1. All: incluir todos los campos internos (PartitionKey, RowKey y Timestamp)
   2. None: excluir todos los campos internos
   3. RowKey: incluir sólo el campo RowKey
2. Seleccionar columnas
   1. Los filtros de almacenamiento de tablas de Azure no admiten proyecciones. Si desea importar sólo las propiedades específicas de la entidad de tablas de Azure, agréguelas a la lista Seleccionar columnas. Se omitirán todas las demás propiedades de la entidad.

A continuación se muestra un ejemplo de línea de comandos para importar desde el almacenamiento de tablas de Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Para importar desde Amazon DynamoDB
La opción de importador de origen Amazon DynamoDB permite importar de una tabla individual de Amazon DynamoDB individual y, opcionalmente, filtrar las entidades que desea importar. Se proporcionan varias plantillas para que configurar una importación resulte lo más fácil posible.

![Captura de pantalla de opciones de origen de Amazon DynamoD: herramientas de migración de base de datos](./media/import-data/dynamodbsource1.png)

![Captura de pantalla de opciones de origen de Amazon DynamoD: herramientas de migración de base de datos](./media/import-data/dynamodbsource2.png)

El formato de la cadena de conexión de Amazon DynamoDB es:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de Amazon DynamoDB especificada en el campo de la cadena de conexión.
> 
> 

A continuación se muestra un ejemplo de línea de comandos para importar desde Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Para importar archivos desde Azure Blob storage
El archivo JSON, archivo de exportación de MongoDB y opciones de importador de origen de archivo CSV permiten importar uno o más archivos del almacenamiento de blobs de Azure. Después de especificar una dirección URL de contenedor de blobs y una clave de cuenta, basta con proporcionar una expresión regular para seleccionar los archivos para importar.

![Captura de pantalla de opciones de origen de archivos Blob](./media/import-data/blobsource.png)

Este es el ejemplo de línea de comandos para importar archivos JSON del almacenamiento de blobs de Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="DocumentDBSource"></a>Para importar desde una colección de la API de DocumentDB de Azure Cosmos DB
La opción del importador de origen de Azure Cosmos DB permite importar datos de una o varias colecciones de Azure Cosmos DB y, opcionalmente, filtrar los documentos mediante una consulta.  

![Captura de pantalla de opciones de origen de Azure Cosmos DB](./media/import-data/documentdbsource.png)

El formato de la cadena de conexión de Azure Cosmos DB es:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La cadena de conexión de la cuenta de Azure Cosmos DB se puede obtener en la hoja Claves de Azure Portal, como se describe en [Administración de una cuenta de Azure Cosmos DB](manage-account.md); sin embargo, el nombre de la base de datos se debe anexar a la cadena de conexión con el siguiente formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de Azure Cosmos DB especificada en el campo de la cadena de conexión.
> 
> 

Para realizar la importación desde una sola colección de Azure Cosmos DB, escriba el nombre de la colección de la que se van a importar los datos. Para realizar la importación desde varias colecciones de Azure Cosmos DB, especifique una expresión regular que coincida con uno o varios nombres de colección (por ejemplo, collección01 | collección02 | collección03). Opcionalmente, puede especificar o facilitar un archivo para una consulta para filtrar los datos que se van a importar y darles forma.

> [!NOTE]
> Dado que el campo de colecciones acepta expresiones regulares, si la importación se va a realizar de una sola colección cuyo nombre contenga caracteres de expresión regular, dichos caracteres deben incluir una secuencia de escape.
> 
> 

La opción del importador de origen de Azure Cosmos DB tiene las siguientes opciones avanzadas:

1. Incluir campos internos: especifica si se debe o no incluir propiedades del sistema de documentos de Azure Cosmos DB en la exportación (por ejemplo, _rid, _ts).
2. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
3. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
4. Modo de conexión: especifica el modo de conexión para utilizar con Azure Cosmos DB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Captura de pantalla de opciones avanzadas de origen de Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.
> 
> 

Estos son algunos ejemplos de línea de comandos para importar desde Azure Cosmos DB:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> La herramienta de importación de datos de Azure Cosmos DB también admite la importación de datos desde el [Emulador de Azure Cosmos DB](local-emulator.md). Al importar datos desde un emulador local, establezca el punto de conexión en `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Para importar desde HBase
La opción del importador de origen de HBase le permite importar datos de una tabla HBase y, opcionalmente, filtrar los datos. Se proporcionan varias plantillas para que configurar una importación resulte lo más fácil posible.

![Captura de pantalla de opciones de origen de HBase](./media/import-data/hbasesource1.png)

![Captura de pantalla de opciones de origen de HBase](./media/import-data/hbasesource2.png)

El formato de la cadena de conexión de HBase Stargate es:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de HBase especificada en el campo de la cadena de conexión.
> 
> 

A continuación se muestra un ejemplo de línea de comandos para importar desde HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="DocumentDBBulkTarget"></a>Para importar a la API de DocumentDB (importación masiva)
El importador masivo de Azure Cosmos DB permite importar desde cualquier opción de origen disponible con un procedimiento almacenado de Azure Cosmos DB para que resulte más eficaz. La herramienta admite la importación en una sola colección de Azure Cosmos DB con particiones, así como la importación con particiones por la cual los datos se dividen entre varias colecciones de Azure Cosmos DB con una única partición. Para más información sobre la creación de particiones de datos, consulte [Partición y escalado de datos en Azure Cosmos DB](partition-data.md). La herramienta creará, ejecutará y eliminará el procedimiento almacenado de las colecciones de destino.  

![Captura de pantalla de opciones de importación masiva de Azure Cosmos DB](./media/import-data/documentdbbulk.png)

El formato de la cadena de conexión de Azure Cosmos DB es:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La cadena de conexión de la cuenta de Azure Cosmos DB se puede obtener en la hoja Claves de Azure Portal, como se describe en [Administración de una cuenta de Azure Cosmos DB](manage-account.md); sin embargo, el nombre de la base de datos se debe anexar a la cadena de conexión con el siguiente formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de Azure Cosmos DB especificada en el campo de la cadena de conexión.
> 
> 

Para realizar la importación en una sola colección, escriba el nombre de la colección en la que se van a importar los datos y haga clic en el botón Agregar. Para importar en varias colecciones, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection_prefix*[índice inicial - índice final]. Si va a especificar varias colecciones a través de esta sintaxis, tenga en cuenta lo siguiente:

1. Solo se admiten patrones de nombre de intervalo entero. Por ejemplo, la especificación de colección [0-3] generará las siguientes colecciones: colección0, colección1, colección2, colección3.
2. Puede usar una sintaxis abreviada: colección[3] generará el mismo conjunto de colecciones que el paso 1.
3. Se pueden proporcionar varias sustituciones. Por ejemplo, colección[0-1] [0-9] 20 nombres de colección con ceros delante (colección01,... 02... 03).

Una vez especificados los nombres de las colecciones, elija la capacidad de proceso deseada de la colección (de 400 RU a 10 000 RU). Para conseguir el mejor rendimiento en la importación, elija una mayor capacidad de proceso. Para más información sobre los niveles de rendimiento, consulte [Niveles de rendimiento en Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> La configuración del nivel de rendimiento solo se aplica a la creación de la colección. Si la colección especificada ya existe, no se modificará su capacidad de proceso.
> 
> 

Cuando se importa a varias colecciones, la herramienta de importación admite el particionamiento basado en hash. En este escenario, especifique la propiedad de documento que desea utilizar como clave de partición (si el valor de Clave de partición se deja en blanco, los documentos se particionarán aleatoriamente entre las colecciones de destino).

También puede especificar qué campo del origen de importación debe utilizarse como la propiedad de identificador de documentos Azure Cosmos DB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de la propiedad del identificador).

Hay una serie de opciones avanzadas disponibles durante la importación. En primer lugar, mientras que la herramienta incluye un procedimiento predeterminado almacenado de importación masiva (BulkInsert.js), puede especificar su propio procedimiento almacenado de importación:

 ![Captura de pantalla de opciones de importación de insert sproc masiva de Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Además, al importar los tipos de fecha (por ejemplo, desde SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Captura de pantalla de opciones de importación de fecha y hora de Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadena: conservar como un valor de cadena
* Tiempo: conservar como un valor de número de tiempo
* Ambos: conservar los valores de cadena y de número  Esta opción creará un subdocumento, por ejemplo "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

El importador masivo de Azure Cosmos DB tiene las siguientes opciones avanzadas adicionales:

1. Tamaño del lote: el tamaño de lote predeterminado de la herramienta es 50.  Si los documentos que desea importar son grandes, considere la posibilidad de reducir el tamaño del lote. Si los documentos que desea importar son pequeños, considere la posibilidad de aumentar el tamaño del lote.
2. Tamaño máximo de script (bytes): el valor predeterminado máximo del script en la herramienta es de 512 KB.
3. Deshabilitar generación de Id. automática: si cada documento que se va a importar contiene un campo de Id., seleccione esta opción para aumentar el rendimiento. No se importarán los documentos  que no contengan el campo de identificador exclusivo.
4. Actualización de documentos existentes: la opción predeterminada es no reemplazar los documentos existentes por conflictos de identificador. Si selecciona esta opción, permitirá que se sobrescriban los documentos existentes con identificadores que coinciden entre sí. Esta característica es útil para las migraciones de datos programadas que actualizan los documentos existentes.
5. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
6. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
7. Modo de conexión: especifica el modo de conexión para utilizar con Azure Cosmos DB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Captura de pantalla de opciones avanzadas de importación masiva de Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.
> 
> 

## <a id="DocumentDBSeqTarget"></a>Para importar a la API de DocumentDB (importación de registros secuenciales)
El importador de registros secuenciales de Azure Cosmos DB permite importar desde cualquiera de las opciones de origen disponibles según cada registro. Puede elegir esta opción si va a importar a una colección existente que ha alcanzado su cuota de procedimientos almacenados. La herramienta admite la importación en una única colección de Azure Cosmos DB (de una o varias particiones), así como la importación con particiones por la cual los datos se dividen entre varias colecciones de Azure Cosmos DB de una o varias particiones. Para más información sobre la creación de particiones de datos, consulte [Partición y escalado de datos en Azure Cosmos DB](partition-data.md).

![Captura de pantalla de opciones de importación de registros secuenciales de Azure Cosmos DB](./media/import-data/documentdbsequential.png)

El formato de la cadena de conexión de Azure Cosmos DB es:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La cadena de conexión de la cuenta de Azure Cosmos DB se puede obtener en la hoja Claves de Azure Portal, como se describe en [Administración de una cuenta de Azure Cosmos DB](manage-account.md); sin embargo, el nombre de la base de datos se debe anexar a la cadena de conexión con el siguiente formato:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de Azure Cosmos DB especificada en el campo de la cadena de conexión.
> 
> 

Para realizar la importación en una sola colección, escriba el nombre de la colección en la que se van a importar los datos y haga clic en el botón Agregar. Para importar en varias colecciones, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection_prefix*[índice inicial - índice final]. Si va a especificar varias colecciones a través de esta sintaxis, tenga en cuenta lo siguiente:

1. Solo se admiten patrones de nombre de intervalo entero. Por ejemplo, la especificación de colección [0-3] generará las siguientes colecciones: colección0, colección1, colección2, colección3.
2. Puede usar una sintaxis abreviada: colección[3] generará el mismo conjunto de colecciones que el paso 1.
3. Se pueden proporcionar varias sustituciones. Por ejemplo, colección[0-1] [0-9] 20 nombres de colección con ceros delante (colección01,... 02... 03).

Una vez especificados los nombres de las colecciones, elija la capacidad de proceso deseada de la colección (de 400 RU a 250 000 RU). Para conseguir el mejor rendimiento en la importación, elija una mayor capacidad de proceso. Para más información sobre los niveles de rendimiento, consulte [Niveles de rendimiento en Azure Cosmos DB](performance-levels.md). Cualquier importación a colecciones con una capacidad de proceso de >10 000 RU requerirá una clave de partición. Si elige tener más de 250 000 RU, debe registrar una solicitud en el portal para que le aumenten su cuenta.

> [!NOTE]
> La configuración de la capacidad de proceso solo se aplica a la creación de colecciones. Si la colección especificada ya existe, no se modificará su capacidad de proceso.
> 
> 

Cuando se importa a varias colecciones, la herramienta de importación admite el particionamiento basado en hash. En este escenario, especifique la propiedad de documento que desea utilizar como clave de partición (si el valor de Clave de partición se deja en blanco, los documentos se particionarán aleatoriamente entre las colecciones de destino).

También puede especificar qué campo del origen de importación debe utilizarse como la propiedad de identificador de documentos Azure Cosmos DB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de la propiedad del identificador).

Hay una serie de opciones avanzadas disponibles durante la importación. En primer lugar, al importar los tipos de fecha (por ejemplo, desde SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Captura de pantalla de opciones de importación de fecha y hora de Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadena: conservar como un valor de cadena
* Tiempo: conservar como un valor de número de tiempo
* Ambos: conservar los valores de cadena y de número  Esta opción creará un subdocumento, por ejemplo "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

El importador de registros secuenciales de Azure Cosmos DB tiene estas opciones avanzadas adicionales:

1. Número de solicitudes paralelas: la opción predeterminada es 2. Si los documentos que desea importar son pequeños, considere la posibilidad de aumentar el número de solicitudes paralelas. Tenga en cuenta que si este número es demasiado elevado, podrá haber limitaciones en la importación.
2. Deshabilitar generación de Id. automática: si cada documento que se va a importar contiene un campo de Id., seleccione esta opción para aumentar el rendimiento. No se importarán los documentos  que no contengan el campo de identificador exclusivo.
3. Actualización de documentos existentes: la opción predeterminada es no reemplazar los documentos existentes por conflictos de identificador. Si selecciona esta opción, permitirá que se sobrescriban los documentos existentes con identificadores que coinciden entre sí. Esta característica es útil para las migraciones de datos programadas que actualizan los documentos existentes.
4. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
5. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a Azure Cosmos DB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
6. Modo de conexión: especifica el modo de conexión para utilizar con Azure Cosmos DB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Captura de pantalla de opciones avanzadas de importación de registros secuenciales de Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.
> 
> 

## <a id="IndexingPolicy"></a>Especificar una directiva de indexación al crear colecciones de Azure Cosmos DB
Si permite que la herramienta de migración cree colecciones durante la importación, puede especificar la directiva de indización de las colecciones. En la sección de opciones avanzadas de las opciones de Importación masiva de Azure Cosmos DB y Registro secuencial de Azure Cosmos DB, vaya a la sección de la directiva de indexación.

![Captura de pantalla de opciones avanzadas de política de indexación de Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

Mediante la opción avanzada de Directiva de indización, puede seleccionar un archivo de directiva de indización, especificar manualmente una directiva de indización o seleccionar de un conjunto de plantillas predeterminadas (haciendo clic con el botón derecho en el cuadro de texto de la directiva de indización).

La herramienta proporciona las plantillas de directiva siguientes:

* Predeterminada. Esta directiva es mejor cuando se realizan consultas de igualdad en cadenas y se usan consultas ORDER BY, de rango y de igualdad para números. Esta directiva tiene una sobrecarga de almacenamiento de índices menor que la de intervalo.
* Intervalo. Esta directiva es mejor si está usando consultas de ORDER BY, intervalo e igualdad en números y cadenas. Esta directiva tiene una mayor sobrecarga de almacenamiento de índice que la Predeterminada o Hash.

![Captura de pantalla de opciones avanzadas de política de indexación de Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Si no especifica una directiva de indización, se aplicará la directiva predeterminada. Para más información sobre las directivas de indexación, consulte [Directivas de indexación de Azure Cosmos DB](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportación a archivos JSON
El exportador JSON de Azure Cosmos DB permite exportar cualquiera de las opciones de origen disponibles a un archivo JSON que contiene una matriz de documentos JSON. La herramienta controlará la exportación por usted, o puede ver el comando resultante de la migración y ejecutar el comando usted mismo. El archivo JSON resultante puede almacenarse localmente o en el almacenamiento de blobs de Azure.

![Captura de pantalla de opción de exportación de archivo local JSON de Azure Cosmos DB](./media/import-data/jsontarget.png)

![Captura de pantalla de opción de exportación de Azure Blob Storage de JSON de Azure Cosmos DB](./media/import-data/jsontarget2.png)

Opcionalmente, puede optar por adornar el JSON resultante, lo que aumentará el tamaño del documento resultante al mismo tiempo que el contenido será más legibles.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Configuración avanzada
En la pantalla Configuración avanzada, especifique la ubicación del archivo de registro en que desee que se escriban los errores. Las siguientes reglas se aplican a esta página:

1. Si no se proporciona un nombre de archivo, todos los errores se devolverán a la página de resultados.
2. Si se proporciona un nombre de archivo sin un directorio, el archivo se creará (o sobrescribirá) en el directorio del entorno actual.
3. Si selecciona un archivo existente, este se sobrescribirá, ya que no hay opción de anexar.

A continuación, elija si desea registrar todos los mensajes de error, los críticos o ninguno. Por último, decida con qué frecuencia se actualizará el progreso en el mensaje de transferencia en pantalla.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirmación de las opciones de importación y visualización de la línea de comandos
1. Después de especificar la información de origen y destino, y la configuración avanzada, revise el resumen de la migración y, opcionalmente, vea o copie el comando resultante de la migración (copiar el comando es útil para automatizar las operaciones de importación):
   
    ![Screenshot of summary screen](./media/import-data/summary.png)
   
    ![Screenshot of summary screen](./media/import-data/summarycommand.png)
2. Una vez que esté satisfecho con las opciones de origen y de destino, haga clic en **Importar**. El tiempo transcurrido, el número transferido y la información sobre los errores (si no se ha especificado un nombre de archivo en la configuración avanzada) se actualizarán mientras la importación está curso. Una vez que haya finalizado, puede exportar los resultados (por ejemplo, para tratar los errores de importación).
   
    ![Captura de pantalla de opción de exportación de JSON de Azure Cosmos DB](./media/import-data/viewresults.png)
3. También puede iniciar una nueva importación, conservando la configuración existente (por ejemplo, elección de información de origen y de destino de la cadena de conexión, etc.) o restableciendo todos los valores.
   
    ![Captura de pantalla de opción de exportación de JSON de Azure Cosmos DB](./media/import-data/newimport.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Instalación de la herramienta de migración de datos
> * Importación de datos de orígenes de datos diferentes
> * Exportación desde Azure Cosmos DB a JSON

Ahora puede pasar al siguiente tutorial y obtener más información sobre cómo consultar los datos con Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Consulta de datos](../cosmos-db/tutorial-query-documentdb.md)

