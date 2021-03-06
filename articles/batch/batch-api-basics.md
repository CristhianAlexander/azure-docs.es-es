---
title: "Introducción a Azure Batch para desarrolladores | Microsoft Docs"
description: "Conozca las características del servicio Lote y sus API desde el punto de vista del desarrollo."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 0120a63ed398cf3e0e91cd4329c4024ba2fbfdca
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
<a id="develop-large-scale-parallel-compute-solutions-with-batch" class="xliff"></a>

# Desarrollo de soluciones de procesos paralelos a gran escala con Batch

En este artículo se proporciona información general acerca los componentes principales del servicio Lote de Azure y se describen los principales recursos y características del servicio que pueden usar los desarrolladores de Lote para crear soluciones de proceso en paralelo a gran escala.

Utilizará muchos de los recursos y las características que se describen en este artículo, tanto si está desarrollando una aplicación informática distribuida o un servicio que emita llamadas directas a la [API de REST][batch_rest_api], como si usa uno de los [SDK de Batch](batch-apis-tools.md#azure-accounts-for-batch-development).

> [!TIP]
> Para ver una introducción de nivel superior sobre el servicio Lote, consulte [Datos básicos de Lote de Azure](batch-technical-overview.md).
>
>

<a id="batch-service-workflow" class="xliff"></a>

## Flujo de trabajo del servicio Lote
El siguiente flujo de trabajo de alto nivel es típico de casi todas las aplicaciones y los servicios que usan el servicio Lote para procesar cargas de trabajo paralelas:

1. Cargue los **archivos de datos** que desee procesar en una cuenta de [Azure Storage][azure_storage]. Lote incluye compatibilidad integrada con el acceso al almacenamiento de blobs de Azure y, cuando se ejecutan, las tareas pueden descargar estos archivos a [nodos de proceso](#compute-node) .
2. Cargue los **archivos de la aplicación** que las tareas ejecutarán. Estos archivos pueden ser binarios o scripts y sus dependencias, y los ejecutan las tareas de los trabajos. Las tareas pueden descargar estos archivos desde la cuenta de Almacenamiento, o bien puede usar la característica de [paquetes de aplicación](#application-packages) en Lote para la implementación y la administración de aplicaciones.
3. Cree un [grupo](#pool) de nodos de proceso. Cuando cree un grupo, especifique el número de nodos de proceso para el grupo, su tamaño y el sistema operativo. Cuando se ejecutan las tareas de su trabajo, estas se asignan para que se ejecuten en uno de los nodos del grupo.
4. Creación de un [trabajo](#job). Un trabajo administra una colección de tareas. Se asocia cada trabajo a un grupo específico donde se ejecutarán las tareas de dicho trabajo.
5. Agregue [tareas](#task) al trabajo. Cada tarea ejecuta la aplicación o el script que haya cargado para procesar los archivos de datos que descarga de la cuenta de Almacenamiento. A medida que se complete cada tarea, puede cargar su resultado a Almacenamiento de Azure.
6. Supervise el progreso del trabajo y recupere el resultado de la tarea de Almacenamiento de Azure.

Las secciones siguientes tratan estos, y otros, recursos de Batch que permitirán que haya un escenario de cálculo distribuido.

> [!NOTE]
> Necesitará una [cuenta de Batch](#account) para utilizar este servicio. Además, casi todas las soluciones usan una cuenta de [Azure Storage][azure_storage] para el almacenamiento y la recuperación de archivos. Actualmente, Batch solo admite el tipo de cuenta de almacenamiento **de uso general**, como se describe en el paso 5 de la sección [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).
>
>

<a id="batch-service-resources" class="xliff"></a>

## Recursos del servicio de Lote
Algunos de los siguientes recursos son necesarios para todas las soluciones que usan el servicio Lote: cuentas, nodos de proceso, grupos, trabajos, tareas. Otros, como las programaciones de los trabajos y los paquetes de aplicación, son características útiles, pero opcionales.

* [Cuenta](#account)
* [Nodo de ejecución](#compute-node)
* [Grupo](#pool)
* [Trabajo](#job)

  * [Programaciones del trabajo](#scheduled-jobs)
* [Task](#task)

  * [Tarea de inicio](#start-task)
  * [Tareas del Administrador de trabajos](#job-manager-task)
  * [Tareas de preparación y liberación de trabajos](#job-preparation-and-release-tasks)
  * [Tarea de instancias múltiples (MPI)](#multi-instance-tasks)
  * [Dependencias de las tareas](#task-dependencies)
* [Paquetes de aplicación](#application-packages)

<a id="account" class="xliff"></a>

## Cuenta
Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se asocia con una cuenta de Lote.

Puede crear una cuenta de Azure Batch mediante el [portal de Azure](batch-account-create-portal.md) o por medio de programación, como con la [biblioteca .NET de administración de lotes](batch-management-dotnet.md). Al crear la cuenta, puede asociar una cuenta de almacenamiento de Azure.

Batch admite dos configuraciones de cuenta y deberá seleccionar la configuración adecuada al crear su cuenta de Batch. La diferencia entre las dos configuraciones de cuenta está en cómo se asignan los [grupos](#pool) de Batch para la cuenta. Puede asignar grupos de nodos de proceso en una suscripción administrada por Azure Batch o bien puede asignarlos en su propia suscripción. La propiedad *pool allocation mode* para la cuenta determina qué configuración utiliza. 

Para decidir qué configuración de cuenta va a utilizar, tenga en cuenta la que mejor se adapte a su escenario:

* **Servicio de Batch**: es la configuración de cuenta predeterminada. Para una cuenta creada con esta configuración, los grupos de Batch se asignan en segundo plano en suscripciones administradas por Azure. Tenga en cuenta estos puntos clave acerca de la configuración de la cuenta del servicio de Batch:

    - La configuración de la cuenta del servicio de Batch admite grupos de Cloud Services y de máquinas virtuales.
    - La configuración de cuenta del servicio de Batch admite el acceso a las Batch API mediante la autenticación de clave compartida o la [autenticación de Azure Active Directory](batch-aad-auth.md). 
    - Puede usar nodos de proceso dedicados o de prioridad baja en grupos en la configuración de la cuenta de servicio de Batch.
    - No utilice la configuración de la cuenta del servicio de Batch si tiene previsto crear grupos de máquinas virtuales de Azure a partir de imágenes de máquinas virtuales personalizadas o si prevé utilizar una red virtual. En su lugar, cree la cuenta con la configuración de la cuenta de suscripción de usuario.
    - Los grupos de máquinas virtuales aprovisionados en una cuenta con la configuración de la cuenta de suscripción del servicio de Batch se deben crear a partir de las imágenes de [Azure Virtual Machines Marketplace] [ vm_marketplace].

* **Suscripción de usuario**: con la configuración de la cuenta de suscripción de usuario, los grupos de Batch se asignan en la suscripción de Azure donde se crea la cuenta. Tenga en cuenta estos puntos clave acerca de la configuración de la cuenta de suscripción de usuario:
     
    - La configuración de la cuenta de suscripción de usuario admite solo grupos de máquinas virtuales. No admite grupos de Cloud Services.
    - Para crear grupos de máquinas virtuales a partir de imágenes de máquinas virtuales personalizadas o usar una red virtual con grupos de máquinas virtuales, debe usar la configuración de suscripción de usuario.  
    - Debe autenticar las solicitudes al servicio de Batch mediante la [autenticación de Azure Active Directory](batch-aad-auth.md). 
    - Con la configuración de la cuenta de suscripción de usuario debe configurar un almacén de claves de Azure para su cuenta de Batch. 
    - Solo puede usar nodos de proceso dedicados en grupos en la configuración de la cuenta de suscripción de usuario. No se admiten nodos de prioridad baja.
    - Los grupos de máquinas virtuales aprovisionados en una cuenta con la configuración de la cuenta de suscripción de usuario se deben crear a partir de las imágenes de [Azure Virtual Machines Marketplace] [ vm_marketplace] o a partir de las imágenes personalizadas que proporcione.

> [!IMPORTANT]
> Actualmente, Batch solo admite el tipo de cuenta de almacenamiento de uso general, como se describe en el paso 5 de la sección [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). Las tareas de Batch (incluidas las tareas estándar, las de inicio, las de preparación de trabajos y las de liberación de trabajos) deben especificar archivos de recursos que residan en cuentas de almacenamiento de uso general.
>
>


<a id="compute-node" class="xliff"></a>

## Nodo de ejecución
Un nodo de ejecución es una máquina virtual de Azure o una máquina virtual de servicio en la nube dedicada al proceso de una parte de la carga de trabajo de la aplicación. El tamaño de un nodo determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asignan al nodo. Puede crear grupos de nodos de Windows o Linux mediante Servicios en la nube o imágenes de Máquinas virtuales de Marketplace. Consulte la sección [Grupo](#pool) a continuación para más información sobre estas opciones.

Los nodos pueden ejecutar cualquier archivo ejecutable o script compatible con el entorno de sistema operativo del nodo. Esto incluye scripts \*.exe, \*.cmd, \*.bat y de PowerShell para Windows, además de archivos binarios y scripts de shell y de Python para Linux.

Todos los nodos de proceso en Lote también incluyen:

* Una [estructura de carpetas](#files-and-directories) estándar y [variables de entorno](#environment-settings-for-tasks) asociadas, disponibles para que las tareas hagan referencia a ellas.
* **firewall** para controlar el acceso.
* [Acceso remoto](#connecting-to-compute-nodes) a nodos de Windows (Protocolo de escritorio remoto (RDP)) y nodos de Linux (Secure Shell (SSH)).

<a id="pool" class="xliff"></a>

## grupo
Un grupo es una colección de nodos en la que se ejecuta la aplicación. El usuario lo puede crear manualmente, o bien el servicio Lote lo crea automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga los requisitos de recursos de su aplicación. Un grupo solo se puede usar con la cuenta de Lote en la que se creó. Una cuenta de Lote puede tener más de un grupo.

Los grupos de Lote de Azure se basan en la plataforma de proceso principal de Azure. y proporcionan asignación a gran escala, instalación de aplicaciones, distribución de datos, supervisión de estado y ajuste flexible del número de nodos de proceso en un grupo ([escalado](#scaling-compute-resources)).

A cada nodo que se agrega a un grupo se le asigna un nombre y una dirección IP únicos. Cuando se quita un nodo de un grupo, los cambios realizados en el sistema operativo o los archivos se pierden, y su nombre y dirección IP se liberan para usarlos en un futuro. Cuando un nodo abandona un grupo, su vigencia finaliza.

Cuando se crea un grupo, puede especificar los siguientes atributos: Algunas opciones de configuración difieren, según el modo de asignación de grupo de la [cuenta](#account) de Batch:

- Sistema operativo y versión de nodo de proceso
- Tipo de nodo de proceso y número de nodos de destino
- Tamaño de los nodos de proceso
- Directiva de escalado
- Directiva de programación de tareas
- Estado de comunicación para nodos de proceso
- Tareas de inicio para nodos de proceso
- paquetes de aplicación
- Network configuration (Configuración de red)

Cada uno de estos valores se describe con más detalle en las secciones siguientes.

> [!IMPORTANT]
> Las cuentas de Batch creadas con la configuración del servicio de Batch tienen una cuota predeterminada que limita el número de núcleos en una cuenta de Batch. El número de núcleos corresponde al número de nodos de proceso. Tanto las cuotas predeterminadas como las instrucciones para [aumentar una cuota](batch-quota-limit.md#increase-a-quota) se pueden encontrar en [Límites y cuotas del servicio de Batch de Azure](batch-quota-limit.md). Si el grupo no está consiguiendo su número de nodos de destino, la causa podría ser la cuota básica.
>
>Las cuentas de Batch que se crean con la configuración de suscripción de usuario no tienen en cuenta las cuotas del servicio de Batch. En su lugar, comparten la cuota básica de la suscripción especificada. Para más información, consulte [Límites de Virtual Machines](../azure-subscription-service-limits.md#virtual-machines-limits) en [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

<a id="compute-node-operating-system-and-version" class="xliff"></a>

### Sistema operativo y versión de nodo de proceso

Al crear un grupo de Batch, puede especificar la configuración de máquina virtual de Azure y el tipo de sistema operativo que desea ejecutar en cada nodo de proceso del grupo. Los dos tipos de configuraciones disponibles en Batch son:

- La **configuración de máquina virtual**, que especifica que el grupo está formado por máquinas virtuales de Azure. Estas máquinas virtuales pueden crearse a partir de imágenes de Linux o Windows. 

    Cuando crea un grupo basado en la configuración de máquina virtual, debe especificar no solo el tamaño de los nodos y el origen de las imágenes utilizadas para crearlos, sino también la **referencia de la imagen de máquina virtual** y la **SKU del agente de nodo** de Batch que desea instalar en los nodos. Para más información sobre cómo especificar estas propiedades del grupo, consulte [Aprovisionamiento de nodos de proceso de Linux en grupos del servicio Lote de Azure](batch-linux-nodes.md).

- La **configuración de Cloud Services**, que especifica que el grupo está formado por nodos de Azure Cloud Services. Cloud Services proporciona *solo* nodos de proceso Windows.

    La lista de los sistemas operativos disponibles para los grupos de configuración de Servicios en la nube aparece en [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Cuando crea un grupo que contiene nodos de Cloud Services, debe especificar el tamaño del nodo y su *familia de sistema operativo*. Cloud Services se implementa en Azure de forma más rápida que las máquinas virtuales que ejecutan Windows. Si desea grupos de nodos de proceso Windows, es posible que Cloud Services proporcione una mejora en el rendimiento en términos de tiempo de implementación.

    * La *familia de sistema operativo* también determina qué versiones de .NET se instalan con el sistema operativo.
    * Al igual que con los roles de trabajo en Cloud Services, es posible especificar la *versión del sistema operativo* (para más información sobre los roles de trabajo, consulte la sección [Información sobre Cloud Services](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) en la [introducción a Cloud Services](../cloud-services/cloud-services-choose-me.md)).
    * Como con los roles de trabajo, se recomienda que se especifique `*` para la *versión del sistema operativo* de forma que los nodos se actualicen automáticamente; así no es necesario realizar ningún trabajo para las versiones recién publicadas. El principal caso de uso para seleccionar una versión específica del sistema operativo es asegurarse de la compatibilidad de las aplicaciones; debe ser posible realizar pruebas de compatibilidad con versiones anteriores antes de permitir la actualización de la versión. Después de la validación, se puede actualizar la *versión del sistema operativo* para el grupo e instalar la nueva imagen del sistema operativo. Cualquier tarea en ejecución se interrumpirá y se volverá a poner en cola.

Consulte la sección [Cuenta](#account) para obtener información acerca de cómo establecer el modo de asignación del grupo al crear una cuenta de Batch.

<a id="custom-images-for-virtual-machine-pools" class="xliff"></a>

#### Imágenes personalizadas de grupos de máquinas virtuales

Para utilizar imágenes personalizadas para los grupos de máquinas virtuales, cree la cuenta de Batch con la configuración de la cuenta de suscripción de usuario. Con esta configuración, los grupos de Batch se asignan en la suscripción en la que reside la cuenta. Consulte la sección [Cuenta](#account) para obtener información acerca de cómo establecer el modo de asignación del grupo al crear una cuenta de Batch.

Para crear un grupo de configuración de máquinas virtuales con una imagen personalizada, necesitará una o varias cuentas de Azure Storage estándares para almacenar imágenes VHD personalizadas. Las imágenes personalizadas se almacenan como blobs. Para hacer referencia a sus imágenes personalizadas al crear un grupo, especifique los URI de los blobs VHD de imágenes personalizadas para la propiedad [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) de la propiedad [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

Asegúrese de que las cuentas de almacenamiento cumplen los criterios siguientes:   

- Las cuentas de almacenamiento que contienen los blobs VHD de imágenes personalizadas deben estar en la misma suscripción que la cuenta de Batch (la suscripción de usuario).
- Las cuentas de almacenamiento especificadas deben estar en la misma región que la cuenta de Batch.
- Actualmente, solo se admiten las cuentas de almacenamiento estándar. En un futuro, se admitirá Azure el almacenamiento Premium de Azure.
- Puede especificar una cuenta de almacenamiento con varios blobs VHD personalizados o varias cuentas de almacenamiento, que tenga cada una un único blob. Se recomienda usar varias cuentas de almacenamiento para obtener un mejor rendimiento.
- Un único blob VHD de imágenes personalizadas puede admitir hasta 40 instancias de máquinas virtuales Linux o 20 instancias de máquinas virtuales Windows. Deberá crear copias del blob VHD para crear grupos con más máquinas virtuales. Por ejemplo, un grupo con 200 máquinas virtuales Windows necesita 10 blobs VHD únicos especificados para la propiedad **osDisk**.

Cuando crea un grupo, debe seleccionar la opción **nodeAgentSkuId** apropiada, en función del sistema operativo de la imagen base de su VHD. Puede obtener una asignación de identificadores de SKU de agentes de nodos disponibles a sus referencias de imágenes del sistema operativo mediante una llamada a la operación [List supported node agent SKUs](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) (Lista de SKU admitidas de agentes de nodos).

Para crear un grupo a partir de una imagen personalizada mediante Azure Portal:

1. Vaya a la cuenta de Batch en Azure Portal.
2. En la hoja **Configuración**, seleccione el elemento de menú **Grupos**.
3. En la hoja **Grupos**, seleccione el comando **Agregar**, tras lo que aparecerá la hoja **Agregar grupo**.
4. Seleccione **Imagen personalizada (Linux/Windows)** en la lista desplegable **Tipo de imagen**. El portal muestra el selector **Imagen personalizada**. Elija uno o más VHD en el mismo contenedor y haga clic en el botón **Seleccionar**. 
    En el futuro, se agregará compatibilidad con varios VHD de diferentes cuentas de almacenamiento y contenedores.
5. Seleccione el valor correcto de **Publicador/Oferta/SKU** para los VHD personalizados, elija el modo de **Almacenamiento en caché** que desee y rellene el resto de los parámetros para el grupo.
6. Para comprobar si un grupo se basa en una imagen personalizada, vea la propiedad **Operating System** en la sección del resumen de recursos de la hoja **Grupo**. El valor de esta propiedad debe ser **Custom VM image**.
7. Todos los VHD personalizados asociados a un grupo se muestran en la hoja **Propiedades** del grupo.

<a id="compute-node-type-and-target-number-of-nodes" class="xliff"></a>

### Tipo de nodo de proceso y número de nodos de destino

Al crear un grupo, puede especificar los tipos de nodos de ejecución que le interesan y un número de destino para cada uno. Los dos tipos de nodos de ejecución son los siguientes:

- **Nodos de ejecución dedicados.** Los nodos de ejecución dedicados están reservados para las cargas de trabajo que usted tenga. Son más caros que los nodos de prioridad baja, pero se garantiza que nunca se verán reemplazados.

- **Nodos de ejecución de prioridad baja.** Los nodos de prioridad baja aprovechan la capacidad sobrante en Azure para ejecutar las cargas de trabajo de Batch. Los nodos de prioridad baja son menos costosos por hora que los nodos dedicados y habilitan las cargas de trabajo que requieren una gran potencia de proceso. Para obtener más información, vea [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Usar máquinas virtuales de prioridad baja con Batch).

    Los nodos de ejecución de prioridad baja pueden verse reemplazados cuando Azure no tiene una capacidad sobrante suficiente. Si se reemplaza un nodo mientras ejecuta tareas, las tareas se vuelven a poner en cola y se ejecutan de nuevo en cuanto está disponible un nodo de ejecución. Los nodos de prioridad baja son una buena opción para las cargas de trabajo en las que el tiempo de finalización del trabajo es flexible y el trabajo se distribuye entre muchos nodos. Antes de decidirse a usar nodos de prioridad baja para un escenario, asegúrese de que los trabajos perdidos debido a la preferencia sean mínimos y fáciles de volver a crear.

    Los nodos de ejecución de prioridad baja solo están disponibles para las cuentas de Batch creadas con el modo de asignación de grupo establecido en **Servicio de Batch**.

Puede tener nodos de ejecución de prioridad baja y dedicados en el mismo grupo. Cada tipo de nodo (prioridad baja y dedicado) tiene su propia configuración de destino, para la que puede especificar el número deseado de nodos. 
    
El número de nodos de ejecución se conoce como *destino* porque, en algunas situaciones, es posible que el grupo no alcance el número deseado de nodos. Por ejemplo, un grupo podría no alcanzar el destino si alcanza primero la [cuota de núcleos](batch-quota-limit.md) de la cuenta de Batch. O bien, el grupo podría no alcanzar el destino si ha aplicado una fórmula de escalado automático al grupo que limita el número máximo de nodos.

Para obtener información sobre los precios de los nodos de ejecución de prioridad baja y dedicados, vea [Precios de Batch](https://azure.microsoft.com/pricing/details/batch/).

<a id="size-of-the-compute-nodes" class="xliff"></a>

### Tamaño de los nodos de proceso

**configuración de Servicios en la nube** aparecen en [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md). Batch admite todos los tamaños de Cloud Services excepto `ExtraSmall`, `STANDARD_A1_V2` y `STANDARD_A2_V2`.

Los tamaños de los nodos de proceso de la **configuración de máquina virtual** se muestran en [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/linux/sizes.md) y [Tamaños de las máquinas virtuales Windows en Azure](../virtual-machines/windows/sizes.md). Lote admite todos los tamaños de máquina virtual de Azure excepto `STANDARD_A0` y aquellos con almacenamiento premium (series `STANDARD_GS`, `STANDARD_DS` y `STANDARD_DSV2`).

Al seleccionar un tamaño de nodo de proceso, tenga en cuenta las características y los requisitos de las aplicaciones que se van a ejecutar en los nodos. Aspectos tales como si la aplicación es multiproceso y cuánta memoria consume pueden ayudar a determinar el tamaño de nodo más adecuado y rentable. Normalmente, se selecciona un tamaño de nodo bajo el supuesto de que se ejecutará una sola tarea en un nodo cada vez. No obstante, es posible [ejecutar en paralelo](batch-parallel-node-tasks.md) varias tareas y, por tanto, varias instancias de la aplicación, en varios nodos de proceso durante la ejecución del trabajo. En este caso, es habitual elegir un tamaño de nodo más grande para acomodar el aumento de la demanda por la ejecución de tareas en paralelo. Para más información, consulte [Directiva de programación de tareas](#task-scheduling-policy).

Todos los nodos de un grupo son del mismo tamaño. Si va a ejecutar aplicaciones con requisitos del sistema o niveles de carga diferentes, es recomendable usar grupos separados.

<a id="scaling-policy" class="xliff"></a>

### Directiva de escalado

Para cargas de trabajo dinámicas, puede escribir y aplicar una [fórmula de escalado automático](#scaling-compute-resources) a un grupo. El servicio Lote evalúa periódicamente la fórmula y ajusta el número de nodos dentro del grupo en función de los diversos parámetros de grupo, trabajo y tarea que especifique.

<a id="task-scheduling-policy" class="xliff"></a>

### Directiva de programación de tareas

La opción de configuración [Máximo de tareas por nodo](batch-parallel-node-tasks.md) determina la cantidad máxima de tareas que se pueden ejecutar en paralelo en cada nodo de proceso del grupo.

La configuración predeterminada especifica que se ejecuta una sola tarea al mismo tiempo en un nodo. Sin embargo, hay situaciones en las que resulta ventajoso que se ejecuten simultáneamente dos o más tareas en un nodo. Consulte el [escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo sobre [tareas simultáneas de nodo](batch-parallel-node-tasks.md) para ver cómo puede beneficiarse de la ejecución de varias tareas por nodo.

También puede especificar un *tipo de relleno* que determina si el servicio Lote distribuye las tareas uniformemente entre todos los nodos de un grupo o llena cada uno con el número máximo de tareas antes de asignar tareas a otro.

<a id="communication-status-for-compute-nodes" class="xliff"></a>

### Estado de comunicación para nodos de proceso

En la mayoría de los escenarios, las tareas funcionan de forma independiente y no necesitan comunicarse entre sí. Sin embargo, hay algunas aplicaciones en las que las tareas deben comunicarse, como en los [escenarios MPI](batch-mpi.md).

Puede configurar un grupo que permita la **comunicación entre nodos**, de manera que los nodos de un grupo se puedan comunicar en tiempo de ejecución. Cuando se habilita la comunicación entre nodos, los nodos en grupos de configuración de Servicios en la nube pueden comunicarse entre sí en los puertos superiores a 1100, mientras que los grupos de configuración de máquina virtual no restringen el tráfico en ningún puerto.

Tenga en cuenta que habilitar la comunicación entre nodos también afecta a la colocación de los nodos dentro de los clústeres y puede limitar el número máximo de nodos en un grupo a causa de las restricciones de implementación. Si la aplicación no requiere comunicación entre los nodos, el servicio Lote puede asignar un número potencialmente alto de nodos al grupo desde muchos clústeres y centros de datos diferentes para permitir el aumento de la potencia del procesamiento paralelo.

<a id="start-tasks-for-compute-nodes" class="xliff"></a>

### Tareas de inicio para nodos de proceso

La *tarea de inicio* opcional se ejecuta en cada nodo cuando dicho nodo se une al grupo y cada vez que se reinicia un nodo o se restablece su imagen inicial. La tarea de inicio resulta especialmente útil para preparar los nodos de proceso para la ejecución de tareas, como la instalación de las aplicaciones que las tareas ejecutan en los nodos de proceso.

<a id="application-packages" class="xliff"></a>

### paquetes de aplicación

Puede especificar los [paquetes de aplicación](#application-packages) que se implementarán en los nodos de proceso del grupo. Los paquetes de aplicación proporcionan una implementación simplificada y el control de las versiones de las aplicaciones que ejecutan las tareas. Los paquetes de aplicación que se especifiquen para un grupo se instalarán en todos los nodos que se unan al grupo cada vez que un nodo se reinicie o se restablezca la imagen inicial. Los paquetes de aplicación no se admiten actualmente en los nodos de proceso de Linux.

<a id="network-configuration" class="xliff"></a>

### Network configuration (Configuración de red)

Puede especificar la subred de una [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) de Azure en la que se deben crear los nodos de proceso del grupo. Consulte la sección [Configuración de la red del grupo](#pool-network-configuration) para más información.


<a id="job" class="xliff"></a>

## Trabajo
Un trabajo es una colección de tareas. El trabajo administra cómo sus tareas realizan el cálculo en los nodos de proceso de un grupo.

* El trabajo especifica el **grupo** en el que se va a ejecutar el trabajo. Puede crear un grupo para cada trabajo o usar uno solo para muchos de ellos. Puede crear un grupo para cada trabajo asociado con una programación de trabajo o para todos los trabajos asociados con ella.
* Puede especificar una **prioridad del trabajo**opcional. Cuando se envía un trabajo con una prioridad más alta que la de otros trabajos actualmente en curso, las tareas del trabajo de prioridad más alta se insertan en la cola por delante de las tareas del trabajo de prioridad más baja. No adelantarán a las tareas de trabajos de prioridad más baja que ya estén en ejecución.
* Puede utilizar **restricciones** de trabajo para fijar determinados límites para sus trabajos:

    Puede establecer un **tiempo de reloj máximo**, de modo que si un trabajo lleva más tiempo que el especificado, el trabajo y todas sus tareas se finalizarán.

    Lote puede detectar tareas con errores y después volver a intentarlas. Puede especificar el **número máximo de reintentos de tarea** como restricción. Puede incluso especificar si una tarea se debe reintentar *siempre* o no se debe reintentar *nunca*. Volver a intentar una tarea significa que la tarea se vuelve a poner en cola para ejecutarse de nuevo.
* La aplicación cliente puede agregar tareas a un trabajo o puede especificar una [tarea del administrador de trabajos](#job-manager-task). Una tarea del administrador de trabajos contiene la información necesaria para crear las tareas que se requieren para un trabajo. Estas tareas se ejecutan en uno de los nodos de proceso del grupo. El servicio Lote controla específicamente las tareas del administrador de trabajos: las pone en cola en cuanto se crea el trabajo y las reinicia si se produce un error. Se *requiere* una tarea del administrador de trabajos para los trabajos creados mediante una [programación de trabajo](#scheduled-jobs), ya que es la única manera de definir las tareas antes de que se cree una instancia del trabajo.
* De manera predeterminada, los trabajos permanecen en estado activo cuando se completan todas las tareas que contienen. Este comportamiento se puede cambiar, con el fin de que el trabajo finalice automáticamente cuando se completen todas sus tareas. Establezca la propiedad **onAllTasksComplete** ([OnAllTasksComplete][net_onalltaskscomplete] en .NET de Batch) en *terminatejob* para que el trabajo finalice automáticamente cuando todas sus tareas estén en estado completado.

    Tenga en cuenta que el servicio de Lote considera que un trabajo *sin* tareas tiene todas sus tareas completadas. Por lo tanto, esta opción se utiliza normalmente con una [tarea del administrador de trabajos](#job-manager-task). Si desea usar la finalización automática de trabajos sin un administrador de trabajos, debe establecer inicialmente la propiedad **onAllTasksComplete** de cada trabajo nuevo en *noaction* y, después, establecerla en *terminatejob*, pero solo después de que haya terminado de agregar tareas al trabajo.

<a id="job-priority" class="xliff"></a>

### prioridad del trabajo
Puede asignar una prioridad a los trabajos que cree en Lote. El servicio Lote usa el valor de prioridad del trabajo para determinar el orden de programación de trabajos dentro de una cuenta (esto no se debe confundir con un [trabajo programado](#scheduled-jobs)). Los valores de prioridad pueden oscilar entre -1000 y 1000, siendo -1000 la prioridad más baja y 1000 la más alta. Para actualizar la prioridad de un trabajo, llame a la operación [Actualizar las propiedades de un trabajo][rest_update_job] (REST de Batch) o modifique la propiedad [CloudJob.Priority][net_cloudjob_priority] (.NET de Batch).

Dentro de la misma cuenta, los trabajos de mayor prioridad tienen precedencia de programación sobre los trabajos con menor prioridad. Un trabajo con un valor de prioridad mayor en una cuenta no tiene precedencia de programación sobre otro trabajo con un valor de prioridad inferior de una cuenta diferente.

La programación de trabajos entre los grupos es independiente. Entre grupos diferentes, no se garantiza que un trabajo con una prioridad más alta se programe antes si el grupo asociado tiene pocos nodos inactivos. En el mismo grupo, los trabajos con el mismo nivel de prioridad tienen la misma probabilidad de ser programados.

<a id="scheduled-jobs" class="xliff"></a>

### Scheduled jobs
Las [programaciones de trabajos][rest_job_schedules] permiten crear trabajos recurrentes dentro del servicio Batch. Una programación de trabajo especifica cuándo se deben ejecutar los trabajos e incluye las especificaciones de los trabajos que se van a ejecutar. Puede especificar la duración de la programación (cuánto dura y cuándo está vigente), y con qué frecuencia se crean trabajos durante el período programado.

<a id="task" class="xliff"></a>

## Tarea
Una tarea es una unidad de cálculo que está asociada con un trabajo. Se ejecuta en un nodo. Las tareas se asignan a un nodo para su ejecución o se ponen en cola hasta que quede libre un nodo. Es decir, una tarea ejecuta uno o más programas o scripts en un nodo de proceso para llevar a cabo el trabajo necesario.

Cuando crea una tarea, puede especificar:

* La **línea de comandos** de la tarea. Se trata de la línea de comandos que ejecuta la aplicación o el script en el nodo de proceso.

    Es importante comprobar que la línea de comandos no se ejecute realmente en un shell. Por tanto, no se podrá beneficiar de forma nativa de las características de shell como la expansión de [variables de entorno](#environment-settings-for-tasks) (esto incluye `PATH`). Para beneficiarse de estas características, debe invocar el shell en la línea de comandos, por ejemplo, iniciando `cmd.exe` en nodos de Windows o `/bin/sh` en Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Si las tareas deben ejecutar una aplicación o un script que no se encuentra en `PATH` en el nodo o en las variables de entorno de referencia, invoque el shell explícitamente en la línea de comandos de la tarea.
* **archivos de recursos** que contienen los datos que se procesan. Estos archivos se copian automáticamente en el nodo desde Blob Storage en una cuenta de Azure Storage de uso general antes de que se ejecute la línea de comandos de la tarea. Para más información, consulte las secciones [Tarea de inicio](#start-task) y [Archivos y directorios](#files-and-directories).
* Las **variables de entorno** que requiere la aplicación. Para más información, consulte la sección [Configuración del entorno para las tareas](#environment-settings-for-tasks) .
* Las **restricciones** con que se debe ejecutar la tarea. Por ejemplo, las restricciones incluyen el tiempo máximo que se permite que la tarea se ejecute, el número máximo de veces que se debe volver a intentar una tarea si se produce un error y el tiempo máximo que se conservan los archivos en el directorio de trabajo de la tarea.
* **Application packages** que se implementan en el nodo de proceso en el que está programado que se ejecute la tarea. [Application packages](#application-packages) proporcionan una implementación simplificada y el control de las versiones de las aplicaciones que ejecutan las tareas. Los paquetes de aplicaciones de nivel de tarea son especialmente útiles en entornos de grupo compartido, donde los distintos trabajos se ejecutan en un grupo que no se elimina cuando se completa un trabajo. Si el trabajo tiene menos tareas que nodos en el grupo, los paquetes de aplicación de las tareas pueden minimizar la transferencia de datos, ya que la aplicación se implementa solo en los nodos que ejecutan tareas.

Además de las tareas que se definen para realizar cálculos en un nodo, el servicio de Lote también proporciona las siguientes tareas especiales:

* [Tarea de inicio](#start-task)
* [Tareas del Administrador de trabajos](#job-manager-task)
* [Tareas de preparación y liberación de trabajos](#job-preparation-and-release-tasks)
* [Tareas de instancias múltiples (MPI)](#multi-instance-tasks)
* [Dependencias de las tareas](#task-dependencies)

<a id="start-task" class="xliff"></a>

### Tarea de inicio
Al asociar una **tarea de inicio** con un grupo, puede preparar el entorno operativo de sus nodos. Por ejemplo, puede realizar acciones como instalar las aplicaciones que las tareas ejecutarán o iniciar procesos en segundo plano. La tarea de inicio se ejecuta cada vez que se inicia un nodo, siempre y cuando dicho nodo permanezca en el grupo. Aquí se incluye también la primera vez que el nodo se agrega al grupo o cuando se reinicia o se restablece su imagen inicial.

Una de las principales ventajas de la tarea de inicio es que contiene toda la información necesaria para configurar un nodo de proceso e instalar las aplicaciones necesarias para ejecutar tareas. Por lo tanto, aumentar el número de nodos en un grupo es tan simple como especificar el nuevo número de nodos de destino. La tarea de inicio proporciona al servicio Batch la información que se necesita para configurar los nuevos nodos y prepararlos para aceptar tareas.

Como con cualquier tarea de Azure Batch, se puede especificar una lista de **archivos de recursos** en [Azure Storage][azure_storage], además de una **línea de comandos** para ejecutar. El servicio Batch copia primero los archivos de recursos desde Azure Storage al nodo y después ejecuta la línea de comandos. Para una tarea de inicio de grupo, la lista de archivos contiene normalmente la aplicación de la tarea y sus dependencias.

Sin embargo, la tarea de inicio también puede incluir datos de referencia que se utilizarán en todas las tareas que se ejecuten en el nodo de proceso. Por ejemplo, la línea de comandos de una tarea de inicio podría realizar una operación `robocopy` para copiar los archivos de la aplicación (que se especificaron como archivos de recursos y se descargaron en el nodo) del [directorio de trabajo](#files-and-directories) de la tarea de inicio a la [carpeta compartida](#files-and-directories) y después ejecutar un archivo MSI o `setup.exe`.

Habitualmente, es conveniente que el servicio Lote espere a que la tarea de inicio finalice antes de considerar que el nodo está listo para asignarle tareas, pero este procedimiento se puede configurar.

Si una tarea de inicio da error en un nodo de proceso, el estado del nodo se actualiza para reflejar el error y no se le asigna al nodo ninguna tarea. Una tarea de inicio puede generar un error si existe un problema al copiar sus archivos de recursos desde el almacenamiento o si el proceso ejecutado mediante su línea de comandos devuelve un código de salida distinto de cero.

Si agrega una tarea de inicio a un grupo existente o la actualiza, debe reiniciar sus nodos de proceso para que la tarea de inicio se les aplique.

>[!NOTE]
> El tamaño total de una tarea de inicio debe ser menor o igual a 32 768 caracteres, incluidos los archivos de recursos y las variables de entorno. Para garantizar que la tarea de inicio cumple este requisito, puede usar uno de estos dos enfoques:
>
> 1. Puede utilizar paquetes de aplicación para distribuir las aplicaciones o los datos por los nodos del grupo de Batch. Para más información sobre los paquetes de aplicación, consulte [Implementación de aplicaciones en nodos de proceso con paquetes de aplicación de Batch](batch-application-packages.md).
> 2. Puede crear manualmente un archivo comprimido que contenga los archivos de aplicación. Cargue el archivo comprimido en Azure Storage como blob. Defina el archivo comprimido como archivo de recursos para la tarea de inicio. Antes de ejecutar la línea de comandos para la tarea de inicio, descomprima el archivo desde la línea de comandos. 
>
>    Para ello, puede usar la herramienta de archivado que prefiera. Debe incluir la herramienta que utilice para descomprimir el archivo como archivo de recursos para la tarea de inicio.
>
>

<a id="job-manager-task" class="xliff"></a>

### Tareas del Administrador de trabajos
Normalmente, se utiliza una **tarea del administrador de trabajos** para controlar y supervisar la ejecución del trabajo: por ejemplo, para crear y enviar las tareas de un trabajo, determinar las tareas adicionales que se deben ejecutar y determinar si el trabajo ha finalizado. Sin embargo, una tarea del administrador de trabajos no se limita a estas actividades. Es una tarea completa que puede realizar todas las acciones que se requieren para el trabajo. Por ejemplo, una tarea del administrador de trabajos podría descargar un archivo especificado como un parámetro, analizar el contenido de ese archivo y enviar tareas adicionales en función de ese contenido.

Una tarea del administrador de trabajos se inicia antes que cualquier otra. Ofrece las siguientes características:

* Se envía automáticamente como una tarea mediante el servicio Lote cuando se crea el trabajo.
* Está programada para ejecutarse antes que las otras tareas de un trabajo.
* Su nodo asociado es el último en eliminarse de un grupo cuando se reduce el tamaño del grupo.
* Su finalización se puede vincular a la finalización de todas las tareas en el trabajo.
* A una tarea del administrador de trabajos se le otorga la más alta prioridad cuando es necesario reiniciarla. Si un nodo inactivo no está disponible, el servicio Lote puede finalizar una de las otras tareas en ejecución del grupo para dejar espacio para la ejecución de la tarea del administrador de trabajos.
* Una tarea del Administrador de trabajos de un trabajo no tiene prioridad sobre las tareas de otros trabajos. Entre trabajos, solo se tienen en cuenta las prioridades de nivel de trabajo.

<a id="job-preparation-and-release-tasks" class="xliff"></a>

### Tareas de preparación y liberación de trabajos
El servicio Lote proporciona la tarea de preparación del trabajo para la configuración de ejecución previa al trabajo. Tareas de liberación del trabajo para el mantenimiento o la limpieza posteriores al trabajo.

* **Tarea de preparación del trabajo**: una tarea de preparación del trabajo se ejecuta en todos los nodos de proceso programados para ejecutar tareas, antes de que se ejecute ninguna otra tarea del trabajo. Por ejemplo, puede usar una tarea de preparación del trabajo para copiar los datos compartidos por todas las tareas, pero que sean únicos para el trabajo.
* **Tarea de liberación del trabajo**: cuando un trabajo se ha completado, la tarea de liberación del trabajo se ejecuta en cada nodo del grupo que ejecutó al menos una tarea. Por ejemplo, puede usar la tarea de liberación del trabajo para eliminar los datos copiados por la tarea de preparación del trabajo, o comprimir y cargar los datos del registro de diagnóstico.

Tanto para la tarea de preparación del trabajo como para las de liberación, puede especificar una línea de comandos que se ejecute cuando se invoque la tarea. Las tareas ofrecen características tales como descarga de archivos, ejecución con elevación de privilegios, variables de entorno personalizadas, duración máxima de ejecución, número de reintentos y tiempo de retención de archivo.

Para obtener más información sobre las tareas de preparación y liberación del trabajo, consulte [Ejecución de las tareas de preparación y realización de trabajos en los nodos de ejecución de Lote de Azure](batch-job-prep-release.md).

<a id="multi-instance-task" class="xliff"></a>

### Tarea de instancias múltiples
Una [tarea de instancias múltiples](batch-mpi.md) es aquella que está configurada para ejecutarse simultáneamente en varios nodos de proceso. Con tareas de instancias múltiples, puede habilitar escenarios de informática de alto rendimiento que requieren tener un grupo de nodos de proceso asignados juntos para procesar una carga de trabajo única como, por ejemplo, la interfaz de paso de mensajes (MPI).

Para ver una explicación detallada sobre la ejecución de trabajos de MPI en el servicio Lote mediante la biblioteca .NET de Lote, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Lote de Azure](batch-mpi.md).

<a id="task-dependencies" class="xliff"></a>

### Dependencias de las tareas
Las [dependencias de tareas](batch-task-dependencies.md), como el propio nombre implica, permiten especificar que una tarea depende de que se completen otras tareas antes de su ejecución. Esta característica proporciona compatibilidad con aquellas situaciones en las que una tarea "de bajada" consume el resultado de una tarea "del canal de subida", o cuando una tarea del canal de subida realiza alguna inicialización requerida por una tarea de bajada. Para utilizar esta característica, primero debe habilitar las dependencias de tareas en su trabajo de Lote. Luego, en cada tarea que dependa de otra (o de muchas otras), especifique las tareas de las que depende dicha tarea.

Con las dependencias de tareas, se pueden configurar escenarios como los siguientes:

* *taskB* depende de *taskA* (la ejecución de *taskB* no se iniciará hasta que *taskA* se haya completado).
* *taskC* depende de *taskA* y *taskB*.
* *taskD* depende de un intervalo de tareas, como las tareas de *1* a *10*, antes de ejecutarse.

Consulte [Dependencias de tareas en Azure Batch](batch-task-dependencies.md) y el código de ejemplo de [TaskDependencies][github_sample_taskdeps] en el repositorio de ejemplos de GitHub [azure-batch-samples][github_samples] para información más detallada acerca de esta característica.

<a id="environment-settings-for-tasks" class="xliff"></a>

## Configuración del entorno para las tareas
Cada tarea que ejecuta el servicio Batch tiene acceso a las variables de entorno que establece en los nodos de proceso. Esto incluye las variables de entorno definidas por el servicio Batch ([definidas por el servicio][msdn_env_vars]) y las variables de entorno personalizadas que se pueden definir para las tareas. Las aplicaciones y los scripts que ejecutan las tareas tienen acceso a estas variables de entorno durante la ejecución.

Puede establecer variables de entorno personalizadas en el nivel de tarea o de trabajo rellenando la propiedad *environment settings* de estas entidades. Por ejemplo, consulte la operación [Add a task to a job][rest_add_task] (Incorporación de una tarea a un trabajo) (API de REST de Batch) o las propiedades [CloudTask.EnvironmentSettings][net_cloudtask_env] y [CloudJob.CommonEnvironmentSettings][net_job_env] en .NET de Batch.

La aplicación cliente o el servicio pueden obtener las variables de entorno de una tarea, tanto las definidas por el servicio como las personalizadas, mediante la operación [Get information about a task][rest_get_task_info] (Obtener información acerca de una tarea) (REST de Batch) o accediendo a la propiedad [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET de Batch). Los procesos que se ejecutan en un nodo de proceso pueden acceder a estas y a otras variables de entorno en el nodo, por ejemplo mediante la conocida sintaxis de `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` (Linux).

Puede encontrar una lista completa de las variables de entorno definidas por el servicio en [Compute node environment variables][msdn_env_vars] (Variables de entorno en los nodos de proceso).

<a id="files-and-directories" class="xliff"></a>

## Archivos y directorios
Cada tarea tiene un *directorio de trabajo* en el que se crean ninguno o más archivos y directorios. Este directorio de trabajo se puede usar para almacenar el programa que va a ejecuta la tarea, los datos que procesa y el resultado del procesamiento que realiza. Todos los archivos y directorios de una tarea son propiedad del usuario de la tarea.

El servicio Lote expone parte del sistema de archivos en un nodo como *directorio raíz*. Las tareas pueden acceder al directorio raíz haciendo referencia a la variable de entorno `AZ_BATCH_NODE_ROOT_DIR` . Para obtener más información sobre el uso de variables de entorno, consulte [Configuración del entorno para las tareas](#environment-settings-for-tasks).

El directorio raíz contiene la siguiente estructura de directorio:

![Estructura de directorio de nodo de ejecución][1]

* **shared**: este directorio proporciona acceso de lectura y escritura a *todas* las tareas que se ejecutan en un nodo. Toda tarea que se ejecute en el nodo puede crear, leer, actualizar y eliminar archivos en este directorio. Las tareas pueden acceder a este directorio haciendo referencia a la variable de entorno `AZ_BATCH_NODE_SHARED_DIR` .
* **startup**: una tarea de inicio usa este directorio como directorio de trabajo. Aquí se almacenan todos los archivos que la tarea de inicio descarga en el nodo. La tarea de inicio puede crear, leer, actualizar y eliminar archivos en este directorio. Las tareas pueden acceder a este directorio haciendo referencia a la variable de entorno `AZ_BATCH_NODE_STARTUP_DIR` .
* **Tasks**: se crea un directorio para cada tarea que se ejecuta en el nodo. Se puede acceder a este directorio haciendo referencia a la variable de entorno `AZ_BATCH_TASK_DIR` .

    Dentro de cada directorio de la tarea, el servicio Lote crea un directorio de trabajo (`wd`) cuya ruta de acceso único se especifica con la variable de entorno `AZ_BATCH_TASK_WORKING_DIR`. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio. Este directorio se conserva en función de la restricción *RetentionTime* especificada para la tarea.

    `stdout.txt` y `stderr.txt`: estos archivos se escriben en la carpeta de la tarea durante la ejecución de esta.

> [!IMPORTANT]
> Cuando se quita un nodo del grupo, se quitan *todos* los archivos que están almacenados en el nodo.
>
>

<a id="application-packages" class="xliff"></a>

## paquetes de aplicación
La característica de [paquetes de aplicación](batch-application-packages.md) permite administrar e implementar fácilmente aplicaciones en los nodos de proceso de los grupos. Puede cargar y administrar fácilmente varias versiones de las aplicaciones que ejecutan las tareas, incluidos sus archivos binarios y auxiliares. A continuación, se pueden implementar automáticamente una o varias de estas aplicaciones en los nodos de proceso del grupo.

Los paquetes de aplicaciones se pueden especificar a nivel de grupo y de tarea. Si se especifican paquetes de aplicación de grupo, la aplicación se implementa en todos los nodos del grupo. Si se especifican paquetes de aplicación de tarea, la aplicación se implementa solo en los nodos que tienen programado ejecutar al menos una de las tareas del trabajo, inmediatamente antes que se ejecute la línea de comandos de la tarea.

Lote controla los detalles del trabajo con Almacenamiento de Azure para almacenar los paquetes de aplicación e implementarlos en los nodos de proceso, por lo que tanto el código como la sobrecarga de administración se pueden simplificar.

Para más información acerca de la característica de paquetes de aplicación, consulte [Implementación de aplicaciones con paquetes de aplicación de Lote de Azure](batch-application-packages.md).

> [!NOTE]
> Si se agregan paquetes de aplicación de grupo a un grupo *existente* , es preciso reiniciar los nodos de proceso de los paquetes de aplicación que se van a implementar en los nodos.
>
>

<a id="pool-and-compute-node-lifetime" class="xliff"></a>

## Vigencia de grupo y nodo de proceso
Al diseñar la solución de Lote de Azure, se debe tomar una decisión de diseño con respecto a cómo y cuándo se crean los grupos y cuánto tiempo se mantendrán disponibles los nodos de proceso dentro de esos grupos.

Por una parte, puede crear un grupo para cada trabajo que envíe el trabajo y eliminarlo a medida que se terminen de ejecutar las tareas. De esta manera, se maximiza su uso dado que los nodos solo se asignan cuando es necesario y se apagan en cuanto se vuelven inactivos. Aunque esto significa que el trabajo debe esperar a que se asignen los nodos, es importante tener en cuenta que las tareas se programarán para su ejecución tan pronto como los nodos estén disponibles de forma individual, se asignen y se haya completado la tarea de inicio. Batch *no* espera a que todos los nodos de un grupo estén disponibles antes de asignarles tareas. Esto garantiza la utilización máxima de todos los nodos disponibles.

Por otro lado, si lo más importante es que los trabajos se inicien inmediatamente, se puede crear un grupo antes de tiempo y hacer que sus nodos estén disponibles antes de que se envíen los trabajos. En este escenario, las tareas se pueden iniciar inmediatamente, pero los nodos permanecerán inactivos mientras esperan a que se les asignen las tareas.

Normalmente, se utiliza un enfoque combinado para controlar una carga variable, pero en curso. Puede tener un grupo al que se envíen varios trabajos y puede aumentar o reducir el número de nodos según la carga de trabajo (consulte [Escalado de recursos de procesos](#scaling-compute-resources) en la sección siguiente). Puede realizar esto de manera reactiva, según la carga actual o de forma anticipada si se puede predecir la carga.

<a id="pool-network-configuration" class="xliff"></a>

## Configuración de la red del grupo

Al crear un grupo de nodos de proceso en Azure Batch, puede especificar el identificador de subred de una [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) de Azure en la que se deben crear los nodos de proceso del grupo.

* La red virtual debe:

   * Estar en la misma **región** de Azure que la cuenta de Azure Batch.
   * Estar en la misma **suscripción** que la cuenta de Azure Batch.

* El tipo de red virtual que se admite depende de la forma en que se asignan los grupos en la cuenta de Batch:
    - Si la cuenta de Batch se creó con su propiedad **poolAllocationMode** establecida en 'BatchService', la red virtual especificada debe ser una red virtual clásica.
    - Si la cuenta de Batch se creó con su propiedad **poolAllocationMode** establecida en 'UserSubscription', la red virtual especificada puede ser una red virtual clásica o una red virtual de Azure Resource Manager. Los grupos se deben crear con una configuración de máquina virtual para usar una red virtual. Los grupos creados con una configuración de servicio en la nube no se admiten.

* Si la cuenta de Batch se creó con su propiedad **poolAllocationMode** establecida en 'BatchService', debe proporcionar permisos para la entidad de servicio de Batch acceda a la red virtual. La entidad de servicio de Batch, denominada "Microsoft Azure Batch" o "MicrosoftAzureBatch", debe tener al rol de [control de acceso basado en roles (RBAC) Colaborador de la máquina virtual clásica](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/#classic-virtual-machine-contributor) para la red virtual especificada. Si no se proporciona el rol de RBAC especificado, el servicio Batch devuelve 400 (Solicitud incorrecta).

* La subred especificada debe disponer de suficientes **direcciones IP** libres para alojar el número total de nodos de destino; es decir, la suma de las propiedades `targetDedicatedNodes` y `targetLowPriorityNodes` del grupo. Si la subred no tiene suficientes direcciones IP libres, el servicio Batch asigna parcialmente los nodos de proceso en el grupo y devuelve un error de cambio de tamaño.

* La subred especificada debe permitir la comunicación del servicio Batch para poder programar tareas en los nodos de proceso. Si un **grupo de seguridad de red (NSG)** asociado a la red virtual deniega la comunicación con los nodos de proceso, el servicio Batch establece el estado de dichos nodos en **No utilizable**.

* Si la red virtual especificada tiene grupos de seguridad de red (NSG) asociados, será preciso habilitar varios puertos del sistema reservados para las comunicaciones entrantes. Para los grupos creados con una configuración de máquina virtual, habilite los puertos 29876 y 29877, así como el puerto 22 para Linux y el 3389 para Windows. Para los grupos creados con una configuración de servicio en la nube, habilite los puertos 10100, 20100 y 30100. Además, habilite las conexiones salientes a Azure Storage en el puerto 443.

    En la tabla siguiente se describen los puertos de entrada que debe habilitar para los grupos que ha creado con la configuración de la máquina virtual:

    |    Puertos de destino    |    Dirección IP de origen      |    ¿Agrega Batch los grupos de seguridad de red?    |    ¿Es necesario para que la máquina virtual se pueda usar?    |    Acción del usuario   |
    |---------------------------|---------------------------|----------------------------|-------------------------------------|-----------------------|
    |    <ul><li>Para los grupos creados con la configuración de máquina virtual 29876 y 29877</li><li>Para los grupos creados con la configuración de servicio en la nube 10100, 20100 y 30100</li></ul>         |    Solo direcciones IP de rol de servicio de Batch |    Sí. Batch agrega los grupos de seguridad de red en el nivel de las interfaces de red (NIC) que se conectan a las máquinas virtuales. Estos grupos de seguridad de red permiten el tráfico únicamente desde las direcciones IP de rol de servicio de Batch. Aunque abra estos puertos para todo el sitio web, el tráfico se bloqueará en la NIC. |    Sí  |  No es necesario especificar un grupo de seguridad de red, ya que Batch solo permite direcciones IP de Batch. <br /><br /> Sin embargo, si lo hace, asegúrese de que estos puertos estén abiertos al tráfico entrante. <br /><br /> Si especifica * como dirección IP de origen en el grupo de seguridad de red, Batch seguirá agregando grupos de seguridad de red en el nivel de la NIC conectada a las máquinas virtuales. |
    |    3389, 22               |    Máquinas de usuario de depuración, para poder acceder a la máquina virtual de manera remota.    |    No                                    |    No                     |    Agregue grupos de seguridad de red si desea permitir el acceso remoto (RDP/SSH) a la máquina virtual.   |                 

    En la tabla siguiente se describe el puerto de salida que se debe habilitar para permitir el acceso a Azure Storage:

    |    Puertos de salida    |    Destino    |    ¿Agrega Batch los grupos de seguridad de red?    |    ¿Es necesario para que la máquina virtual se pueda usar?    |    Acción del usuario    |
    |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
    |    443    |    Almacenamiento de Azure    |    No    |    Sí    |    Si agrega algún grupo de seguridad de red, asegúrese de que este puerto esté abierto al tráfico saliente.    |


La necesidad de configuración adicional de la red virtual depende del modo de asignación de grupo de la cuenta de Batch.

<a id="vnets-for-pools-provisioned-in-the-batch-service" class="xliff"></a>

### Redes virtuales para grupos aprovisionados en el servicio Batch

En modo de asignación del servicio Batch, solo se pueden asignar grupos de **configuración de Cloud Services** a una red virtual. Además, la red virtual especificada debe ser una red virtual **clásica**. No se admiten redes virtuales creadas con el modelo de implementación de Azure Resource Manager.



* La entidad de servicio *MicrosoftAzureBatch* debe tener al rol de Control de acceso basado en roles (RBAC) [Colaborador de la máquina virtual clásica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) para la red virtual especificada. En Azure Portal:

  * Seleccione la **red virtual** y, a continuación, **Control de acceso (IAM)** > **Roles** > **Colaborador de la máquina virtual clásica** > **Agregar**.
  * Escriba "MicrosoftAzureBatch" en el cuadro **Buscar**.
  * Seleccione la casilla **MicrosoftAzureBatch**.
  * Seleccione el botón **Seleccionar**.



<a id="vnets-for-pools-provisioned-in-a-user-subscription" class="xliff"></a>

### Redes virtuales para grupos aprovisionados en una suscripción de usuario

En el modo de suscripción de asignación de usuario, solo los grupos de **configuración de máquina virtual** se admiten y se pueden asignar a una red virtual. Además, la red virtual especificada debe ser una red virtual basada en **Resource Manager**. No se admiten redes virtuales creadas con el modelo de implementación clásica.



<a id="scaling-compute-resources" class="xliff"></a>

## Escalado de recursos de procesos
Con el [escalado automático](batch-automatic-scaling.md), el servicio Lote puede ajustar dinámicamente el número de nodos de proceso de un grupo según la carga de trabajo actual y el uso de los recursos en el escenario de proceso. Esto le permite reducir el costo general de la ejecución de la aplicación usando solo los recursos que necesita, y liberando los que no.

Para habilitar el escalado automático, escriba una [fórmula de escalado automático](batch-automatic-scaling.md#automatic-scaling-formulas) y asóciela con un grupo. El servicio Lote usa la fórmula para determinar el número objetivo de nodos del grupo para el siguiente intervalo de escalado (un intervalo que puede configurar). Puede especificar la configuración de escalado automático para un grupo cuando lo cree o habilitar el escalado en un grupo más adelante. También puede actualizar la configuración de escalado en un grupo habilitado para el escalado.

Como ejemplo, quizás un trabajo requiera que envíe un gran número de tareas para que se ejecuten. Puede asignar al grupo una fórmula de escalado que ajuste el número de nodos del grupo en función del número actual de tareas en cola y la tasa de finalización de las tareas del trabajo. El servicio Batch evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo y del resto de ajustes de configuración de la fórmula. El servicio agrega nodos según sea necesario cuando haya un gran número de tareas en cola y quita los nodos cuando no haya ninguna tarea en cola o en ejecución.

Una fórmula de escalado puede basarse en las siguientes métricas:

* **Métricas de tiempo** : se basan en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.
* **Métricas de recursos** : se basan en el uso de CPU, ancho de banda y memoria y en el número de nodos.
* **Métricas de tareas**: se basan en el estado de la tarea, como *Activa* (en cola), *En ejecución* o *Completada*.

Cuando el escalado automático reduce el número de nodos de proceso en un grupo, debe considerar cómo administrará las tareas que se están ejecutando en el momento en que se realiza la operación de reducción. Para ello, Lote proporciona una *opción de desasignación de nodos* que se puede incluir en las fórmulas. Por ejemplo, puede especificar que las tareas en ejecución se detengan de inmediato, se detengan de inmediato y después se vuelvan a poner en cola para ejecutarlas en otro nodo o se dejen finalizar antes de que se quite el nodo del grupo.

Para obtener más información sobre cómo escalar automáticamente una aplicación, consulte [Escalado automático de nodos de ejecución en un grupo de Lote de Azure](batch-automatic-scaling.md).

> [!TIP]
> Para maximizar el uso de los recursos de proceso, establezca en cero el número objetivo de nodos al final de un trabajo, pero permita que las tareas en ejecución finalicen.
>
>

<a id="security-with-certificates" class="xliff"></a>

## Seguridad con certificados
Normalmente necesitará usar certificados al cifrar o descifrar información confidencial para las tareas, como la clave de una [cuenta de Azure Storage][azure_storage]. Para ello, puede instalar certificados en los nodos. Los secretos cifrados se pasan a las tareas mediante parámetros de línea de comandos o se insertan en uno de los recursos de tarea, y los certificados instalados se pueden usar para descifrarlos.

Use la operación [Agregar certificado][rest_add_cert] (REST de Batch) o el método [CertificateOperations.CreateCertificate][net_create_cert] (.NET de Batch) para agregar un certificado a una cuenta de Batch. Se puede asociar el certificado a un grupo nuevo o existente. Cuando se asocia un certificado a un grupo, el servicio Lote instala el certificado en cada nodo del grupo. El servicio Lote instala los certificados adecuados cuando se inicia el nodo, antes de iniciar ninguna tarea (incluidas la de inicio y la del Administrador de trabajos).

Si se agregan certificados a un grupo *existente* , es preciso reiniciar los nodos de proceso de los certificados que se van a aplicar a los nodos.

<a id="error-handling" class="xliff"></a>

## Control de errores
Esta operación puede ser necesaria para controlar los errores de las tareas y las aplicaciones en la solución de Lote.

<a id="task-failure-handling" class="xliff"></a>

### Control de errores de tareas
Los errores de las tareas se incluyen en estas categorías:

* **Errores de procesamiento previo**

    Si una tarea no se inicia, se establece un error de procesamiento previo para la misma.  

    Se pueden producir errores de procesamiento previo si se han movido los archivos de recursos de la tarea, la cuenta de Storage deja de estar disponible u ha surgido algún otro problema que ha impedido la copia correcta de los archivos en el nodo.

* **Errores de carga de archivos**

    Si por cualquier motivo no se realiza la carga de archivos especificados para una tarea, se establece un error de carga de archivos para la tarea.

    Los errores de carga de archivos se pueden producir si el SAS suministrado para acceder a Azure Storage no es válido o no proporciona permisos de escritura, si la cuenta de Storage no está disponible o si ha surgido cualquier otro problema que ha impedido la copia correcta de los archivos desde el nodo.    

* **Errores de aplicación**

    El proceso especificado por la línea de comandos de la tarea también puede presentar errores. Se considera que el proceso ha generado un error cuando el proceso ejecutado por la tarea devuelve un código de salida distinto de cero (consulte *Códigos de salida de la tarea* en la siguiente sección).

    Para los errores de aplicaciones, puede configurar el servicio Lote para volver a intentar automáticamente la tarea hasta un número especificado de veces.

* **Errores de restricción**

    Puede establecer una restricción que especifique la duración máxima de ejecución de un trabajo o una tarea con *maxWallClockTime*. Esto puede ser útil para terminar las tareas que no avanzan.

    Cuando se supere la cantidad máxima de tiempo, la tarea se marcará como *completada* pero el código de salida se establecerá en `0xC000013A` y el campo *schedulingError* se marcará como `{ category:"ServerError", code="TaskEnded"}`.

<a id="debugging-application-failures" class="xliff"></a>

### Depuración de errores de aplicación
* `stderr` y `stdout`

    Durante la ejecución, una aplicación produce resultados de diagnóstico que se pueden usar para la solución de problemas. Como se mencionó en la sección anterior llamada [Archivos y directorios](#files-and-directories), el servicio Batch escribe resultados de salida estándar y error estándar en los archivos `stdout.txt` y `stderr.txt` ubicados en el directorio de la tarea en el nodo de proceso. Para descargar estos archivos, puede usar el Portal de Azure, o bien uno de los SDK de Lote. Por ejemplo, puede recuperar estos y otros archivos para solucionar problemas con [ComputeNode.GetNodeFile][net_getfile_node] y [CloudTask.GetNodeFile][net_getfile_task] en la biblioteca .NET de Batch.

* **Códigos de salida de la tarea**

    Como ya se mencionó anteriormente, el servicio Lote marcará una tarea como con errores si el proceso ejecutado por la tarea devuelve un código de salida distinto de cero. Cuando una tarea ejecuta un proceso, Lote rellena la propiedad de código de salida de la tarea con el *código de retorno del proceso*. Es importante tener en cuenta que el código de salida de una tarea **no** lo determina el servicio Batch. El código de salida de una tarea lo determina el proceso en sí o el sistema operativo en que se ejecuta el proceso.

<a id="accounting-for-task-failures-or-interruptions" class="xliff"></a>

### Consideraciones sobre errores o interrupciones
En ocasiones, las tareas pueden presentar errores o interrumpirse. Se podría producir un error en la propia aplicación de la tarea, se podría reiniciar el nodo donde se ejecuta la tarea o se podría quitar el nodo del grupo durante una operación de cambio de tamaño si la directiva de desasignación del grupo está configurada para quitar los nodos de inmediato, sin esperar a que finalicen las tareas. En todos los casos, el servicio Lote puede volver a poner la tarea automáticamente en la cola para ejecutarla en otro nodo.

También es posible que un problema intermitente haga que una tarea deje de responder o tarde demasiado tiempo en ejecutarse. Puede establecer el intervalo de ejecución máximo para una tarea. Si se supera, el servicio Batch interrumpe la aplicación de la tarea.

<a id="connecting-to-compute-nodes" class="xliff"></a>

### Conexión a los nodos de proceso
Puede realizar tareas adicionales de depuración y solución de problemas si se inicia una sesión remota en un nodo de proceso. Puede usar el Portal de Azure para descargar un protocolo de escritorio remoto (RDP) para los nodos de Windows y obtener información de conexión de Secure Shell (SSH) para los nodos de Linux. También puede hacer esto con las API de Batch, como [.NET de Batch][net_rdpfile] o [Python de Batch](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Para conectarse a un nodo a través de RDP o SSH, primero debe crear un usuario en el nodo. Para ello, puede usar Azure Portal, [agregar una cuenta de usuario a un nodo][rest_create_user] con la API de REST de Batch y llamar al método [ComputeNode.CreateComputeNodeUser][net_create_user] en .NET de Batch o al método [add_user][py_add_user] en el módulo Python de Batch.
>
>

<a id="troubleshooting-problematic-compute-nodes" class="xliff"></a>

### Solución de problemas de nodos de proceso problemáticos
Si algunas de las tareas producen errores, el servicio o la aplicación de cliente de Lote pueden examinar los metadatos de las tareas con errores para identificar un nodo con un comportamiento incorrecto. Cada nodo de un grupo recibe un identificador único y el nodo en el que se ejecuta una tarea se incluye en los metadatos de la tarea. Una vez que haya identificado un "nodo problemático", puede llevar a cabo varias acciones con él:

* **Reiniciar el nodo** ([REST][rest_reboot] | [.NET][net_reboot])

    Algunas veces, reiniciar el nodo puede solucionar problemas latentes tales como los procesos bloqueados. Tenga en cuenta que si su grupo usa una tarea de inicio o el trabajo usa una tarea de preparación de trabajo, se ejecutarán cuando el nodo se reinicie.
* **Restablecer la imagen inicial del nodo** ([REST][rest_reimage] | [.NET][net_reimage])

    Esto reinstala el sistema operativo en el nodo. Al igual que al reiniciar un nodo, las tareas de inicio y las tareas de preparación del trabajo se vuelven a ejecutar después de restablecer la imagen inicial del nodo.
* **Quitar el nodo del grupo** ([REST][rest_remove] | [.NET][net_remove])

    A veces es necesario quitar completamente el nodo del grupo.
* **Deshabilitar la programación de tareas en el nodo** ([REST][rest_offline] | [.NET][net_offline])

    Esto desconecta el nodo para que no se le asigne ninguna tarea adicional, pero permite que el nodo permanezca en ejecución y en el grupo. Esto permite seguir investigando la causa de los errores sin perder los datos de la tarea con errores, y sin que el nodo produzca más errores en la tarea. Por ejemplo, puede deshabilitar la programación de tareas en el nodo y después [iniciar una sesión remota](#connecting-to-compute-nodes) para examinar los registros de eventos del nodo o solucionar otros problemas. Una vez que haya terminado la investigación, puede volver a conectar el nodo, para lo que debe habilitar la programación de tareas ([REST][rest_online] | [.NET][net_online]), o bien puede realizar una de las otras acciones mencionadas antes.

> [!IMPORTANT]
> Con cada una de las acciones descritas en esta sección (reiniciar, restablecer la imagen inicial, quitar, deshabilitar la programación de tareas), puede especificar cómo se controlan las tareas que se están ejecutando en el nodo al realizar la acción. Por ejemplo, cuando deshabilita la programación de tareas en un nodo con la biblioteca de cliente .NET de Batch, puede indicar un valor de enumeración [DisableComputeNodeSchedulingOption][net_offline_option] para especificar si se finalizarán las tareas en ejecución (**Terminate**), si se volverán a poner en la cola para que se programen en otros nodos (**Requeue**) o si se permitirá que se completen antes de realizar la acción (**TaskCompletion**).
>
>

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
* Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.
* Recorra paso a paso una aplicación de Lote de ejemplo en [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md). También existe una [versión para Python](batch-python-tutorial.md) del tutorial, en la que se ejecuta una carga de trabajo en nodos de proceso de Linux.
* Descargue y compile el proyecto de ejemplo [Batch Explorer][github_batchexplorer] para usarlo mientras desarrolla sus soluciones de Batch. Mediante Batch Explorer puede realizar lo siguiente y mucho más:

  * Supervisar y manipular grupos, trabajos y tareas dentro de su cuenta de Lote
  * Descargar `stdout.txt`, `stderr.txt`, y otros archivos de los nodos
  * Crear usuarios en los nodos y descargar archivos RDP para el inicio de sesión remoto.
* Aprenda a [crear grupos de nodos de proceso de Linux](batch-linux-nodes.md).
* Visite el [foro de Azure Batch][batch_forum] en MSDN. El foro es un buen lugar donde plantear preguntas tanto si está aprendiendo como si tiene experiencia en el uso de Lote.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

