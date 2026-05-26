### Function 1: choose search result representation

Function name:
choose search result representation

Core endpoint(s):
- `GET /search`

Preconditions:
- None. This function operates directly on the search endpoint and does not require a setup endpoint or mutable database state. Searchable videos, channels, playlists, channel ownership data, and content-owner data are seeded in memory by the source code.

Successful execution:
- Result:
  The endpoint returns search results while selecting which search result fields are included. The implementation accepts `part=snippet`, `part=id`, or a comma-separated combination containing both values.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `part=id`, or `part=id,snippet`, plus any compatible search filters.
- Constraints:
  `part` is required. The Swagger document only enumerates `snippet`, but the source code also accepts `id` and `id,snippet`; the source code is the runtime authority. `part=snippet` returns snippet data, `part=id` returns only resource identifiers, and `part=id,snippet` returns both.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No representation part is supplied in the request. This omitted state is produced by calling `GET /search` without the required `part` query parameter.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The resource validates `part` before repository search and returns HTTP 400 when it is missing.
  - Intentionally violated constraints:
    The required query parameter `part` was omitted.
- Branch 2:
  - Preconditions:
    - The request uses an unsupported `part` token such as `statistics`. This invalid query state is produced by calling `GET /search` with `part=statistics`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation only accepts `snippet` and `id` tokens in the comma-separated `part` value.
  - Intentionally violated constraints:
    `part` contains a token outside the implementation-supported values.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Controls the visible fields included in returned search result items.

### Function 2: search public content

Function name:
search public content

Core endpoint(s):
- `GET /search`

