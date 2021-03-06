---
title: "Personalización de una máquina virtual Linux en el primer arranque en Azure | Microsoft Docs"
description: "Aprenda a usar cloud-init y Key Vault para personalizar máquinas virtuales Linux la primera vez que arrancan en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 8d7d69fb6a27dfac3c96fb6bc811cad6a531e24e
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Personalización de una máquina virtual Linux en el primer arranque
En un tutorial anterior, ha aprendido cómo acceder mediante SSH a una máquina virtual y a instalar NGINX de forma manual. Para crear máquinas virtuales de manera rápida y coherente, alguna forma de automatización suele ser deseable. Un enfoque común para personalizar una máquina virtual en el primer arranque es usar el comando [cloud-init](https://cloudinit.readthedocs.io). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un archivo de configuración cloud-init
> * Crear una máquina virtual que usa un archivo cloud-init
> * Ver una aplicación Node.js en ejecución una vez creada la máquina virtual
> * Usar Key Vault para almacenar de forma segura certificados
> * Automatizar implementaciones seguras de NGINX con cloud-init


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).  



## <a name="cloud-init-overview"></a>Introducción a cloud-init
[cloud-init](https://cloudinit.readthedocs.io) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Como cloud-init se ejecuta durante el proceso de arranque inicial, no hay pasos adicionales o agentes requeridos que aplicar a la configuración.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. Por el contrario, puede definir una lista de paquetes para instalar y cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

Trabajamos con nuestros asociados para que cloud-init se incluya y funcione en las imágenes que estos proporcionan a Azure. En la tabla siguiente se describe la disponibilidad actual de cloud-init en imágenes de la plataforma de Azure:

| Alias | Publicador | Oferta | SKU | Versión |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |más reciente |
| CoreOS |CoreOS |CoreOS |Stable |más reciente |


## <a name="create-cloud-init-config-file"></a>Creación de un archivo de configuración cloud-init
Para ver cloud-init en acción, cree una máquina virtual que instale NGINX y ejecute una aplicación Node.js sencilla "Hello World". Con la siguiente configuración de cloud-init se instalan los paquetes necesarios, se crea una aplicación Node.js y luego se inicializa e inicia la aplicación.

Cree un archivo denominado *cloud-init.txt* y pegue la siguiente configuración:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Para más información sobre las opciones de configuración de cloud-init, consulte los [ejemplos de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)]

