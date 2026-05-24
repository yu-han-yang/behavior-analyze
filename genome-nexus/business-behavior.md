# Domain-Level Behavior Analysis

## Domain Summary

Genome Nexus is a genomic annotation and reference lookup service. Its core business concepts are variant annotations, genomic locations, dbSNP/COSMIC variant identifiers, Ensembl genes and transcripts, external gene cross-references, PDB protein structures, PFAM protein domains, post-translational modifications, and service/source version metadata.

The service is primarily read-through and lookup-oriented. The documented API functions do not create domain resources through the API; annotation and reference data must already exist in configured repositories or be retrievable from configured upstream services. Some lookup calls cache fetched upstream data internally, but that cache population is a side effect, not a caller-managed resource lifecycle.

## Available Function Inventory

### Variant Annotation

- `retrieve VEP annotations for variant list`  
  Endpoint: `POST /annotation`  
  Domain meaning: annotate multiple variant strings and optionally enrich them.

- `retrieve VEP annotation for single variant`  
  Endpoint: `GET /annotation/{variant}`  
  Domain meaning: annotate one variant string.

- `retrieve VEP annotations for genomic location list`  
  Endpoint: `POST /annotation/genomic`  
  Domain meaning: annotate multiple structured genomic locations.

- `retrieve VEP annotation for single genomic location`  
  Endpoint: `GET /annotation/genomic/{genomicLocation}`  
  Domain meaning: annotate one comma-delimited genomic location.

- `retrieve VEP annotations for variant ID list`  
  Endpoint: `POST /annotation/dbsnp/`  
  Domain meaning: annotate multiple dbSNP or COSMIC identifiers.

- `retrieve VEP annotation for single variant ID`  
  Endpoint: `GET /annotation/dbsnp/{variantId}`  
  Domain meaning: annotate one dbSNP or COSMIC identifier.

### Ensembl Gene and Transcript Reference Data

- `retrieve canonical genes by Hugo symbol list`  
  Endpoint: `POST /ensembl/canonical-gene/hgnc`  
  Domain meaning: resolve multiple Hugo symbols to canonical Ensembl gene records.

- `retrieve canonical gene by Hugo symbol`  
  Endpoint: `GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`  
  Domain meaning: resolve one Hugo symbol to a canonical Ensembl gene record.

- `retrieve canonical genes by Entrez ID list`  
  Endpoint: `POST /ensembl/canonical-gene/entrez`  
  Domain meaning: resolve multiple Entrez Gene IDs to canonical Ensembl gene records.

- `retrieve canonical gene by Entrez ID`  
  Endpoint: `GET /ensembl/canonical-gene/entrez/{entrezGeneId}`  
  Domain meaning: resolve one Entrez Gene ID to a canonical Ensembl gene record.

- `retrieve canonical transcripts by Hugo symbol list`  
  Endpoint: `POST /ensembl/canonical-transcript/hgnc`  
  Domain meaning: resolve multiple Hugo symbols to canonical Ensembl transcripts.

- `retrieve canonical transcript by Hugo symbol`  
  Endpoint: `GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`  
  Domain meaning: resolve one Hugo symbol to a canonical Ensembl transcript.

- `filter Ensembl transcripts by query values`  
  Endpoint: `GET /ensembl/transcript`  
  Domain meaning: filter transcripts by gene ID, protein ID, and/or Hugo symbol query values.

- `request unfiltered Ensembl transcripts`  
  Endpoint: `GET /ensembl/transcript`  
  Domain meaning: request transcripts without filters; implementation returns an empty list.

- `retrieve transcripts by transcript ID list`  
  Endpoint: `POST /ensembl/transcript`  
  Domain meaning: retrieve transcripts by multiple transcript IDs.

- `retrieve transcripts by gene ID list`  
  Endpoint: `POST /ensembl/transcript`  
  Domain meaning: retrieve transcripts by multiple Ensembl gene IDs.

- `retrieve transcripts by protein ID list`  
  Endpoint: `POST /ensembl/transcript`  
  Domain meaning: retrieve transcripts by multiple Ensembl protein IDs.

- `retrieve transcripts by Hugo symbol list`  
  Endpoint: `POST /ensembl/transcript`  
  Domain meaning: retrieve transcripts by multiple Hugo symbols.

- `request transcripts without body filters`  
  Endpoint: `POST /ensembl/transcript`  
  Domain meaning: request transcripts with no usable body filter; implementation returns an empty list.

- `retrieve transcript by transcript ID`  
  Endpoint: `GET /ensembl/transcript/{transcriptId}`  
  Domain meaning: retrieve one transcript by Ensembl transcript ID.

- `retrieve Ensembl xrefs`  
  Endpoint: `GET /ensembl/xrefs`  
  Domain meaning: retrieve external database references for an Ensembl accession.

### Protein Structure, Domain, and PTM Reference Data

- `retrieve PDB headers by ID list`  
  Endpoint: `POST /pdb/header`  
  Domain meaning: retrieve PDB header metadata for multiple PDB IDs.

- `retrieve PDB header by ID`  
  Endpoint: `GET /pdb/header/{pdbId}`  
  Domain meaning: retrieve PDB header metadata for one PDB ID.

- `retrieve PFAM domains by accession list`  
  Endpoint: `POST /pfam/domain`  
  Domain meaning: retrieve PFAM domain records for multiple accessions.

- `retrieve PFAM domain by accession`  
  Endpoint: `GET /pfam/domain/{pfamAccession}`  
  Domain meaning: retrieve one PFAM domain record.

- `retrieve PTMs by transcript ID`  
  Endpoint: `GET /ptm/experimental`  
  Domain meaning: retrieve PTM entries associated with one transcript ID prefix.

- `request PTMs without transcript ID`  
  Endpoint: `GET /ptm/experimental`  
  Domain meaning: request PTMs without a transcript query; implementation normally returns an empty list.

