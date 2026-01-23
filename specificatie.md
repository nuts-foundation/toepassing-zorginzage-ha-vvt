# Scope
## Doel
Het doel van deze toepassing is tweeledig: het verbeteren van de informatievoorziening van de huisarts die behoefte heeft aan informatie over wat er in het VVT domein met de patient gebeurt (en het gelijktijdig optimaliseren van het werkproces van een verpleegkundige die belast is met de taak om de huisarts waar nodig op de hoogte te houden). Als ook het het regelen van inzicht van de informatie die beschikbaar is in het huisartsendossier voor de verpleegkundige in de VVT instelling.
  
In de huidige situatie wordt de huisarts daarover vaak geïnformeerd doordat de VVT-medewerker een dubbele administratie bijhoudt in het eigen systeem (ECD) en dat de huisarts (HIS of samenwerkingsplatform). Deze toepassing beschrijft de techniek waarmee het mogelijk wordt voor de huisarts om rechtstreeks de informatie uit het ECD te raadplegen vanuit het eigen systeem. Andersom kan de verpleegkundige de informatie die bij de huisarts is vastgelegd inzien.

## Scope:
Inzage is een eerste stap van een veel bredere en rijkere integratie. Hierbij gaat het bv om het uitzetten van taken. Deze functionaliteiten worden in latere versies van deze usecase toegevoegd. De usecase is iteratief ontwikkeld (eerst de ene kant op, de huisarts inzage geven en daarna de andere kant op) maar het ontwerp is generiek opgezet. 

# Governance
De Governance en besluitvorming rondom deelname is belegd bij de projectgroep VVT-Huisarts inzage van Nuts.

# Functioneel Ontwerp
Deze usecase ondersteunt het ophalen van informatie twee kanten op:
Usecase 1. informatie zoals vastgelegd door de (wijk)verpleging in de VVT om deze informatie beschikbaar te stellen aan de huisarts.
Usecase 2. Informatie zoals vastgelegd door de huisarts beschikbaar te stellen aan de (wijk)verpleegkundige

Het proces werkt als volgt:

De huisarts wil informatie inzien in het bronsysteem (VVT). In het huisartsensysteem start de gebruiker de zoekopdracht. Gezocht wordt op een individuele patiënt. Gebaseerd op de informatie die door de VVT instellingen in de discoveryservice is vastgelegd in combinatie met het BSN van de patiënt wordt in het opvragende systeem een lijst getoond van instellingen waar potentieel informatie op te halen is voor de betreffende patiënt. De huisarts selecteert de juiste instelling. Op basis daarvan vindt een FHIR-request ‘Patient’ plaats naar het bronsysteem. Het bronsysteem beoordeelt deze op een aantal parameters:
- Ken ik deze patiënt?
- Heeft deze patiënt een consent afgegeven?
- Klopt het Verifiable Credential van de aanvrager (geldig en gelijk aan hetgeen is vastgelegd bij de patiënt)?

Als middel wordt hiervoor wordt het URA nummer van de opvragende organisatie gebruikt. Deze informatie is door de (wijk)verpleging vastgelegd in het bronsysteem bij de patiënt.
Indien er informatie beschikbaar is en deze vrijgegeven mag worden wordt het interne PatientID terugekoppeld aan het opvragende systeem. Openstaande vraag hierin is de informatie rondom de contactinformatie van de zorgverlener waarbij de patient in behandeling is. Dit kan verschillende verschijningsvormen hebben zoals de afdelingstelefoon of het hoofd van het behandelteam. Hiermee kan de vraag beantwoord worden ‘wie moet ik bellen voor deze patiënt?'. Deze vraag staat nog open, het huidige ontwerp voorziet hier nog niet in. In de komende tijd wordt een addendum op het ontwerp gemaakt waarin hiervoor een oplossing wordt geschetst.

Op basis van het PatientID kan verdere (medisch inhoudelijke) informatie opgevraagd worden. Er is een lijst beschikbaar van informatie die opgevraagd kan worden indien het bronsysteem deze informatie beschikbaar heeft. Deze bestaat uit rapportages en meetwaarden volgens een afgesproken stramien (in aantal / tijd). Zie hiervoor de paragraaf ‘zorginhoudelijke informatie’.

