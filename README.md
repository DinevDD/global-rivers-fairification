# Global Rivers FAIRification
FAIRification of the **Global Rivers – Lengths and Countries** dataset, with a semantic RDF representation, external entity links, SHACL models, descriptive metadata, and persistent-identifier redirects.

This project aims to make river information easier to **find, understand, integrate, and reuse**. The original CSV data has been redisigned as linked data using established vocabularies. Accompanying metadata describes the source, remodelling decisions, provenance, and data (re)distributions.

The project is performed as part of the FAIR Data Engineering course at the University of Twente.

## Dataset Overview
| Object | (Number of) Contents |
| --- | ---: |
| Rivers | **1,387** |
| RDF triples | **43,499** |
| Coordinate resources (source and mouth) | **2,774** |
| Length resources | **1,387** |
| Length-category concepts | **3** |
| External identity-link records | **1,229** |
| RDF format | Turtle (`text/turtle`) |
| Dataset metadata version | `1.0.1` |

Each **River** entry contains a name, numeric length in kilometres, length categories (*short, medium, or long*), main and passed-through countries, continents, source and mouth coordinates, and available external entity links. Geographic entities are referenced by external identifiers where reconciliation was possible.
## Access the published resources

| Resource | Link |
| --- | --- |
| Dataset identifier / landing resource | [Dataset W3ID](https://w3id.org/FAIR-course-UT/2026/group07/dataset/rivers) |
| River data distribution | [Distribution W3ID](https://w3id.org/FAIR-course-UT/2026/group07/distribution/rivers-ttl) |
| Download RDF (Turtle) | [Raw Turtle](data/global-rivers-dataset-csv-RDF.ttl) |
| Machine-readable dataset metadata | [Dataset metadata W3ID](https://w3id.org/FAIR-course-UT/2026/group07/metadata/rivers) |
| River data SHACL | [River SHACL W3ID](https://w3id.org/FAIR-course-UT/2026/group07/shapes/river-shapes) |
| Metadata SHACL (Contour) | [Metadata SHACL W3ID](https://w3id.org/FAIR-course-UT/2026/group07/shapes/metadata-shapes) |

The W3ID identifiers resolve through redirect rules to resources maintained in this repository. Depending on the requested representation, a URI may resolve to a GitHub page in this repo or a raw Turtle file for download.
## Repository contents

```text
data/
  global-rivers-dataset-csv-RDF.ttl        FAIRified river RDF (43,499 triples)
  schema-data.ttl                        SHACL shapes for river RDF
metadata/
  global-rivers-dataset-SCHACL-schema.ttl Descriptive dataset metadata in RDF
  global-rivers-dataset-metadata-RDF.ttl  Contour metadata SHACL profile
documents/
  data-dictionary.md                     Fields, entities, predicates and units
  methodology.md                         Preparation, reconciliation and publication
README.md                                Repository overview
```
## Semantic model

Each **River** has a project specific URI and is modelled as both `schema:RiverBodyOfWater` and `geo:Feature`. Its name and identifier use `rdfs:label` and `dct:identifier`. Country and continent relationships use `dbo:country`, `schema:geoIntersects`, and `schema:containedInPlace`.

Length is modelled as a separate `schema:PropertyValue` resource, which is linked using `schema:additionalProperty`, with a numeric `schema:value` and a kilometre unit identifier (`unit:KiloM`). Source and mouth coordinates are modelled as separate `schema:GeoCoordinates` resources and are linked by `dbo:sourcePosition` and `dbo:mouthPosition`.

The three `skos:Concept` length categories are *short* (below 500 km), *medium* (500 to below 2000 km), and *long* (at least 2000 km). Available external identity links use `owl:sameAs`; corresponding reified RDF statements record link provenance. See [data dictionary](documents/data-dictionary.md) for information about the complete mapping and link semantics.
## FAIRification workflow
Read the [methodology](documents/methodology.md) for the detailed process and the corresponding project report on Canvas.

## Metadata and FAIR Data Point
The public GitHub-hosted metadata uses DCAT, Dublin Core Terms, VoID, PROV-O and related vocabularies. A separate **FAIR Data Point (FDP)** deployment provides a navigable metadata hierarchy:

`FAIR Data Point --> Catalogue --> Dataset --> RDF Distribution`

The FDP deployment used in this project runs locally through Docker and is **not a publicly accessible metadata service**, as specified in Lecture 9. Its locally generated resource URLs (such as `http://localhost:8085/dataset/...`) identify FDP records, while the public W3ID identifiers identify the corresponding project resources.
## Reuse and limitations
The dataset can be downloaded as Turtle and used by RDF tools, such as `OpenRefine`. The [River SHACL shapes](data/schema-data.ttl) describe expected node structures and datatypes. The linked data supports queries by river, country, continent, length category, and external identifiers.

## License
As specified by the original distribution of the dataset, this FAIRified redistribution is available under the [MIT License](LICENSE).

## Sources and documentation

- [Original Global Rivers dataset (Kaggle)](https://www.kaggle.com/datasets/sangampaudel530/global-rivers-dataset-lengths-and-countries)
- [Natural Earth](https://www.naturalearthdata.com/)
- [Data dictionary](documents/data-dictionary.md)
- [Methodology](documents/methodology.md)
