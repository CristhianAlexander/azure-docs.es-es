---
title: Carga de datos para trabajos de Hadoop en HDInsight | Microsoft Docs
description: "Aprenda a cargar datos en HDInsight y a obtener acceso a ellos para trabajos de Hadoop con la CLI de Azure, el Explorador de almacenamiento de Azure, Azure PowerShell, la línea de comandos de Hadoop o Sqoop."
keywords: "extracción, transformación y carga de datos de hadoop, obtención de datos en hadoop, carga de datos de hadoop"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 134afd3495c555f85e8838cbe0344a3a48534950
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carga de datos para trabajos de Hadoop en HDInsight
HDInsight de Azure ofrece un sistema de archivos distribuido de Hadoop (HDFS) completo a través del servicio de almacenamiento de blobs de Azure. Está diseñado como una extensión de HDFS para ofrecer una experiencia continua para los clientes. Habilita que el conjunto completo de componentes en el ecosistema de Hadoop opere directamente en los datos que administra. El almacenamiento de blobs de Azure y HDFS son sistemas de archivos diferentes que se han optimizado para el almacenamiento de datos y el cálculo en ellos. Para obtener más información sobre las ventajas del uso de Azure Blob Storage, consulte [Uso de Azure Blob Storage con HDInsight][hdinsight-storage].

**Requisitos previos**

Tenga en cuenta el requisito siguiente antes de empezar:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

## <a name="why-blob-storage"></a>Motivos para almacenar blobs
Los clústeres de HDInsight de Azure se implementan normalmente para ejecutar trabajos de MapReduce y dichos clústeres se anulan cuando los trabajos se completan. El mantenimiento de datos en los clústeres de HDFS después de que se hayan completado los cálculos supondría un alto coste para el almacenamiento de estos datos. El almacenamiento de blobs de Azure tiene una excelente disponibilidad, es altamente escalable, cuenta con una gran capacidad, un bajo coste y la opción de almacenamiento que se puede compartir para los datos que se van a procesar usando HDInsight. El almacenamiento de los datos en un blob permite que los clústeres de HDInsight que se usan para los cálculos se lancen de forma segura y sin perder los datos.

### <a name="directories"></a>Directorios
Los contenedores del almacenamiento de blobs de Azure almacenan los datos como pares de clave/valor y no hay jerarquía de directorios. No obstante, el carácter "/" se puede usar en el nombre de la clave para que parezca que el archivo está almacenado dentro de una estructura de directorios. HDInsight los ve como si fueran directorios reales.

Por ejemplo, la clave de un blob puede ser *input/log1.txt*. No hay directorios "input", pero dada la presencia del carácter "/" en el nombre de la clave, parece la ruta de un archivo.

Por eso, si usa las herramientas de Azure Explorer, puede que vea algunos archivos de 0 bytes. Estos archivos tienen dos propósitos:

* Si hay carpetas vacías, son una marca de la existencia de la carpeta. El almacenamiento de blobs de Azure es suficientemente inteligente para saber que si existe un blob que se llama foo/bar, es porque hay una carpeta llamada **foo**. Pero la única forma de indicar una carpeta vacía llamada **foo** es disponer este archivo especial de 0 bytes en su sitio.
* Contienen metadatos especiales que necesita el sistema de archivos de Hadoop, concretamente los permisos y los propietarios de las carpetas.

## <a name="command-line-utilities"></a>Utilidades de la ea de comandos
Microsoft proporciona las utilidades siguientes para trabajar con el almacenamiento de blobs de Azure:

| Herramienta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interfaz de la línea de comandos de Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] | | |✔ |
| [Línea de comandos de Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Mientras que la CLI de Azure y Azure PowerShell AzCopy se pueden utilizar desde fuera de Azure, la línea de comandos de Hadoop sólo está disponible en el clúster de HDInsight y sólo permite cargar datos del sistema de archivos local en el almacenamiento de blobs de Azure.
>
>

### <a id="xplatcli"></a>Azure CLI
La CLI de Azure es una herramienta multiplataforma que le permite administrar los servicios de Azure. Para cargar datos en el almacenamiento de blobs de Azure, siga estos pasos:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalación y configuración de la CLI de Azure para Mac, Linux y Windows](../cli-install-nodejs.md).
2. Abra un símbolo del sistema, bash u otro shell y use lo siguiente para autenticarse en su suscripción de Azure.

        azure login

    Cuando se le solicite, escriba el nombre de usuario y la contraseña de su suscripción.
3. Escriba el comando siguiente para enumerar las cuentas de almacenamiento de su suscripción:

        azure storage account list
