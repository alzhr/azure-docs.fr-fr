---
title: Relocalisation grossière | Microsoft Docs
description: Guide de démarrage rapide de la relocalisation grossière.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 1d9e58f2d7eda818665a6253a8d0508104b17405
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093263"
---
# <a name="coarse-relocalization"></a>Relocalisation grossière

La relocalisation grossière est une fonctionnalité qui fournit une première réponse à la question : *Où se trouve mon appareil maintenant/Quel contenu dois-je observer ?* La réponse n’est pas précise, mais se présente au format suivant : *Vous êtes proche de ces ancres, essayez de localiser l’une d’entre elles*.

La relocalisation grossière fonctionne en associant différents relevés de capteur sur l’appareil à la création et à l’interrogation des ancres. Pour les scénarios d’extérieur, les données de capteur sont généralement la position GPS (Global Positioning System) de l’appareil. Lorsque le GPS n’est pas disponible ou n’est pas fiable (par exemple, à l’intérieur), les données de capteur se trouvent dans les points d’accès Wi-Fi et les balises Bluetooth situées à portée. Toutes les données de capteur collectées contribuent au maintien d’un index spatial. L’index spatial est exploité par le service d’ancrage pour déterminer rapidement les ancres situées à environ 100 mètres de votre appareil.

La recherche rapide d’ancres permise par la relocalisation grossière simplifie le développement d’applications grâce aux collections d’ancres (millions d’ancres géodistribuées). La complexité de la gestion des ancres est cachée, ce qui vous permet de vous concentrer davantage sur votre logique d’application extraordinaire. Le gros du travail relatif aux ancres est effectué pour vous en arrière-plan par le service !

## <a name="collected-sensor-data"></a>Données de capteur collectées

Les données de capteur que vous pouvez envoyer au service d’ancrage peuvent être les suivantes :

* Position GPS : latitude, longitude, altitude.
* Force du signal des points d’accès Wi-Fi à portée.
* Intensité du signal des balises Bluetooth à portée.

En général, votre application doit acquérir des autorisations spécifiques d’appareils pour accéder aux données GPS, WiFi ou BLE. En outre, certaines des données de capteur ci-dessus ne sont par nature pas disponibles sur certaines plateformes. Pour tenir compte de ces situations, la collecte des données de capteur est facultative et désactivée par défaut.

## <a name="set-up-the-sensor-data-collection"></a>Configurer la collecte de données du capteur

Commençons par créer un fournisseur d’empreintes digitales de capteur et à faire en sorte que la session les prenne en compte :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the ASA factory
SpatialAnchorsFactory m_asaFactory { nullptr };
// . . .

// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider;
sensorProvider = m_asaFactory.CreatePlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Ensuite, vous devez déterminer les capteurs que vous souhaitez utiliser pour la relocalisation grossière. Cette décision est, en général, propre à l’application que vous développez, mais les recommandations du tableau suivant doivent vous offrir un bon point de départ :


|             | Intérieur | Extérieur |
|-------------|---------|----------|
| GPS         | Off | Il en va |
| WiFi        | Il en va | On (facultatif) |
| Balises BLE | On (facultatif avec des mises en garde, voir ci-dessous) | Off |


### <a name="enabling-gps"></a>Activation du GPS

En supposant que votre application ait déjà l’autorisation d’accéder à la position GPS de l’appareil, vous pouvez configurer Azure Spatial Anchors de manière à l’utiliser :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Lorsque vous utilisez le GPS dans votre application, gardez à l’esprit que les relevés fournis par le matériel sont généralement :

* asynchrones et de faible fréquence (inférieurs à 1 Hz).
* non fiables/bruyants (en moyenne déviation standard de 7 m).

En général, le système d’exploitation de l’appareil et Azure Spatial Anchors effectuent des opérations de filtrage et d’extrapolation sur le signal GPS brut en vue d’atténuer ces problèmes. Ce traitement supplémentaire nécessite plus de temps pour la convergence. Par conséquent, pour obtenir les meilleurs résultats, vous devez essayer d’effectuer les opérations suivantes :