- `retrieve PTMs by transcript ID list`  
  Endpoint: `POST /ptm/experimental`  
  Domain meaning: retrieve PTM entries associated with multiple transcript ID prefixes.

### Service Metadata

- `retrieve service version information`  
  Endpoint: `GET /version`  
  Domain meaning: retrieve Genome Nexus and VEP/source version information.

## Supported Business Behaviors

### Behavior 1: Annotate Variants by Variant String

Business goal:  
Convert caller-supplied variant notation into VEP-style genomic and transcript annotations.

Domain context:  
This is the central Genome Nexus workflow: callers submit variant strings such as HGVS genomic variants and receive normalized biological consequence information.

Starting point:  
Configured annotation backend/cache exists. No API function creates prerequisite variant data.

Required execution workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with `variant=17:g.41242962_41242963insGA`, optional `isoformOverrideSource=mskcc|genome_nexus|ensembl`, optional `fields`, and optional `token={"oncokb":"<token>"}` to annotate one variant.
2. Alternatively, use function `retrieve VEP annotations for variant list` (`POST /annotation`) with body `["17:g.41242962_41242963insGA","7:g.140453136A>T"]`, optional `isoformOverrideSource`, optional `fields`, and optional `token` to annotate multiple variants in one request.

Optional verification workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with the same `variant` and same enrichment query values to inspect one item from a batch response.
2. Use function `retrieve service version information` (`GET /version`) to inspect the Genome Nexus/VEP source version used by the running service.

Existing-state shortcuts:
- No setup function can be skipped because the main action is itself the lookup.
- Equivalent state may already exist in the annotation repository/index cache, or it may be fetched from the configured VEP service during the call.
- Direct database setup can seed annotation/cache/index records, but the same normalized variant key must match the submitted `variant`.

Parameter and value bindings:
- The path `variant` or each body variant string is the annotation input and is reused as `originalVariantQuery` in successful responses.
- `isoformOverrideSource` controls transcript selection enrichment and must be repeated for comparable calls.
- If `fields` includes `oncokb`, `token` must be a JSON map containing an `oncokb` entry; no function issues this token.
- In region-annotation mode, HGVS values containing `g.` may be converted internally to genomic-location notation before annotation.

Business result:  
The caller receives VEP-style annotation objects for resolvable variants. Depending on configured fields, returned annotations may include hotspot, mutation assessor, nucleotide context, MyVariant.info, PTM, SIGNAL, OncoKB, ClinVar, and annotation-summary enrichments. Fetched base annotations are cached and indexed; post-enrichment is not saved to the annotation repository.

Constraints and invariants:
- The request body for batch annotation must be a JSON array of variant strings.
- If region annotation is enabled, a single batch cannot mix HGVS `g.` values and non-HGVS values.
- Enrichment field names are case-sensitive implementation checks.
- Batch web-service failure can return an empty list instead of failing item-by-item.

Failure and exceptional cases:
- Failing function: `retrieve VEP annotation for single variant`  
  Failure condition: `variant` is invalid, unsupported, not found, or has a conflicting reference allele.  
  Why it fails: downstream annotation lookup or verification raises not-found/web-service behavior, or returns a failed annotation object.  
  Violated prerequisite or constraint: submitted variant must be annotatable and reference-consistent.

- Failing function: `retrieve VEP annotations for variant list`  
  Failure condition: batch contains mixed HGVS `g.` and non-HGVS formats when region annotation is enabled.  
  Why it fails: `SelectedAnnotationServiceImpl` throws `VariantAnnotationQueryMixedFormatException`.  
  Violated prerequisite or constraint: one batch must use a compatible annotation format.

Implementation notes:
- Source code confirms annotation fetches can cache base VEP data and save an index record.
- Post-enrichment is applied after base annotation and is not persisted to the annotation repository.
- Deprecated `/hgvs` endpoints exist in source but are hidden and not part of `full-behavior.md`.

### Behavior 2: Annotate Structured Genomic Locations with Reference-Allele Verification

Business goal:  
Annotate genomic locations supplied as chromosome/start/end/reference/alternate allele values and verify that the supplied reference allele matches annotation output.

Domain context:  
Clinical and genomics pipelines often hold variants as structured genomic coordinates rather than HGVS strings.

Starting point:  
Configured annotation backend/cache exists. No API function creates genomic reference data.

Required execution workflow:
1. Use function `retrieve VEP annotation for single genomic location` (`GET /annotation/genomic/{genomicLocation}`) with `genomicLocation=7,140453136,140453136,A,T`, optional `isoformOverrideSource`, optional `fields`, and optional `token` to annotate one location.
2. Alternatively, use function `retrieve VEP annotations for genomic location list` (`POST /annotation/genomic`) with body `[{"chromosome":"7","start":140453136,"end":140453136,"referenceAllele":"A","variantAllele":"T"}]`, optional `isoformOverrideSource`, optional `fields`, and optional `token` to annotate multiple locations.

Optional verification workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with a generated equivalent HGVS or region-compatible value, if known, to compare returned variant consequences.
2. Use function `retrieve service version information` (`GET /version`) to inspect source versions.

Existing-state shortcuts:
- Direct database seeding can provide cached annotations for the normalized converted location.
- No API setup function can establish reference genome validity; the location must be valid against the configured annotation backend.

Parameter and value bindings:
- For `GET`, the comma-delimited `genomicLocation` path value must bind chromosome, start, end, reference allele, and variant allele in order.
- For `POST`, each body object’s chromosome/start/end/referenceAllele/variantAllele values are converted to the backend annotation format.
- The original genomic-location value is preserved as `originalVariantQuery`.
- The reference allele supplied by the caller is compared to the annotation response allele.

Business result:  
The caller receives VEP-style annotation for the genomic location. If reference verification fails, the service returns a failed annotation object rather than a normal successful biological annotation.

