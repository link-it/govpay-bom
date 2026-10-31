<p align="center">
<img src="https://www.link.it/wp-content/uploads/2025/01/logo-govpay.svg" alt="GovPay Logo" width="200"/>
</p>

# GovPay - Porta di accesso al sistema pagoPA - BOM

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=link-it_govpay-bom&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=link-it_govpay-bom)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://raw.githubusercontent.com/link-it/govpay-bom/main/LICENSE)
[![Spring Boot](https://img.shields.io/badge/dynamic/xml?url=https%3A%2F%2Fraw.githubusercontent.com%2Flink-it%2Fgovpay-bom%2Fmain%2Fpom.xml&query=%2F%2F*%5Blocal-name()%3D'spring-boot.version'%5D&label=Spring%20Boot&color=brightgreen)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://openjdk.org/projects/jdk/21/)

Parent POM dei componenti GovPay: fissa in un solo punto le versioni di Java, Spring Boot, driver JDBC, librerie e plugin Maven, e definisce i controlli di build comuni (versione minima di Maven, analisi OWASP Dependency-Check).

## Sommario

`govpay-bom` è un progetto Maven di tipo `pom`, senza codice e senza configurazione applicativa. I progetti GovPay lo dichiarano come `<parent>` ed ereditano:

- le **versioni delle dipendenze** (`dependencyManagement`): importa i BOM di Spring Boot, Spring Framework, Spring Security, Spring Data, Jackson 2 e 3, Log4j, JUnit e Testcontainers, e sovrascrive con voci esplicite le librerie per cui serve una versione diversa da quella di Spring Boot (correzioni di sicurezza, driver JDBC). Le dipendenze si dichiarano senza `<version>`;
- le **versioni e la configurazione dei plugin** (`pluginManagement`): compilatore Java 21 con Lombok e MapStruct, surefire/failsafe, OWASP Dependency-Check con l'esecuzione `check-owasp`, JaCoCo, firma GPG e pubblicazione su Maven Central;
- un **controllo sempre attivo**: `maven-enforcer-plugin` blocca la build con Maven più vecchio di 3.8.1;
- due **profili**: `release` (sorgenti, javadoc, firma e pubblicazione su Maven Central) e `dist` (pacchetto `target/sql.zip` con gli script SQL per le immagini Docker dei batch).

Il pom è la fonte delle versioni: le tabelle di questo README le riportano per comodità e vanno aggiornate quando si modifica il pom.

## Integrazione con GovPay

| Componente | Quando serve | Obbligatorio | Ruolo |
|------------|--------------|--------------|-------|
| [spring-boot-dependencies](https://docs.spring.io/spring-boot/appendix/dependency-versions/coordinates.html) | build | sì | BOM di Spring Boot importato in `dependencyManagement` (proprietà `spring-boot.version`): fornisce le versioni di tutto ciò che il BOM non dichiara esplicitamente (Tomcat, Micrometer, Netty, FreeMarker, ecc.) |
| [govpay-common](https://github.com/link-it/govpay-common) | build, solo profilo `dist` | no | Il profilo `dist` estrae dal suo jar gli script `sql/cleanup/**` di svecchiamento dei metadati Spring Batch (proprietà `govpay-common.sql.version`). È distinta da `govpay-common.version`, la dipendenza di codice che ogni progetto dichiara da sé |
| Maven Central | build | sì | Release del BOM (`https://central.sonatype.com`) e SNAPSHOT di sviluppo (`https://central.sonatype.com/repository/maven-snapshots`, server id `central-snapshots`) |
| Progetti che lo usano come parent | build | - | Su `main` tutti alla versione `2.0.5-SNAPSHOT`. API: [govpay-console-api](https://github.com/link-it/govpay-console-api), [govpay-gde-api](https://github.com/link-it/govpay-gde-api), [govpay-pafornode-api](https://github.com/link-it/govpay-pafornode-api), [govpay-pendenze-api](https://github.com/link-it/govpay-pendenze-api), [govpay-portal-api](https://github.com/link-it/govpay-portal-api), [govpay-riconciliazioni-api](https://github.com/link-it/govpay-riconciliazioni-api), [govpay-stampe-api](https://github.com/link-it/govpay-stampe-api). Batch: [govpay-aca-batch](https://github.com/link-it/govpay-aca-batch), [govpay-appio-batch](https://github.com/link-it/govpay-appio-batch), [govpay-fdr-batch](https://github.com/link-it/govpay-fdr-batch), [govpay-iban-batch](https://github.com/link-it/govpay-iban-batch), [govpay-maggioli-jppa](https://github.com/link-it/govpay-maggioli-jppa), [govpay-mail-batch](https://github.com/link-it/govpay-mail-batch), [govpay-notify-batch](https://github.com/link-it/govpay-notify-batch), [govpay-rt-batch](https://github.com/link-it/govpay-rt-batch), [govpay-tracciati-batch](https://github.com/link-it/govpay-tracciati-batch). Librerie: [govpay-common](https://github.com/link-it/govpay-common), [govpay-common-auth](https://github.com/link-it/govpay-common-auth), [govpay-common-pendenze](https://github.com/link-it/govpay-common-pendenze), [govpay-ec-client](https://github.com/link-it/govpay-ec-client) |

Non lo usano: il core [govpay](https://github.com/link-it/govpay) (build Maven propria), `govpay-pagamenti-api` (derivato dal core) e i frontend [govpay-console](https://github.com/link-it/govpay-console) e [govpay-portal](https://github.com/link-it/govpay-portal) (Angular).

## Compilazione

Requisiti: JDK 21 e Maven 3.8.1 o successivo (lo verifica `maven-enforcer-plugin`).

```bash
mvn clean install
```

Il BOM non ha codice: la build esegue solo `maven-enforcer-plugin` e installa `govpay-bom-<versione>.pom` nel repository Maven locale, dove lo trovano i progetti che lo usano come parent. L'analisi OWASP non gira sul BOM stesso: il plugin è solo in `pluginManagement` (vedi [Plugin ed esecuzioni ereditate](#plugin-ed-esecuzioni-ereditate)).

Comandi utili:

```bash
# Aggiornamenti disponibili per dipendenze e plugin
mvn versions:display-dependency-updates
mvn versions:display-plugin-updates

# Valore di una proprietà (es. versione del driver PostgreSQL)
mvn -q -DforceStdout help:evaluate -Dexpression=postgresql.version

# Versioni effettive viste da un progetto figlio (da lanciare nel progetto figlio)
mvn help:effective-pom
```

### Pubblicazione

Il workflow [publish.yml](.github/workflows/publish.yml) gira su push e pull request verso `main` e sui tag:

1. `dependency-check`: analisi OWASP con il database NVD in cache;
2. `osv-scan`: scansione OSV del `pom.xml` (su push di `main` e sui tag, oppure con la variabile `FORCE_OSV_JOB=true`);
3. `snapshot-on-main`: su push di `main`, se la versione è `-SNAPSHOT`, `mvn deploy` sul repository SNAPSHOT di Maven Central;
4. `release-on-tag`: su tag `X.Y.Z` uguale alla versione del pom (non SNAPSHOT), `mvn deploy -Prelease` su Maven Central.

Il workflow [refresh-owasp-db.yml](.github/workflows/refresh-owasp-db.yml) aggiorna ogni notte (03:00 UTC) il database NVD in cache con la CLI di Dependency-Check, alla stessa versione di `owasp.plugin.version`.

Le novità di ogni versione sono in [ChangeLog](ChangeLog) e nei file `RELEASE_NOTES-<versione>.md`.

## Utilizzo

Dichiarazione come parent:

```xml
<parent>
    <groupId>org.gov4j.govpay</groupId>
    <artifactId>govpay-bom</artifactId>
    <version>2.0.4</version>
    <relativePath/>
</parent>
```

L'ultima release su Maven Central è `2.0.4`; la versione di sviluppo su `main` è `2.0.5-SNAPSHOT`. Per usare una SNAPSHOT il progetto deve dichiarare il repository delle SNAPSHOT, come fanno tutti i progetti GovPay:

```xml
<repositories>
    <repository>
        <id>central-snapshots</id>
        <url>https://central.sonatype.com/repository/maven-snapshots</url>
        <releases><enabled>false</enabled></releases>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
</repositories>
```

Le dipendenze si dichiarano senza versione:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### Versioni gestite

Valori del pom su `main`. "Override" indica che il BOM fissa una versione diversa da quella di Spring Boot 4.1.2.

| Componente | Proprietà | Versione | Note |
|------------|-----------|----------|------|
| Java | `java.version` | 21 | Anche `maven.compiler.source`/`target` |
| Spring Boot | `spring-boot.version` | 4.1.2 | BOM `spring-boot-dependencies` e `spring-boot-maven-plugin` |
| Spring Framework | `spring-framework.version` | 7.0.10 | BOM importato prima di Spring Boot |
| Spring Security | `spring-security.version` | 7.1.2 | BOM importato prima di Spring Boot |
| Spring Data | `spring-data.version` | 2026.0.2 | BOM importato prima di Spring Boot |
| Spring Batch | `spring-batch.version` | 6.0.6 | Anche gli schemi SQL estratti dal profilo `dist` |
| Hibernate ORM | `hibernate.version` | 7.4.14.Final | Override (Spring Boot: 7.4.13.Final) |
| HikariCP | `hikaricp.version` | 7.1.0 | Override (Spring Boot: 7.0.2) |
| PostgreSQL JDBC (`org.postgresql:postgresql`) | `postgresql.version` | 42.7.14 | |
| MySQL Connector/J (`com.mysql:mysql-connector-j`) | `mysql.version` | 26.10.0 | Override per CVE-2026-60586/60623/60624/61082 |
| Oracle JDBC (`com.oracle.database.jdbc:ojdbc11`) | `oracle.version` | 23.26.3.0.0 | |
| SQL Server JDBC (`com.microsoft.sqlserver:mssql-jdbc`) | `mssql.version` | 13.6.1.jre11 | Override (Spring Boot: 13.4.0.jre11) |
| H2 (`com.h2database:h2`) | `h2.version` | 2.5.252 | Override (Spring Boot: 2.4.240) |
| SLF4J | `slf4j.version` | 2.0.20 | |
| Logback | `logback.version` | 1.6.6 | Override per CVE-2026-19880 |
| Log4j 2 | `log4j2.version` | 2.26.1 | Override per CVE-2026-49844 (BOM `log4j-bom`) |
| Apache HttpClient 5 / HttpCore 5 | `httpclient5.version`, `httpcore5.version` | 5.6.5 / 5.4.4 | |
| Jackson 3 (`tools.jackson`) | `jackson.version` | 3.2.4 | Override (BOM `jackson-bom` 3) |
| Jackson 2 (`com.fasterxml.jackson`) | `jackson2.version` | 2.22.4 | Override per le vulnerabilità del ramo 2.x arrivato in modo transitivo |
| Jakarta XML Bind / JAXB runtime | `jakarta-xml-bind.version`, `jaxb-runtime.version` | 4.0.5 / 4.0.9 | |
| Jakarta Servlet / Validation / Persistence / Annotation | `jakarta-servlet.version`, `jakarta-validation.version`, `jakarta-persistence.version`, `jakarta-annotation.version` | 6.1.0 / 3.1.1 / 3.2.0 / 3.0.0 | |
| Commons Lang3 / IO / Codec / CSV | `commons-lang3.version`, `commons-io.version`, `commons-codec.version`, `commons-csv.version` | 3.21.0 / 2.22.0 / 1.23.0 / 1.14.1 | |
| Guava | `guava.version` | 33.7.2-jre | |
| Lombok / MapStruct | `lombok.version`, `mapstruct.version` | 1.18.48 / 1.6.3 | Anche come annotation processor del compilatore |
| JUnit | `junit-jupiter.version` | 6.1.3 | Override (BOM `junit-bom`; Spring Boot: 6.0.3) |
| Mockito / AssertJ | `mockito.version`, `assertj.version` | 5.24.0 / 3.27.7 | |
| Testcontainers | `testcontainers.version` | 2.0.5 | BOM `testcontainers-bom` |
| springdoc-openapi | `springdoc-openapi.version` | 3.2.0 | `springdoc-openapi-starter-webmvc-ui`, `-webflux-ui` |
| OpenAPI Generator | `openapi.tool.codegen.version` | 7.26.0 | Libreria e `openapi-generator-maven-plugin` |
| jackson-databind-nullable | `jackson-databind-nullable.version` | 0.2.13 | |
| Swagger Annotations v1 / v3 | `swagger-v1.version`, `swagger-v3.version` | 1.6.16 / 2.2.55 | |

Tutto il resto (Tomcat, Micrometer, Netty, FreeMarker, ...) arriva dal BOM di Spring Boot.

### Plugin ed esecuzioni ereditate

| Plugin | Versione | Cosa eredita il progetto figlio |
|--------|----------|---------------------------------|
| `maven-enforcer-plugin` | 3.6.3 | **Sempre attivo** (è in `<build><plugins>`): esecuzione `enforce-maven-version`, regola `requireMavenVersion` `[${maven.min.version},)` |
| `maven-compiler-plugin` | 3.16.0 | `source`/`target` = `${java.version}`, `-parameters`, annotation processor Lombok e MapStruct |
| `maven-surefire-plugin`, `maven-failsafe-plugin` | 3.6.1 | Solo versione |
| `maven-jar-plugin`, `maven-war-plugin` | 3.5.1 | Solo versione |
| `maven-resources-plugin`, `maven-clean-plugin` | 3.5.0 | Solo versione |
| `maven-install-plugin`, `maven-deploy-plugin` | 3.2.0 | Solo versione |
| `maven-source-plugin`, `maven-javadoc-plugin` | 3.4.0, 3.12.0 | Solo versione; esecuzioni nel profilo `release` |
| `spring-boot-maven-plugin` | `${spring-boot.version}` | Solo versione |
| `jaxb2-maven-plugin` | 4.2.0 | Solo versione |
| `dependency-check-maven` (OWASP) | 13.0.0 | Configurazione ed esecuzione `check-owasp` (goal `aggregate`, fase `${owasp.phase}`). **Si attiva solo se il progetto dichiara il plugin** nei suoi `<build><plugins>`, anche senza versione né configurazione |
| `jacoco-maven-plugin` | 0.8.16 | Solo versione: le esecuzioni (`prepare-agent`, `report`) le dichiara il progetto |
| `maven-dependency-plugin`, `maven-assembly-plugin` | 3.11.0, 3.8.0 | Esecuzioni `unpack-spring-batch-schema` e `sql-zip`, attive solo nel profilo `dist` |
| `maven-gpg-plugin` | 3.2.8 | Firma con Bouncy Castle (`signer=bc`), esecuzione `sign-artifacts` in `verify`; attivo nel profilo `release` |
| `central-publishing-maven-plugin` | 0.12.0 | `publishingServerId=central`, `autoPublish=true`; attivo nel profilo `release` |

Per attivare l'analisi OWASP basta:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.owasp</groupId>
            <artifactId>dependency-check-maven</artifactId>
        </plugin>
    </plugins>
</build>
```

Il progetto può aggiungere configurazione propria (per esempio `suppressionFiles`), ma **non deve dichiarare una seconda esecuzione** dell'analisi: `check-owasp` è già ereditata e un'esecuzione con un altro id la farebbe girare due volte.

### Profili

| Profilo | Attivazione | Cosa fa |
|---------|-------------|---------|
| `release` | `-Prelease` | Allega sorgenti e javadoc, firma gli artefatti con GPG e li pubblica su Maven Central. Lo usa il workflow di pubblicazione sui tag |
| `dist` | `-Pdist`, sempre insieme al profilo di packaging del progetto (es. `-Pjar,dist`) | Produce `target/sql.zip` per le immagini Docker dei batch: gli schemi Spring Batch estratti da `spring-batch-core` (`target/sql-upstream`), gli script di svecchiamento estratti da `govpay-common` (`target/sql-govpay-common`) e gli script del progetto. Il progetto deve avere il descrittore `src/assembly/sql.xml`. Va combinato con il profilo di packaging perché `-P` disattiva i profili `activeByDefault`, che nei batch portano i driver JDBC in scope `provided` |

### Compatibilità

- Java 21+
- Maven 3.8.1+
- Spring Boot 4.x

## Configurazione

Il BOM non ha proprietà applicative. Le proprietà del pom elencate qui si possono sovrascrivere nel `<properties>` del progetto figlio oppure da riga di comando con `-D<proprietà>=<valore>`.

### Analisi OWASP

| Proprietà | Default | Descrizione |
|-----------|---------|-------------|
| `owasp.phase` | `verify` | Fase in cui gira l'esecuzione `check-owasp`. `none` la disattiva |
| `owasp.autoUpdate` | `false` | Aggiornamento del database NVD durante l'analisi. Disattivato per il bug di parsing delle date NVD ([DependencyCheck #8424](https://github.com/dependency-check/DependencyCheck/issues/8424)): il database si aggiorna a parte (in CI con [refresh-owasp-db.yml](.github/workflows/refresh-owasp-db.yml)) |
| `owasp.failBuildOnCVSS` | `11` | Punteggio CVSS da cui la build fallisce. Con `11` (fuori scala) non fallisce mai: le vulnerabilità finiscono solo nel report |
| `owasp.ossindex.analyzer.enabled` | `true` | Analizzatore Sonatype OSS Index, in aggiunta al database NVD |
| `owasp.nvdMaxRetryCount` | `30` | Ritentativi sulle richieste all'API NVD |
| `owasp.nvdApiDelay` | `4000` | Pausa in ms tra due richieste all'API NVD |
| `owasp.plugin.version` | `13.0.0` | Versione di `dependency-check-maven` (la legge anche la CI per la chiave della cache) |

Flag da usare nei progetti figli:

```bash
# Build senza analisi OWASP (sviluppo)
mvn clean install -Dowasp.phase=none

# Analisi con aggiornamento del database NVD
mvn clean verify -Dowasp.autoUpdate=true
```

**Attenzione:** il pom fissa `<autoUpdate>${owasp.autoUpdate}</autoUpdate>` nella configurazione del plugin, quindi la proprietà del plugin `-DautoUpdate=...` **non ha effetto**: si usa sempre `-Dowasp.autoUpdate=...`. Allo stesso modo `-Dowasp=none` non disattiva `check-owasp`: vale solo per i progetti che hanno ancora un'esecuzione propria legata alla proprietà `${owasp}`. Le proprietà del plugin non fissate dal pom (`-DdataDirectory`, `-DnvdApiKey`, `-DossIndexUsername`, `-DossIndexPassword`, `-Ddependency-check.skip`) funzionano normalmente.

### Build

| Proprietà | Default | Descrizione |
|-----------|---------|-------------|
| `maven.min.version` | `3.8.1` | Versione minima di Maven richiesta da `maven-enforcer-plugin` (minimo di `dependency-check-maven` 13.x) |
| `java.version` | `21` | Versione di Java per il compilatore |
| `project.build.sourceEncoding`, `project.reporting.outputEncoding` | `UTF-8` | Codifica dei sorgenti e dei report |
| `govpay-common.sql.version` | `2.0.4-SNAPSHOT` | Versione di `govpay-common` da cui il profilo `dist` estrae gli script `sql/cleanup/**` |

### Versioni

Le proprietà di versione della tabella [Versioni gestite](#versioni-gestite) si possono sovrascrivere nel progetto figlio, con un limite: hanno effetto solo sulle voci che `govpay-bom` dichiara esplicitamente e sui BOM che importa (per esempio `spring-boot.version` cambia la versione del BOM di Spring Boot). Le proprietà interne al BOM di Spring Boot (es. `tomcat.version`) definite nel `<properties>` del figlio **non** cambiano nulla, perché un BOM importato usa i propri valori: per quelle serve una voce in `<dependencyManagement>` del progetto.

## Licenza

GovPay BOM è distribuito con licenza [GNU General Public License v3.0](LICENSE).

Copyright (c) 2014-2026 Link.it srl.
