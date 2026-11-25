# The WASABI ontology

The WASABI ontology is a vocabulary to formalize the WASABI dataset's metadata. Written in the W3C OWL Web Ontology Language, it primarily relies on the [Music Ontology](http://purl.org/ontology/mo/) that defines a rich vocabulary for describing and linking music information, and extends it with terms about some specific entities and properties not covered by the Music Ontology.

The WASABI ontology also reuses relevant terms from common vocabularies such as Dublin Core Metadata, FOAF, Schema.org and the DBpedia ontology, as well as some specific terms from the [Audio Features Ontology](http://purl.org/ontology/af/) and the [OMRAS2 Chord Ontology](http://purl.org/ontology/chord/).

This current version of the ontology includes 8 classes and 50 properties.



## Ontology description
### Namespace
[`http://ns.inria.fr/wasabi/ontology/`](http://ns.inria.fr/wasabi/ontology/)
### Current Version
v2
### Publisher
Inria
### Imported ontologies
[`http://dbpedia.org/ontology/`](http://dbpedia.org/ontology/)
[`http://purl.org/dc/terms/`](http://purl.org/dc/terms/)
[`http://purl.org/ontology/mo/`](http://purl.org/ontology/mo/)
[`http://xmlns.com/foaf/0.1/`](http://xmlns.com/foaf/0.1/)
### Classes
The WASABI ontology extends the Muusic Ontology, which is why 7 out of 8 classes from its classes are linked to ones in Music Ontology.
- **Album** (`wsb:Album`)
- **Artist_Group** (`wsb:Artist_Group`) : equivalent class of `mo:MusicGroup`
- **Artist_Person** (`wsb:Artist_Person`) : equivalent class of `mo:SoloMusicArtist`
- **Choir** (`wsb:Choir`) & **Orchestra** (`wsb:Orchestra`) : subclasses of `mo:MusicArtist`
- **Lyrics** (`wsb:Lyrics`) : equivalent class of `mo:Lyrics`
- **Song** (`wsb:Song`) : equivalent class of `mo:Track`
- **Classic song of pop/rock music** (`wsb:Classic_Song`)
- - **Topic** (`wsb:Topic`)

### Properties
The wasabi dataset contains some specific properties that are not covered by the Music Ontology, of which 22 are links to social network pages, represented in the WASABI ontology as object properties.
The dataset also includes several calculated features. Here are a few examples:
- **audio gain** (`wsb:gain`)
- **detected language** (`wsb:language_detected`) : sub-property of `dcterms:language`
- **number of explicit lyrics** (`wsb:explicit_lyrics_count`)
- **presence of explicit lyrics** (`wsb:has_explicit_lyrics`)
- **name without title** (`wsb:name_without_accent`) : sub-property of `foaf:name`

### Examples of terms reused from other ontologies or common vocabularies
To describe songs:
- **performer** (`mo:performer`) : from the Music Ontology [`http://purl.org/ontology/mo/`](http://purl.org/ontology/mo/)
- **author** (`schema:author`) : from Schema.org Vocabulary [`http://schema.org/`](http://schema.org/)
- **publication date** (`schema:Published`) : from Schema.org Vocabulary [`http://schema.org/`](http://schema.org/)

To describe artists:
- **homepage** (`mo:homepage`) : from the Music Ontology [`http://purl.org/ontology/mo/`](http://purl.org/ontology/mo/)
- **associated musical artist** (`dbo:associatedMusicalArtist`) : from the DBpedia ontology [`http://dbpedia.org/ontology/`](http://dbpedia.org/ontology/)
- **genre** (`schema:genre`) : from Schema.org Vocabulary [`http://schema.org/`](http://schema.org/)

To describe albums:
- **title** (`dcterms:title`) : from the Dublin Core Metadata Vocabulary [`http://purl.org/dc/terms/`](http://purl.org/dc/terms/)
- **upc** (`mo:upc`) : from the Music Ontology [`http://purl.org/ontology/mo/`](http://purl.org/ontology/mo/)
- **release date** (`schema:releaseDate`) : from Schema.org Vocabulary [`http://schema.org/`](http://schema.org/)

To describe chords:
- **chord** (`chord:chord`) : from the Chord Ontology [`http://purl.org/ontology/chord/`](http://purl.org/ontology/chord/)
- **confidence** (`af:confidence`) : from the Audio Features Ontology [`http://purl.org/ontology/af/`](http://purl.org/ontology/af/)
- **duration** (`schema:duration`) : from Schema.org Vocabulary [`http://schema.org/`](http://schema.org/)






## Artist similarity vocabulary (wsim, this fork)

This fork adds a small vocabulary for the derived artist-similarity graphs in [`../similarity/`](../similarity/). WASABI's `wsb` ontology above is unchanged.

- **File:** [`wsim-0.1.ttl`](wsim-0.1.ttl), version 0.1, OWL in Turtle.
- **Namespace:** `wsim:` = `https://wsim.semanticaudio.net/ontology/wsim-0.1.ttl#`. Term URIs resolve to the vocabulary file.
- **JSON-LD context:** [`wsim-0.1.context.jsonld`](wsim-0.1.context.jsonld), used by the `.jsonld.gz` files.
- **Examples:** [`example_similarity.ttl`](example_similarity.ttl), one item of every kind.
- **Reused vocabularies:** Music Ontology (`mo:MusicArtist`, `wsim:similarTo` ⊑ `mo:similar_to`), PROV-O (model provenance), Dublin Core terms, SKOS (tags and facets).

### Classes
- **artist similarity** (`wsim:ArtistSimilarity`): a scored, ranked link from a source artist to a target artist, produced by a model
- **artist dissimilarity** (`wsim:ArtistDissimilarity`): a link to one of the least-similar artists within a shared genre
- **similarity model** (`wsim:SimilarityModel`, ⊑ `prov:Activity`): the model run that produced a graph, with method, features and evaluation
- **genre** (`wsim:Genre`) and **genre span exemplar** (`wsim:GenreSpanExemplar`)
- **chord degree** (`wsim:ChordDegree`) and **chord transition** (`wsim:ChordTransition`)
- **prompt term** (`wsim:PromptTerm`), **prompt tag** (`wsim:PromptTag`, ⊑ `skos:Concept`) and **prompt facet** (`wsim:PromptFacet`, ⊑ `skos:Collection`)

### Properties
- **Similarity and dissimilarity links:**
  - `wsim:source`, `wsim:target`, `wsim:score`, `wsim:rank`;
  - `wsim:dimension` (`cultural` or `musical`);
  - `wsim:withinGenre`, `wsim:genreConfirmedByTags`;
  - shortcuts `wsim:similarTo` and `wsim:contrastsWithinGenre`.
- **Models:** `wsim:version`, `wsim:method`, `wsim:features`, `wsim:topK`, `wsim:evaluation`.
- **Genre spans:** `wsim:artist`, `wsim:role`.
- **Chords:** `wsim:sharesTwoTonesWith`, `wsim:fifthRelatedTo`, `wsim:sameFunctionAs`, `wsim:involvesChord`, `wsim:sameRootMotionAs`, `wsim:cadentialVariantOf`.
- **Tag prompts:** `wsim:tagPrompt`, `wsim:tagPromptByFacet`, `wsim:promptTerm`, `wsim:term`, `wsim:facet`, `wsim:order`.
