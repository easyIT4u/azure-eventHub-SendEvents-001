# azure-eventHub-SendEvents-001
Habilitar en el portal de Azure un recurso Event Hub para envio de mensajes mediante un aplicativo JAVA+Springboot


<picture>
 <source media="(prefers-color-scheme: dark)" srcset="YOUR-DARKMODE-IMAGE">
 <source media="(prefers-color-scheme: light)" srcset="YOUR-LIGHTMODE-IMAGE">
 <img alt="" src="https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png">
</picture>

## Objetivo del curso:
<details>
<summary>Indice</summary>

| # | Tema |
|-----:|-----------|
|     1| Crear un projecto Maven|
|     2| Crear un recurso Event en Azure |
|     3| Conectarse desde java al Event Hub y producir un mensaje |

</details>

## 1.- Crear un projecto Maven

Dentro de la linea de comando de su ordenador 
```
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=mx.com.easyit4u -DartifactId=AzureSDKEventHubApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

```

### 1.2.- Agregar dependecias al projecto

Dentro del proyecto Maven agregar la siguiente dependencia en el archivo pom.xml

```
<dependency>
		    <groupId>com.azure</groupId>
		    <artifactId>azure-messaging-eventhubs</artifactId>
		    <version>5.15.0</version>
		</dependency>
```

## 2.- Crear un recurso Event en Azure

https://github.com/easyIT4u/azure-eventHub-SendEvents-001/blob/main/img/azure-eventhub-send-001.png

### 1.3.- Crear un grupo de cursos 

Comando:
echo "Creating $resourceGroup in $location..."
az group create --name $resourceGroup --location "$location" --tags $tag


### 1.4.- Crear un servidor PostgreSQL en el grupo de recursos
# El nombre de un servidor se asigna al nombre DNS y, por lo tanto, debe ser globalmente único en Azure.

Comando:
echo "Creating $server in $location..."
az postgres server create --name $server --resource-group $resourceGroup --location "$location" --admin-user $login --admin-password $password --sku-name $sku

### 1.5.- Configurar una regla en el servidor firewall  

Comando:
echo "Configuring a firewall rule for $server for the IP address range of $startIp to $endIp"
az postgres server firewall-rule create --resource-group $resourceGroup --server $server --name AllowIps --start-ip-address $startIp --end-ip-address $endIp


### 1.6.- Listar las reglas en el servidor firewall 

Comando:
echo "List of server-based firewall rules for $server"
az postgres server firewall-rule list --resource-group $resourceGroup --server-name $server
# You may use the switch `--output table` for a more readable table format as the output.

### 1.7.- Detener el uso SSL y Activar acceso a recursos de Azure

Dentro del portal de azure en la la congiguraciones de seguridad de la base datos tener el uso de SSL y activar el acceso a recursos de Azure

> [!IMPORTANT]
> Para fines practicos de este curso se elimina el uso del SSL, pero bajo ninguna circustancia  se recomienda que ningun entorno previo o productivo carazca de esta propiedad.


## 2.- Get the connection information

Comando:
az postgres server show --resource-group $resourceGroup --name $server

Ejemplo:
```
{
  "administratorLogin": "azureuser",
  "byokEnforcement": "Disabled",
  "earliestRestoreDate": "2024-02-03T15:59:51.867000+00:00",
  "fullyQualifiedDomainName": "msdocs-postgresql-server-84763490.postgres.database.azure.com",
  "id": "/subscriptions/0641a35d-f3bd-48d1-afab-4b5a00ad9d31/resourceGroups/msdocs-postgresql-rg-84763490/providers/Microsoft.DBforPostgreSQL/servers/msdocs-postgresql-server-84763490",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "eastus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "msdocs-postgresql-server-84763490",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "msdocs-postgresql-rg-84763490",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "11"
}

```

### 2.1.- Conectar a la Base de Datos PostgreSQL usando psql

Commando:
psql --host=msdocs-postgresql-server-84763490.postgres.database.azure.com --port=5432 --username=azureuser@msdocs-postgresql-server-84763490 --dbname=postgres  -v sslmode=true

> [!TIP]
> Colocar el password de la base datos con el valor del variable $password


### 2.1.- Crear una  Base de Datos, tablas, insertar registro en PostgreSQL usando sql

2.2.2.- Crear una base de datos
```
  CREATE DATABASE mypgsqldb;
```  
Para conectarse a la base de datos recien creada
```
  \c mypgsqldb
```

2.2.3.- Crear una tabla
```
  CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
  );
```
Conectarse a la tabla recien creada
```
  \dt
```

INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);



### Eliminar recursos
Al concluir se deben eliminar todos los servicios generados para no incurrir en un costo por uso

Eliminar base de datos
```
az postgres down --delete-group
```

Eliminar gruop de recursos
```
az group delete --name $resourceGroup
```


---
> Curso basado en documentación Azure
> https://learn.microsoft.com/en-us/azure/postgresql/single-server/tutorial-design-database-using-azure-cli

— easyIT4y Learninng - 2024

