# Scope
# Governance
Governance en deelname ligt binnen het project.

# Functioneel Ontwerp
Deze usecase ondersteunt het ophalen van informatie zoals vastgelegd door de (wijk)verpleging in de VVT om deze informatie beschikbaar te stellen aan de huisarts. Het proces werkt als volgt:

De huisarts wil informatie inzien in het bronsysteem (VVT). In het huisartsensysteem start de gebruiker de zoekopdracht. Gezocht wordt op een individuele patient. Gebaseerd op de informatie die door de VVT instellingen in de discoveryservice is vastgelegd in combinatie met het BSN van de patient wordt in het opvragende systeem een lijst getoond van instellingen waar potentieel informatie op te halen is voor de betreffende patient. De huisarts selecteert de juiste instelling. Op basis daarvan vindt een FHIR-request 'Patient' plaats naar het bronsysteem. Het bronsysteem beoordeelt deze op een aantal parameters:

Ken ik deze patient
Heeft deze patient een consent afgegeven
Klopt het Verifiable Credential van de aanvrager (geldig en gelijk aan hetgeen is vastgelegd bij de patient). Als middel wordt hiervoor wordt het URA nummer van de opvragende organisatie gebruikt. Deze informatie is door de (wijk)verpleging vastgelegd in het bronsysteem bij de patiënt.
Indien er informatie beschikbaar is en deze vrijgegeven mag worden wordt het interne PatientID terugekoppeld aan het opvragende systeem. Hierin is ook de informatie opgenomen rondom de contactinformatie van de zorgverlener. Hiermee kan de vraag beantwoord worden 'wie moet ik bellen voor deze patient'.

Op basis van dit PatientID kan verdere (medisch inhoudelijke) informatie opgevraagd worden. Er is een lijst beschikbaar van informatie die opgevraagd kan worden indien het bronsysteem deze informatie beschikbaar heeft. Deze bestaat uit rapportages en meetwaarden volgens een afgesproken stramien (in aantal / tijd). Zie hiervoor de paragraaf 'zorginhoudelijke informatie'.

