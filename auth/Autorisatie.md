# Autorisatie
Voor fase 1 van de Huisartsinzage gaan wij uit van autorisatie op 4 niveaus:
- Behandelrelatie
- Consent
- Zib
- Fhir query parameters

## Behandelrelatie
Om toegang te krijgen tot gegevens van een patient is het vereist dat er een behandelrelatie tussen de huisartspraktijk
en de patient bekend is bij het bronsysteem. Deze controle wordt gedaan op basis van het URA-nummer waarmee een 
resource wordt opgehaald. Dit nummer is terug te vinden in het `organization_ura` veld van een token introspect 
op de Nuts node.

## Consent
Om een resource van een patient op te kunnen halen moet het bronsysteem toestemming van de patient geregistreerd hebben
of op kunnen halen. Een algemene toestemming volstaat hier, waarbij de patient toestemming geeft om zijn of haar
gegevens te delen met huisartsen.

## Zib
Voor de Huisartsinzage is een beperkte set aan Zibs beschikbaar. Deze zijn uitgewerkt in de 
[fhir map](../fhir/resources/resources-phase-1.md). Alleen deze Zibs kunnen opgehaald worden binnen de scope van de
Huisartsinzage.

## Fhir query parameters
Om te voorkomen dat er teveel informatie opgehaald kan worden (bijvoorbeeld middels het gebruik van `_include` 
parameters) wordt er ook autorisatie uitgevoerd op basis van de gebruikte query parameters. Hier wordt onderscheid
gemaakt tussen verplichte parameters en toegestane parameters. Alle query parameters die gedefinieerd zijn in de
[set aan gebruikte Zibs](../fhir/resources/resources-phase-1.md) zijn verplicht, met uitzondering van de `_include`
parameters op de Patient resource. Het gebruik van andere query parameters is niet toegestaan.
