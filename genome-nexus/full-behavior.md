### Function 1: retrieve variant annotations for multiple variant strings

Function name:
retrieve VEP annotations for variant list

Core endpoint(s):
- `POST /annotation`

Preconditions:
- The submitted variant strings are valid inputs for the configured annotation backend, or matching cached annotation records are already present in the backing annotation repository. This can be satisfied by seeding the cache/index collections directly or by relying on the configured VEP/external annotation fetcher; no project endpoint creates prerequisite variant data.
- If enrichment is requested through `fields`, the corresponding reference data or external service must be available for each selected field. Recognized implementation-backed field values include `hotspots`, `mutation_assessor`, `nucleotide_context`, `my_variant_info`, `ptms`, `signal`, `oncokb`, `clinvar`, and `annotation_summary`.
- If `fields` includes `oncokb`, the optional `token` query value should be a JSON map containing an `oncokb` entry. The token is not created by another project endpoint and must be supplied by the caller.

Successful execution:
- Result:
  Returns one VEP-style annotation object per submitted variant string, optionally enriched with selected fields.
- Invocation:
  Step 1: `POST /annotation` with a JSON array request body of variant strings and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  The request body must be a list of variant strings. When region annotation is enabled, all variants in one request must use a compatible format; a mixed list of HGVS values containing `g.` and non-HGVS values is rejected before annotation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted variant string is invalid, unsupported, or cannot be mapped by the configured annotation backend. This state can be produced by sending that invalid value directly in the `POST /annotation` body; no setup endpoint is involved.
  - Failure endpoint:
    `POST /annotation`
  - Why this fails:
    The batch implementation catches annotation web-service failures and may return an empty list for a web-service failure, while invalid per-item annotations can be represented as failed annotation objects with `successfullyAnnotated=false`.
  - Intentionally violated constraints:
    A body item is not a valid annotatable variant.
- Branch 2:
  - Preconditions:
    - Region annotation is enabled through the configured VEP region URL.
    - The request body contains both HGVS-formatted values containing `g.` and non-HGVS values.
  - Failure endpoint:
    `POST /annotation`
  - Why this fails:
    `SelectedAnnotationServiceImpl` detects mixed formats and throws `VariantAnnotationQueryMixedFormatException`. The global exception handler does not define a custom mapping for this exception.
  - Intentionally violated constraints:
    The request relies on a mixed-format variant list that the selected annotation service refuses to process in one batch.

Endpoint coverage:
- Covers:
  `POST /annotation`
- Distinct meaning:
  Batch annotation by variant notation.

### Function 2: retrieve annotation for one variant string

Function name:
retrieve VEP annotation for single variant

Core endpoint(s):
- `GET /annotation/{variant}`

Preconditions:
- `{variant}` is a valid input for the configured annotation backend, or a matching cached annotation record is already present in the backing annotation repository. This can be satisfied by seeding the cache/index collections directly or by relying on the configured VEP/external annotation fetcher; no project endpoint creates prerequisite variant data.
- If enrichment is requested through `fields`, the corresponding reference data or external service must be available. If `fields=oncokb` is used, any required OncoKB token must be supplied in the `token` query map.

Successful execution:
- Result:
  Returns the annotation for the variant supplied in the path.
