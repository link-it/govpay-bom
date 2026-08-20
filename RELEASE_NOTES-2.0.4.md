# GovPay BOM 2.0.4

Data rilascio: 2026-08-19

Release di sicurezza che risolve 23 segnalazioni su cinque librerie, corregge
l'ordinamento degli import di BOM per Spring e allinea un blocco di dipendenze
alle ultime patch stabili.

---

## Sicurezza — segnalazioni risolte

### Netty

- **GHSA-558v-64gr-wgg4** (CVSS 8.7) — `netty-codec-compression`
- **GHSA-4mp9-239f-g9hg**, **GHSA-6cqp-g7gg-8hr5**, **GHSA-6jqx-86gh-f27w**,
  **GHSA-gcjf-9mgh-3p7g**, **GHSA-jppx-w49h-x2qq**, **GHSA-mvh2-crg5-v77c**,
  **GHSA-q4f6-jm68-57ww** — `netty-codec-http`
- **GHSA-c69g-56f8-xwqj** — `netty-codec-http2`
- **GHSA-hpcc-26xq-25fv** — `netty-codec-http3`
- **CVE-2026-59903** / **GHSA-8c42-7qj2-3j46** — `netty-codec-http`, cache
  poisoning e information disclosure via sovrascrittura dell'header `Vary`
  in CORS
- **CVE-2026-59902** / **GHSA-2qj4-mmr9-4v2f** — `netty-transport-sctp`,
  memory exhaustion in `SctpMessageCompletionHandler`

Fix: `netty-bom` **4.2.17.Final** (Spring Boot 4.1.0 gestisce 4.2.15.Final).

### Apache HttpComponents

- **CVE-2026-64607** / **GHSA-hjcp-jmpx-g3qm** — `httpclient5`, connection leak
  su errore di decode del `Content-Encoding` che porta a esaurimento del pool.
  Fix upstream in 5.6.3, adottata la **5.6.4**.
- **CVE-2026-54399** / **GHSA-hf6x-8p5f-cgmf** — `httpcore5`, memory exhaustion
  nel parsing degli header HTTP/1.
- **CVE-2026-54428** / **GHSA-v3jc-474w-2wm6** — `httpcore5-h2`, `HPackDecoder`
  senza limite di header list size prima del `SETTINGS` ACK.

Fix: `httpclient5` **5.6.4**, `httpcore5` e `httpcore5-h2` **5.4.3**
(Spring Boot 4.1.0 gestisce 5.6.1 e 5.4.2).

### Jackson

- **CVE-2026-59889** / **GHSA-5gvw-p9qm-jgwh** — `@JsonView` bypassed for
  `@JsonUnwrapped` container properties
- **CVE-2026-54515** / **GHSA-5jmj-h7xm-6q6v** — deserializzazione
  case-insensitive che aggira `@JsonIgnoreProperties` per-property
- **GHSA-mhm7-754m-9p8w** — `@JsonView` bypass per creator properties con
  `@JsonTypeInfo(include=As.EXTERNAL_PROPERTY)`

Fix: `com.fasterxml.jackson:jackson-bom` **2.21.5** (Spring Boot 4.1.0 gestisce
2.21.4). GovPay usa Jackson 3, ma il ramo 2.x resta gestito dal BOM di Spring
Boot e può arrivare transitivamente: l'override è difensivo.

### Log4j

- **CVE-2026-49844** / **GHSA-qv9r-c865-cp47** — `log4j-api`, encoding errato
  dei valori floating-point non finiti durante la serializzazione di
  `MapMessage`.

Fix: `log4j-bom` **2.25.5** (Spring Boot 4.1.0 gestisce 2.25.4).

### MySQL Connector/J

- **CVE-2026-60586**, **CVE-2026-60623**, **CVE-2026-60624**, **CVE-2026-61082**
  — Oracle CPU, versioni affette 9.7.0-9.7.1.

Fix: **26.7.0**. Attenzione: non è un major "vero" ma il passaggio di Oracle al
versionamento a calendario dopo la linea 9.7.x.

---

## Fix

### Ordinamento import BOM per Spring

`spring-framework-bom`, `spring-security-bom` e `spring-data-bom` sono ora
importati **prima** di `spring-boot-dependencies`. Con l'ordine precedente i tre
import erano inerti per la regola *first-declared-wins* dei BOM importati con
`<scope>import</scope>`, e le property `spring-framework.version`,
`spring-security.version` e `spring-data.version` non avevano effetto.