4. Seleccione la cuenta de almacenamiento que contiene el blob con en el que quiere trabajar y use el comando siguiente para recuperar la clave de esta cuenta:

        azure storage account keys list <storage-account-name>

    Esto debería devolver las claves **Principal** y **Secundaria**. Copie el valor de la clave **Principal** ya que se usará en los pasos siguientes.
5. Use el comando siguiente para recuperar una lista de contenedores de blobs dentro de la cuenta de almacenamiento:

        azure storage container list -a <storage-account-name> -k <primary-key>
6. Use los comandos siguientes para cargar y descargar archivos en el blob:

   * Para cargar un archivo:

           azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
   * Para descargar un archivo:

           azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [!NOTE]
> Si siempre trabajará con la misma cuenta de almacenamiento, puede establecer las siguientes variables de entorno en lugar de especificar la cuenta y la clave para cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: el nombre de la cuenta de almacenamiento.
> * **AZURE\_STORAGE\_ACCESS\_KEY**: la clave de la cuenta de almacenamiento.
>
>

### <a id="powershell"></a>Azure PowerShell
Azure PowerShell es un eficaz entorno de scripting que se puede usar para controlar y automatizar la implementación y la administración de cargas de trabajo en Azure. Para obtener información sobre cómo configurar su estación de trabajo para que ejecute Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para cargar un archivo local en el almacenamiento de blobs de Azure**

1. Abra la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
2. Configure los valores de las cinco primeras variables del script siguiente:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
3. Pegue el script en la consola de Azure PowerShell para ejecutarlo para copiar el archivo.

