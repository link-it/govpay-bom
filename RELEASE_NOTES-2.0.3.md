# GovPay BOM 2.0.3

Data rilascio: 2026-07-23

Patch release che risolve una segnalazione OSV su Jackson `databind`
riordinando gli import di BOM per rendere effettivo l'override di versione.

---

## Sicurezza — segnalazioni risolte

- **GHSA-5gvw-p9qm-jgwh** (CVSS 6.5) — `tools.jackson.core:jackson-databind`,
  `@JsonView` bypassed for `@JsonUnwrapped` container properties on
  deserialization. Fix per il branch 3.2.x: Jackson **3.2.1**.

## Fix

- **Ordinamento import BOM in `<dependencyManagement>`**: `jackson-bom` è
  ora importato **prima** di `spring-boot-dependencies`. Nella 2.0.2 la
  property `<jackson.version>3.2.1</jackson.version>` non era effettiva:
  Spring Boot 4.1.0 (importato prima) imponeva la sua versione managed di
  Jackson (3.1.4) per la regola *first-declared-wins* dei BOM importati
  con `<scope>import</scope>`, lasciando `jackson-databind` vulnerabile.
  A partire da 2.0.3 `jackson-databind` risolve effettivamente a `3.2.1`
  (verificato con `mvn help:effective-pom`).

## Nota tecnica

Per BOM importati con `<scope>import</scope>` in Maven vale la regola
**first-declared-wins**: la sola property nell'`<properties>` non
sovrascrive versioni fissate da BOM importati prima. Stesso pattern
applicato nella 2.0.0 per l'override di Tomcat.

## Upgrade da 2.0.2

Nessuna breaking change. Basta aggiornare la coordinata `<version>` a `2.0.3`.

## Coordinate Maven

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.gov4j.govpay</groupId>
            <artifactId>govpay-bom</artifactId>
            <version>2.0.3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Per il changelog completo della linea 2.0.x fare riferimento a
[RELEASE_NOTES-2.0.0.md](RELEASE_NOTES-2.0.0.md),
[RELEASE_NOTES-2.0.1.md](RELEASE_NOTES-2.0.1.md) e
[RELEASE_NOTES-2.0.2.md](RELEASE_NOTES-2.0.2.md).