- Invocation:
  Step 1: `GET /annotation/{variant}` with `{variant}` set to the variant string and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  `{variant}` is passed as the annotation input. If region annotation is enabled and `{variant}` is HGVS genomic notation containing `g.`, the service converts it to a genomic location representation before annotation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{variant}` is invalid, unsupported, not found by the configured annotation backend, or has a provided reference allele that conflicts with the returned annotation.
  - Failure endpoint:
    `GET /annotation/{variant}`
  - Why this fails:
    Single-variant lookup can throw `VariantAnnotationNotFoundException` or `VariantAnnotationWebServiceException`, which are mapped by the global handler. Reference-allele verification can also convert the returned object into a failed annotation with `successfullyAnnotated=false`.
  - Intentionally violated constraints:
    The path variant is not a valid annotatable variant, or its stated reference allele conflicts with annotation output.

Endpoint coverage:
- Covers:
  `GET /annotation/{variant}`
- Distinct meaning:
  Single variant annotation by path value.

### Function 3: retrieve annotations for multiple genomic locations

Function name:
retrieve VEP annotations for genomic location list

Core endpoint(s):
- `POST /annotation/genomic`

Preconditions:
- Each body object describes a genomic location with the fields needed by `GenomicLocation.toString()`, including chromosome, start, end, reference allele, and variant allele. This state is supplied directly in the request body; no setup endpoint creates it.
- The configured annotation backend or backing cache can annotate the converted genomic locations. This can be satisfied by seeding the backing annotation repository directly or by relying on the configured VEP region/HGVS annotation service.

Successful execution:
- Result:
  Converts each submitted genomic location object to the configured VEP input format and returns annotations.
- Invocation:
  Step 1: `POST /annotation/genomic` with a JSON array of genomic location objects and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  The service normalizes and converts locations either to Ensembl REST region notation or HGVS notation depending on server configuration. Duplicate locations that normalize to the same converted variant can produce duplicated response annotations with different `originalVariantQuery` values.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted genomic location has invalid or unannotatable coordinates or alleles.
  - Failure endpoint:
    `POST /annotation/genomic`
  - Why this fails:
    Conversion or backend annotation may fail for that location. In batch mode, invalid converted variants can be omitted or returned as failed annotations, depending on the conversion and annotation result.
  - Intentionally violated constraints:
    A body object does not describe a valid annotatable genomic location.

Endpoint coverage:
- Covers:
  `POST /annotation/genomic`
- Distinct meaning:
  Batch annotation by structured genomic locations.

### Function 4: retrieve annotation for one genomic location

Function name:
retrieve VEP annotation for single genomic location

Core endpoint(s):
- `GET /annotation/genomic/{genomicLocation}`

Preconditions:
- `{genomicLocation}` is a comma-delimited genomic location that can be parsed into chromosome, start, end, reference allele, and variant allele. This value is supplied directly in the path; no setup endpoint creates it.
- The converted genomic location is annotatable by the configured backend or exists in the backing annotation cache. This can be satisfied by seeding the cache directly or by relying on the configured VEP region/HGVS annotation service.

Successful execution:
- Result:
  Returns a VEP annotation for the genomic location encoded in the path.
- Invocation:
  Step 1: `GET /annotation/genomic/{genomicLocation}` with `{genomicLocation}` formatted like `chromosome,start,end,referenceAllele,variantAllele` and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  The path value must parse into at least five comma-separated values. The implementation converts the parsed location before annotation and sets the original path value as `originalVariantQuery`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{genomicLocation}` is malformed, lacks required comma-delimited values, contains invalid coordinates or alleles, or cannot be converted into an annotatable variant.
  - Failure endpoint:
    `GET /annotation/genomic/{genomicLocation}`
  - Why this fails:
    The notation converter may throw during parsing or the downstream annotation lookup may return a failed annotation or mapped not-found/web-service error.
  - Intentionally violated constraints:
    The path value is not a valid five-part genomic location.

Endpoint coverage:
- Covers:
  `GET /annotation/genomic/{genomicLocation}`
- Distinct meaning:
  Single annotation by comma-delimited genomic location.

### Function 5: retrieve annotations for multiple dbSNP or COSMIC IDs

Function name:
retrieve VEP annotations for variant ID list

Core endpoint(s):
- `POST /annotation/dbsnp/`

Preconditions:
- Each submitted ID is intended to identify dbSNP or COSMIC variant data available through the configured ID annotation fetcher or backing cache. This can be satisfied by seeding the backing annotation repository directly or by relying on the configured variant-ID annotation service; no project endpoint creates this state.
- If enrichment is requested through `fields`, the corresponding reference data or external service must be available for the selected fields.

Successful execution:
- Result:
  Returns annotations for submitted dbSNP or COSMIC variant identifiers.
- Invocation:
  Step 1: `POST /annotation/dbsnp/` with a JSON array body of `variantIds` and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  Runtime support is intended for IDs such as `rs` plus digits or `COSM` plus digits. Unsupported IDs can become failed annotation placeholders or be omitted depending on the fetcher result.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one body item is not a supported dbSNP or COSMIC-style ID, or no annotation can be fetched for it.
  - Failure endpoint:
    `POST /annotation/dbsnp/`
  - Why this fails:
    The variant-ID annotation path delegates to the cached variant-ID fetcher and batch annotation service. Invalid or missing IDs are not converted into a successful annotation for that item.
  - Intentionally violated constraints:
    A body item is not a supported dbSNP or COSMIC-style ID.

Endpoint coverage:
- Covers:
  `POST /annotation/dbsnp/`
- Distinct meaning:
  Batch annotation by variant IDs.

### Function 6: retrieve annotation for one dbSNP or COSMIC ID

Function name:
retrieve VEP annotation for single variant ID

Core endpoint(s):
- `GET /annotation/dbsnp/{variantId}`

Preconditions:
- `{variantId}` identifies dbSNP or COSMIC variant data available through the configured ID annotation fetcher or backing cache. This can be satisfied by seeding the backing annotation repository directly or by relying on the configured variant-ID annotation service; no project endpoint creates this state.

Successful execution:
- Result:
  Returns one VEP annotation for the dbSNP or COSMIC ID in the path.
- Invocation:
  Step 1: `GET /annotation/dbsnp/{variantId}` with `{variantId}` set to an ID such as `rs116035550` and optional query values `isoformOverrideSource`, `token`, and `fields`.
