# Global Rivers FAIRification
FAIRification of the **Global Rivers – Lengths and Countries** dataset, with a semantic RDF representation, external entity links, SHACL models, descriptive metadata, and persistent-identifier redirects.

This project aims to make river information easier to **find, understand, integrate, and reuse**. The original CSV data has been redesigned as Linked Data using established vocabularies. Accompanying metadata describes the source, remodelling decisions, provenance, and data (re)distributions.

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
| Competency questions SPARQL queries | [Queries](competency-questions/queries/) |
| Competency questions results | [Answers](competency-questions/answers/) |
| Original CSV distribution | [Original CSV W3ID](https://w3id.org/FAIR-course-UT/2026/group07/distribution/rivers-original-csv) |
| Download original CSV | [Raw CSV](data/original/global_rivers_dataset.csv) |
| FDP Dataset Profile | [Dataset SHACL](metadata/fdp/dataset-profile.ttl) |
| FDP Distribution Profile | [Distribution SHACL](metadata/fdp/distribution-profile.ttl) |
| FDP metadata records | [Exported FDP metadata](metadata/fdp/) |
| Semantic data model (OWL) | [OWL Ontology](data/ont.owl) |


The W3ID identifiers resolve through redirect rules to resources maintained in this repository. Depending on the requested representation, a URI may resolve to a GitHub page in this repo or a raw Turtle file for download.
## Repository contents

```text
data/
  global-rivers-dataset-csv-RDF.ttl         FAIRified river RDF (43,499 triples)
  schema-data.ttl                         SHACL shapes for the river RDF
  ont.owl                                 OWL ontology defining the semantic data model
  original/
    global_rivers_dataset.csv             Original Kaggle CSV dataset
metadata/
  global-rivers-dataset-SCHACL-schema.ttl  FAIRified dataset metadata in RDF
  global-rivers-dataset-metadata-RDF.ttl   Contour SHACL profile for FAIRified metadata
  original/
    original-dataset-metadata.ttl          Retrospective metadata for the Kaggle dataset
    original-dataset-shacl.ttl             SHACL shape for the original dataset metadata
  fdp/
    dataset-profile.ttl                   FDP Dataset SHACL profile
    distribution-profile.ttl              FDP Distribution SHACL profile
    dataset-record.ttl                    Exported FDP dataset metadata
    distribution-original-csv.ttl         Exported original CSV distribution metadata
    distribution-fairified-ttl.ttl        Exported FAIRified RDF distribution metadata
    datapoint.ttl                         Exported FAIR Data Point metadata
    catalogue.ttl                         Exported FDP catalogue metadata
documents/
  data-dictionary.md                      Attributes, entities, predicates, and units
  methodology.md                          Preparation, reconciliation, and publication
competency-questions/
  queries/                                SPARQL queries for the competency questions (.rq)
  answers/                                Corresponding query results (.csv)
presentation/
  FAIR_presentation.pptx                  Project presentation
w3id/
  .htaccess                               W3ID redirect rules
  tests.yaml                              W3ID redirect tests
LICENSE                                   MIT license
README.md                                 Repository overview
```
## Semantic model

Each **River** has a project specific URI and is modelled as both `schema:RiverBodyOfWater` and `geo:Feature`. Its name and identifier use `rdfs:label` and `dct:identifier`. Country and continent relationships use `dbo:country`, `schema:geoIntersects`, and `schema:containedInPlace`.

Length is modelled as a separate `schema:PropertyValue` resource, which is linked using `schema:additionalProperty`, with a numeric `schema:value` and a kilometre unit identifier (`unit:KiloM`). Source and mouth coordinates are modelled as separate `schema:GeoCoordinates` resources and are linked by `dbo:sourcePosition` and `dbo:mouthPosition`.

The three `skos:Concept` length categories are *short* (below 500 km), *medium* (500 to below 2000 km), and *long* (at least 2000 km). Available external identity links use `owl:sameAs`; corresponding reified RDF statements record link provenance. See [data dictionary](documents/data-dictionary.md) for information about the complete mapping and link semantics.
## FAIRification workflow
Read the [methodology](documents/methodology.md) for the detailed process and the corresponding project report on Canvas.

The semantic data model is formally defined in an [OWL ontology](data/ont.owl), which documents the classes, properties, and relationships used in the FAIRified RDF dataset. The ontology reuses concepts and predicates from established vocabularies, while project-specific definitions use the group's namespace. The visual representation of the semantic model is included in the project report. Cardinality and datatype constraints are specified separately in the accompanying [SHACL shapes](data/schema-data.ttl).

## Metadata and FAIR Data Point

The public GitHub-hosted metadata uses DCAT, Dublin Core Terms, VoID, PROV-O, and related vocabularies. A separate **FAIR Data Point (FDP)** deployment provides a structured metadata hierarchy:

FAIR Data Point --> Catalogue --> Dataset --> Two Distributions

The dataset contains two published distributions:
- **Original CSV Distribution:** The original Kaggle dataset, prior to FAIRification, available as `text/csv`.
- **FAIRified RDF Distribution:** The triplified dataset, available as `text/turtle`.

The distributions' `dcat:accessURL` properties reference their corresponding GitHub file pages, allowing users to inspect the published data. Their `dcat:downloadURL` properties use persistent W3ID identifiers, which resolve directly to the downloadable CSV and RDF Turtle files respectively.

The FDP's Dataset and Distribution metadata profiles were extended using SHACL to accommodate the project's metadata requirements. The resulting profiles, together with the exported RDF metadata records of the FAIR Data Point, catalogue, dataset, and both distributions, are available in the [metadata/fdp/](metadata/fdp/) directory.

These five metadata records document the complete FDP hierarchy and preserve its RDF representation independently of the running FDP instance.

The FDP deployment runs locally using Docker and is **not publicly accessible**. Its locally generated resource URIs identify the FDP metadata records, while the public W3ID identifiers provide persistent references to the published project resources. Exporting the FDP metadata to GitHub additionally makes these records publicly accessible independently of the local deployment.

## License
As specified by the original distribution of the dataset, this FAIRified redistribution is available under the [MIT License](LICENSE).

## Sources and documentation

- [Original Global Rivers dataset (Kaggle)](https://www.kaggle.com/datasets/sangampaudel530/global-rivers-dataset-lengths-and-countries)
- [Natural Earth](https://www.naturalearthdata.com/)
- [Data dictionary](documents/data-dictionary.md)
- [Methodology](documents/methodology.md)