Il caso peggiore era il framework: un aggiornamento di
`spring-framework.version` avrebbe alzato solo `spring-web` e `spring-webmvc`,
che hanno entry esplicite in `<dependencyManagement>`, lasciando `spring-core` e
il resto alla versione di Spring Boot — un classpath Spring spezzato su due
versioni, senza alcun errore di build a segnalarlo.

È la stessa classe di problema corretta per `jackson-bom` nella 2.0.3.

**Nessuna versione risolta cambia con questa release**: i valori delle property
coincidono già con quelli gestiti da Spring Boot 4.1.0 (framework 7.0.8,
security 7.1.0, data 2026.0.0, batch 6.0.4). Verificato confrontando
l'`effective-pom` prima e dopo lo spostamento su tutti i 1891 artefatti
gestiti: zero differenze.

### SLF4J allineato a Spring Boot

La property del BOM fissava SLF4J a **2.0.17**, *abbassandolo* rispetto alla
2.0.18 gestita da Spring Boot 4.1.0. Ora è allineata.

---

## Aggiornamenti dipendenze

- **SLF4J**: 2.0.17 → 2.0.18
- **Jackson BOM** (Jackson 3): 3.2.1 → 3.2.2
- **springdoc-openapi**: 3.0.3 → 3.1.0 (build su `spring-boot-parent` 4.1.0)
- **Hibernate ORM**: 7.4.1.Final → 7.4.5.Final
- **Google Guava**: 33.6.0-jre → 33.7.1-jre
- **Oracle JDBC** (`ojdbc11`): 23.26.2.0.0 → 23.26.3.0.0
- **Commons Codec**: 1.22.0 → 1.22.1
- **Swagger v3** (`swagger-annotations`): 2.2.52 → 2.2.54
- **swagger-ui** (`org.webjars`): 5.32.8 → 5.32.14
- **jackson-databind-nullable**: 0.2.10 → 0.2.11

Le versioni Spring sono già tutte all'ultima stabile e restano invariate:
Boot 4.1.0, Framework 7.0.8, Security 7.1.0, Data 2026.0.0, Batch 6.0.4.

---

## Segnalazioni note non risolvibili

**HdrHistogram 2.2.2** — `CVE-2026-14683`, `CVE-2026-14686`.

Non esiste una versione con il fix: la 2.2.2 è l'ultima pubblicata su Maven
Central e il progetto upstream è fermo. La libreria arriva transitivamente da
`micrometer-core`, che la dichiara in scope `runtime` con versione fissata;
escluderla romperebbe il supporto agli istogrammi a percentili di Micrometer.

Entrambe le segnalazioni sono contestate (*disputed*) dal vendor su NVD, in
stato *Deferred*, con CVSS 3.1 pari a **3.3 LOW** e vettore
`AV:L/AC:L/PR:L/UI:N/S:U/C:N/I:L/A:N`, quindi sfruttabili solo con accesso
locale. Vanno gestite come falso positivo nei progetti che le segnalano, per
esempio con una soppressione dependency-check dedicata.

---

## Upgrade da 2.0.3

Nessuna breaking change nel BOM: basta aggiornare la coordinata `<version>` a
`2.0.4`. Due punti da verificare nei progetti a valle:

- **MySQL Connector/J 26.7.0** — il salto di schema di versionamento merita una
  passata dei test di integrazione su MySQL.
- **springdoc-openapi 3.1.0** — tocca la generazione della documentazione API.
  Il BOM mantiene l'override esplicito di `swagger-ui` a 5.32.14, che resta più
  avanti della 5.32.11 tirata transitivamente da springdoc.

## Coordinate Maven

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.gov4j.govpay</groupId>
            <artifactId>govpay-bom</artifactId>
            <version>2.0.4</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Per il changelog completo della linea 2.0.x fare riferimento a
[RELEASE_NOTES-2.0.0.md](RELEASE_NOTES-2.0.0.md),
[RELEASE_NOTES-2.0.1.md](RELEASE_NOTES-2.0.1.md),
[RELEASE_NOTES-2.0.2.md](RELEASE_NOTES-2.0.2.md) e
[RELEASE_NOTES-2.0.3.md](RELEASE_NOTES-2.0.3.md).