Constraints and invariants:
- The path value must parse into at least five comma-separated components.
- Empty reference alleles cannot be meaningfully verified.
- Duplicate submitted locations that normalize to the same converted variant can produce duplicate response entries with distinct original query values.

Failure and exceptional cases:
- Failing function: `retrieve VEP annotation for single genomic location`  
  Failure condition: malformed location, invalid coordinates, invalid alleles, or backend cannot annotate it.  
  Why it fails: parsing/conversion or downstream annotation fails.  
  Violated prerequisite or constraint: location must be parseable and annotatable.

- Failing function: `retrieve VEP annotations for genomic location list`  
  Failure condition: one or more body objects omit required coordinate/allele values.  
  Why it fails: conversion relies on `GenomicLocation.toString()` and backend annotation of converted values.  
  Violated prerequisite or constraint: body must describe valid genomic locations.

Implementation notes:
- `VerifiedGenomicLocationAnnotationServiceImpl` performs allele verification and may issue a follow-up deletion query for delins reference recovery.
- Verification failure can be represented as an annotation object with unsuccessful status rather than as a conventional HTTP error.

### Behavior 3: Annotate Known Variant Identifiers

Business goal:  
Resolve dbSNP or COSMIC identifiers into VEP-style variant annotation.

Domain context:  
Researchers commonly refer to known variants by identifiers such as `rs116035550` or COSMIC IDs rather than genomic notation.

Starting point:  
Configured variant-ID annotation fetcher/cache exists. No API function creates dbSNP/COSMIC records.

Required execution workflow:
1. Use function `retrieve VEP annotation for single variant ID` (`GET /annotation/dbsnp/{variantId}`) with `variantId=rs116035550`, optional `isoformOverrideSource`, optional `fields=annotation_summary`, and optional `token` to annotate one identifier.
2. Alternatively, use function `retrieve VEP annotations for variant ID list` (`POST /annotation/dbsnp/`) with body `["rs116035550","COSM476"]`, optional `isoformOverrideSource`, optional `fields`, and optional `token` to annotate multiple identifiers.

Optional verification workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with the variant string returned by the ID annotation response to inspect equivalent notation, if the response exposes it.

Existing-state shortcuts:
- Cached variant-ID annotation records can be seeded directly.
- The identifier must still be in the format and cache/upstream scope expected by the configured variant-ID fetcher.

Parameter and value bindings:
- `variantId` in the path or each body value is used as the upstream/cache lookup key.
- Optional `fields`, `isoformOverrideSource`, and `token` must be reused if the caller wants comparable enrichment.

Business result:  
The caller receives annotation objects for resolvable known variant IDs. Batch requests may omit unsupported or unresolved IDs rather than failing the whole call.

Constraints and invariants:
- Runtime support is intended for dbSNP-style `rs...` and COSMIC-style `COSM...` identifiers.
- Unsupported IDs are not converted into successful biological annotations.

Failure and exceptional cases:
- Failing function: `retrieve VEP annotation for single variant ID`  
  Failure condition: unsupported, absent, or unfetchable `variantId`.  
  Why it fails: the variant-ID service cannot resolve the identifier to annotation data.  
  Violated prerequisite or constraint: identifier must be supported and resolvable.

- Failing function: `retrieve VEP annotations for variant ID list`  
  Failure condition: one or more IDs are unsupported or missing.  
  Why it fails: batch lookup delegates to the same fetcher and can omit failed items.  
  Violated prerequisite or constraint: each item must identify available dbSNP/COSMIC data.

Implementation notes:
- OpenAPI labels this path as dbSNP, while function analysis and implementation behavior also allow COSMIC-style IDs.

### Behavior 4: Produce an Enriched Variant Evidence Profile

Business goal:  
Return a variant annotation decorated with clinically or biologically relevant evidence from multiple sources.

Domain context:  
A downstream interpretation workflow may need one response containing canonical transcript summary, hotspots, mutation impact, nucleotide context, population/knowledge-base data, PTMs, SIGNAL, OncoKB, and ClinVar.

Starting point:  
Configured annotation backend/cache and the selected enrichment data sources exist.

Required execution workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with `variant=7:g.140453136A>T`, `fields=hotspots,mutation_assessor,nucleotide_context,my_variant_info,ptms,signal,oncokb,clinvar,annotation_summary`, optional `isoformOverrideSource=mskcc`, and `token={"oncokb":"<token>"}` to produce one enriched profile.
2. Alternatively, use function `retrieve VEP annotations for variant list` (`POST /annotation`) with a body of variant strings and the same `fields`, `isoformOverrideSource`, and `token` values to produce enriched profiles in batch.

Optional verification workflow:
1. Use function `retrieve PTMs by transcript ID` (`GET /ptm/experimental`) with `ensemblTranscriptId=<transcriptId from returned transcript consequence>` to inspect PTM records separately.
2. Use function `retrieve PFAM domain by accession` (`GET /pfam/domain/{pfamAccession}`) with `pfamAccession=<PFAM accession from returned domain/consequence data>` if a PFAM accession is present.
3. Use function `retrieve PDB header by ID` (`GET /pdb/header/{pdbId}`) with `pdbId=<PDB ID from returned structure/domain data>` if a PDB ID is present.

Existing-state shortcuts:
- Annotation, hotspot, mutation assessor, nucleotide context, MyVariant.info, SIGNAL, ClinVar, PTM, PFAM, and PDB data may be pre-seeded or externally available.
- OncoKB token acquisition cannot be performed by this API; the caller must already have the token.
- Optional verification steps can be skipped when the enriched annotation response already contains enough detail.

Parameter and value bindings:
- The same `variant` or body variant value drives base annotation and all enrichers.
- `fields` explicitly selects enrichment modules; omitted field names produce absent enrichment sections.
- `token.oncokb` is consumed only by the OncoKB enricher.
- Transcript IDs and PFAM/PDB identifiers returned by annotation are reused as path/query values in optional verification functions.