- Constraints:
  `{variantId}` must use a supported variant-ID format and must be resolvable by the configured fetcher/cache.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{variantId}` has an unsupported format, is absent from the backing cache, or cannot be fetched from the configured upstream annotation service.
  - Failure endpoint:
    `GET /annotation/dbsnp/{variantId}`
  - Why this fails:
    Single-ID lookup delegates to the variant-ID annotation service and can throw not-found or web-service exceptions, or return a failed annotation when annotation is not possible.
  - Intentionally violated constraints:
    The path ID is not a resolvable dbSNP or COSMIC-style ID.

Endpoint coverage:
- Covers:
  `GET /annotation/dbsnp/{variantId}`
- Distinct meaning:
  Single annotation by variant ID.

### Function 7: retrieve canonical Ensembl gene IDs by Hugo symbols

Function name:
retrieve canonical genes by Hugo symbol list

Core endpoint(s):
- `POST /ensembl/canonical-gene/hgnc`

Preconditions:
- The backing Ensembl repository contains canonical gene records for the submitted Hugo symbols. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns canonical Ensembl gene records for the submitted Hugo symbols.
- Invocation:
  Step 1: `POST /ensembl/canonical-gene/hgnc` with a JSON array body of Hugo symbols.
- Constraints:
  Each Hugo symbol is looked up case-insensitively against approved symbols, previous symbols, and synonyms through the repository query.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted Hugo symbol has no canonical gene mapping in the backing Ensembl repository.
  - Failure endpoint:
    `POST /ensembl/canonical-gene/hgnc`
  - Why this fails:
    The implementation catches the per-symbol not-found exception and omits that symbol from the returned list instead of failing the whole request.
  - Intentionally violated constraints:
    The body includes a Hugo symbol absent from canonical mapping data.

Endpoint coverage:
- Covers:
  `POST /ensembl/canonical-gene/hgnc`
- Distinct meaning:
  Batch canonical gene lookup by Hugo symbols.

### Function 8: retrieve canonical Ensembl gene ID by one Hugo symbol

Function name:
retrieve canonical gene by Hugo symbol

Core endpoint(s):
- `GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`

Preconditions:
- The backing Ensembl repository contains a canonical gene record for `{hugoSymbol}`. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns the canonical Ensembl gene record for one Hugo symbol.
- Invocation:
  Step 1: `GET /ensembl/canonical-gene/hgnc/{hugoSymbol}` with `{hugoSymbol}` set to the target symbol.
- Constraints:
  `{hugoSymbol}` is matched against approved symbols, previous symbols, and synonyms.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{hugoSymbol}` has no canonical gene mapping in the backing Ensembl repository.
  - Failure endpoint:
    `GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`
  - Why this fails:
    `EnsemblServiceImpl` throws `NoEnsemblGeneIdForHugoSymbolException`. The global exception handler does not define a custom handler for this exception.
  - Intentionally violated constraints:
    The path symbol is absent from canonical mapping data.

Endpoint coverage:
- Covers:
  `GET /ensembl/canonical-gene/hgnc/{hugoSymbol}`
- Distinct meaning:
  Single canonical gene lookup by Hugo symbol.

### Function 9: retrieve canonical Ensembl gene IDs by Entrez IDs

Function name:
retrieve canonical genes by Entrez ID list

Core endpoint(s):
- `POST /ensembl/canonical-gene/entrez`

Preconditions:
- The backing Ensembl repository contains canonical gene records for the submitted Entrez Gene IDs. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.
- Each body value is parseable as an integer-like Entrez Gene ID where repository lookup requires integer conversion.

Successful execution:
- Result:
  Returns canonical Ensembl gene records for submitted Entrez Gene IDs.
- Invocation:
  Step 1: `POST /ensembl/canonical-gene/entrez` with a JSON array body of Entrez Gene ID strings.
- Constraints:
  Each Entrez ID must be parseable as an integer string. Unmapped but well-formed IDs are skipped in the batch response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted Entrez ID is well formed but has no canonical mapping in the backing Ensembl repository.
  - Failure endpoint:
    `POST /ensembl/canonical-gene/entrez`
  - Why this fails:
    The implementation catches the per-ID not-found exception and omits that ID from the returned list.
  - Intentionally violated constraints:
    The ID is absent from canonical mapping data.
- Branch 2:
  - Preconditions:
    - At least one submitted Entrez ID is not parseable as an integer string.
  - Failure endpoint:
    `POST /ensembl/canonical-gene/entrez`
  - Why this fails:
    Repository lookup performs integer parsing, and `NumberFormatException` is not caught by the service loop.
  - Intentionally violated constraints:
    A body item is not an integer-like Entrez Gene ID.

Endpoint coverage:
- Covers:
  `POST /ensembl/canonical-gene/entrez`
- Distinct meaning:
  Batch canonical gene lookup by Entrez IDs.

### Function 10: retrieve canonical Ensembl gene ID by one Entrez ID

Function name:
retrieve canonical gene by Entrez ID

Core endpoint(s):
- `GET /ensembl/canonical-gene/entrez/{entrezGeneId}`

Preconditions:
- The backing Ensembl repository contains a canonical gene record for `{entrezGeneId}`. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.
- `{entrezGeneId}` is parseable as an integer-like Entrez Gene ID.

Successful execution:
- Result:
  Returns the canonical Ensembl gene record for one Entrez Gene ID.