De verdere medisch inhoudelijke informatie wordt opgehaald op basis van FHIR (ZIB’s waar mogelijk). De informatie wordt in het doelsysteem getoond. Het doelsysteem is er verantwoordelijk voor de informatie in de juiste context te tonen (denk aan verschillen met eigen informatie). Logging vindt in de gehele keten plaats.

Voor usecase 2 geldt hetzelfde proces, alleen is de informatieflow omgekeerd. De informatie die beschikbaar gesteld wordt is wel verschillend tussen de 2 stromen, zie hiervoor de ZIB-specificatie verderop in dit ontwerp. Daarnaast wordt de autorisatie bepaald op basis van de toestemming voor gegevensuitwisseling die de patient heeft afgegeven aan de huisarts. Waarmee in de basis alle VVT instellingen de benodigde informatie op kunnen vragen.


# Uitgangspunten
- De patiënt is reeds bekend/in zorg bij zowel de huisarts als bij de VVT instelling
- De patiënt heeft consent afgegeven om data te delen met de huisarts en/of de VVT instelling
- Informatie kan opgehaald worden bij het bronsysteem en getoond in het doelsysteem
 In de documentatie is vastgelegd welke informatie (ZIB's) er beschikbaar gemaakt kan worden via de koppeling (Zie hoofdstuk ‘Lijst met ZIB’s’). Deze lijst is vastgesteld in overleg met de koepels (ACTIZ, InEEN). Indien informatie opgehaald wordt, zal deze ook getoond worden in een vorm die functioneel passend is bij hoe de informatie is bedoeld in het doelsysteem. Dit geldt ook voor gegevens uit bijvoorbeeld de Patient ZIB. Het doel is om de context van de informatie zoveel mogelijk te behouden tussen bron en doel. Wanneer er bijvoorbeeld discrepanties zijn, dan kan het doelsysteem deze discrepanties naast elkaar tonen aan de gebruiker. Er is een lijst beschikbaar van informatie die opgehaald kan worden.
- De medewerkers blijven in hun eigen systeem werken. De leveranciers zijn zelf verantwoordelijk hoe zij de medewerker het beste willen/kunnen ondersteunen.
- Er wordt gebruik gemaakt van bestaande zorginformatiebouwstenen die voor de leveranciers al bekend zijn. Hierbij wordt FHIR versie STU3 gebruikt en daarbij gekoppeld de ZIB'S 2017 of versie R4 met ZIB'S 2020. 
- Het afhandelen van de consent vraag vindt plaats in het bronsysteem. Het systeem bepaalt zelf hoe dit vastgelegd wordt en hoe het gecheckt wordt (en dus eventueel gedelegeerd aan Mitz).
- Authenticatie vindt plaats op basis van een Verifiable Credential: het URA nummer van de organisatie waar de opvrager werkt en zoals vastgelegd in een UZI Servercertificaat.
- Het bronsysteem moet vastleggen en checken welke organisatie bij de informatie mag. Er kan niet op medewerker niveau of rol worden gecontroleerd. Verdere uitwerking in hoofdstuk autorisatie. 
-Logging vindt in de gehele keten plaats.

Shortcuts en Toekomstige ontwikkelingen: Sommige zaken kunnen we op dit moment niet invullen zoals we willen omdat dit (om verschillende redenen) nog niet realistisch is. Deze zaken plaatsen we in deze usecase buiten scope en worden later eventueel toegevoegd. Het gaat om:
- We eisen geen aansluiting op Mitz (maar accepteren ook consent lokaal). Als de applicatie het consent opslaat in Mitz moet dit ook ontsloten kunnen worden
- Er is nog geen goed authenticatiemiddel voor de gebruiker beschikbaar (DEZI) dus werken we met een VC waarbij de organisatie obv een UZI certificaat wordt geauthenticeerd
- We implementeren niet alle mogelijke ZIB’s want die zijn niet beschikbaar in de bronsystemen, maar hanteren een subset
- dPOP wordt pas in een later stadium toegevoegd, voor nu maakt dit geen onderdeel uit van de usecase
- We verrijken / corrigeren de informatie uit de UZI credentials niet (human readable namen), dit moet bij de bron opgelost worden
- Bij gebrek aan een generieke lokalisatiedienst wordt er een workaround toegepast waarbij handmatig wordt vastgelegd waar de patiënt in zorg is en waar dus informatie opgehaald kan worden.


## Architectuur beschrijving
### Gebruik van Nuts
Voor deze usecase wordt gebruikt gemaakt van de Nuts infrastructuur. Specifiek wordt gebruik gemaakt van de mogelijkheden die de V6.1 versie van Nuts biedt en daarmee dus ook van did:web. Voor de informatie specifiek over de Nuts-laag wordt verwezen naar de officiële documentatie: https://nuts-node.readthedocs.io/en/stable/   

### Registreren voor de use-case
<!-- ![sequence diagram for care organization management](img/sequence-diagram-use-case-registration.svg) -->
<img src="img/sequence-diagram-use-case-registration.svg" width="700">

**X509CredentialTool**: 
De Nuts community biedt op dit moment 2 tools aan om een X509Credential te genereren:
- Een [Java library](https://github.com/nuts-foundation/uzi-did-x509-issuer-java)
- Een [Go tool en docker image](https://github.com/nuts-foundation/go-didx509-toolkit)

Beide tools geven instructies om stappen 9-10 uit te kunnen voeren.

### Versies van ZIBs en FHIR:
Een bronhouder kan kiezen voor twee varianten qua versies. FHIR R4 en ZIBs 2020 of FHIR STU3 en ZIBs 2017. De bevrager achterhaalt de FHIR versie door het meta endpoint van de bronhouder aan te roepen. 
Zie https://hl7.org/fhir/STU3/http.html#capabilities voor meer informatie. Sommige FHIR clients hebben standaard functionaliteit om het metadata endpoint te bevragen. 

### Data ophalen bij de VVT
<!-- ![sequence diagram ophalen data bij VVT](img/sequence-diagram-resource-request.svg) -->
<img src="img/sequence-diagram-resource-request.svg" width="1500">

## Lokalisatie
Bij gebrek aan een generieke lokalisatiedienst wordt lokalisatie lokaal ingevuld. Dit houdt in dat er in de systemen zelf bijgehouden wordt waar data opgehaald kan worden. Bijvoorbeeld door de VVT instelling waar de patient bij in behandeling is expliciet vast te leggen in het HIS. Of in geval van inzicht in het huisartsendossier door de betreffende huisarts vast te leggen in het ECD bij het dossier.

## Grondslag
De grondslag voor uitwisseling is de toestemming (consent) die vastgelegd is bij de patient. 

### Consent specificatie
Consent: de verantwoordelijkheid voor het vastleggen en checken van consent wordt ingevuld door het bronsysteem zelf.

## Adressering
Discovery Service

### Discovery Definition

Algemene uitleg over wat is een Service Discovery is te vinden op de [Nuts Wiki pagina Service Discovery](https://wiki.nuts.nl/books/designing-a-nuts-use-case/page/service-discovery)

#### Input descriptors:
Qua benodigde credentials kennen wij 2 verschillende credentials, een X509Credential en een DiscoveryRegistrationCredential.

##### X509Credential
Dit betreft een X509Credential conform [Nuts RFC023](https://wiki.nuts.nl/books/x509credential/page/uzi-server-certificates-with-rfc023), ondertekend met een UZI-servercertificaat.

Aanmaken kan bijv. met de [go-didx509-toolkit](https://github.com/nuts-foundation/go-didx509-toolkit/tree/main) of de [Java library](https://github.com/nuts-foundation/uzi-did-x509-issuer-java/). Als uitgever is besloten om hier de chain op te nemen het dichtst bij de leaf-certificaat.

Gezocht kan worden op de naam van de DID, organisatienaam, organisatie type, plaats & URA.

##### DiscoveryRegistrationCredential
Dit betreft een DiscoveryRegistrationCredential conform de uitleg op de [Nuts Wiki pagina over Service Discovery](https://wiki.nuts.nl/books/designing-a-nuts-use-case/page/service-discovery#bkmrk-require-registration).

Hierbij is een veld toegevoegd aan de credentialSubject genaamd fhirBaseURL. Deze kan gebruikt worden door gebruikers van de Discovery Service om te weten waar de daadwerkelijke FHIR data opgehaald kan worden.

Het is van belang het organisatie type mee te geven bij de registratie op de Discovery Service, zodat onderscheid gemaakt kan worden tussen een VVT-instelling en een huisarts. Hiervoor wordt het HL7 organisation type Codelijst gebruikt: https://zibs.nl/wiki/Zorgaanbieder-v3.4(2020NL)#OrganisatieTypeCodelijst 

Voor deze usecase gelden er 2 types die gebruikt kunnen worden: 
1. Huisartsinstelling:	**H1**
2. Verplegings- of verzorgingsinstelling	**X3**	


Note: dit is een uitbreiding op een eerdere versie van deze specificatie en vereist een aanpassing op de reeds bestaande fase 1 koppeling. 


## Authenticatie
Om veilig gegevens te kunnen delen tussen verschillende zorgaanbieders, is zorgaanbieder-overstijgende authenticatie van zorgorganisaties en zorgverleners essentieel. Vanuit de NEN wordt gewerkt aan een norm met betrekking tot identificatie en authenticatie. Op het moment dat deze norm gepubliceerd wordt zullen we de landelijke ontwikelingen mbt tot deze norm volgen. Om op korte termijn informatie uitwisseling mogelijk te maken, zal de authenticatie geborgd worden op de volgende manier:

### Server & Client authenticatie
Voor server & client authenticatie is besloten om de volgende uitgangspunten te hanteren:
- Testomgevingen maken gebruik van Test UZI-servercertificaten voor de X509Credentials & geldige certificaten voor TLS (publiekelijk vertrouwde & PKIOverheid) op de FHIR endpoints en als clientAuthenticatie
- Acceptatieomgevingen maken gebruik van of test of productie UZI-servercertificaten voor de X509Credentials & PKIOverheid voor TLS op de FHIR endpoints en als clientAuthenticatie
- Productieomgevingen maken enkel gebruik van productie UZI-servercertificaten voor de X509Credentials & PKIOverheid voor TLS op de FHIR endpoints en als clientAuthenticatie

## Identificatie
Om de raadplegende organisatie te identificeren wordt gebruik gemaakt van het UZI abonnee nummer ook wel bekend als het URA nummer. Als middel voor de authenticatie van dit nummer gebruiken we een verifiable credential en de OpenID Foundation standaarden voor authenticatie.

Omdat het UZI register als authentieke bron zelf nog geen URA Credentials uitgeeft wordt dit credential afgeleid van een UZI Servercertificaat van de betreffende organisatie. We gebruiken hiervoor een nieuw credential NutsX509Credential waarvan de ontwikkeling hier te volgen is: https://github.com/nuts-foundation/nuts-node/issues/3582

### Policy Definition

#### Input descriptors:
Qua benodigde credentials kennen wij 2 verschillende credentials, een X509Credential en een NutsEmployeeCredential.

##### X509Credential
Zie de X509Credential onder Discovery Definition, deze is exact het zelfde.

##### NutsEmployeeCredential
Zie de [Nuts Wiki pagina over Requesting Access](https://wiki.nuts.nl/books/implementing-a-nuts-use-case-Ssg/page/requesting-access) voor context.

## Autorisatie
Voor deze usecase gaan wij uit van autorisatie op 5 niveaus:
- Aanwezig op Discovery service
- Behandelrelatie
- Consent
- Zib
- Fhir query parameters

#### Discovery service
Om data bij een bronhouder op te halen, wordt de discovery service gebruikt om het adres te vinden. Hoewel het technisch mogelijk is om data op te halen bij een partij die niet (meer) aangemeld is bij de discovery service, is dit functioneel en qua beveiliging niet wenselijk. Daarom moet de bronhouder ook controleren of de partij waarvoor de aanvraag ingediend wordt nog steeds aangemeld is voor de toepassing op de discovery service.

#### Behandelrelatie
De standaard gaat er vanuit dat de juridische grondslag voor de uitwisseling de toestemming voor uitwisseling is die de patient gegeven heeft.
Voor de route waarin de huisarts informatie ophaalt bij de VVT geldt dat optioneel ook de behandelrelatie als juridische grondslag toegepast kan worden. Dit betekent dat om toegang te krijgen tot gegevens van een patient het in dat geval vereist is dat huisarts vastgelegd en bekend is bij de patient in het bronsysteem. Er wordt een controle gedaan op basis van het URA-nummer waarmee een resource wordt opgehaald. Dit nummer is terug te vinden in het `organization_ura` veld van een token introspect op de Nuts node. Zie ook stap 11 in "Request data at VVT" onder de architectuurbeschrijving. Voor de route waarin de VVT instelling informatie op haalt bij de huisarts accepteren we dat er een generieke toestemming is voor VVT-instelling en dat bij de huisarts dus niet iets vastlegt is om te ‘borgen’ dat alleen VVT instelling X bij de informatie mag. Dat is inherent aan hoe de grondslag obv toestemming geregeld is.

#### Consent
Om een resource van een patient op te kunnen halen moet het bronsysteem toestemming van de patient geregistreerd hebben of op kunnen halen. Een algemene toestemming volstaat hier, waarbij de patient toestemming geeft om zijn of haar gegevens te delen.

#### Zib
Voor de inzage is een beperkte set aan Zibs beschikbaar. Deze zijn uitgewerkt in de lijst van ZIBs. Alleen deze Zibs kunnen opgehaald worden binnen de scope van deze usecase.

#### Fhir query parameters
Om te voorkomen dat er teveel informatie opgehaald kan worden (bijvoorbeeld middels het gebruik van `_include` parameters) wordt er ook autorisatie uitgevoerd op basis van de gebruikte query parameters. Hier wordt onderscheid gemaakt tussen verplichte parameters en toegestane parameters. Alle query parameters die gedefinieerd zijn in de set aan gebruikte Zibs (zie hieronder) zijn verplicht. Het gebruik van andere query parameters is niet toegestaan.

## MedewerkerID
Uitgangspunt: Identificatie van de medewerker op basis van een (intern) MedewerkerID, gecombineerd met een VC gebaseerd op informatie binnen het UZI-servercertificaat.


## Informatie
### Lijst van ZIBs

### Tabellen met FHIR resources en queries
Hieronder staan de endpoints die beschikbaar gesteld moeten worden door de partijen die toegang tot het ophalen van de informatie. Ter verheldering zijn de kolommen Sort en Count toegevoegd om aan te tonen hoeveel resultaten er geretourneerd worden en op welke manier deze worden gesorteerd. Het is van belang dat bij een aanroep alle parameters gebruikt worden die in de tabel staan en ook geen extra. Dit heeft te maken met de controle die de systemen doen op de verifiable credentials. Die controle wordt op die manier gedaan om te voorkomen dat met een parameter zoals een include extra gegevens meekomen.

### Algemeen 
De koppeling werkt twee kanten op, vandaar dat er hieronder twee tabellen worden getoond. 

Voor de route waarin de informatie voor de (wijk)verpleegkundinge uit het Huisarts Dossier opgehaald wordt geldt dat in de basis hiervoor de MedMij specificaties zijn gebruikt. Deze zijn te vinden op: https://informatiestandaarden.nictiz.nl/wiki/MedMij:V2020.01/FHIR_GP_Data. Note: er zijn afwijkingen gedaan op de MedMij specificatie om het passend te maken op de usecase. Hoewel de basis van MedMij gebruikt is, kan die specificatie niet 1-op-1 toegepast worden. De verschillen zitten op het gebied van de zoekpaden (specifieke profiles opvragen ipv alles) en beperking van de informatie (maximaal aantallen ipv alles, gesorteerd op nieuwste eerst,profiles expliciteit opvragen ipv de hele set in 1 keer terug te krijgen). 
Daarnaast geldt dat de HISsen naast de STU3 versie uit de MedMij specificatie ook de R4 versie kunnen ondersteunen. Note: er zitten technische en inhoudelijke verschillen tussen deze 2 FHIR versies. Deze komen in de tabel tot uiting.

Voor het ophalen van de Patiënt geldt een standaard methodiek:
POST /fhir/Patient/_search
- Header: Content-Type = x-www-form-urlencoded (zie https://www.hl7.org/fhir/http.html#search-post)
- Body: identifier=http://fhir.nl/fhir/NamingSystem/bsn|{bsn}

Specifiek voor usecase 2: 'VVT haalt data op uit HIS' geldt dat de volgende includes meegegeven dienen te worden in de search query:
- include=Patient:general-practitioner en include:iterate=PractitionerRole:organization



**Beschikbare informatie voor de huisarts in het VVT Dossier**




| ZIB                 | Method | Sort      | Count | Endpoint                                                                                                                            | Profiel                                                                                                                                                              |
|:--------------------|--------|-----------|-------|:------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alerts              | GET    |           |       | /fhir/Flag?patient={patientId}}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-Alert                                        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954733](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954733) |
| Allergie            | GET    |           |       | /fhir/AllergyIntolerance?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-AllergyIntolerance              | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.18/files/2317138](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.18/files/2317138) |
| Bloeddruk           | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BloodPressure&_sort=-date&_count=5     | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954744](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954744) |
| Lichaamsgewicht     | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyWeight&_sort=-date&_count=5        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954750](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954750) |
| Lichaamslengte      | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyHeight&_sort=-date&_count=5        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954746](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954746) |
| Lichaamstemperatuur | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyTemperature&_sort=-date&_count=5   | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954748](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954748) |
| Patiënt             | POST   |           |       | Zie onder de tabel                                                                                                                  | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954638](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954638) |
| Respiration         | GET    |           |       | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-Respiration&_sort=-date&_count=5       | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954947](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954947) |
| TekstRapportage     | GET    | Date DESC | 10    | /fhir/Observation?patient={patientId}&_profile=https://nuts.nl/fhir/StructureDefinition/nl-core-nursingreport&_sort=-date&_count=10 | [https://simplifier.net/anw/nl-core-nursingreport](https://simplifier.net/anw/nl-core-nursingreport)                                                                 |
| Wilsverklaring      | GET    |           |       | /fhir/Consent?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-AdvanceDirective                           | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954726](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954726) |
| Woonsituatie        | GET    |           |       | /fhir/Observation?patient={patientId}&_profile=http://nictiz.nl/fhir/StructureDefinition/zib-LivingSituation                        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954848](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954848) |