Business result:  
The returned annotation object contains base VEP consequence data plus selected enrichment sections. Base annotation may be cached; enrichment is computed for the response and is not persisted with the annotation cache.

Constraints and invariants:
- Enrichment depends on selected `fields`; the API does not guarantee all evidence sources are available for every variant.
- Unknown or unavailable enrichment data may be missing without invalidating the base annotation.
- OncoKB enrichment requires caller-managed credentials when required by the upstream service.

Failure and exceptional cases:
- Failing function: `retrieve VEP annotation for single variant`  
  Failure condition: base variant cannot be annotated.  
  Why it fails: enrichment is post-processing and cannot run meaningfully without base annotation.  
  Violated prerequisite or constraint: the submitted variant must be annotatable.

- Failing function: `retrieve VEP annotation for single variant`  
  Failure condition: an enrichment backend is unavailable or has no matching data.  
  Why it fails: enrichers depend on configured repositories/external services; missing enrichment may be absent or logged rather than returned as a hard domain error.  
  Violated prerequisite or constraint: selected evidence sources must be available for the selected fields.

Implementation notes:
- `BaseVariantAnnotationServiceImpl` always registers isoform enrichment and conditionally registers named enrichers based on `fields`.
- The code comment explicitly states post-enrichment is not saved to the annotation repository.

### Behavior 5: Resolve Gene Identifiers to Canonical Ensembl Gene Records

Business goal:  
Map common gene identifiers to canonical Ensembl gene records.

Domain context:  
Variant interpretation and transcript workflows often begin with Hugo symbols or Entrez Gene IDs and need canonical Ensembl identifiers.

Starting point:  
Ensembl repository contains canonical gene records.

Required execution workflow:
1. Use function `retrieve canonical gene by Hugo symbol` (`GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`) with `hugoSymbol=TP53` to resolve one symbol.
2. Alternatively, use function `retrieve canonical genes by Hugo symbol list` (`POST /ensembl/canonical-gene/hgnc`) with body `["TP53","PIK3CA","BRCA1"]` to resolve symbols in batch.
3. Alternatively, use function `retrieve canonical gene by Entrez ID` (`GET /ensembl/canonical-gene/entrez/{entrezGeneId}`) with `entrezGeneId=7157` to resolve one Entrez ID.
4. Alternatively, use function `retrieve canonical genes by Entrez ID list` (`POST /ensembl/canonical-gene/entrez`) with body `["7157","5290"]` to resolve Entrez IDs in batch.

Optional verification workflow:
1. Use function `retrieve Ensembl xrefs` (`GET /ensembl/xrefs`) with `accession=<returned Ensembl gene accession>` to inspect external references.
2. Use function `filter Ensembl transcripts by query values` (`GET /ensembl/transcript`) with `geneId=<returned Ensembl gene ID>` to inspect transcripts for that gene.

Existing-state shortcuts:
- Direct database setup can seed Ensembl gene records.
- No API setup step can create or update canonical mappings.
- Batch lookup can be used instead of repeated single lookup when all identifiers are known.

Parameter and value bindings:
- Hugo symbol path/body values are matched against approved symbols, previous symbols, and synonyms.
- Entrez ID values must be integer-like strings.
- Returned Ensembl gene IDs can be reused as `geneId` in transcript lookup or `accession` in xref lookup.

Business result:  
The caller obtains canonical Ensembl gene records for resolvable input identifiers. Batch calls return only successfully resolved records.

Constraints and invariants:
- Hugo lookup is case-insensitive and alias-aware in repository logic.
- Entrez lookup parses IDs as integers.
- Batch requests silently omit unmapped well-formed values.

Failure and exceptional cases:
- Failing function: `retrieve canonical gene by Hugo symbol`  
  Failure condition: symbol has no canonical mapping.  
  Why it fails: service throws `NoEnsemblGeneIdForHugoSymbolException`.  
  Violated prerequisite or constraint: symbol must exist in canonical mapping data.

- Failing function: `retrieve canonical gene by Entrez ID`  
  Failure condition: ID is non-numeric or unmapped.  
  Why it fails: repository lookup parses integer IDs and returns null for unmapped values; non-numeric parsing can raise an uncaught exception.  
  Violated prerequisite or constraint: Entrez ID must be integer-like and mapped.

Implementation notes:
- Batch functions catch per-item not-found exceptions and omit missing values.
- The global handler does not define custom mappings for some canonical gene exceptions.

### Behavior 6: Select Canonical Transcripts for Genes

Business goal:  
Resolve Hugo symbols to canonical Ensembl transcripts under a selected isoform override source.

Domain context:  
Many downstream annotations depend on which transcript is considered canonical.

Starting point:  
Ensembl repository contains transcript records and canonical transcript source metadata.

Required execution workflow:
1. Use function `retrieve canonical transcript by Hugo symbol` (`GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`) with `hugoSymbol=TP53` and optional `isoformOverrideSource=mskcc|genome_nexus|ensembl` to retrieve one canonical transcript.
2. Alternatively, use function `retrieve canonical transcripts by Hugo symbol list` (`POST /ensembl/canonical-transcript/hgnc`) with body `["TP53","PIK3CA"]` and optional `isoformOverrideSource` to retrieve multiple canonical transcripts.

Optional verification workflow:
1. Use function `retrieve transcript by transcript ID` (`GET /ensembl/transcript/{transcriptId}`) with `transcriptId=<returned transcriptId>` to inspect the exact transcript record.
2. Use function `retrieve PTMs by transcript ID` (`GET /ptm/experimental`) with `ensemblTranscriptId=<returned transcriptId>` to inspect PTMs for the canonical transcript.

Existing-state shortcuts:
- Direct database setup can seed transcript and canonical-source records.
- If the caller already has a transcript ID, skip canonical-symbol resolution and use transcript lookup directly for verification or downstream PTM lookup.