* Créez le fournisseur d’empreintes digitales du capteur le plus tôt possible dans votre application.
* Maintenez le fournisseur d’empreintes digitales actif et partagez-le entre plusieurs sessions.

Si vous envisagez d’utiliser le fournisseur d’empreintes digitales de capteur en dehors d’une session d’ancrage, veillez à le démarrer avant de demander des estimations de capteur. Par exemple, le code suivant s’occupe de la mise à jour de la position de l’appareil sur la carte en temps réel :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Activation du Wi-Fi

En supposant que votre application ait déjà l’autorisation d’accéder à la position à l’état Wi-Fi de l’appareil, vous pouvez configurer Azure Spatial Anchors de manière à l’utiliser :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Lorsque vous utilisez le WiFi dans votre application, gardez à l’esprit que les relevés fournis par le matériel sont généralement :

* asynchrones et de faible fréquence (inférieurs à 0,1 Hz).
* potentiellement limités au niveau du système d’exploitation.
* non fiables/bruyants (en moyenne déviation standard de 3 dBm).

Azure Spatial Anchors tente de générer une carte relative à la force du signal WiFi filtrée pendant une session afin d’atténuer ces problèmes. Pour obtenir les meilleurs résultats, vous devez tenter d’effectuer les opérations suivantes :

* Créez la session avant de placer la première ancre.
* Veillez à ce que la session soit active aussi longtemps que possible (c’est-à-dire créer toutes les ancres et les requêtes dans une session).

### <a name="enabling-bluetooth-beacons"></a>Activation des balises Bluetooth

En supposant que votre application ait déjà l’autorisation d’accéder à la position à l’état Bluetooth de l’appareil, vous pouvez configurer Azure Spatial Anchors de manière à l’utiliser :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Les balises sont généralement des appareils polyvalents, où tout, y compris les UUID et les adresses MAC, peuvent être configurés. Cette flexibilité peut être problématique pour Azure Spatial Anchors qui considère les balises comme étant identifiées de manière unique par leur UUID. Le fait de ne pas garantir ce caractère unique se traduira probablement par des vortex. Pour obtenir les meilleurs résultats, vous devez tenter d’effectuer les opérations suivantes :

* Affectez des UUID uniques à vos balises.
* Déployez-les, généralement selon un schéma normal, tel qu’une grille.
* Transmettez la liste des UUID de balise unique au fournisseur d’empreintes digitales du capteur :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure Spatial Anchors effectue uniquement le suivi des balises Bluetooth qui figurent dans la liste. Toutefois, les balises malveillantes programmées pour avoir des UUID figurant sur une liste blanche peuvent toujours avoir un impact négatif sur la qualité du service. Pour cette raison, vous devez utiliser des balises uniquement dans les espaces organisés dans lesquels vous pouvez contrôler leur déploiement.

## <a name="querying-with-sensor-data"></a>Interrogation avec des données de capteur

Une fois les ancres créées avec les données de capteur associées, vous pouvez commencer à les récupérer à l’aide des relevés de capteur signalés par votre appareil. Vous n’avez plus besoin de fournir au service une liste d’ancres connues que vous vous attendez à trouver. Au lieu de cela, vous informez simplement le service de l’emplacement de votre appareil comme indiqué par ses capteurs intégrés. Le service Spatial Anchors va ensuite déterminer l’ensemble des ancres proches de l’appareil et tenter de les faire correspondre visuellement.

Pour que les requêtes utilisent les données de capteur, commencez par créer un critère de localisation :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = m_asaFactory.CreateNearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = m_asaFactory.CreateAnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Le paramètre `DistanceInMeters` contrôle le degré d’exploration du graphique d’ancrage pour récupérer du contenu. Supposons par exemple que vous ayez rempli un espace avec des ancres à une densité constante de 2 par mètre. Ensuite, l’appareil photo de votre appareil observe une seule ancre et le service parvient à la localiser. Il est très probable que vous souhaitiez récupérer toutes les ancres que vous avez placées à proximité plutôt que l’ancre unique que vous observez actuellement. En supposant que les ancres que vous avez placées sont connectées dans un graphique, le service peut récupérer toutes les ancres voisines pour vous en suivant les extrémités du graphique. La taille de la traversée de graphique effectuée est contrôlée par `DistanceInMeters`. Vous disposez de toutes les ancres connectées à celle que vous avez localisée, qui sont situées à une proximité inférieure à `DistanceInMeters`.

