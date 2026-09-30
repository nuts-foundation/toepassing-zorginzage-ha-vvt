# Discovery-definities en access policies

Per omgeving: `discovery_*` (Discovery Service-definitie, voor server en alle clients) en `policy_*` (access policy, voor iedere data-houder). Beide worden bij het starten van de node ingelezen.

## ca-fingerprints

De `$.issuer` van een `X509Credential` is een `did:x509` die de SHA-256 (base64url, zonder padding) van de uitgevende CA van het UZI-servercertificaat bevat. De definities staan alleen de CA's van het UZI-register toe:

| Omgeving | Uitgevende CA | ca-fingerprint |
|---|---|---|
| productie | `UZI-register Private Server CA G1` (tot 12-11-2028) | `vdhg746H4rLH67NN1unhdxo6PF3shQunCA4-KQTb2Jc` |
| productie | `UZI Server - G4 PKIo Priv G-TLS SYS - 2025` (vanaf 12-11-2026) | `sDTL__yv54TtrvSf7601TtvHgj2NZSLT2m-Okf8oqkI` |
| test / acceptatie | `TEST UZI-register Private Server CA G1` (tot 12-11-2028) | `GwlhBZuEFlSHXSRUXQuTs3_YpQxAahColwJJj35US1A` |
| test / acceptatie | `ACCEPTATIE UZI Server - G4 Priv G-TLS SYS - 2025` (vanaf 12-11-2026) | `svKzNxhS07V1dKkekWOZV0d6Ao7_qvjqXegWB32YjZE` |

Controleren:

```sh
curl -sO http://cert.pkioverheid.nl/UZIServerG4PKIoPrivGTLSSYS2025.cer
openssl dgst -sha256 -binary UZIServerG4PKIoPrivGTLSSYS2025.cer | base64 | tr '+/' '-_' | tr -d '='
```

De G4-CA `... SYS - 2024` is op 29-10-2025 ingetrokken en wordt niet vertrouwd.

## Overgang G1 → G4

Alle deelnemers en de Discovery Server moeten de bijgewerkte bestanden in gebruik hebben vóórdat een deelnemer een credential van een G4-certificaat gaat gebruiken. Na 12-11-2028 kan de G1-fingerprint vervallen.

Achtergrond: https://github.com/nuts-foundation/nuts-node/issues/4355.