Parameter and value bindings:
- `hugoSymbol` scopes transcript selection.
- `isoformOverrideSource` must be reused across single and batch canonical transcript calls for comparable canonicality.
- Returned `transcriptId` is reused in transcript and PTM functions.

Business result:  
The caller receives canonical Ensembl transcript records for symbols that resolve under the selected source.

Constraints and invariants:
- Supported source values include implementation paths for `mskcc`, `genome_nexus`, and `ensembl`; null or unknown values follow repository default behavior.
- Batch calls omit symbols without matching canonical transcripts.

Failure and exceptional cases:
- Failing function: `retrieve canonical transcript by Hugo symbol`  
  Failure condition: no transcript exists for the symbol/source pair.  
  Why it fails: service throws `EnsemblTranscriptNotFoundException`.  
  Violated prerequisite or constraint: symbol and isoform source must map to a canonical transcript.

- Failing function: `retrieve canonical transcripts by Hugo symbol list`  
  Failure condition: one body symbol has no canonical transcript.  
  Why it fails: service catches the per-symbol exception and omits that result.  
  Violated prerequisite or constraint: each symbol must be present for complete batch coverage.

Implementation notes:
- Source and OpenAPI examples mention `uniprot`, but function analysis identifies implementation-backed source values as `mskcc`, `genome_nexus`, and `ensembl`.

### Behavior 7: Discover Ensembl Transcript Records

Business goal:  
Find transcript records by transcript, gene, protein, or Hugo-symbol identifiers.

Domain context:  
Transcript discovery supports variant interpretation, PTM lookup, protein mapping, and gene-centric analysis.

Starting point:  
Ensembl repository contains transcript records.

Required execution workflow:
1. Use function `retrieve transcript by transcript ID` (`GET /ensembl/transcript/{transcriptId}`) with `transcriptId=ENST00000361390` to retrieve one transcript.
2. Or use function `filter Ensembl transcripts by query values` (`GET /ensembl/transcript`) with one or more of `geneId=ENSG00000141510`, `proteinId=ENSP00000269305`, and `hugoSymbol=TP53` to filter transcripts.
3. Or use function `retrieve transcripts by transcript ID list` (`POST /ensembl/transcript`) with body `{"transcriptIds":["ENST00000361390"]}`.
4. Or use function `retrieve transcripts by gene ID list` (`POST /ensembl/transcript`) with body `{"geneIds":["ENSG00000141510"]}` and no non-empty `transcriptIds`.
5. Or use function `retrieve transcripts by protein ID list` (`POST /ensembl/transcript`) with body `{"proteinIds":["ENSP00000269305"]}` and no non-empty `transcriptIds` or `geneIds`.
6. Or use function `retrieve transcripts by Hugo symbol list` (`POST /ensembl/transcript`) with body `{"hugoSymbols":["TP53"]}` and no non-empty higher-priority lists.

Optional verification workflow:
1. Use function `retrieve canonical transcript by Hugo symbol` (`GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`) with the same `hugoSymbol` to compare a filtered set against the canonical transcript.

Existing-state shortcuts:
- If the caller already has a transcript ID, direct transcript lookup skips gene/protein/symbol discovery.
- Direct database setup can seed transcript records.
- `request unfiltered Ensembl transcripts` and `request transcripts without body filters` are not useful setup shortcuts because they return empty lists.

Parameter and value bindings:
- In `GET /ensembl/transcript`, supplied query values bind directly to repository filters.
- If all three query values are supplied, implementation ignores `geneId` and `proteinId` and filters only by `hugoSymbol`.
- In `POST /ensembl/transcript`, body lists are prioritized: `transcriptIds`, then `geneIds`, then `proteinIds`, then `hugoSymbols`.

Business result:  
The caller receives matching transcript records, or an empty list when no records match. Single transcript lookup may return null/empty when absent rather than a custom not-found response.

Constraints and invariants:
- POST filter lists are mutually prioritized, not combined.
- GET all-three filtering is not truly conjunctive.
- Unfiltered GET and empty-filter POST return empty lists.

Failure and exceptional cases:
- Failing function: `filter Ensembl transcripts by query values`  
  Failure condition: caller expects all three query parameters to be combined.  
  Why it fails: implementation has no all-three branch and falls through to Hugo-symbol-only lookup.  
  Violated prerequisite or constraint: implementation does not support the expected combined filter.

- Failing function: `retrieve transcripts by transcript ID list`  
  Failure condition: body includes unknown transcript IDs.  
  Why it fails: repository returns only matching records and omits missing IDs.  
  Violated prerequisite or constraint: submitted IDs must exist for complete results.

Implementation notes:
- OpenAPI says unfiltered GET retrieves all transcripts, but `EnsemblServiceImpl` returns a new empty list when no query parameters are supplied.
- POST empty filter also returns an empty list.

### Behavior 8: Retrieve PTM Evidence for Transcripts

Business goal:  
Find experimentally observed post-translational modification entries associated with transcript IDs.

Domain context:  
PTM evidence helps interpret whether a variant affects modified protein sites.

Starting point:  
PTM repository contains transcript-linked PTM records.

Required execution workflow:
1. Use function `retrieve PTMs by transcript ID` (`GET /ptm/experimental`) with `ensemblTranscriptId=ENST00000646891` to retrieve PTMs for one transcript ID prefix.
2. Alternatively, use function `retrieve PTMs by transcript ID list` (`POST /ptm/experimental`) with body `{"transcriptIds":["ENST00000646891","ENST00000371953"]}` to retrieve PTMs for multiple transcript ID prefixes.
3. Composite setup alternative: use function `retrieve canonical transcript by Hugo symbol` (`GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`) with `hugoSymbol=TP53` and optional `isoformOverrideSource`, capture returned `transcriptId`, then use function `retrieve PTMs by transcript ID` (`GET /ptm/experimental`) with `ensemblTranscriptId=<captured transcriptId>`.