N’oubliez pas que des valeurs élevées pour `MaxResultCount` peuvent affecter de manière négative les performances. Essayez de définir une valeur raisonnable qui est logique pour votre application.

Enfin, vous devez indiquer à la session d’utiliser la recherche basée sur le capteur :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Résultats attendus

Les appareils GPS grand public sont généralement assez précis. Une étude de [Zandenbergen et Barbeau (2011)][6] indique que le taux d’exactitude médian des téléphones mobiles dotés de la technologie Assisted GPS (A-GPS) est d’environ 7 mètres, ce qui est important ! Pour tenir compte de ces erreurs de mesure, le service traite les ancres comme des distributions de probabilités dans l’espace GPS. Par conséquent, une ancre se trouve désormais dans la zone spatiale qui a le plus de chance (avec un indice de confiance de plus de 95 %) de contenir sa véritable position GPS inconnue.

Le même raisonnement est appliqué lors de l’interrogation avec le GPS. L’appareil est représenté sous la forme d’une autre région spatiale de confiance autour de sa véritable position GPS inconnue. La découverte des ancres situées à proximité se traduit par la simple recherche des ancres dont les régions de confiance sont *suffisamment proches* de la région de confiance de l’appareil, comme illustré dans l’image ci-dessous :

![Sélection d’ancres candidates avec GPS](media/coarse-reloc-gps-separation-distance.png)

La précision du signal GPS, aussi bien lors de la création de l’ancre que lors de l’interrogation, a une grande influence sur l’ensemble des ancres renvoyées. En revanche, les requêtes reposant sur le WiFi/les balises prendront en compte toutes les ancres ayant au moins un point d’accès ou une balise en commun avec la requête. En ce sens, le résultat d’une requête basée sur le Wi-Fi/les balises est généralement déterminé par la plage physique des points d’accès/balises et les obstructions environnementales.

Le tableau ci-dessous estime l’espace de recherche attendu pour chaque type de capteur :

| Capteur      | Rayon de l’espace de recherche (approx.) | Détails |
|-------------|:-------:|---------|
| GPS         | 20 à 30 m | Déterminé par l’incertitude GPS parmi d’autres facteurs. Les nombres signalés sont estimés pour l’exactitude GPS médiane des téléphones mobiles dotés d’un A-GPS, soit 7 mètres. |
| WiFi        | 50 à 100 m | Déterminé par la plage des points d’accès sans fil. Dépend de la fréquence, de la puissance de l’émetteur, des obstructions physiques, des interférences, etc. |
| Balises BLE |  70 m | Déterminé par la plage de la balise. Dépend de la fréquence, de la puissance de transmission, des obstructions physiques, des interférences, etc. |

## <a name="per-platform-support"></a>Prise en charge par plateforme

Le tableau suivant récapitule les données de capteur collectées sur chacune des plates-formes prises en charge, ainsi que tous les avertissements spécifiques de la plateforme :


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/A | Prise en charge via les API [LocationManager][3] (GPS et réseau) | Pris en charge via les API [CLLocationManager][4] |
| WiFi        | Prise en charge à un débit d’environ une analyse toutes les 3 secondes | Pris en charge. À partir du niveau d’API 28, les analyses Wi-Fi sont limitées à 4 appels toutes les 2 minutes. À partir d’Android 10, la limitation peut être désactivée dans le menu des paramètres du développeur. Pour plus d’informations, consultez la [documentation Android][5]. | Sans objet - Aucune API publique |
| Balises BLE | Limité à [Eddystone][1] et [iBeacon][2] | Limité à [Eddystone][1] et [iBeacon][2] | Limité à [Eddystone][1] et [iBeacon][2] |

## <a name="next-steps"></a>Étapes suivantes

Utilisez la relocalisation grossière dans une application.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9