- Invocation:
  Step 1: `GET /ensembl/canonical-gene/entrez/{entrezGeneId}` with `{entrezGeneId}` set to an integer-like ID.
- Constraints:
  `{entrezGeneId}` must parse as an integer string and exist in the canonical mapping data.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{entrezGeneId}` is numeric but has no canonical mapping in the backing Ensembl repository.
  - Failure endpoint:
    `GET /ensembl/canonical-gene/entrez/{entrezGeneId}`
  - Why this fails:
    `EnsemblServiceImpl` throws `NoEnsemblGeneIdForEntrezGeneIdException`. The global exception handler does not define a custom handler for this exception.
  - Intentionally violated constraints:
    The path ID is absent from canonical mapping data.
- Branch 2:
  - Preconditions:
    - `{entrezGeneId}` is not parseable as an integer string.
  - Failure endpoint:
    `GET /ensembl/canonical-gene/entrez/{entrezGeneId}`
  - Why this fails:
    Repository lookup attempts integer parsing and can throw an uncaught `NumberFormatException`.
  - Intentionally violated constraints:
    The path value is not an integer-like Entrez Gene ID.

Endpoint coverage:
- Covers:
  `GET /ensembl/canonical-gene/entrez/{entrezGeneId}`
- Distinct meaning:
  Single canonical gene lookup by Entrez ID.

### Function 11: retrieve canonical Ensembl transcripts by Hugo symbols

Function name:
retrieve canonical transcripts by Hugo symbol list

Core endpoint(s):
- `POST /ensembl/canonical-transcript/hgnc`

Preconditions:
- The backing Ensembl repository contains canonical transcript records for the submitted Hugo symbols under the requested or default `isoformOverrideSource`. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns canonical Ensembl transcript records for the submitted Hugo symbols.
- Invocation:
  Step 1: `POST /ensembl/canonical-transcript/hgnc` with a JSON array body of Hugo symbols and optional query value `isoformOverrideSource`.
- Constraints:
  `isoformOverrideSource` selects the canonical transcript source. Supported implementation values include `mskcc`, `genome_nexus`, and `ensembl`; null or unknown values use the repository default lookup path.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one Hugo symbol has no canonical transcript for the selected source in the backing Ensembl repository.
  - Failure endpoint:
    `POST /ensembl/canonical-transcript/hgnc`
  - Why this fails:
    The service catches the per-symbol `EnsemblTranscriptNotFoundException` and omits that item from the response.
  - Intentionally violated constraints:
    The body includes a symbol without a matching canonical transcript.

Endpoint coverage:
- Covers:
  `POST /ensembl/canonical-transcript/hgnc`
- Distinct meaning:
  Batch canonical transcript lookup by Hugo symbols.

### Function 12: retrieve canonical Ensembl transcript by one Hugo symbol

Function name:
retrieve canonical transcript by Hugo symbol

Core endpoint(s):
- `GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`

Preconditions:
- The backing Ensembl repository contains a canonical transcript for `{hugoSymbol}` under the requested or default `isoformOverrideSource`. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns the canonical Ensembl transcript for one Hugo symbol.
- Invocation:
  Step 1: `GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}` with `{hugoSymbol}` set to the target symbol and optional query value `isoformOverrideSource`.
- Constraints:
  `{hugoSymbol}` must resolve to a canonical transcript under the selected or default isoform override source.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No canonical transcript exists for `{hugoSymbol}` and the selected source in the backing Ensembl repository.
  - Failure endpoint:
    `GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`
  - Why this fails:
    The service throws `EnsemblTranscriptNotFoundException`, which the global handler maps to a not-found response.
  - Intentionally violated constraints:
    The requested symbol/source pair has no canonical transcript.

Endpoint coverage:
- Covers:
  `GET /ensembl/canonical-transcript/hgnc/{hugoSymbol}`
- Distinct meaning:
  Single canonical transcript lookup by Hugo symbol.

### Function 13: retrieve Ensembl transcripts by query filters

Function name:
filter Ensembl transcripts by query values

Core endpoint(s):
- `GET /ensembl/transcript`

Preconditions:
- The backing Ensembl repository contains transcript records matching the supplied `geneId`, `proteinId`, or `hugoSymbol` query values. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns Ensembl transcripts matching query parameters.
- Invocation:
  Step 1: `GET /ensembl/transcript` with one or more of query parameters `geneId`, `proteinId`, and `hugoSymbol`.
- Constraints:
  `geneId` alone filters by gene ID. `proteinId` alone filters by protein ID. `hugoSymbol` alone filters by Hugo symbol. `geneId+proteinId`, `geneId+hugoSymbol`, and `proteinId+hugoSymbol` apply combined repository filters. If all three are supplied, implementation falls through to Hugo-symbol-only lookup and ignores `geneId` and `proteinId`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Query parameters are syntactically valid but do not match any stored transcript records.
  - Failure endpoint:
    `GET /ensembl/transcript`
  - Why this fails:
    Repository lookup returns an empty list; no exception is thrown.
  - Intentionally violated constraints:
    Query values do not correspond to stored transcript data.
- Branch 2:
  - Preconditions:
    - The caller supplies `geneId`, `proteinId`, and `hugoSymbol` together and expects all three query parameters to be combined.
  - Failure endpoint:
    `GET /ensembl/transcript`
  - Why this fails:
    The implementation has no all-three branch and uses only `hugoSymbol` in the final `else` branch.
  - Intentionally violated constraints:
    The request relies on a combined all-three filter that the implementation does not apply.

Endpoint coverage:
- Covers:
  `GET /ensembl/transcript`
- Distinct meaning:
  Filtered transcript lookup by query parameters.

### Function 14: request Ensembl transcripts with no query filters

Function name:
request unfiltered Ensembl transcripts

Core endpoint(s):
- `GET /ensembl/transcript`

Preconditions:
- No query parameter is supplied for `geneId`, `proteinId`, or `hugoSymbol`. This state is created by omitting those query values from the request; no setup endpoint is involved.

Successful execution:
- Result:
  Returns an empty list. The OpenAPI summary says no query parameter retrieves all transcripts, but the implementation explicitly returns a new empty list when all three query parameters are null.
- Invocation:
  Step 1: `GET /ensembl/transcript` with no `geneId`, `proteinId`, or `hugoSymbol` query parameter.
- Constraints:
  This is an OpenAPI/source discrepancy. The source code in `EnsemblServiceImpl` is the runtime authority and does not perform an all-transcripts lookup for this mode.

Failure or exceptional branches:
- None identified from the implementation; this mode returns an empty list.

Endpoint coverage:
- Covers:
  `GET /ensembl/transcript`
- Distinct meaning:
  No-filter transcript request; documented as all-transcript retrieval but implemented as empty-list retrieval.

### Function 15: retrieve Ensembl transcripts by transcript ID list

Function name:
retrieve transcripts by transcript ID list

Core endpoint(s):
- `POST /ensembl/transcript`

Preconditions:
- The backing Ensembl repository contains transcript records whose transcript IDs are in the submitted `transcriptIds` list. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns transcripts whose transcript IDs are in the submitted list.
- Invocation:
  Step 1: `POST /ensembl/transcript` with a JSON body containing non-empty `transcriptIds`.
- Constraints:
  If `transcriptIds` is non-empty, it takes precedence over `geneIds`, `proteinIds`, and `hugoSymbols` even if those fields are also present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - One or more submitted transcript IDs do not exist in the backing Ensembl repository.
  - Failure endpoint:
    `POST /ensembl/transcript`
  - Why this fails:
    The repository returns only matching transcript records; missing IDs are omitted.
  - Intentionally violated constraints:
    The body includes transcript IDs absent from the transcript collection.

Endpoint coverage:
- Covers:
  `POST /ensembl/transcript`
- Distinct meaning:
  Batch transcript lookup by transcript IDs.

### Function 16: retrieve Ensembl transcripts by gene ID list

Function name:
retrieve transcripts by gene ID list

Core endpoint(s):
- `POST /ensembl/transcript`

Preconditions:
- The backing Ensembl repository contains transcript records whose gene IDs are in the submitted `geneIds` list. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.
- The request body does not contain a non-empty `transcriptIds` field, because `transcriptIds` has higher priority.

Successful execution:
- Result:
  Returns transcripts whose gene IDs are in the submitted list.
- Invocation:
  Step 1: `POST /ensembl/transcript` with a JSON body containing non-empty `geneIds` and no non-empty `transcriptIds`.
- Constraints:
  `geneIds` is used only when `transcriptIds` is null or empty. It takes precedence over `proteinIds` and `hugoSymbols`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Submitted gene IDs do not match any stored transcript records.
    - The request body has no non-empty `transcriptIds` value that would take precedence.
  - Failure endpoint:
    `POST /ensembl/transcript`
  - Why this fails:
    Repository lookup returns an empty list.
  - Intentionally violated constraints:
    Submitted gene IDs are absent from transcript data.

Endpoint coverage:
- Covers:
  `POST /ensembl/transcript`
- Distinct meaning:
  Batch transcript lookup by gene IDs.

### Function 17: retrieve Ensembl transcripts by protein ID list

Function name:
retrieve transcripts by protein ID list

Core endpoint(s):
- `POST /ensembl/transcript`

Preconditions:
- The backing Ensembl repository contains transcript records whose protein IDs are in the submitted `proteinIds` list. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.
- The request body does not contain non-empty `transcriptIds` or `geneIds` fields, because those fields have higher priority.

Successful execution:
- Result:
  Returns transcripts whose protein IDs are in the submitted list.
- Invocation:
  Step 1: `POST /ensembl/transcript` with a JSON body containing non-empty `proteinIds` and no non-empty `transcriptIds` or `geneIds`.
- Constraints:
  `proteinIds` is used only after `transcriptIds` and `geneIds` are absent or empty. It takes precedence over `hugoSymbols`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Submitted protein IDs do not match any stored transcript records.
    - The request body has no non-empty `transcriptIds` or `geneIds` value that would take precedence.
  - Failure endpoint:
    `POST /ensembl/transcript`
  - Why this fails:
    Repository lookup returns an empty list.
  - Intentionally violated constraints:
    Submitted protein IDs are absent from transcript data.

Endpoint coverage:
- Covers:
  `POST /ensembl/transcript`
- Distinct meaning:
  Batch transcript lookup by protein IDs.

### Function 18: retrieve Ensembl transcripts by Hugo symbol list

Function name:
retrieve transcripts by Hugo symbol list

Core endpoint(s):
- `POST /ensembl/transcript`

Preconditions:
- The backing Ensembl repository contains transcript records whose Hugo symbol list contains one of the submitted `hugoSymbols` values. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.
- The request body does not contain non-empty `transcriptIds`, `geneIds`, or `proteinIds` fields, because those fields have higher priority.

Successful execution:
- Result:
  Returns transcripts whose Hugo symbol list contains one of the submitted symbols.
- Invocation:
  Step 1: `POST /ensembl/transcript` with a JSON body containing non-empty `hugoSymbols` and no non-empty `transcriptIds`, `geneIds`, or `proteinIds`.
- Constraints:
  `hugoSymbols` is used only if all higher-priority body lists are absent or empty.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Submitted Hugo symbols do not match any stored transcript records.
    - The request body has no non-empty `transcriptIds`, `geneIds`, or `proteinIds` value that would take precedence.
  - Failure endpoint:
    `POST /ensembl/transcript`
  - Why this fails:
    Repository lookup returns an empty list.
  - Intentionally violated constraints:
    Submitted Hugo symbols are absent from transcript data.

Endpoint coverage:
- Covers:
  `POST /ensembl/transcript`
- Distinct meaning:
  Batch transcript lookup by Hugo symbols.

### Function 19: request Ensembl transcripts with no usable body filter

Function name:
request transcripts without body filters

Core endpoint(s):
- `POST /ensembl/transcript`

Preconditions:
- The request body omits all supported filter lists or supplies only empty `transcriptIds`, `geneIds`, `proteinIds`, and `hugoSymbols` lists.

Successful execution:
- Result:
  Returns an empty list when the POST filter body has no non-empty supported list.
- Invocation:
  Step 1: `POST /ensembl/transcript` with an empty filter body or all supported filter lists empty.
- Constraints:
  The implementation checks `transcriptIds`, then `geneIds`, then `proteinIds`, then `hugoSymbols`; if none is non-empty, it returns a new empty list.

Failure or exceptional branches:
- None identified from the implementation; this mode returns an empty list.

Endpoint coverage:
- Covers:
  `POST /ensembl/transcript`
- Distinct meaning:
  Empty-filter transcript request.

### Function 20: retrieve Ensembl transcript by transcript ID

Function name:
retrieve transcript by transcript ID

Core endpoint(s):
- `GET /ensembl/transcript/{transcriptId}`

Preconditions:
- The backing Ensembl repository contains a transcript record with `transcriptId = {transcriptId}`. This can be satisfied by directly seeding the Ensembl collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns the transcript record with the requested Ensembl transcript ID.
- Invocation:
  Step 1: `GET /ensembl/transcript/{transcriptId}` with `{transcriptId}` set to an Ensembl transcript ID.
- Constraints:
  The path value is passed directly to `findOneByTranscriptId`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{transcriptId}` is absent from the backing Ensembl repository.
  - Failure endpoint:
    `GET /ensembl/transcript/{transcriptId}`
  - Why this fails:
    The implementation returns the repository result directly. Although the interface and controller declare `EnsemblTranscriptNotFoundException`, the service implementation does not throw it for this lookup, so the user-visible result is a null/empty response rather than the custom not-found branch.
  - Intentionally violated constraints:
    The path transcript ID is absent from transcript data.