Optional verification workflow:
1. Use function `retrieve transcript by transcript ID` (`GET /ensembl/transcript/{transcriptId}`) with the same transcript ID to inspect transcript metadata.

Existing-state shortcuts:
- If the caller already has the target transcript ID, canonical transcript lookup can be skipped.
- Direct database setup can seed PTM records.
- Existing state must preserve the transcript-ID prefix relationship used by PTM lookup.

Parameter and value bindings:
- `transcriptId` returned from canonical transcript or transcript lookup becomes `ensemblTranscriptId` in the PTM GET query.
- POST `transcriptIds` values are converted to starts-with patterns; they need not be exact full transcript IDs to match.

Business result:  
The caller receives PTM records whose stored transcript IDs begin with the supplied transcript ID values.

Constraints and invariants:
- PTM matching is prefix-based, not strict equality.
- `POST /ptm/experimental` requires a non-null `transcriptIds` list.
- `GET /ptm/experimental` omits required enforcement even though omitting the query is not semantically useful.

Failure and exceptional cases:
- Failing function: `retrieve PTMs by transcript ID`  
  Failure condition: no PTM entry has a transcript ID beginning with the supplied value.  
  Why it fails: repository returns an empty list.  
  Violated prerequisite or constraint: PTM data must contain a matching transcript prefix.

- Failing function: `retrieve PTMs by transcript ID list`  
  Failure condition: body omits `transcriptIds` or sets it to null.  
  Why it fails: service calls `.stream()` on a null list during pattern conversion.  
  Violated prerequisite or constraint: body must contain a non-null `transcriptIds` list.

Implementation notes:
- OpenAPI marks the GET query parameter as optional, but source does not implement list-all PTM behavior.

### Behavior 9: Retrieve External Cross-References for an Ensembl Accession

Business goal:  
Map an Ensembl accession to references in other biological databases.

Domain context:  
Cross-references connect Genome Nexus data to external identifiers used by other systems.

Starting point:  
Configured Ensembl xrefs upstream service is reachable.

Required execution workflow:
1. Use function `retrieve canonical gene by Hugo symbol` (`GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`) with `hugoSymbol=TP53` to obtain an Ensembl gene accession when starting from a gene symbol.
2. Use function `retrieve Ensembl xrefs` (`GET /ensembl/xrefs`) with `accession=<returned Ensembl gene accession>` to retrieve external cross-references.
3. If the caller already has the accession, use function `retrieve Ensembl xrefs` (`GET /ensembl/xrefs`) with `accession=ENSG00000141510` directly.

Optional verification workflow:
1. Use function `retrieve canonical genes by Hugo symbol list` (`POST /ensembl/canonical-gene/hgnc`) with body containing the same symbol to compare batch resolution output.

Existing-state shortcuts:
- Skip canonical gene resolution when an Ensembl accession is already known.
- No API setup function creates xref data; configure upstream service or direct cache/supporting data where applicable.

Parameter and value bindings:
- Returned canonical Ensembl gene ID/accession is reused as `accession` in `GET /ensembl/xrefs`.
- The xref call is scoped only by `accession`; it does not consume Hugo symbol or Entrez ID directly.

Business result:  
The caller receives external database references for the Ensembl accession.

Constraints and invariants:
- `accession` is required.
- Resolution depends on the configured external Ensembl xrefs service and response mapping.

Failure and exceptional cases:
- Failing function: `retrieve canonical gene by Hugo symbol`  
  Failure condition: starting symbol cannot resolve to an Ensembl accession.  
  Why it fails: canonical mapping is absent.  
  Violated prerequisite or constraint: xref lookup needs a valid accession.

- Failing function: `retrieve Ensembl xrefs`  
  Failure condition: accession is invalid, upstream rejects it, mapping fails, or upstream is unavailable.  
  Why it fails: `GeneXrefServiceImpl` wraps mapping/client/access failures in `EnsemblWebServiceException`.  
  Violated prerequisite or constraint: accession must be resolvable by upstream xrefs service.

Implementation notes:
- The xref endpoint is external-service-backed rather than repository-only.

### Behavior 10: Retrieve Protein Structure and Domain Reference Metadata

Business goal:  
Look up protein structure headers and PFAM domain definitions.

Domain context:  
Protein structure and domain metadata are useful for interpreting protein-level effects of variants.

Starting point:  
PDB header cache/upstream and PFAM repository data exist.

Required execution workflow:
1. Use function `retrieve PDB header by ID` (`GET /pdb/header/{pdbId}`) with `pdbId=1a37` to retrieve one PDB header.
2. Alternatively, use function `retrieve PDB headers by ID list` (`POST /pdb/header`) with body `["1a37","1a4o"]` to retrieve multiple PDB headers.
3. Use function `retrieve PFAM domain by accession` (`GET /pfam/domain/{pfamAccession}`) with `pfamAccession=PF02827` to retrieve one PFAM domain.
4. Alternatively, use function `retrieve PFAM domains by accession list` (`POST /pfam/domain`) with body `["PF02827","PF00093"]` to retrieve multiple PFAM domains.

Optional verification workflow:
1. Use function `retrieve VEP annotation for single variant` (`GET /annotation/{variant}`) with a variant whose response contains transcript/protein-domain context to inspect where PDB/PFAM identifiers may appear.

Existing-state shortcuts:
- PDB headers can be pre-cached or fetched from configured external service.
- PFAM domains can be seeded directly in the PFAM repository.
- If identifiers are already known from annotation output or another system, gene/transcript lookup is unnecessary.

Parameter and value bindings:
- `pdbId` path/body values are PDB lookup keys and are also set on returned header objects.
- `pfamAccession` path/body values must match stored PFAM accessions.
- Batch PDB IDs are de-duplicated before lookup.

Business result:  
The caller receives PDB header metadata and/or PFAM domain records for available identifiers. Batch calls return only successfully resolved records.

