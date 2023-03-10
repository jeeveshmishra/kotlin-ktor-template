# sokos-ktor-template

Kan brukes som utgangspunkt for å opprette nye Ktor-apper for Team Økonomi.

## Tilpass repo-et
1. Gi rettighet for å kjøre scriptet `chmod 755 setupTemplate.sh`
2. Kjør scriptet: 
   ```
   ./setupTemplate.sh
   ```
3. Fyll inn prosjektnavn og artifaktnavn (no.nav.sokos.xxx)

## Workflows

NB! Endre navn på mappen `.github/workflow_files` til `.github/workflows` for at github actions skal plukke dem opp. Dette vil sørge for at du får fire github actions:
1. [Deploy alarmer](.github/workflows/alerts.yaml) -> For å pushe opp [alerterator.yaml](.nais/alerterator.yaml) og pushe alarmer for både prod og dev
   1. Denne workflow kjører inviduelt og trigges også hvis det gjøres endringer i [naiserator.yaml](.nais/naiserator.yaml)
2. [Bygg og push Docker image](.github/workflows/build-and-push-docker-image.yaml) -> For å bygge/teste prosjektet og bygge/pushe Docker image
   1. Denne workflow er den aller første som kjøres når kode er i `master/main` branch
3. [Deploy til dev og prod](.github/workflows/deploy-dev-prod.yaml) -> For å pushe [naiserator.yaml](.nais/naiserator.yaml) og deploye applikasjonen til dev og prod
   1. Denne workflow tar seg KUN av deploy av applikasjonen til NAIS. Den er avhengig av at [Bygg og test](.github/workflows/build-and-push-docker-image.yaml) går gjennom
4. [Bygg og test PR](.github/workflows/build-pr.yaml) -> For å bygge og teste alle PR som blir opprettet
   1. Denne workflow kjøres kun når det opprettes pull requester
5. [Sikkerhet](.github/workflows/snyk.yaml) -> For å skanne sårbarhet av avhengigheter og docker image. Kjøres hver morgen kl 06:00
   1. Denne kjøres når [Deploy til dev og prod](.github/workflows/deploy-dev-prod.yaml) har kjørt ferdig
6. [Manuell deploy](./.github/workflows/manual-deploy.yaml) -> For å kjøre manuelle deploys
   7. Denne workflow er for å kunne gjøre manuelle deploy basert på hvilken branch du velger

NB! Hvis du ønsker at [Sikkerhet](.github/workflows/snyk.yaml) kjøres først og [Deploy til dev og prod](.github/workflows/deploy-dev-prod.yaml) kjøres NÅR `Sikkerhet` er ferdig så gjør følgende:

i [snyk.yaml](.github/workflows/snyk.yaml) endrer du fra:
```
on:
  workflow_run:
    workflows: [ "Deploy til dev og prod" ]
```
til
```
on:
  workflow_run:
    workflows: [ "Bygg og push Docker image" ]
```

og i [deploy-dev-prod.yaml](.github/workflows/deploy-dev-prod.yaml) endrer du fra:
```
on:
  workflow_run:
    workflows: [ "Bygg og push Docker image" ]
```
til
```
on:
  workflow_run:
    workflows: [ "Sikkerhet" ]
```

