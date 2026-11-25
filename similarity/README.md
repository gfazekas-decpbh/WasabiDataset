# WASABI artist similarity layer (RDF)

Derived RDF graphs over WASABI artists, built from the WASABI knowledge graph (Zenodo [5603369](https://zenodo.org/records/5603369)) and MTG-SAS similarity data, by George Fazekas (Centre for Digital Music, Queen Mary University of London).

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24511585.svg)](https://doi.org/10.5281/zenodo.24511585) The complete set, including CSV versions of all graphs, is archived on Zenodo as one zip (`wasabi-similarity-graphs-v1.0.zip`). This folder holds the Turtle and JSON-LD versions.

| Folder | Graph | Size |
|---|---|---|
| `cultural-similarity/` | **Cultural similarity v0.3**: 20 nearest artists for each of 36,839 artists, from Last.fm tags, genres and Wikipedia categories (TF-IDF profiles, ComplEx KG embeddings and a KG-guided transformer) | 5.9M triples, 18 MB |
| `musical-similarity/` | **Musical similarity v0.4 from artist harmonic (chord) profiles**: 20 nearest artists for each of 14,508 artists. Cosine between key-relative chord and chord-transition profiles from WASABI's automatic chord transcriptions; no tags or genres used | 2.3M triples, 6.5 MB |
| `dissimilarity/` | **Within-genre dissimilarity v0.3-dis**: for each artist and genre, the 5 least-similar artists of that genre (8,232 artists, 153 genres), plus 5 artists spanning each genre | 0.9M triples, 2.2 MB |
| `chord-theory/` | **Chord-theory graph v0.4**: 24 triads and 576 transitions with music-theory relations (common tones, fifths, function, root motion, cadential variants) | 7k triples |
| `artist-tag-prompts/` | **Artist tag prompts v0.3.2**: per artist (23,826), genres and most characteristic descriptive Last.fm **tags** (listener tags, not generated text), as ordered terms with tag facets | 0.7M triples, 3.1 MB |

- **Formats:** each graph is available as Turtle (`.ttl.gz`) and as **JSON-LD** (`.jsonld.gz`). The JSON-LD is also plain JSON: one object per artist with its links nested inside, plus names and MBIDs. A JSON-LD processor gets the same RDF as the Turtle, except the `similarTo` / `contrastsWithinGenre` shortcut triples; the names are convenience fields mapped to null in the context. The shared context is [`../ontology/wsim-0.1.context.jsonld`](../ontology/wsim-0.1.context.jsonld).
- **Samples:** next to each dump are a small `…_sample.ttl` and `…_sample.jsonld` (a few entries) that you can read on GitHub without downloading the dump.
- **Vocabulary:** [`../ontology/wsim-0.1.ttl`](../ontology/wsim-0.1.ttl). Terms resolve there: `wsim:` = `https://wsim.semanticaudio.net/ontology/wsim-0.1.ttl#`.
- **Small examples of every kind:** [`../ontology/example_similarity.ttl`](../ontology/example_similarity.ttl).
- **Dataset description (VoID/DCAT), with triple counts and download links:** [`wasabi-similarity-dataset.ttl`](wasabi-similarity-dataset.ttl).
- **Identifiers.** Artists are WASABI URIs (`http://ns.inria.fr/wasabi/artist/…`), so the graphs join directly with WASABI's own RDF. Our data URIs start with `https://wsim.semanticaudio.net/similarity/` and identify links, models, tags and facets; they don't resolve to pages of their own.

## Shape
```turtle
wsart:A wsim:similarTo wsart:B .                                  # shortcut
sim:v0.3/sim/A-B a wsim:ArtistSimilarity ; wsim:source wsart:A ; wsim:target wsart:B ;
    wsim:score 17.2 ; wsim:rank 1 ; wsim:dimension "cultural" ;      # "musical" in the chord-profile graph
    prov:wasGeneratedBy sim:v0.3/model .                           # model node: method, features, evaluation (JSON)
wsart:A wsim:contrastsWithinGenre wsart:C .                       # dissimilarity shortcut; details on wsim:ArtistDissimilarity nodes
wsart:A wsim:tagPrompt "trip hop, downtempo, female vocalists, …" ; wsim:promptTerm sim:v0.3.2/term/A/1 , … .
sim:tag/trip-hop wsim:facet sim:facet/genre .
```

## Use
```python
import gzip, pyoxigraph as ox                     # pip install pyoxigraph (rdflib also works)
st = ox.Store()
st.bulk_load(gzip.open('similarity/cultural-similarity/artist_similarity_cultural_v0.3.ttl.gz').read(), ox.RdfFormat.TURTLE)
q = '''PREFIX wsim: <https://wsim.semanticaudio.net/ontology/wsim-0.1.ttl#>
SELECT ?rank ?other WHERE { ?l a wsim:ArtistSimilarity ; wsim:source <http://ns.inria.fr/wasabi/artist/56d95d7dcc2ddd0c0f6b9c59> ;  # Radiohead
                            wsim:rank ?rank ; wsim:target ?other } ORDER BY ?rank LIMIT 5'''
for r in st.query(q): print(r['rank'].value, r['other'].value)
```
**Plain JSON:** `json.load(gzip.open('…/artist_similarity_cultural_v0.3.jsonld.gz'))['@graph']` gives one object per artist with `neighbours` (`rank`, `score`, `target`, `targetName`).

Load several graphs into one store to compare them, e.g. filter on `wsim:dimension` to compare cultural and musical neighbours. Artist names come from WASABI itself.

**CSV versions** of all graphs are in the [Zenodo archive](https://doi.org/10.5281/zenodo.24511585). Artist embeddings, models and text tools (text → artists, artist → tag prompt) are in [wsim-tools](https://github.com/gfazekas-decpbh/wsim-tools).

## Caveats
- **Labels come from listeners.** Similarity, genres and tags reflect how listeners describe artists (Last.fm, Wikipedia). Musical similarity uses automatic chord transcriptions. Nothing comes from audio analysis.
- **Coverage follows WASABI**, which leans towards popular Western music.
- **Tag prompts** are keyword lists in Last.fm's tag language.

## Cite
Fazekas, G. (2026). *WASABI artist similarity graphs: cultural and chord-profile similarity, within-genre dissimilarity, chord theory and artist tag prompts* (Version 1.0) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.24511585