Endpoint coverage:
- Covers:
  `GET /ensembl/transcript/{transcriptId}`
- Distinct meaning:
  Single transcript lookup by transcript ID.

### Function 21: retrieve external cross-references for an Ensembl accession

Function name:
retrieve Ensembl xrefs

Core endpoint(s):
- `GET /ensembl/xrefs`

Preconditions:
- The query `accession` identifies an Ensembl gene accession that the configured Ensembl xrefs service can resolve. This can be satisfied by configuring an upstream xrefs service that returns the accession data; no project endpoint creates xref data.

Successful execution:
- Result:
  Returns external database cross-references for an Ensembl gene accession.
- Invocation:
  Step 1: `GET /ensembl/xrefs` with required query parameter `accession`.
- Constraints:
  `accession` is sent to the configured Ensembl xrefs service and mapped into `GeneXref` records.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The accession is invalid, rejected by the upstream xrefs service, cannot be mapped, or the upstream service is unavailable.
  - Failure endpoint:
    `GET /ensembl/xrefs`
  - Why this fails:
    `GeneXrefServiceImpl` wraps mapping, client, or access errors in `EnsemblWebServiceException`, which the global handler returns with the upstream status or service-unavailable status.
  - Intentionally violated constraints:
    The query accession cannot be resolved by the external xrefs service.

