# Global Rivers — Data Dictionary
The current document describes the source attributes and the semantic representation of the **FAIRified Global Rivers** dataset. It refers to the published Turtle snapshot containing **1,387 river resources and 43,499 RDF triples**. The [Project README](../README.md) provides download and model links.

## 1. Original tabular attributes
These are the conceptual fields used in the tabular-to-RDF mapping.

| Attribute | Description | Expected value / unit | RDF representation |
| --- | --- | --- | --- |
| `river_name` | Name of a river | Text | `rdfs:label`  |
| `length_km` | Length of the river | Numeric, **kilometres** | `schema:additionalProperty` --> `schema:PropertyValue` with `schema:value` |
| `main_country` | Main country through which the river flows | Country name, reconciled to IRI | `dbo:country` |
| `countries_passed` | All countries intersected by the river | One or multiple country names/IRIs | Repeated `schema:geoIntersects` values |
| `continent` | Continent associated with the river | Continent name, reconciled to IRI | `schema:containedInPlace` |
| `source_lat`, `source_lon` | Latitude and longitude of the river source | Numeric degrees, WGS 84 | `dbo:sourcePosition` --> `schema:latitude`, `schema:longitude` |
| `mouth_lat`, `mouth_lon` | Latitude and longitude of the river mouth | Numeric degrees, WGS 84 | `dbo:mouthPosition` --> `schema:latitude`, `schema:longitude` |
| Generated river ID | Distinguishes project records, including ambiguous river names | `river-` followed by a 16-character hexadecimal identifier | `dct:identifier` |
| Derived length category | Classification calculated from `length_km` | *Short* / *Medium* / *Long* | `dct:type` --> `skos:Concept` |

## 2. Core RDF classes
| Class | Role | Number of Instances |
| --- | --- | ---: |
| `schema:RiverBodyOfWater` | River resource | 1,387 |
| `geo:Feature` | Additional geographic type of each river | 1,387 (same river resources) |
| `schema:PropertyValue` | River-length measurement | 1,387 |
| `schema:GeoCoordinates` | River source and mouth position resources | 2,774 |
| `wgs:SpatialThing` | Additional type of the coordinate resources | 2,774 (same position resources) |
| `skos:Concept` | Defined length categories | 3 |
| `rdf:Statement` / `prov:Entity` | Reified provenance of external identity links | 1,229 (same link records) |


## 3. River resource properties
Example river identifier: `https://w3id.org/FAIR-course-UT/2026/group07/river/river-14deb23c806d097f`.

| RDF predicate | Object | Description |
| --- | --- | --- |
| `rdf:type` | `schema:RiverBodyOfWater`, `geo:Feature` | Identifies each river as a river body of water and a geographic feature |
| `rdfs:label` | String | Human-readable river name |
| `dct:identifier` | String | Project-specific stable river ID |
| `dbo:country` | IRI | Main country |
| `schema:geoIntersects` | One or more IRIs | Countries intersected |
| `schema:containedInPlace` | IRI | Continent |
| `schema:additionalProperty` | IRI | River length resource |
| `dct:type` | IRI | One of the three length-category concepts |
| `dbo:sourcePosition` | IRI | Coordinate resource describing the river source |
| `dbo:mouthPosition` | IRI | Coordinate resource describing the river mouth |
| `owl:sameAs` | External resource IRI, when available | Identity link to Wikidata or GeoNames |
| `prov:wasDerivedFrom` | Source IRI | Original source provenance |

## 4. Length values and classification
Lengths are stored as their own `schema:PropertyValue` resources:

| Predicate | Value | Meaning |
| --- | --- | --- |
| `rdf:type` | `schema:PropertyValue` | Measurement/resource type |
| `schema:value` | `xsd:double` | Numeric length |
| `schema:unitCode` | `http://qudt.org/vocab/unit/KiloM` | Kilometres |
| `schema:propertyID` | `http://dbpedia.org/ontology/length` | Quantity measured |



| Category IRI suffix | Definition | Count|
| --- | --- | ---: |
| `length-category/short` | Length below **500 km** | 815 |
| `length-category/medium` | Length between **500 km** and **2000 km** | 513 |
| `length-category/long` | Length above **2000 km** | 59 |



## 5. Geographic coordinates
Source and mouth locations are separate resources:

```text
river/{river-id}
  ├── dbo:sourcePosition --> position/source/{river-id}
  └── dbo:mouthPosition  --> position/mouth/{river-id}
```

## 6. External identity links and provenance
**1,229** river identity links appear: **1,185** target Wikidata resources and **44** target GeoNames resources. The river itself uses `owl:sameAs` to point to an external resource when a match exists.

Each available link is also represented by a resource typed as `rdf:Statement` and `prov:Entity`. The record uses:

| Predicate | Meaning |
| --- | --- |
| `rdf:subject` | Local river IRI |
| `rdf:predicate` | `owl:sameAs` |
| `rdf:object` | External entity IRI |
| `dct:source` | Link provider, e.g. Wikidata or GeoNames |
| `prov:wasDerivedFrom` | External record used as source |


## 7. Identifiers and namespaces
Project resource IRIs use the prefix:

```text
https://w3id.org/FAIR-course-UT/2026/group07/
```

Main patterns are `river/{river-id}`, `length/{river-id}`, `position/source/{river-id}`, `position/mouth/{river-id}`, `link/{river-id}`, and `length-category/{short|medium|long}`. The W3ID service redirects to repository resources; it is not a per-entity query endpoint.

Key vocabularies include Schema.org (`http://schema.org/`), GeoSPARQL (`http://www.opengis.net/ont/geosparql#`), WGS 84, DBpedia ontology, SKOS, Dublin Core Terms, PROV-O, RDF, OWL and QUDT unit identifiers.

## 8. Documentation and validation

- [River RDF](../data/global-rivers-dataset-csv-RDF.ttl)
- [River SHACL shapes](../data/schema-data.ttl)
- [Dataset metadata RDF](../metadata/global-rivers-dataset-SCHACL-schema.ttl)
- [Metadata SHACL shapes](../metadata/global-rivers-dataset-metadata-RDF.ttl)
- [FAIRification methodology](methodology.md)