De verdere medisch inhoudelijke informatie wordt opgehaald op basis van FHIR (ZIB's waar mogelijk). De informatie wordt in het doelsysteem getoond. Het doelsysteem is er verantwoordelijk voor de informatie in de juiste context te tonen (denk aan verschillen met eigen informatie). Indien gewenst kan informatie overgenomen worden / opgeslagen in het doelsysteem,

Logging vindt in de gehele keten plaats.

# Uitgangspunten
De cliënt is reeds bekend/in zorg bij zowel de huisarts als bij de VVT instelling

De client heeft consent afgegeven om data te delen met de huisarts

Informatie kan opgehaald worden bij het bronsysteem en vastgelegd in het doelsysteem

Er is een lijst beschikbaar van informatie die opgehaald kan worden

De medewerkers blijven in hun eigen systeem werken. De leveranciers zijn zelf verantwoordelijk hoe zij de medewerker het beste willen/kunnen ondersteunen.

Er wordt gebruik gemaakt van bestaande zorginformatiebouwstenen die voor de leveranciers al bekend zijn. Hierbij wordt FHIR versie van de eOverdracht gebruikt: FHIR STU3.

Het afhandelen van de cobsentvraag vindt plaats in het bronsysteem. Het systeem bepaalt zelf hoe dit vastgelegd wordt en hoe het gecheckt wordt

Authenticatie vindt plaats op basis van een Verifiable Credential: het URA nummer van de organisatie waar de opvrager werkt en zoals vastgelegd in een UZI Servercertificaat.

Autorisatie vindt plaats door een check te doen of het VC in lijn is met wat er is vastgelegd bij de patient in het bronsysteem

Ten behoeve van logging wordt door het doelsysteem het MedewerkerID van de opvrager meegegeven. Deze informatie wordt bij de aanvraag vastgelegd.

Logging vindt in de gehele keten plaats

Shortcuts en Toekomstige ontwikkelingen:
Sommige zaken kunnen we op dit moment niet invullen zoals we willen omdat dit (om verschillende redenen) nog niet realistisch. Deze zaken plaatsen we in deze usecase buiten scope en worden later eventueel toegevoegd. Het gaat om:
-Consent lokaal oplossen (ipv Mitz)
-VC met UZI certificaat (nog geen goed authenticatiemiddel voor de gebruiker beschikbaar / DEZI)
-Toekomstige ZIB's (nu niet beschikbaar in bronsystemen)
-DPOB security (is aanvullende beveiliging die ook in andere usecase niet ingevuld)
-UZI credentials (human readable namen) moet bij de bron opgelost worden

## Architectuur beschrijving

## Lokalisatie

## Grondslag

### Consent specificatie

## Adressering
Discovery Service

### Presentation Definition

## Authenticatie

### Presentation Definition

## Autorisatie

## Informatie
### Lijst van ZIBs


### Tabel met FHIR resources en queries
Hieronder staan de endpoints die beschikbaar gesteld moeten worden door de partijen die toegang tot het ophalen van de informatie in het VVT domein verlenen. Ter verheldering zijn de kolommen Sort en Count toegevoegd om
aan te tonen hoeveel resultaten er geretourneerd worden en op welke manier deze worden gesorteerd. Het is van belang dat bij een aanroep alle parameters gebruikt worden die in de tabel staan en ook geen extra. Dit heeft te maken met de
controle die de systemen doen op de verifiable credentials. Die controle wordt op die manier gedaan om te voorkomen dat
met een parameter zoals een include extra gegevens meekomen.

| ZIB                                                           | Method | Sort      | Count | Endpoint                                                                                                                               | Profiel                                                                                                                                                              |
|:--------------------------------------------------------------|--------|-----------|-------|:---------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Patiënt, Contactpersoon, Behandelaar(huisarts), Zorgaanbieder | GET    |           |       | /fhir/Patient?\_id={patientId}&\_include=Patient:general-practitioner&\_include:iterate=PractitionerRole:organization                  | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954638](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954638) |
| Bloeddruk                                                     | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-PulseRate&\_sort=-date&\_count=5         | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954945](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954945) |
| Lichaamstemperatuur                                           | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyTemperature&\_sort=-date&\_count=5   | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954748](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954748) |
| Lichaamslengte                                                | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyHeight&\_sort=-date&\_count=5        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954746](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954746) |
| Lichaamsgewicht                                               | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-BodyWeight&\_sort=-date&\_count=5        | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954750](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954750) |
| Respiration                                                   | GET    | Date DESC | 5     | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-Respiration&\_sort=-date&\_count=5       | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954947](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954947) |
| Rapportage                                                    | GET    | Date DESC | 10    | /fhir/Observation?patient={patientId}&\_profile=https://nuts.nl/fhir/StructureDefinition/nl-core-nursingreport&\_sort=-date&\_count=10 | [https://simplifier.net/anw/nl-core-nursi ngreport](https://simplifier.net/anw/nl-core-nursingreport)                                                              |
| Woonsituatie                                                  | GET    |           |       | /fhir/Observation?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-LivingSituation                          | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954848](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954848) |
| Alerts                                                        | GET    |           |       | /fhir/Flag?patient={patientId}}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-Alert                                          | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954733](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954733) |
| Wilsverklaring                                                | GET    |           |       | /fhir/Consent?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-AdvanceDirective                             | [https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954726](https://simplifier.net/packages/nictiz.fhir.nl.stu3.zib2017/2.2.10/files/1954726) |
| Allergie                                                      | GET    |           |       | /fhir/AllergyIntolerance?patient={patientId}&\_profile=http://nictiz.nl/fhir/StructureDefinition/zib-AllergyIntolerance                | [http://nictiz.nl/fhir/StructureDefinition/zib-AllergyIntolerance](http://nictiz.nl/fhir/StructureDefinition/zib-AllergyIntolerance)                                 |
## Foutafhandeling
In deze fase concentreren we ons op beveiliging – juist in de pilot zal bekeken moeten worden of dit ook leidt tot voldoende gebruiksgemak. Informatie die publiekelijk beschikbaar is (bv inhoud van de usecase als zijnde 'deze resource valt buiten de usecase') zou toegevoegd mogen worden om nieuwe ontwikkelaars makkelijker te maken bij het testen

## Versiebeheer
Wordt momenteel uitgewerkt in usecase ANW. Resultaat wordt hier opgenomen

## Omgevingen