## <a name="create-virtual-machine"></a>Create virtual machine
Antes de poder crear una máquina virtual, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupAutomate* en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupAutomate --location eastus
```

Ahora cree una máquina virtual con el comando [az vm create](/cli/azure/vm#create). Use el parámetro `--custom-data` para pasar su archivo de configuración cloud-init. Proporcione la ruta de acceso completa a la configuración de *cloud-init.txt* si guardó el archivo fuera de su directorio de trabajo actual. En el ejemplo siguiente se crea una VM denominada *myAutomatedVM*:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Transcurren unos minutos hasta que la máquina virtual se crea, los paquetes se instalan y la aplicación se inicia. Cuando se haya creado la máquina virtual, anote el valor `publicIpAddress` mostrado por la CLI de Azure. Esta dirección se usa para acceder a la aplicación Node.js mediante un explorador web.

Para permitir que el tráfico web llegue a la máquina virtual, abra el puerto 80 desde Internet con el comando [az vm open-port](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Prueba de la aplicación web
Ahora puede abrir un explorador web y escribir *http://<publicIpAddress>* en la barra de direcciones. Proporcione su propia dirección IP pública obtenida del proceso de creación de la máquina virtual. Su aplicación Node.js se muestra como en el ejemplo siguiente:

![Ver sitio de NGINX en funcionamiento](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Inserción de certificados desde Key Vault
En esta sección opcional se muestra cómo puede almacenar certificados de forma segura en Azure Key Vault e insertarlos durante la implementación de máquinas virtuales. En lugar de usar una imagen personalizada que incluye los certificados ya preparados, este proceso garantiza que los certificados más actualizados se insertan en una máquina virtual en el primer arranque. Durante el proceso, el certificado nunca deja la plataforma de Azure ni se expone en un script, el historial de la línea de comandos o una plantilla.

Azure Key Vault protege claves y secretos criptográficos, como certificados y contraseñas. Key Vault ayuda a agilizar el proceso de administración de claves y le permite mantener el control de las claves que acceden a sus datos y los cifran. En este escenario se presentan algunos conceptos de Key Vault para crear y usar un certificado, si bien no es una introducción exhaustiva sobre cómo usar Key Vault.

Los pasos siguientes muestran cómo puede:

- Crear una instancia de Azure Key Vault
- Generar o cargar un certificado en Key Vault
- Crear un secreto a partir del certificado para insertarlo en una máquina virtual
- Crear una máquina virtual e insertar el certificado

### <a name="create-an-azure-key-vault"></a>Crear una instancia de Azure Key Vault
En primer lugar, cree una instancia de Key Vault con [az keyvault create](/cli/azure/keyvault#create) y habilítela para su uso al implementar una máquina virtual. Cada instancia de Key Vault requiere un nombre único, que debe estar todo en minúsculas. Reemplace *<mykeyvault>* en el siguiente ejemplo por su propio nombre único de Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generación de un certificado y su almacenamiento en Key Vault
Para usarlo en producción, debe importar un certificado válido firmado por un proveedor de confianza con [az keyvault certificate import](/cli/azure/certificate#import). En este tutorial, el ejemplo siguiente muestra cómo puede generar un certificado autofirmado con [az keyvault certificate create](/cli/azure/certificate#create) que usa la directiva de certificado predeterminada:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparación del certificado para usarlo con la máquina virtual
Para usar el certificado durante el proceso de creación de la máquina virtual, obtenga el id. del certificado con [az keyvault secret list-versions](/cli/azure/keyvault/secret#list-versions). Convierta el certificado con [az vm format-secret](/cli/azure/vm#format-secret). En el ejemplo siguiente se asigna la salida de estos comandos a las variables para facilitar su uso en los pasos siguientes:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Creación de la configuración de cloud-init para proteger NGINX
Cuando crea una máquina virtual, los certificados y las claves se almacenan en el directorio */var/lib/waagent/* protegido. Para automatizar el proceso de agregar el certificado a la máquina virtual y configurar NGINX, puede expandir la configuración de cloud-init del ejemplo anterior.

Cree un archivo denominado *cloud-init-secured.txt* y pegue la siguiente configuración:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Creación de una máquina virtual segura
Ahora cree una máquina virtual con el comando [az vm create](/cli/azure/vm#create). Los datos del certificado se insertan desde Key Vault con el parámetro `--secrets`. Como en el ejemplo anterior, se pasa la configuración de cloud-init con el parámetro `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Transcurren unos minutos hasta que la máquina virtual se crea, los paquetes se instalan y la aplicación se inicia. Cuando se haya creado la máquina virtual, anote el valor `publicIpAddress` mostrado por la CLI de Azure. Esta dirección se usa para acceder a la aplicación Node.js mediante un explorador web.

Para permitir que el tráfico web llegue a la máquina virtual, abra el puerto 443 desde Internet con el comando [az vm open-port](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Prueba de la aplicación web segura
Ahora puede abrir un explorador web y escribir *https://<publicIpAddress>* en la barra de direcciones. Proporcione su propia dirección IP pública obtenida del proceso de creación de la máquina virtual. Acepte la advertencia de seguridad si usó un certificado autofirmado:

![Aceptar la advertencia de seguridad del explorador web](./media/tutorial-automate-vm-deployment/browser-warning.png)

Se muestran el sitio de NGINX protegido y la aplicación Node.js, como en el ejemplo siguiente:

![Ver el sitio de NGINX seguro en funcionamiento](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha configurado las máquinas virtuales en el primer inicio con cloud-init. Ha aprendido a:

> [!div class="checklist"]
> * Crear un archivo de configuración cloud-init
> * Crear una máquina virtual que usa un archivo cloud-init
> * Ver una aplicación Node.js en ejecución una vez creada la máquina virtual
> * Usar Key Vault para almacenar de forma segura certificados
> * Automatizar implementaciones seguras de NGINX con cloud-init

Avanzar al siguiente tutorial para aprender a crear imágenes de máquina virtual personalizadas.

> [!div class="nextstepaction"]
> [Creación de imágenes personalizadas de máquinas virtuales](./tutorial-custom-images.md)

