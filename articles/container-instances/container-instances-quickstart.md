---
title: "Creación del primer contenedor de Azure Container Instances | Documentos de Azure"
description: Implemente y empiece a usar Azure Container Instances
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances

Azure Container Instances facilita la creación y administración de contenedores en Azure. En esta guía de inicio rápido, creará un contenedor en Azure y lo expondrá a Internet con una dirección IP pública. Esta operación se completa en un solo comando. En pocos segundos, verá lo siguiente en el explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Actualmente, los comandos de la CLI de Azure Container Instances solo están disponibles en Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Azure Container Instances son recursos de Azure y se deben colocar en un grupo de recursos de Azure, una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure. Dicho contenedor se puede exponer opcionalmente a Internet con una dirección IP pública. En este caso, se va a usar un contenedor que hospeda una aplicación web muy simple escrita en [Node.js](http://nodejs.org).

Actualmente, los comandos de la CLI de Azure Container Instances solo están disponibles en Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

En pocos segundos, debería obtener una respuesta a su solicitud. Inicialmente, el contenedor estará en un estado de **creación**, pero debería comenzar en pocos segundos. Para comprobar el estado, use el comando `show`:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

En la parte inferior de la salida, verá el estado de aprovisionamiento del contenedor y su dirección IP:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Una vez que el contenedor pasa al estado **Correcto**, se puede acceder a él desde el navegador mediante la dirección IP proporcionada. 

![Aplicación implementada mediante Azure Container Instances vista en el explorador][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extracción de los registros del contenedor

Para extraer los registros del contenedor que creó, utilice el comando `logs`:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Salida:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminación del contenedor

Cuando haya terminado con el contenedor, puede eliminarlo con el comando `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Todo el código del contenedor utilizado en esta guía de inicio rápido está disponible [en GitHub][app-github-repo], junto con su Dockerfile. Si desea intentar compilarlo usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