Constraints and invariants:
- PDB lookup can use external read-through cache.
- PFAM lookup is repository-backed.
- Batch PDB lookup silently omits not-found or upstream-failed IDs.

Failure and exceptional cases:
- Failing function: `retrieve PDB header by ID`  
  Failure condition: PDB ID not found, cannot be fetched, or cannot be mapped.  
  Why it fails: service throws `PdbHeaderNotFoundException` or `PdbHeaderWebServiceException`.  
  Violated prerequisite or constraint: PDB ID must be available and mappable.

- Failing function: `retrieve PFAM domain by accession`  
  Failure condition: accession does not exist in repository.  
  Why it fails: service throws `PfamDomainNotFoundException`.  
  Violated prerequisite or constraint: accession must exist in PFAM data.

Implementation notes:
- PDB batch lookup catches failures per ID and omits failed records.
- PFAM batch lookup returns only repository matches.

### Behavior 11: Retrieve Service and Source Version Provenance

Business goal:  
Inspect which Genome Nexus and VEP/source metadata the running service is using.

Domain context:  
Annotation results are meaningful only in the context of data-source and tool versions.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `retrieve service version information` (`GET /version`) to retrieve aggregate version/source information.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. This behavior is the metadata lookup itself.
- Direct configuration or repository source-info setup determines returned values.

Parameter and value bindings:
- No path, query, body, form, or header values are required.
- Returned version fields can be recorded externally with annotation results for provenance.

Business result:  
The caller receives configured Genome Nexus and VEP/source information.

Constraints and invariants:
- Output depends on application properties and `SourceInfoRepository`.
- The endpoint does not validate whether source data is fresh or complete.

Failure and exceptional cases:
- No meaningful implementation-backed failure branch was identified in `full-behavior.md`.

Implementation notes:
- This is the only documented behavior that does not require caller-supplied domain identifiers.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: API-Managed Creation or Update of Annotation and Reference Data

Priority:  
Critical domain gap

Expected business goal:  
A service operator or data pipeline can create, update, import, refresh, or delete variant annotations, Ensembl records, PFAM domains, PTM records, PDB headers, and source metadata through API workflows.

Why it is unsupported:  
All available functions are read or read-through lookup functions. None expose create, update, import, refresh, delete, or administrative lifecycle operations.

Existing functions considered:
- `retrieve VEP annotation for single variant`: can fetch/cache annotation as a side effect, but cannot explicitly create, correct, version, or delete annotation data.
- `retrieve canonical gene by Hugo symbol`: reads existing Ensembl repository records only.
- `retrieve PFAM domain by accession`: reads existing PFAM records only.
- `retrieve PTMs by transcript ID`: reads existing PTM records only.
- `retrieve service version information`: reads configured source metadata only.

Missing capability:  
Administrative data lifecycle endpoints, validation rules, versioned imports, explicit cache refresh, and delete/update operations.

Proof that function composition is insufficient:  
Chaining lookup calls can only read or incidentally cache some upstream responses. It cannot create missing Ensembl/PFAM/PTM records, correct stale mappings, remove invalid records, or atomically update source metadata.

Evidence from existing functions/source:
- Controllers expose GET/POST lookup operations but no PUT/PATCH/DELETE lifecycle endpoints in the documented OpenAPI function set.
- Service implementations rely on repositories or upstream fetchers for existing data.

Business impact:  
Data stewardship must happen outside the API, making operational workflows non-discoverable, non-transactional, and difficult to audit through the service interface.

### Missing Behavior 2: Reliable List-All Transcript and PTM Browsing

Priority:  
Important robustness gap

Expected business goal:  
A caller can browse all Ensembl transcripts or all PTM entries, optionally with pagination.

Why it is unsupported:  
The documented no-filter functions return empty lists rather than all records.

Existing functions considered:
- `request unfiltered Ensembl transcripts`: OpenAPI summary says no query retrieves all transcripts, but implementation returns an empty list.
- `request transcripts without body filters`: returns an empty list.
- `request PTMs without transcript ID`: passes null into starts-with matching and normally returns an empty result rather than listing all PTMs.

Missing capability:  
A true list-all endpoint with pagination, sorting, and clear query semantics.

Proof that function composition is insufficient:  
Existing filtered functions require known identifiers. Without an existing list-all or search endpoint, callers cannot discover unknown transcript IDs or PTM transcript IDs from the API alone.

Evidence from existing functions/source:
- `EnsemblServiceImpl.getEnsemblTranscripts(null,null,null)` returns `new ArrayList<>()`.
- PTM lookup is transcript-prefix based and lacks an all-record path.

Business impact:  
Discovery workflows require direct database access or external identifier lists.

### Missing Behavior 3: Strict, Item-Level Batch Error Reporting

Priority:  
Important robustness gap

Expected business goal:  
Batch calls should report success/failure for each submitted item while preserving input order and failure reason.

Why it is unsupported:  
Several batch functions silently omit failed items or return empty lists on upstream failure.

Existing functions considered:
- `retrieve VEP annotations for variant list`: web-service failures can produce empty list behavior.
- `retrieve canonical genes by Hugo symbol list`: omits unmapped symbols.
- `retrieve PDB headers by ID list`: de-duplicates and omits not-found/upstream-failed IDs.
- `retrieve PFAM domains by accession list`: returns only matching domains.
- `retrieve transcripts by transcript ID list`: returns only matching transcripts.

Missing capability:  
A batch response envelope with original input, status, error code, and result per item.

Proof that function composition is insufficient:  
Callers can retry missing items individually, but they cannot distinguish omitted due to not found, upstream failure, duplicate collapse, mapping failure, or unsupported format from the original batch response alone.

Evidence from existing functions/source:
- Batch Ensembl and PDB services catch per-item exceptions and omit results.
- Repository batch lookups naturally return only matches.