**Beschikbare informatie voor de (wijk)verpleegkundige in het Huiarts Dossier**







|Onderdelen uit het Huisarts-EPD|Beschrijving|Count|Sort|ZIB **STU3**|Endpoint **STU3**|ZIB **R4**|Endpoint **R4**|Profile|
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
|1 Huisarts|De huisarts waarvan de gegevens afkomstig zijn.|||<https://zibs.nl/wiki/HealthProfessional-v3.1(2017EN)>|Zie kopje Algemeen|<https://zibs.nl/wiki/Zorgverlener-v3.5(2020NL)>|Zie kopje Algemeen|<http://nictiz.nl/fhir/StructureDefinition/nl-core-HealthProfessional-Practitioner>|
|2 huisartsenpraktijk|De huisartsenpraktijk waarvan de gegevens afkomstig zijn.|||<https://zibs.nl/wiki/HealthcareProvider-v3.1(2017EN)>|Zie kopje Algemeen|<https://zibs.nl/wiki/Zorgaanbieder-v3.4(2020NL)>|Zie kopje Algemeen|<http://nictiz.nl/fhir/StructureDefinition/nl-core-HealthcareProvider>|
|3 Patiënt|De patiëntgegevens van de patiënt van wie de gegevens zijn.|||<https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954638>|Zie kopje Algemeen|<https://zibs.nl/wiki/Patient-v3.2(2020NL)>|Zie kopje Algemeen|<http://nictiz.nl/fhir/StructureDefinition/nl-core-Patient>|
|4 Episodes|Een gezondheidsprobleem zoals een klacht of een aandoening, waarvan de aard in de loop van de tijd kan veranderen door voortschrijdend inzicht of door het verloop van de aandoening.|5|DESC|Zie specs MedMij mbt episodes in STU3|GET \[base\]/fhir/stu3/EpisodeOfCare|<https://zibs.nl/wiki/ZorgEpisode-v1.0(2020NL)>|GET \[base\]/fhir/r4/EpisodeOfCare?patient=\{id\}&\_include=condition:diagnosis|<http://nictiz.nl/fhir/StructureDefinition/nl-core-EpisodeOfCare>|
|5 Episodes met een attentievlag|Episodes kunnen een attentievlag hebben om extra aandacht voor de episode te vragen. De attentievlag kan aanwezig blijven als een episode is afgesloten wordt/is, wanneer de gebruiker het belangrijk vindt dit probleem in beeld te houden.|5|DESC|Zie specs MedMij mbt episodes in STU3 \+ <https://zibs.nl/wiki/Alert-v3.2(2017EN)>|GET \[base\]/fhir/stu3/Flag|<https://zibs.nl/wiki/Alert-v4.1(2020NL)>|GET \[base\]/fhir/r4/Flag?patient=\{id\}|<http://nictiz.nl/fhir/StructureDefinition/nl-core-Alert>|
|6 Open en gesloten episodes|Episodes kunnen open (actueel) of gesloten (niet langer actueel) zijn. Gesloten episodes met een attentievlag zijn functioneel interessant, overige gesloten episodes niet.|5|DESC|Zie specs MedMij mbt episodes in STU3|GET \[base\]/EpisodeOfCare|<https://zibs.nl/wiki/ZorgEpisode-v1.0(2020NL)>|GET \[base\]/fhir/r4/EpisodeOfCare?patient=\{id\}|<http://nictiz.nl/fhir/StructureDefinition/nl-core-EpisodeOfCare>|
|7 Actuele medicatie|**Medicatie-afspraak**<br>Het voorstel van een voorschrift tot gebruik van medicatie waarmee de patiënt akkoord gaat. De afspraak kan zowel starten, herhalen, wijzigen als stoppen van medicatie betreffen.|5|DESC|<https://zibs.nl/wiki/MedicationAgreement-v1.0(2017EN)>|GET \[base\]/fhir/stu3/MedicationRequest?patient=\{id\}|<https://zibs.nl/wiki/Medicatieafspraak-v1.2(2020NL)>|GET \[base\]/fhir/r4/MedicationRequest?patient=\{id\}&category=[http://snomed.info/sct\|16076005&\_include=MedicationRequest:medication](http://snomed.info/sct%7C16076005&_include=MedicationRequest:medication)|<http://nictiz.nl/fhir/StructureDefinition/mp-MedicationAgreement>|
|8 Medicatie-overgevoeligheid|Een medicatie-overgevoeligheid beschrijft een overgevoeligheid van een patiënt voor een geneesmiddel, een stof of een geneesmiddelengroep, waarmee rekening gehouden moet worden bij het voorschrijven van medicatie.|5|DESC|<https://zibs.nl/wiki/AllergyIntolerance-v3.1(2017EN)>|GET \[base\]/fhir/stu3/AllergyIntolerance?patient=\{id\}|<https://zibs.nl/wiki/AllergieIntolerantie-v3.3(2020NL)>|GET \[base\]/fhir/r4/AllergyIntolerance?patient=\{id\}&category=medication|<http://nictiz.nl/fhir/StructureDefinition/nl-core-AllergyIntolerance>|
|9 Resultaten van bepalingen – laatste veertien maanden|Een bepaling is een objectiveerbare diagnostische verrichting. Het resultaat is de (uitkomst) van een bepaling.|5 (per profile)|DESC||Note: zoekvraag per profile . beschikbaar zijn: <https://zibs.nl/wiki/LaboratoryTestResult-v4.0(2017EN)> <https://zibs.nl/wiki/BloodPressure-v3.1(2017EN)> <https://zibs.nl/wiki/BodyHeight-v3.1(2017EN)> <https://zibs.nl/wiki/BodyTemperature-v3.1(2017EN)> <https://zibs.nl/wiki/BodyWeight-v3.1(2017EN)> <https://zibs.nl/wiki/GeneralMeasurement-v3.0(2017EN)> <https://zibs.nl/wiki/HeartRate-v3.1(2017EN)> <https://zibs.nl/wiki/O2Saturation-v3.1(2017EN)> <https://zibs.nl/wiki/PulseRate-v3.1(2017EN)>|GET \[base\]/fhir/stu3/Observation?patient=\{id\}|Note: zoekvraag per profile . beschikbaar zijn:<https://zibs.nl/wiki/LaboratoriumUitslag-v4.6(2020NL)> <https://zibs.nl/wiki/Bloeddruk-v3.2.1(2020NL)> <https://zibs.nl/wiki/Lichaamsgewicht-v3.2(2020NL)> <https://zibs.nl/wiki/Lichaamslengte-v3.1.1(2020NL)> <https://zibs.nl/wiki/Lichaamstemperatuur-v3.1.2(2020NL)> <https://zibs.nl/wiki/Polsfrequentie-v3.3(2020NL)>|GET \[base\]/fhir/r4/Observation?patient=\{id\}&category=[http://hl7.org/fhir/observation-category\|vital-signs,laboratory](http://hl7.org/fhir/observation-category%7Cvital-signs,laboratory)|
|10 E- en P-regels van de SOEP-structuur – vastgelegd na invoering van online inzage|Informatie beschikbaar in het bronsysteem ten aanzien van de E- en P-regels van de SOEP-structuur|5|DESC|Note: voor STU3 geldt dat Informatie uit een deelcontact dat in vrije tekst wordt geregistreerd volgens de SOEP-structuur. Zie MedMij specificatie|GET \[base\]/fhir/stu3/Composition?type=[http://loinc.org\|67781-5](http://loinc.org%7C67781-5)&patient=\{id\}|<https://zibs.nl/wiki/SOEPVerslag-v1.0(2020NL)>|GET \[base\]/fhir/r4/Composition?patient=\{id\}&type=[http://loinc.org\|67781-5](http://loinc.org%7C67781-5)|-|
|11 Encounter (Contact)|Informatie uit een deelcontact dat in vrije tekst wordt geregistreerd volgens de SOEP-structuur.|5|DESC|<https://zibs.nl/wiki/Encounter-v3.1(2017EN)>|GET \[base\]/fhir/stu3/Encounter?patient=\{id\}|<https://zibs.nl/wiki/Contact-v4.0.1(2020NL)>|GET \[base\]/fhir/r4/Encounter?patient=\{id\}|<http://nictiz.nl/fhir/StructureDefinition/nl-core-Encounter>|








Functioneel missen nog: Behandelgrenzen, andere zorgverleners, wilsonbekwaamheid. Specifieke afspraken rond PZP volgen we vanuit de PZP coalitie.
Voor medicatie geldt dat er gewerkt wordt met wat er nu is. Toekomstbestendige communicatie omtrent Medicatie volgt uit het MP9 traject.


## Foutafhandeling
In deze fase concentreren we ons op beveiliging – juist in de pilot zal bekeken moeten worden of dit ook leidt tot voldoende gebruiksgemak. In de basis wordt dus alleen getoond DAT er een fout is en geen specifiekere informatie zoals 'patient niet gevonden'. Informatie die publiekelijk beschikbaar is (bv inhoud van de usecase als zijnde 'deze resource valt buiten de usecase') zou toegevoegd mogen worden om nieuwe ontwikkelaars makkelijker te maken bij het testen

## Versiebeheer
Het versiebeheer wordt momenteel uitgewerkt in usecase ANW. Het resultaat daarvan wordt hier opgenomen. In de toekomst wordt dit ontwerp omgeschreven naar een Implementation Guide (IG). Hiermee komen handvatten zoals een capability statement beschikbaar om in het ontwerp op te nemen.

## Omgevingen
De volgende omgevingen zijn beschikbaar in de OTAP omgeving:
-Ontwikkel: lokaal bij de ontwikkelaars zelf
-Test: Testomgeving waar met UZI testcertificaten gewerkt wordt
-Acceptatie: acceptatieomgeving waar met zowel test als productie UZI certificaten gewerkt kan worden (Dit omdat de leveranciers verschillend om gaan met acceptatie en staging).
-Productie: productie omgeving waar met productie UZI servercertificaten gewerkt wordt.

