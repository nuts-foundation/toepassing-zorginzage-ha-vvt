# Scope
# Governance
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

## Architectuur beschrijving

## Lokalisatie
### Care Team specificatie

## Grondslag

### Consent specificatie

## Adressering
Discovery Service

### Presentation Definition

## Authenticatie

### Presentation Definition

## Autorisatie

### Rego policy

## Informatie
### Lijst van ZIBs


### Tabel met FHIR resources en queries

## Foutafhandeling

## Versiebeheer