Endpoint coverage:
- Covers:
  `GET /ensembl/xrefs`
- Distinct meaning:
  External xref lookup by Ensembl accession.

### Function 22: retrieve PDB headers for multiple PDB IDs

Function name:
retrieve PDB headers by ID list

Core endpoint(s):
- `POST /pdb/header`

Preconditions:
- Each submitted PDB ID is available through the configured PDB header fetcher or backing PDB header cache. This can be satisfied by seeding the PDB header cache directly or by relying on the configured external PDB header service; no project endpoint creates PDB header data.

Successful execution:
- Result:
  Returns PDB header records for submitted PDB IDs.
- Invocation:
  Step 1: `POST /pdb/header` with a JSON array body of PDB IDs.
- Constraints:
  Duplicate PDB IDs are de-duplicated before lookup.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted PDB ID is invalid, missing from the cache, not found by the external PDB service, or cannot be mapped to a PDB header.
  - Failure endpoint:
    `POST /pdb/header`
  - Why this fails:
    The service catches `PdbHeaderNotFoundException` per ID and silently omits that item.
  - Intentionally violated constraints:
    The body includes a PDB ID absent from PDB header data.
- Branch 2:
  - Preconditions:
    - At least one submitted PDB ID triggers an external PDB header service error or resource access failure.
  - Failure endpoint:
    `POST /pdb/header`
  - Why this fails:
    The batch service logs `PdbHeaderWebServiceException` per ID and omits that item.
  - Intentionally violated constraints:
    The upstream PDB header lookup cannot complete for that ID.

