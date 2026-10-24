# Global Rivers — FAIRification Methodology
This document describes how the **Global Rivers - Lengths and Countries** data was transformed into the published FAIRified RDF dataset, semantic models, and metadata. It records the implemented workflow. The [data dictionary](data-dictionary.md) documents individual fields, RDF classes, and predicates.

## 1. Source selection and assessment
The primary source is the [Global Rivers dataset on Kaggle](https://www.kaggle.com/datasets/sangampaudel530/global-rivers-dataset-lengths-and-countries). It was selected for the purpose of this project because river names, country relationships, lengths and coordinates are understandable and useful for external linking. [Natural Earth](https://www.naturalearthdata.com/) is also referenced in project provenance and supporting geographic material.

The original CSV data presented numerous FAIRification challenges: not all river names are unique, country and continent values are plain text, lengths lack an explicit unit, length categories are ambiguous, and coordinate pairs lack their own reusable semantic descriptions. 

The original dataset is published under the `MIT License`, which explicitly permits reuse, modification, and redistribution of the data, under the condition of doing so under the same licensing conditions. 

## 2. Data preparation in OpenRefine
Preparation was performed using **OpenRefine 3.10**. The data was reviewed, cleaned, and organized to produce the uploaded **1,387 unique river entries**. The fields retained for the transformation include river name, length, main country, countries passed, continent,  source and mouth coordinates.

Relevant preparation steps include normalizing values, inspecting duplicate and ambiguous river names, and separting lists of numerous contry entries, e.g., `countries_passed`. A unique river ID was created for each unique record, ensuring that two separate rivers bearing the same name would not be merged into one entry.

The original fields and their RDF mappings are described in the [data dictionary](data-dictionary.md).

## 3. Reconciliation and entity linking
Geographic entities were reconciled against **Wikidata**. External river matches were linked to **Wikidata and GeoNames**, wherever possible. In OpenRefine, reconciliation results were reviewed because name-based matching returned multiple possible candidates or no candidate at all for some entries.

- Country and continent references were converted from text values to external resource IRIs;
- The main country was modelled separately from the list of countries passed by a river;
- External river identity links were recorded only for records with a suitable available match.
- The published data contains **1,229** external identity links: **1,185 Wikidata** and **44 GeoNames**.

The RDF schema uses `owl:sameAs` for asserted river identity links. 

## 4. RDF semantic modelling
This part of teh transformation process uses existing vocabularies instead of defining a new river ontology:

| Entity| Vocabularies / predicates |
| --- | --- |
| River and geography | `schema:RiverBodyOfWater`, `geo:Feature`, `dbo:country`, `schema:geoIntersects`, `schema:containedInPlace` |
| River names and IDs | `rdfs:label`, `dct:identifier` |
| Length and unit | `schema:PropertyValue`, `schema:value`, `schema:unitCode`, `QUDT KiloM` |
| Source/mouth coordinates | `schema:GeoCoordinates`, `wgs:SpatialThing`, `dbo:sourcePosition`, `dbo:mouthPosition` |
| Length category | `dct:type`, `skos:Concept`, `skos:definition` |
| External identity | `owl:sameAs`, `rdf:Statement`, `dct:source` |
| Provenance | `prov:wasDerivedFrom`, `prov:Entity` |

Each river is represented by a URI incorporating its project ID. Related length, source coordinates, mouth coordinates and external link resources have separate URIs. Numeric length is expressed in kilometres, while source and mouth coordinates are represented by decimal-degree latitude/longitude values.

All river lengths were investigated to determine the criteria for length classification, present in the original data. These were defined as: **short** (`< 500 km`), **medium** (`500 - 2000 km`) and **long** (`>= 2000 km`). The published graph contains 815, 513 and 59 rivers in each category, respectively.

## 5. RDF generation and inspection
RDF mapping was constructed in OpenRefine using the RDF extension. During the project, errors were caused form a conflict with the second presented RDF extension, `RDF-transform`. The latter was removed to mitigate these problems. The eventual result was exported as Turtle.

The published data contains **43,499 triples**, which describe **1,387 river resources**, **1,387 length resources**, **2,774 coordinate resources**, **three category concepts**, and **1,229 reified external-link records**. 

The dataset is available at [`data/global-rivers-dataset-csv-RDF.ttl`](../data/global-rivers-dataset-csv-RDF.ttl). 

## 6. SHACL semantic models
A data SHACL model was developed in [Contour](https://contour.fairdatapoint.org/) to describe the expected structure of river resources and their dependent entities. It includes shapes for:

1. Rivers and their country/continent relationship;
2. Source and mouth coordinates with numeric latitude/longitude;
3. Length values with kilometre units;
4. Derived SKOS length categories;
5. Reified external identity links and provenance.

The shape file is [`data/schema-data.ttl`](../data/schema-data.ttl), also resolvable through the [river SHACL W3ID](https://w3id.org/FAIR-course-UT/2026/group07/shapes/river-shapes).

Separately, a **metadata SHACL model** was developed using [Contour](https://contour.fairdatapoint.org/), with shapes for a DCAT dataset and distribution, contact information, VoID class partitions, and potential DQV quality measurements. Its RDF can be found in [`metadata/global-rivers-dataset-metadata-RDF.ttl`](../metadata/global-rivers-dataset-metadata-RDF.ttl). 


## 7. Identifiers and linked publication
Project-specific identifiers use the prefix:

```text
https://w3id.org/FAIR-course-UT/2026/group07/
```
As specified for the purpose of this university project.

This prefix is managed through the W3ID redirect mechanism and specific redirect rules. It is used for the dataset, distribution, metadata, river entities, length values, coordinates, categories and SHACL documents.

W3ID redirects connect public IRIs to files and pages in the GitHub repository. For Turtle requests, rules can redirect to raw RDF documents. 

## 8. Metadata model and FAIR Data Point

A machine-readable dataset metadata record was created using DCAT, Dublin Core Terms, PROV-O, VoID, FOAF and related vocabularies. It describes the dataset, source and provenance, licensing claims, persistent identifiers, RDF distribution, vocabularies used, triple count and per-class counts. The metadata record is available at [`metadata/global-rivers-dataset-SCHACL-schema.ttl`](../metadata/global-rivers-dataset-SCHACL-schema.ttl).

A **local FAIR Data Point reference deployment** was configured with a navigable hierarchy:

`FDP root --> Global Rivers FAIRification Catalogue --> FAIRified Global Rivers Dataset --> RDF Distribution`

The FDP metadata profiles were extended with project fields such as the public dataset identifier, provenance, VoID statistics, creator/spatial coverage, links to documentation and semantic models, and references to the separate public metadata record. FDP-created record URIs remain local, e.g.,  `http://localhost:8080/dataset/...`.

The service runs locally via Docker with an FDP backend/client, GraphDB and MongoDB. As specified in Lecture 9, **it is not publicly deployed**.

## 9. FAIRification improvements
- **Findability:** Project-specific identifiers, machine-readable metadata, and discoverable entity links replace some of the unclearness in the original dataset.
- **Accessibility:** The public data, shapes, and metadata are reachable through HTTP-based GitHub/W3ID URLs. 
- **Interoperability:** The largest gains come from RDF, standard vocabularies, explicit measurement units, geographic resources, SKOS categories, and external Wikidata/GeoNames identifiers.
- **Reusability:** Provenance, the data dictionary, methodology, structured metadata, RDF distributions, semantic models, and source references make the data easier to interpret and reuse. 

