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
|     3| Conección mensaje a Event Hub  |
|     4| Ejecutar aplicación |


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

Dentro del portal de Azure buscar Event Hub y crear un recurso de la siguiente manera

 <img alt="" src="https://github.com/easyIT4u/azure-eventHub-SendEvents-001/blob/main/img/azure-eventhub-send-001.png">

### 2.1.-
Crear un topico dentro del Event Hub

<img alt="" src="https://github.com/easyIT4u/azure-eventHub-SendEvents-001/blob/main/img/azure-eventhub-send-002.png">

<img alt="" src="https://github.com/easyIT4u/azure-eventHub-SendEvents-001/blob/main/img/azure-eventhub-send-003.png">

### 2.2.- Obtener cadena de conexión 
Dentro del espacio del event hub generado, se debe ingresar a las Directivas de acceso compartido(**Shared Access Policies**) en las directivas se debera seleccionar la policita **RootManageSharedAccessKey**, lo que hablitar la ventana de la directiva, copiar la Cadena de conexión principal


 <img alt="" src="https://github.com/easyIT4u/azure-eventHub-SendEvents-001/blob/main/img/azure-eventhub-send-004.png">


## 3.- Conección mensaje a Event Hub 

Dentro del proyecto maven geerado previamente se debe crear un clase Sender que contega el siguiene código
> [!TIP]
> Actualizar <Event Hubs namespace connection string> con la cadena de conexión en el espacio de nombres de Event Hubs.
> Actualizar <Event hub name> con el nombre del centro de eventos en el espacio de nombres.

```
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
Posterior se debe crear el metodo publishEvent

```
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the EventData is bigger than the max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));

        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
    
```
## 4.- Ejecutar aplicación

```
mvn clean compile exec:java
```
---
> Curso basado en documentación Azure:
> https://learn.microsoft.com/es-es/azure/event-hubs/event-hubs-java-get-started-send?tabs=connection-string%2Croles-azure-portal

— easyIT4y Learning - 2024