Endpoint coverage:
- Covers:
  `POST /pdb/header`
- Distinct meaning:
  Batch PDB header lookup.

### Function 23: retrieve PDB header for one PDB ID

Function name:
retrieve PDB header by ID

Core endpoint(s):
- `GET /pdb/header/{pdbId}`

Preconditions:
- `{pdbId}` is available through the configured PDB header fetcher or backing PDB header cache. This can be satisfied by seeding the PDB header cache directly or by relying on the configured external PDB header service; no project endpoint creates PDB header data.

Successful execution:
- Result:
  Returns the parsed PDB header for one PDB ID.
- Invocation:
  Step 1: `GET /pdb/header/{pdbId}` with `{pdbId}` set to the PDB ID.
- Constraints:
  `{pdbId}` is used as the cache key and external service query value. The returned object has its `pdbId` set to the original path value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{pdbId}` is not found, cannot be fetched, or cannot be mapped to a PDB header.
  - Failure endpoint:
    `GET /pdb/header/{pdbId}`
  - Why this fails:
    The service throws `PdbHeaderNotFoundException`, handled as a not-found response.
  - Intentionally violated constraints:
    The path ID is absent from PDB header data.
- Branch 2:
  - Preconditions:
    - The external PDB header service returns an error or is unavailable for `{pdbId}`.
  - Failure endpoint:
    `GET /pdb/header/{pdbId}`
  - Why this fails:
    The service throws `PdbHeaderWebServiceException`, handled with the upstream status or service-unavailable status.
  - Intentionally violated constraints:
    The upstream PDB service cannot complete the lookup.

Endpoint coverage:
- Covers:
  `GET /pdb/header/{pdbId}`
- Distinct meaning:
  Single PDB header lookup.

### Function 24: retrieve PFAM domains for multiple accessions

Function name:
retrieve PFAM domains by accession list

Core endpoint(s):
- `POST /pfam/domain`

Preconditions:
- The backing PFAM repository contains domain records whose accessions are in the submitted list. This can be satisfied by directly seeding the PFAM domain collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns PFAM domain records whose accessions are in the submitted list.
- Invocation:
  Step 1: `POST /pfam/domain` with a JSON array body of PFAM accessions.
- Constraints:
  The body values are matched through `findByPfamAccessionIn`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Some submitted PFAM accessions do not exist in the backing PFAM repository.
  - Failure endpoint:
    `POST /pfam/domain`
  - Why this fails:
    Repository lookup returns only matching domains; missing accessions are omitted.
  - Intentionally violated constraints:
    The body includes PFAM accessions absent from PFAM data.

Endpoint coverage:
- Covers:
  `POST /pfam/domain`
- Distinct meaning:
  Batch PFAM domain lookup.

### Function 25: retrieve PFAM domain by accession

Function name:
retrieve PFAM domain by accession

Core endpoint(s):
- `GET /pfam/domain/{pfamAccession}`

Preconditions:
- The backing PFAM repository contains a domain record with `pfamAccession = {pfamAccession}`. This can be satisfied by directly seeding the PFAM domain collection; no project endpoint creates this reference data.

Successful execution:
- Result:
  Returns one PFAM domain for the requested accession.
- Invocation:
  Step 1: `GET /pfam/domain/{pfamAccession}` with `{pfamAccession}` set to a PFAM accession.
- Constraints:
  `{pfamAccession}` must match a stored PFAM domain accession.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{pfamAccession}` is absent from the backing PFAM repository.
  - Failure endpoint:
    `GET /pfam/domain/{pfamAccession}`
  - Why this fails:
    The service throws `PfamDomainNotFoundException`, handled as a not-found response.
  - Intentionally violated constraints:
    The path accession is absent from PFAM domain data.

