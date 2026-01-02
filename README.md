<p align="center">
<img src="https://www.link.it/wp-content/uploads/2025/01/logo-govpay.svg" alt="GovPay Logo" width="200"/>
</p>

# GovPay BOM - Bill of Materials

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://raw.githubusercontent.com/link-it/govpay-bom/master/LICENSE)

Parent POM per la gestione centralizzata delle versioni delle dipendenze nei progetti GovPay.

## Descrizione

**govpay-bom** è un progetto Maven BOM (Bill of Materials) che fornisce:

- Versioni centralizzate per tutte le dipendenze dell'ecosistema GovPay
- Configurazione standard dei plugin Maven
- Garanzia di coerenza tra i vari moduli del progetto

## Utilizzo

Per utilizzare questo BOM come parent nei progetti GovPay:

```xml
<parent>
    <groupId>it.govpay</groupId>
    <artifactId>govpay-bom</artifactId>
    <version>1.0.0</version>
</parent>
```

Le dipendenze possono essere dichiarate senza specificare la versione:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
    </dependency>
</dependencies>
```

## Dipendenze gestite

### Framework

| Dipendenza | Versione |
|------------|----------|
| Spring Boot | 3.5.9 |
| Spring Framework | 6.2.15 |
| Spring Security | 6.5.7 |
| Spring Data | 2025.0.7 |
| Spring Batch | 5.2.4 |

### Database

| Dipendenza | Versione |
|------------|----------|
| Hibernate | 6.6.39.Final |
| HikariCP | 6.3.3 |
| PostgreSQL | 42.7.8 |
| MySQL | 9.5.0 |
| Oracle | 23.7.0.25.01 |
| SQL Server | 12.8.1.jre11 |
| H2 | 2.3.232 |

### Logging

| Dipendenza | Versione |
|------------|----------|
| SLF4J | 2.0.17 |
| Logback | 1.5.22 |

### JSON/XML

| Dipendenza | Versione |
|------------|----------|
| Jackson | 2.19.4 |
| Jakarta XML Bind | 4.0.2 |
| JAXB Runtime | 4.0.5 |

### Utilities

| Dipendenza | Versione |
|------------|----------|
| Commons Lang3 | 3.17.0 |
| Commons IO | 2.21.0 |
| Commons Codec | 1.18.0 |
| Guava | 33.5.0-jre |
| Lombok | 1.18.42 |
| MapStruct | 1.6.3 |

### Testing

| Dipendenza | Versione |
|------------|----------|
| JUnit Jupiter | 5.12.2 |
| Mockito | 5.17.0 |
| AssertJ | 3.27.6 |
| Testcontainers | 1.21.4 |

### API Documentation

| Dipendenza | Versione |
|------------|----------|
| SpringDoc OpenAPI | 3.0.0 |

## Build

```bash
# Build e installazione locale
mvn clean install

# Verifica aggiornamenti dipendenze
mvn versions:display-dependency-updates

# Verifica aggiornamenti plugin
mvn versions:display-plugin-updates
```

## Requisiti

- Java 21+
- Maven 3.8+

## Licenza

GovPay BOM - Bill of Materials per GovPay

Copyright (c) 2014-2025 Link.it srl (http://www.link.it).

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.