Business impact:  
Clinical or research pipelines can accidentally treat incomplete results as complete data unless they implement their own reconciliation.

### Missing Behavior 4: True Combined Transcript Filtering

Priority:  
Important robustness gap

Expected business goal:  
A caller can filter transcripts by `geneId`, `proteinId`, and `hugoSymbol` together as a strict conjunction.

Why it is unsupported:  
The implementation has branches for some combinations, but when all three query parameters are supplied it falls through to Hugo-symbol-only lookup.

Existing functions considered:
- `filter Ensembl transcripts by query values`: supports some combinations but not all three together.
- `retrieve transcripts by gene ID list`, `retrieve transcripts by protein ID list`, `retrieve transcripts by Hugo symbol list`: POST body filters are prioritized rather than combined.

Missing capability:  
Strict multi-field transcript filtering across all supported identifiers.

Proof that function composition is insufficient:  
A caller can fetch by one criterion and filter client-side only if all needed fields are returned and the result set is manageable. The API itself cannot enforce repository-level conjunction or guarantee completeness for large datasets.

Evidence from existing functions/source:
- `EnsemblServiceImpl` final `else` branch calls `findByHugoSymbolsIn` when all three GET query parameters are present.
- POST transcript filtering chooses the first non-empty list in priority order.

Business impact:  
Transcript search can return broader results than requested, weakening data quality checks.

### Missing Behavior 5: Stable Validation or Dry-Run for Variant, Location, Token, and Identifier Inputs

Priority:  
API ergonomics gap

Expected business goal:  
A caller can validate variant notation, genomic-location formatting, supported enrichment fields, OncoKB token presence, Entrez numeric format, and transcript/PFAM/PDB identifier format before executing annotation or lookup.

Why it is unsupported:  
Validation is embedded inside lookup functions and often surfaces as empty results, failed annotations, omitted batch items, or uncaught exceptions.

Existing functions considered:
- `retrieve VEP annotation for single genomic location`: validates only while annotating.
- `retrieve VEP annotations for variant list`: detects mixed formats only during annotation.
- `retrieve canonical gene by Entrez ID`: numeric parsing failure is not a first-class validation response.
- `retrieve VEP annotation for single variant`: token and field issues are handled indirectly through enrichment behavior.

Missing capability:  
Dedicated validation endpoints and explicit schema-level constraints for identifier formats and enrichment dependencies.

Proof that function composition is insufficient:  
Executing lookups mutates caches/indexes as a side effect for successful annotations, so lookup is not equivalent to dry-run validation.

Evidence from existing functions/source:
- Annotation service saves fetched annotations and index records.
- Entrez parsing and PTM null handling can fail outside explicit validation branches.

Business impact:  
Clients must learn validity by attempting operations, which can cause cache side effects and inconsistent error handling.

### Missing Behavior 6: Transactional Variant Interpretation Package

Priority:  
API ergonomics gap

Expected business goal:  
A caller can submit a variant and receive a guaranteed coherent package containing annotation, canonical gene/transcript, PTMs, PFAM domains, PDB headers, xrefs, source versions, and per-source status.

Why it is unsupported:  
The API supports fragments of this workflow, but not a transactional or guaranteed-complete aggregate.

Existing functions considered:
- `retrieve VEP annotation for single variant`: can enrich selected fields, but enrichment is best-effort and not persisted.
- `retrieve PTMs by transcript ID`: requires transcript IDs from prior results.
- `retrieve PFAM domain by accession`: requires PFAM accessions from prior results.
- `retrieve PDB header by ID`: requires PDB IDs from prior results.
- `retrieve service version information`: provides provenance separately.

Missing capability:  
An aggregate endpoint with explicit source status, identifier binding, and consistency/version guarantees.

Proof that function composition is insufficient:  
Manual chaining cannot guarantee all subresults were computed against the same source snapshot, cannot atomically fail or succeed, and cannot distinguish missing data from unavailable data across all sources.

Evidence from existing functions/source:
- Post-enrichment is response-only and not saved.
- Batch and auxiliary lookups often omit missing items rather than returning structured status.

Business impact:  
Clients must implement their own orchestration and consistency checks for interpretation workflows.

## Cross-Behavior Observations

- The service is read-heavy and lacks API-managed lifecycle operations for core data.
- Many prerequisite states are external: VEP, Ensembl xrefs, PDB fetchers, OncoKB token, and seeded Mongo repositories.
- Batch behavior commonly omits failed or missing items instead of returning item-level errors.
- OpenAPI/source discrepancies matter: unfiltered transcript retrieval is documented as list-all but implemented as empty-list; optional PTM query does not mean list-all PTMs.
- Enrichment is selected by `fields` and applied after base annotation; it is not persisted to the annotation repository.
- Transcript filtering has priority and fall-through rules that can surprise callers.
- Several validations are implicit and inconsistent, including mixed variant formats, Entrez numeric parsing, null PTM filter lists, and reference-allele verification.
- No authentication/session/ownership model appears in the documented function set; scoping is by identifier values and configured data sources, not user ownership.

## Coverage Summary

Supported domain areas:
- Variant annotation by variant string, genomic location, and dbSNP/COSMIC ID.
- Optional evidence enrichment during annotation.
- Canonical Ensembl gene and transcript lookup.
- Transcript lookup by selected identifiers.
- Ensembl external xref lookup.
- PDB header, PFAM domain, and PTM reference lookup.
- Service/source version reporting.

Partially supported domain areas:
- Composite interpretation workflows are possible only through client-side orchestration.
- Batch workflows exist but lack robust per-item status.
- Transcript and PTM discovery require known identifiers.

Unsupported domain areas:
- API-managed data creation, update, import, refresh, deletion, and audit.
- True list-all browsing for transcripts/PTMs.
- Strict all-field transcript filtering.
- Dedicated validation/dry-run workflows.
- Transactional, source-consistent interpretation packages.