## OpenApi Generator
1. Endre [pets.json](https://github.com/navikt/sokos-ktor-template/blob/master/build.gradle.kts#L73) til hva spec filen skal hete som ligger i [specs](specs) mappa.
2. Når prosjektet bygges genereres det data klasser i `build` mappa. Disse sjekkes ikke inn i Git pga. datamodellen kan endres ganske mye så slipper du pushe inn hver endring i modellen. Dvs du følger kontrakten, altså api spec

## Bygge og kjøre prosjekt
1. Bygg `sokos-ktor-template` ved å kjøre `./gradlew shadowJar`
1. Start appen lokalt ved å kjøre main metoden i [Bootstrap.kt](src/main/kotlin/no/nav/sokos/prosjektnavn/Bootstrap.kt)
1. Appen nås på `URL`

## Ting som enhver utvikler må ta høyde for og fikse
1. [.nais](.nais) -> Mappen inneholder en `naiserator.yaml` fil og en `alerterator.yaml` for å unngå ha en fil for dev og prod for begge filene. Miljøvariabler legges i `dev-gcp.json` og `prod-gcp.json` hvor de populeres inn i `naiserator.yaml` og `alerterator.yaml`. 
   1. NB! Anbefales å gjøre dette slik med mindre du har behov for å opprette to filer for `naiserator.yaml` og `alerterator.yaml` for å fylle applikasjonens behov
      1. [.nais/alerterator.yaml](.nais/alerterator.yaml) -> Default er lagt inn. Legg inn det applikasjonen har behov for
      2. [.nais/naiserator.yaml](.nais/naiserator.yaml) -> Default er lagt inn. Legg inn det applikasjonen har behov for 

# NB!! Kommer du på noe lurt vi bør ha med i template som default så opprett gjerne en PR 
  
## Henvendelser

- Spørsmål knyttet til koden eller prosjektet kan stilles som issues her på github.
- Interne henvendelser kan sendes via Slack i kanalen [#po-utbetaling](https://nav-it.slack.com/archives/CKZADNFBP)

```
Alt under her skal beholdes som en standard dokumentasjon som må fylles ut av utviklere.
```

# Prosjektnavn

# Innholdsoversikt
* [1. Funksjonelle krav](#1-funksjonelle-krav)
* [2. Utviklingsmiljø](#2-utviklingsmiljø)
* [3. Programvarearkitektur](#3-programvarearkitektur)
* [4. Deployment](#4-deployment)
* [5. Autentisering](#5-autentisering)
* [6. Drift og støtte](#6-drift-og-støtte)
* [7. Swagger](#7-swagger)
* [8. Henvendelser](#7-henvendelser)
---

# 1. Funksjonelle Krav
Hva er oppgaven til denne applikasjonen

# 2. Utviklingsmiljø
### Forutsetninger
* Java 17

### Bygge prosjekt
Hvordan bygger jeg prosjektet.

### Lokal utvikling
Hvordan kan jeg kjøre lokalt og hva trenger jeg?

# 3. Programvarearkitektur
Legg ved skissediagram for hvordan arkitekturen er bygget

# 4. Deployment
Distribusjon av tjenesten er gjort med bruk av Github Actions.
[sokos-ktor-template CI / CD](https://github.com/navikt/sokos-ktor-template/actions)

Push/merge til master branche vil teste, bygge og deploye til produksjonsmiljø og testmiljø.
Det foreligger også mulighet for manuell deploy.

# 7. Autentisering
Applikasjonen bruker [AzureAD](https://docs.nais.io/security/auth/azure-ad/) autentisering

### Hente token
1. Installer `vault` kommandolinje verktøy
2. Gi rettighet for å kjøre scriptet `chmod 755 getToken.sh`
3. Kjør scriptet:
   ```
   ./getToken.sh
   ```
4. Skriv inn applikasjonsnavn du vil hente `client_id` og `client_secret` for

# 6. Drift og støtte

### Logging
Hvor finner jeg logger? Hvordan filtrerer jeg mellom dev og prod logger?

[sikker-utvikling/logging](https://sikkerhet.nav.no/docs/sikker-utvikling/logging) - Anbefales å lese

### Kubectl
For dev-gcp:
```shell script
kubectl config use-context dev-gcp
kubectl get pods -n okonomi | grep sokos-ktor-template
kubectl logs -f sokos-ktor-template-<POD-ID> --namespace okonomi -c sokos-ktor-template
```

For prod-gcp:
```shell script
kubectl config use-context prod-gcp
kubectl get pods -n okonomi | grep sokos-ktor-template
kubectl logs -f sokos-ktor-template-<POD-ID> --namespace okonomi -c sokos-ktor-template
```

### Alarmer
Vi bruker [nais-alerts](https://doc.nais.io/observability/alerts) for å sette opp alarmer. Disse finner man konfigurert i [.nais/alerterator.yaml](.nais/alerterator.yaml) filen.

### Grafana
- [appavn](url)
---

# 7. Swagger
Hva er url til Lokal, dev og prod?