Preconditions:
- Searchable videos, channels, and playlists exist in the seeded in-memory repositories. No active documented or implemented endpoint creates these resources, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns matching public videos, channels, and playlists. Without `type`, the implementation searches all three resource kinds.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`; optional compatible filters include `q`, `channelId`, `publishedAfter`, `publishedBefore`, `topicId`, `regionCode`, `relevanceLanguage`, `safeSearch`, `order`, and `maxResults`.
- Constraints:
  `channelId`, when supplied, must match a seeded channel ID. `q` matches titles and supports basic `|` OR and `-` exclusion logic. `publishedAfter` and `publishedBefore` must use the implementation format `yyyy-MM-dd'T'HH:mm:ss'Z'`. `order=date`, `order=title`, and `order=viewCount` sort results in source code; `rating`, `relevance`, and `videoCount` are accepted but do not add custom sorting.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No seeded channel has the requested `channelId`, such as `missing-channel`. Because no active endpoint creates channels, this invalid state is satisfied by using an ID absent from the seeded channel repository or by direct fixture/database setup that intentionally omits that channel.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation rejects unknown channel IDs before filtering results and returns HTTP 400.
  - Intentionally violated constraints:
    `channelId` must identify an existing seeded channel.
- Branch 2:
  - Preconditions:
    - A common query parameter has an invalid value, such as `maxResults=51`. This invalid request state is produced by calling `GET /search` with `part=snippet` and the out-of-range or unsupported query value.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The resource validates parameter ranges and enums. `maxResults` must be between `0` and `50`; similar validation exists for invalid `order`, `regionCode`, `relevanceLanguage`, and `safeSearch`.
  - Intentionally violated constraints:
    A query value is outside the accepted implementation range or enum.
- Branch 3:
  - Preconditions:
    - A date filter is present but not parseable in the implementation format, such as `publishedAfter=2020-04-01`. This invalid request state is produced by calling `GET /search` with the malformed date value.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation parses date filters as UTC timestamps like `2020-04-01T00:00:00Z` and returns HTTP 400 when parsing fails.
  - Intentionally violated constraints:
    `publishedAfter` or `publishedBefore` does not match the required timestamp format.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Performs the default collection search across videos, channels, and playlists.

### Function 3: search selected resource types

Function name:
search selected resource types

Core endpoint(s):
- `GET /search`

Preconditions:
- Searchable videos, channels, and playlists exist in the seeded in-memory repositories. No active documented or implemented endpoint creates these resources, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint restricts search results to selected resource kinds: videos, channels, playlists, or supported combinations.
- Invocation:
  Step 1: `GET /search` with query `part=snippet` and `type=video`, `type=channel`, `type=playlist`, or a comma-separated combination such as `type=video,playlist`.
- Constraints:
  Recognized type tokens are `video`, `channel`, and `playlist`. The implementation ignores unrecognized tokens instead of validating them. If only unrecognized tokens are supplied, the computed type mask is zero and the repository falls back to default video searching.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A type-specific filter is present while `type` is not the required resource kind, such as `type=playlist` with `videoDuration=short`. This invalid request state is produced by calling `GET /search` with a video-only filter while not setting `type=video`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation rejects video-specific filters unless `type` is exactly `video`.
  - Intentionally violated constraints:
    `videoDuration` is used while `type` is not `video`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Uses the `type` query parameter to restrict which resource kinds are searched.

### Function 4: search channels by channel type

Function name:
search channels by channel type

Core endpoint(s):
- `GET /search`

Preconditions:
- Searchable channels exist in the seeded in-memory channel repository. No active documented or implemented endpoint creates channels, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns channels of the requested channel type.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `type=channel`, and optionally `channelType=any` or `channelType=show`.
- Constraints:
  `channelType` requires `type=channel` exactly. `channelType=show` returns only seeded channels whose internal type is `show`; `channelType=any` does not narrow the channel set.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `channelType` is supplied without the required `type=channel` relationship, such as `channelType=show` with no `type`. This invalid request state is produced by calling `GET /search` with `channelType` while omitting or changing `type`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation dependency validation requires `type=channel` when `channelType` is present.
  - Intentionally violated constraints:
    The required `type=channel` relationship was omitted.
- Branch 2:
  - Preconditions:
    - `channelType` has an unsupported value, such as `creator`. This invalid request state is produced by calling `GET /search` with `part=snippet`, `type=channel`, and `channelType=creator`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation only accepts `any` and `show`.
  - Intentionally violated constraints:
    `channelType` is outside the allowed values.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches channel resources with channel-type filtering.

### Function 5: search videos with video attributes

Function name:
search videos with video attributes

Core endpoint(s):
- `GET /search`

Preconditions:
- Searchable videos exist in the seeded in-memory video repository with metadata such as event status, captions, category, definition, dimension, duration, embeddability, license, and video type. No active documented or implemented endpoint creates videos, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos matching video-only filters such as event status, captions, category, definition, dimension, duration, embeddability, license, syndication, or video type.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, and one or more video filters such as `eventType=live`, `videoCaption=closedCaption`, `videoCategoryId=10`, `videoDefinition=high`, `videoDuration=short`, `videoEmbeddable=true`, `videoLicense=youtube`, `videoSyndicated=true`, or `videoType=episode`.
- Constraints:
  All video filters require `type=video` exactly. `videoSyndicated` is validated and requires `type=video`, but the repository does not apply a syndication filter even though Swagger describes one.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A video-only filter is supplied without the required `type=video` relationship, such as `videoDuration=short` with no `type`. This invalid request state is produced by calling `GET /search` with a video-only query parameter while omitting or changing `type`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation rejects video-only filters unless `type` is exactly `video`.
  - Intentionally violated constraints:
    The required `type=video` relationship was omitted.
- Branch 2:
  - Preconditions:
    - A video filter has an unsupported enum value, such as `videoDefinition=ultra`. This invalid request state is produced by calling `GET /search` with `part=snippet`, `type=video`, and the unsupported video filter value.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The resource validates video filter enums. For example, `videoDefinition` only accepts `any`, `high`, and `standard`.
  - Intentionally violated constraints:
    The video filter value is outside the implementation enum.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Performs video search constrained by video-specific metadata.

### Function 6: search videos by geographic area

Function name:
search videos by geographic area

Core endpoint(s):
- `GET /search`

Preconditions:
- Searchable videos exist in the seeded in-memory video repository, and matching videos must have stored latitude and longitude values. No active documented or implemented endpoint creates geotagged videos, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos whose stored latitude and longitude fall within the requested circular area.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, `location={latitude},{longitude}`, and `locationRadius={number}{unit}`.
- Constraints:
  `location` and `locationRadius` must be supplied together. `location` must be a valid latitude and longitude pair. `locationRadius` units may be `m`, `km`, `ft`, or `mi`, and the converted radius must not exceed 1000 km. Only videos with stored coordinates can match.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Only one of the paired location parameters is supplied, such as `location=37.3565037,-5.9817521` without `locationRadius`. This invalid request state is produced by calling `GET /search` with one location parameter omitted.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation requires `location` and `locationRadius` together and returns HTTP 400 for partial location input.
  - Intentionally violated constraints:
    The required paired `locationRadius` value was omitted.
- Branch 2:
  - Preconditions:
    - The request contains an invalid or too-large radius, such as `locationRadius=2000km` with a valid `location`. This invalid request state is produced by calling `GET /search` with a radius that fails parsing or converts to more than 1000 km.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation validates the radius format and converted distance, then returns HTTP 400 when the radius is invalid or exceeds the 1000 km limit.
  - Intentionally violated constraints:
    The radius is not in the accepted format or exceeds the allowed distance.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches geotagged videos by distance from a coordinate.

### Function 7: search content-owner videos

Function name:
search content-owner videos

Core endpoint(s):
- `GET /search`

Preconditions:
- A seeded content-owner identifier exists on one or more seeded channels, such as `contentOwner1` or `contentOwnerGoogle`. No active documented or implemented endpoint creates content owners or channels, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.
- Searchable videos exist for channels belonging to that content owner. No active endpoint creates videos, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos owned by the content owner named by `onBehalfOfContentOwner`.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `forContentOwner=true`, `onBehalfOfContentOwner={contentOwnerId}`, and `type=video`.
- Constraints:
  `{contentOwnerId}` must match a seeded channel content owner. The request may also use supported filters such as `channelId`, `eventType`, `location`, `publishedAfter`, `publishedBefore`, `q`, `regionCode`, `relevanceLanguage`, `safeSearch`, `topicId`, `videoCaption`, and `videoCategoryId`. It must not combine with `forDeveloper`, `forMine`, or `relatedToVideoId`, and it must not use `videoDefinition`, `videoDimension`, `videoDuration`, `videoLicense`, `videoEmbeddable`, `videoSyndicated`, or `videoType`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `forContentOwner=true` is supplied without a usable `onBehalfOfContentOwner` value. This omitted owner state is produced by calling `GET /search` with `forContentOwner=true` and no content-owner query value.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation treats content-owner search without an owner value as an invalid search filter and returns HTTP 400.
  - Intentionally violated constraints:
    The required `onBehalfOfContentOwner` value was omitted.
- Branch 2:
  - Preconditions:
    - No seeded channel has the requested content owner, such as `missingOwner`. Because no active endpoint creates content owners, this invalid state is satisfied by using an owner ID absent from seeded channel data or by direct fixture/database setup that intentionally omits that content owner.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation returns HTTP 404 for unknown content owners.
  - Intentionally violated constraints:
    `{contentOwnerId}` does not match any seeded channel content owner.
- Branch 3:
  - Preconditions:
    - A content-owner search also supplies a forbidden video filter, such as `videoDuration=short`. This invalid request state is produced by calling `GET /search` with `forContentOwner=true`, `onBehalfOfContentOwner={contentOwnerId}`, `type=video`, and the forbidden filter.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation forbids `videoDefinition`, `videoDimension`, `videoDuration`, `videoLicense`, `videoEmbeddable`, `videoSyndicated`, and `videoType` with content-owner search.
  - Intentionally violated constraints:
    `videoDuration` was used with `forContentOwner=true`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches videos through the content-owner ownership filter.

### Function 8: search authenticated user's videos

Function name:
search authenticated user's videos

Core endpoint(s):
- `GET /search`

Preconditions:
- The seeded video repository contains videos whose channel ID is the implementation's hard-coded current-user channel `UCxwvutsrqponmlkjihgfedc`. No active documented or implemented endpoint creates users, channels, authentication credentials, or videos, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos owned by the implementation's hard-coded authenticated user channel.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `forMine=true`, and `type=video`.
- Constraints:
  The source treats channel `UCxwvutsrqponmlkjihgfedc` as the authenticated user's channel and does not implement actual authentication. `forMine=true` must not combine with `forContentOwner`, `forDeveloper`, or `relatedToVideoId`, and it must not use `videoDefinition`, `videoDimension`, `videoDuration`, `videoLicense`, `videoEmbeddable`, `videoSyndicated`, or `videoType`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `forMine=true` is supplied without the required `type=video` relationship. This invalid request state is produced by calling `GET /search` with `forMine=true` while omitting or changing `type`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation requires `type=video` for `forMine` requests and returns HTTP 400 when the dependency is not satisfied.
  - Intentionally violated constraints:
    The required `type=video` relationship was omitted.
- Branch 2:
  - Preconditions:
    - `forMine=true` is combined with a mutually exclusive ownership or mode filter, such as `forDeveloper=true`. This invalid request state is produced by calling `GET /search` with both query flags.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation allows at most one of `forContentOwner`, `forDeveloper`, `forMine`, and `relatedToVideoId`.
  - Intentionally violated constraints:
    Mutually exclusive search modes were combined.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches videos owned by the current user identity represented in the mock data.

### Function 9: search developer-uploaded videos

Function name:
search developer-uploaded videos

Core endpoint(s):
- `GET /search`

Preconditions:
- The seeded video repository contains videos marked as uploaded through the developer's application or website. No active documented or implemented endpoint creates developer identity, upload attribution, or videos, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos marked in the seeded data as uploaded through the developer's application or website.
- Invocation:
  Step 1: `GET /search` with query `part=snippet` and `forDeveloper=true`, optionally with compatible public search filters.
- Constraints:
  `forDeveloper=true` is mutually exclusive with `forContentOwner`, `forMine`, and `relatedToVideoId`. With no `type`, only developer-marked videos are returned because channel and playlist repositories suppress results when `forDeveloper=true`. The source code does not implement authentication for identifying a real developer despite the Swagger description.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Developer search is combined with another exclusive search mode, such as `forMine=true`. This invalid request state is produced by calling `GET /search` with `forDeveloper=true` and another exclusive mode flag or `relatedToVideoId`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation rejects simultaneous exclusive modes.
  - Intentionally violated constraints:
    `forDeveloper` and `forMine` were combined.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches the subset of videos flagged as developer-uploaded.

### Function 10: search related videos

Function name:
search related videos

Core endpoint(s):
- `GET /search`

Preconditions:
- A seeded video exists with ID `{videoId}`, such as `YVm37gs92ME`. No active documented or implemented endpoint creates videos, so this state is satisfied by source-seeded data, by using a video ID from a previous search response, or by direct test fixture/database setup before the application starts.
- Other seeded videos may share relevant topic IDs or the same category with `{videoId}`. No active endpoint creates those relationships, so this state is satisfied by source-seeded data or by direct test fixture/database setup before the application starts.

Successful execution:
- Result:
  The endpoint returns videos related to the video identified by `relatedToVideoId`, based on shared topic IDs or the same category.
- Invocation:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, and `relatedToVideoId={videoId}`.
- Constraints:
  `{videoId}` must match a seeded video ID. Implementation requires `type=video` and rejects combination with `forContentOwner`, `forDeveloper`, or `forMine`. Swagger says many other parameters are forbidden with `relatedToVideoId`, but the source code does not enforce those additional exclusions; the related-video repository only applies `regionCode`, `relevanceLanguage`, and `safeSearch`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No seeded video has the requested related video ID, such as `missingVideo`. Because no active endpoint creates videos, this invalid state is satisfied by using an ID absent from seeded video data or by direct fixture/database setup that intentionally omits that video.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation requires the related video to exist in seeded video data and returns HTTP 400 when it does not.
  - Intentionally violated constraints:
    `{videoId}` does not match any seeded video.
- Branch 2:
  - Preconditions:
    - `relatedToVideoId` is supplied without the required `type=video` relationship. This invalid request state is produced by calling `GET /search` with `relatedToVideoId={videoId}` while omitting or changing `type`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation dependency validation requires `type=video`.
  - Intentionally violated constraints:
    The required `type=video` relationship was omitted.
- Branch 3:
  - Preconditions:
    - `relatedToVideoId` is combined with another exclusive mode, such as `forMine=true`. This invalid request state is produced by calling `GET /search` with `relatedToVideoId={videoId}` and one of `forContentOwner`, `forDeveloper`, or `forMine`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    `relatedToVideoId` is mutually exclusive with `forContentOwner`, `forDeveloper`, and `forMine`.
  - Intentionally violated constraints:
    `relatedToVideoId` was combined with `forMine`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches for videos related to a specific existing video.

### Function 11: page through search results

Function name:
page through search results

Core endpoint(s):
- `GET /search`

Preconditions:
- A stable logical search result set exists for the chosen filters and `maxResults={pageSize}`. This state is satisfied by seeded in-memory videos, channels, and playlists or by direct test fixture/database setup before the application starts.
- A valid pagination token such as `page-2` has been produced by a prior `GET /search` response using the same search filters and the same `maxResults={pageSize}`. The prior endpoint can establish this token by returning `nextPageToken` or `prevPageToken`; direct test fixture/database setup can satisfy the same state by ensuring enough matching seeded results exist for the requested page.
- The `pageToken` value used in the core request must be reused with the same filter set and page size that produced it. Although the implementation only encodes the page number in the token and does not cryptographically bind it to the filters, changing filters or page size changes the logical result set being paged.

Successful execution:
- Result:
  The endpoint retrieves a later or previous page of a search result set and returns page metadata plus `prevPageToken` and/or `nextPageToken` when adjacent pages exist.
- Invocation:
  Step 1: `GET /search` with the same query filters, the same `maxResults={pageSize}`, and `pageToken={tokenFromPriorSearch}`.
- Constraints:
  `{tokenFromPriorSearch}` must have the implementation format `page-{number}` and must refer to a page number within the available pages for the current result set. `maxResults` defaults to `5` when omitted and must be between `0` and `50`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `pageToken` is malformed, such as `abc`. This invalid token state is produced by calling `GET /search` with a token that was not obtained from a prior search response and does not match the implementation's `page-{number}` parsing expectation.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Pagination parsing expects a token with a numeric component after `page-`; malformed tokens raise a pagination exception that the resource converts to HTTP 400.
  - Intentionally violated constraints:
    `pageToken` is not in implementation format.
- Branch 2:
  - Preconditions:
    - A stable result set exists for `part=snippet` and `maxResults=5`. This can be satisfied by seeded in-memory data or by direct test fixture/database setup with matching resources.
    - A valid pagination token should be obtained from `nextPageToken` or `prevPageToken` in a prior `GET /search` response using the same filters, but the failing request instead uses an out-of-range token such as `page-999`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The requested page number is greater than the number of pages computed for the result set, so repository pagination throws a `BadPaginationException` and the resource returns HTTP 400.
  - Intentionally violated constraints:
    The request did not reuse a token produced for an available page of the same logical result set.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Uses response pagination tokens to navigate a search result set.
