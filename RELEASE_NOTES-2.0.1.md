# GovPay BOM 2.0.1

Data rilascio: 2026-07-10

Patch release che risolve una segnalazione OSV su Logback e completa il
deployment della linea 2.0.x su Maven Central (nella 2.0.0 il deploy era
fallito per un'entry errata in `dependencyManagement`, ora corretta).

---

## Sicurezza — segnalazioni risolte

- **Logback**: 1.5.34 → 1.5.35 (fix **GHSA-jhq6-gfmj-v8fx**, CVSS 2.9)

## Fix

- **Entry `jackson-datatype-jsr310`** in `dependencyManagement`: corretto
  l'artifactId (era `jackson-datatype-datetime`, artifact inesistente
  in Jackson 3) e aggiunta versione esplicita `${jackson.version}` per
  soddisfare la validazione Sonatype Central ("Dependency management
  dependency version information is missing"). Il deploy della 2.0.0
  era fallito per questa entry.

## Coordinate Maven

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.gov4j.govpay</groupId>
            <artifactId>govpay-bom</artifactId>
            <version>2.0.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Upgrade da 2.0.0

Nessuna breaking change. Basta aggiornare la coordinata `<version>` a `2.0.1`.

Per il changelog completo della linea 2.0.x fare riferimento anche a
[RELEASE_NOTES-2.0.0.md](RELEASE_NOTES-2.0.0.md).
