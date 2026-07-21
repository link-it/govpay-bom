# GovPay BOM 2.0.2

Data rilascio: 2026-07-21

Patch release con bump manutentivi di alcune dipendenze e del plugin di
pubblicazione su Maven Central.

---

## Aggiornamenti dipendenze

- **Jackson BOM**: 3.1.4 → 3.2.1 (minor)
- **Logback**: 1.5.35 → 1.5.38
- **PostgreSQL Driver**: 42.7.11 → 42.7.13
- **Swagger v3**: 2.2.51 → 2.2.52
- **swagger-ui** (`org.webjars`): 5.32.6 → 5.32.8

## Build & Tooling

- **central-publishing-maven-plugin**: 0.10.0 → 0.11.0

## Upgrade da 2.0.1

Nessuna breaking change. Basta aggiornare la coordinata `<version>` a `2.0.2`.

## Coordinate Maven

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.gov4j.govpay</groupId>
            <artifactId>govpay-bom</artifactId>
            <version>2.0.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Per il changelog completo della linea 2.0.x fare riferimento a
[RELEASE_NOTES-2.0.0.md](RELEASE_NOTES-2.0.0.md) e
[RELEASE_NOTES-2.0.1.md](RELEASE_NOTES-2.0.1.md).
