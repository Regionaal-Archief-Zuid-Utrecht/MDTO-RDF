# MDTO-RDF

De [shacl voor de validatie van MTDO-bestanden in RDF-formaat](https://www.nationaalarchief.nl/archiveren/mdto/rdf-ontologie) zoals die gepubliceerd wordt door het Nationaal Archief, voldoet niet aan de shacl-standaard. Sommige validatie-tools zijn desondanks te gebruiken, maar mogelijk met onvolledige validatie (bijvoorbeeld [shacl-validator](https://github.com/ontola/shacl-validator) of [EU ITB Shacl Validator](https://www.itb.ec.europa.eu/shacl/any/upload)). De shacl van het Nationaal Archief werkt niet met de python module [pyshacl](https://github.com/RDFLib/pySHACL).

De *shacl* in deze repositoryis gebaseerd op het bestand van het Nationaal Archief, maar corrigeert de fout die maakt dat **pyshacl** het niet accepteert.

## Aanpassing

De *shacl* van het Nationaal Archief maakt her en der de fout dat de eigenschappen van entiteiten van het type `sh:PropertyShape` en `sh:NodeShape` door elkaar gebruikt worden.
Zo bevat het originele *shacl* bijvoorbeeld de volgende regels:

```` 
mdtosh:ObjectShape
    a sh:NodeShape ;
    sh:targetClass mdto:Object ;
    sh:name "ObjectShape" ;
    sh:description "Een fysiek, digitaal of conceptueel ding in de werkelijkheid dat van belang is voor een organisatie." ;
    sh:property mdtosh:IdentificatieShape ;
    sh:property mdtosh:NaamShape .

mdtosh:IdentificatieShape
    a sh:NodeShape ; 
    sh:path mdto:identificatie ;
    sh:class mdto:IdentificatieGegevens;
    sh:minCount 1 ;
    sh:name "Identificatie" ;
    sh:description "Gegevens waarmee het object geïdentificeerd kan worden." .
````

De gecorrigeerde *shacl* trekt al de onjuiste `NodeShape` 's op de volgende wijze uitelkaar:

````
mdtosh:ObjectShape
    a sh:NodeShape ;
    sh:targetClass mdto:Object ;
    sh:name "ObjectShape" ;
    sh:description "Een fysiek, digitaal of conceptueel ding in de werkelijkheid dat van belang is voor een organisatie." ;
    sh:property mdtosh:IdentificatiePropertyShape ;
    sh:property mdtosh:NaamPropertyShape .

mdtosh:IdentificatiePropertyShape
    a sh:PropertyShape ; 
    sh:path mdto:identificatie ;
    sh:minCount 1 ;
    sh:node mdtosh:IdentificatieShape .

mdtosh:IdentificatieShape
    a sh:NodeShape ; 
    sh:class mdto:IdentificatieGegevens;
    sh:name "Identificatie" ;
    sh:description "Gegevens waarmee het object geïdentificeerd kan worden." .
````

### Validator 

Deze repository biedt een op **pyshacl** gebaseerd validatiescript aan. Dit script kan gebruikt worden om de *shacl* te valideren. Het script staat in de map `scripts` en kan met `python scripts/validate.py <path-to-rdf-file>` worden uitgevoerd.

Ter validatie van de shacl zelf kan een PyTest testsuite gebruikt worden. De bestande van deze testsuite staan in de map `tests` en kunnen met `pytest` worden uitgevoerd.