Endpoint coverage:
- Covers:
  `GET /pfam/domain/{pfamAccession}`
- Distinct meaning:
  Single PFAM domain lookup.

### Function 26: retrieve PTM entries by one Ensembl transcript ID

Function name:
retrieve PTMs by transcript ID

Core endpoint(s):
- `GET /ptm/experimental`

Preconditions:
- The backing PTM repository contains post-translational modification entries with transcript IDs beginning with the supplied `ensemblTranscriptId`. This can be satisfied by directly seeding the PTM collection; no project endpoint creates PTM data.

Successful execution:
- Result:
  Returns post-translational modification entries associated with one transcript ID prefix.
- Invocation:
  Step 1: `GET /ptm/experimental` with query parameter `ensemblTranscriptId`.
- Constraints:
  The implementation converts the query value to a starts-with regex, so the lookup may match entries whose transcript IDs begin with the supplied value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No PTM entry has an Ensembl transcript ID beginning with the supplied query value.
  - Failure endpoint:
    `GET /ptm/experimental`
  - Why this fails:
    Repository lookup returns an empty list.
  - Intentionally violated constraints:
    The transcript ID prefix is absent from PTM data.

Endpoint coverage:
- Covers:
  `GET /ptm/experimental`
- Distinct meaning:
  PTM lookup by transcript query parameter.

### Function 27: request PTM entries without transcript query

Function name:
request PTMs without transcript ID

Core endpoint(s):
- `GET /ptm/experimental`

Preconditions:
- The request omits the `ensemblTranscriptId` query parameter. This state is created by leaving the query value out of the request; no setup endpoint is involved.

Successful execution:
- Result:
  Returns the result of searching for transcript IDs starting with the literal null value, typically an empty list.
- Invocation:
  Step 1: `GET /ptm/experimental` without `ensemblTranscriptId`.
- Constraints:
  The Swagger marks `ensemblTranscriptId` as not required, but the implementation does not list all PTMs when it is omitted; it passes null into starts-with pattern conversion.

Failure or exceptional branches:
- None identified from the implementation; this mode normally returns an empty list rather than an explicit error.

Endpoint coverage:
- Covers:
  `GET /ptm/experimental`
- Distinct meaning:
  No-query PTM request; optional in OpenAPI but not implemented as list-all retrieval.

### Function 28: retrieve PTM entries by transcript ID list

Function name:
retrieve PTMs by transcript ID list

Core endpoint(s):
- `POST /ptm/experimental`

Preconditions:
- The backing PTM repository contains post-translational modification entries with transcript IDs beginning with one of the submitted `transcriptIds`. This can be satisfied by directly seeding the PTM collection; no project endpoint creates PTM data.
- The request body contains a non-null `transcriptIds` list.

Successful execution:
- Result:
  Returns post-translational modification entries associated with any submitted transcript ID prefix.
- Invocation:
  Step 1: `POST /ptm/experimental` with a JSON object body containing `transcriptIds`.
- Constraints:
  `transcriptIds` must be present as a list. Each value is converted to a starts-with regex before repository lookup.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No submitted transcript ID matches PTM data by prefix.
    - The request body contains a non-null `transcriptIds` list.
  - Failure endpoint:
    `POST /ptm/experimental`
  - Why this fails:
    Repository lookup returns an empty list.
  - Intentionally violated constraints:
    Submitted transcript IDs are absent from PTM data.
- Branch 2:
  - Preconditions:
    - The request body omits `transcriptIds` or sets it to null.
  - Failure endpoint:
    `POST /ptm/experimental`
  - Why this fails:
    The service calls `.stream()` on the list in `Patterns.toStartsWithPatternList(List<String>)`; a null list can raise an unhandled null-pointer failure.
  - Intentionally violated constraints:
    The required body list is missing.

Endpoint coverage:
- Covers:
  `POST /ptm/experimental`
- Distinct meaning:
  Batch PTM lookup by transcript IDs.

### Function 29: retrieve Genome Nexus and VEP version information

Function name:
retrieve service version information

Core endpoint(s):
- `GET /version`

Preconditions:
- Source information is available from the configured `SourceInfoRepository` and related application properties. This can be satisfied by configuring the source info repository/properties directly; no project endpoint creates version state.

Successful execution:
- Result:
  Returns aggregate source information for Genome Nexus and VEP.
- Invocation:
  Step 1: `GET /version`.
- Constraints:
  Version values are read from configured properties and source info repository data. Runtime output depends on the configured source information.

Failure or exceptional branches:
- None identified from the OpenAPI and implementation.

Endpoint coverage:
- Covers:
  `GET /version`
- Distinct meaning:
  Service and data-source version lookup.