Por ejemplo los scripts de PowerShell creados para trabajar con HDInsight, consulte [HDInsight tools (Herramientas de HDInsight)](https://github.com/blackmist/hdinsight-tools).

### <a id="azcopy"></a>AzCopy
AzCopy es una herramienta de la línea de comandos diseñada para simplificar la tarea de transferir datos dentro y fuera de una cuenta de almacenamiento de Azure. Puede usarla como una herramienta independiente o incorporarla a una aplicación existente. [Descarga de AzCopy][azure-azcopy-download]

La sintaxis de AzCopy es la siguiente:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obtener más información, consulte [AzCopy: carga y descarga de archivos para blobs de Azure][azure-azcopy].

### <a id="commandline"></a>Línea de comandos de Hadoop
La línea de comandos de Hadoop sólo es útil para almacenar los datos en el almacenamiento de blobs cuando los datos ya están presentes en el nodo principal del clúster.

Para usar la línea de comando de Hadoop, primero es preciso conectarse al nodo principal a través de uno de los métodos siguientes:

* **HDInsight para Windows**: [conectar mediante Escritorio remoto](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight basado en Linux**: conectar mediante SSH ([el comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md) o [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md))

Una vez conectado, puede utilizar la siguiente sintaxis para cargar un archivo al almacenamiento.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por ejemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como el sistema de archivos predeterminado de HDInsight está en el almacenamiento de blobs de Azure, /example/datadavinci.txt está en realidad en el almacenamiento de blobs de Azure. También puede referirse al archivo como:

    wasb:///example/data/data.txt

o

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obtener una lista de otros comandos de Hadoop que funcionan con archivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> En los clústeres de HBase, el tamaño de bloque predeterminado al escribir datos es de 256 KB. Aunque esto funciona bien cuando se usa la API de REST o las API de HBase, el uso de los comandos `hadoop` o `hdfs dfs` para escribir más de ~ 12 GB de datos genera un error. Consulte la sección [Excepción de almacenamiento para escritura en blob](#storageexception) a continuación para más información.
>
>

## <a name="graphical-clients"></a>Clientes gráficos
También hay varias aplicaciones que proporcionan una interfaz gráfica para trabajar con el almacenamiento de Azure. Esta es una lista de algunas de estas aplicaciones:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Explorador de almacenamiento de Azure](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools para HDInsight
Para obtener más información, consulte [Navegación por los recursos vinculados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

### <a id="storageexplorer"></a>Explorador de almacenamiento de Azure
*Explorador de almacenamiento de Azure* es una práctica herramienta para inspeccionar y modificar los datos de blobs. Se trata de una herramienta gratuita que se puede descargar de [http://storageexplorer.com/](http://storageexplorer.com/). El código fuente también está disponible en este vínculo.

Antes de usar la herramienta, debe saber el nombre y la clave de la cuenta de almacenamiento de Azure. Para ver instrucciones sobre cómo obtener esta información, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storage-account].

1. Ejecute el explorador de almacenamiento de Azure. Si es la primera vez que ejecuta el Explorador de almacenamiento, se le pedirá el **nombre de la cuenta de almacenamiento** y la **clave de cuenta de almacenamiento**. Si ya lo ejecutó antes, use el botón **Agregar** para agregar un nombre y una clave de cuenta de almacenamiento nuevos.

    Escriba el nombre y la clave para la cuenta de almacenamiento que usa el clúster de HDInsight y seleccione **GUARDAR Y ABRIR**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. En la lista de contenedores de la izquierda de la interfaz, haga clic en el nombre del contenedor que esté asociado al clúster de HDInsight. De forma predeterminada, es el nombre del clúster de HDInsight, pero puede ser diferente si especificó un nombre concreto al crear el clúster.
3. En la barra de herramientas, seleccione el icono de carga.

    ![Barra de herramientas con el icono de carga resaltado](./media/hdinsight-upload-data/toolbar.png)
4. Especifique el archivo que quiere cargar y haga clic en **Abrir**. Cuando se le pida, seleccione **Cargar** para cargar el archivo en la raíz del contenedor de almacenamiento. Si quiere cargar el archivo en una ruta de acceso específica, escriba la ruta de acceso en el campo **Destino** y seleccione **Cargar**.

    ![Cuadro de diálogo de carga de archivos](./media/hdinsight-upload-data/fileupload.png)

    Cuando termine de cargar el archivo, puede usarlo desde trabajos en el clúster de HDInsight.

## <a name="mount-azure-blob-storage-as-local-drive"></a>Montar el almacenamiento de blobs de Azure como unidad local
Vea [Montaje del almacenamiento de blobs de Azure como unidad local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="services"></a>Servicios
### <a name="azure-data-factory"></a>Factoría de datos de Azure
El servicio Factoría de datos de Azure es un servicio completamente administrado para crear servicios de almacenamiento de datos, procesamiento de datos y movimiento en canalizaciones de producción de datos confiable, escalable y simplificado.

Factoría de datos de Azure puede utilizarse para introducir datos en el almacenamiento de blobs de Azure o para crear canalizaciones de datos que usan directamente las características de HDInsight, como Hive y Pig.

Para obtener más información, consulte [Documentación de Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/).

### <a id="sqoop"></a>Apache Sqoop
Sqoop es una herramienta diseñada para transferir datos entre Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos de Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS.

Para obtener más información, consulte [Use Sqoop with Hadoop in HDInsight (Uso de Sqoop con HDInsight)][hdinsight-use-sqoop].

## <a name="development-sdks"></a>SDK de desarrollo
También es posible obtener acceso al almacenamiento de blobs de Azure mediante un SDK de Azure desde los siguientes lenguajes de programación:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obtener más información acerca de cómo instalar los SDK de Azure, consulte [Descargas de Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Solución de problemas
### <a id="storageexception"></a>Excepción de almacenamiento para escritura en blob
**Síntomas**: al usar los comandos `hadoop` o `hdfs dfs` para escribir archivos de ~ 12 GB o mayores en un clúster de HBase, puede encontrar el siguiente error:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: HBase en clústeres de HDInsight toma como valor predeterminado un tamaño de bloque de 256 KB al escribir en Azure Storage. Si bien esto funciona para API de HBase o API de REST, se producirá un error al usar las utilidades de línea de comandos `hadoop` o `hdfs dfs`.

**Solución**: use `fs.azure.write.request.size` para especificar un tamaño de bloque más grande. Puede hacerlo mediante un sistema por uso con el parámetro `-D`. El siguiente es un ejemplo donde se usa este parámetro con el comando `hadoop`:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

También puede aumentar el valor de `fs.azure.write.request.size` globalmente mediante Ambari. Los siguientes pasos se pueden usar para cambiar el valor en la IU web de Ambari:

1. En el explorador, vaya a la IU web de Ambari para el clúster. La puede encontrar en https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre del clúster.

    Cuando se le solicite, escriba el nombre de usuario y la contraseña de administrador para el clúster.
2. En el lado izquierdo de la pantalla, seleccione **HDFS** y luego seleccione la pestaña **Configs** (Configuraciones).
3. En el campo **Filter...** (Filtro...), escriba `fs.azure.write.request.size`. Se mostrará el campo y el valor actual en el centro de la página.
4. Cambie el valor de 262144 (256 KB) al nuevo valor. Por ejemplo, 4194304 (4 MB).

![Imagen de cambiar el valor en la IU web de Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya sabe cómo enviar datos a HDInsight, consulte los artículos siguientes para aprender a realizar el análisis:

* [Introducción a Azure HDInsight][hdinsight-get-started]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

