Analyzed active Swagger/OpenAPI path: `GET /search` under base path `/api`. The file contains many commented-out YouTube endpoints, but only `/search` is active. The Java source also exposes only `GET /search`.

No documented endpoint creates channels, videos, playlists, or content owners. Resource-specific searches therefore rely on seeded in-memory data from `src`, not setup calls. Swagger lists OAuth/401/403/429, but `src` does not implement authentication, authorization, or rate limiting, so those are not inferred as behaviors.

### Behavior 1: choose search result representation

Behavior name:
choose search result representation

Successful execution:
- Result:
  This behavior retrieves search results while choosing which search result fields are returned. The implementation accepts `part=snippet`, `part=id`, or both as a comma-separated value.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `part=id`, or `part=id,snippet`.
- Constraints:
  `part` is required. Implementation accepts `id` even though the Swagger enum only documents `snippet`. `part=snippet` returns snippets; `part=id` returns only resource identifiers; `part=id,snippet` returns both.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required `part` is missing.
  - Endpoint group:
    Step 1: `GET /search` without `part`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The resource validates `part` before searching and returns HTTP 400.
  - Intentionally violated constraints:
    Omitted required query parameter `part`.
- Branch 2:
  - Unsatisfied condition:
    `part` contains an unsupported token.
  - Endpoint group:
    Step 1: `GET /search` with query `part=statistics`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation only accepts `snippet` and `id`.
  - Intentionally violated constraints:
    `part` is not one of the implementation-supported values.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Controls the visible fields included in returned search result items.

### Behavior 2: search public content

Behavior name:
search public content

Successful execution:
- Result:
  This behavior returns matching public videos, channels, and playlists. Without `type`, the implementation searches all three resource kinds.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`; optional filters include `q`, `channelId`, `publishedAfter`, `publishedBefore`, `topicId`, `regionCode`, `relevanceLanguage`, `safeSearch`, `order`, and `maxResults`.
- Constraints:
  `channelId`, when supplied, must match a seeded channel ID. `q` matches titles and supports basic `|` OR and `-` exclusion logic. `publishedAfter` and `publishedBefore` must use `yyyy-MM-dd'T'HH:mm:ss'Z'`. `order=date`, `order=title`, and `order=viewCount` sort results in implementation; `rating`, `relevance`, and `videoCount` are accepted but do not add custom sorting.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `channelId` names no seeded channel.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `channelId=missing-channel`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation rejects unknown channel IDs before filtering results.
  - Intentionally violated constraints:
    `channelId` must match an existing seeded channel because no documented endpoint can create one.
- Branch 2:
  - Unsatisfied condition:
    A common query parameter has an invalid value.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `maxResults=51`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    `maxResults` must be between `0` and `50`. Similar validation exists for invalid `order`, `regionCode`, `relevanceLanguage`, and `safeSearch`.
  - Intentionally violated constraints:
    Query value is outside the accepted implementation range or enum.
- Branch 3:
  - Unsatisfied condition:
    Date filters are not parseable in the implementation format.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `publishedAfter=2020-04-01`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation expects a UTC timestamp such as `2020-04-01T00:00:00Z`.
  - Intentionally violated constraints:
    `publishedAfter` does not match the required timestamp format.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Performs the default collection search across videos, channels, and playlists.

### Behavior 3: search selected resource types

Behavior name:
search selected resource types

Successful execution:
- Result:
  This behavior restricts search results to selected resource kinds: videos, channels, playlists, or supported combinations.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet` and `type=video`, `type=channel`, `type=playlist`, or a comma-separated combination such as `type=video,playlist`.
- Constraints:
  Recognized type tokens are `video`, `channel`, and `playlist`. The implementation ignores unrecognized tokens. If only unrecognized tokens are supplied, implementation behavior falls back to video searching rather than returning a validation error.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Type-specific filter constraints are violated.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=playlist`, and `videoDuration=short`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Video-specific filters require `type=video` exactly.
  - Intentionally violated constraints:
    `videoDuration` is used while `type` is not `video`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Uses the `type` query parameter to restrict which resource kinds are searched.

### Behavior 4: search channels by channel type

Behavior name:
search channels by channel type

Successful execution:
- Result:
  This behavior returns channels of a requested channel type.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `type=channel`, and optionally `channelType=any` or `channelType=show`.
- Constraints:
  `channelType` requires `type=channel` exactly. `channelType=show` returns only seeded channels whose internal type is `show`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `channelType` is used without `type=channel`.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `channelType=show`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation dependency validation requires `type=channel` when `channelType` is present.
  - Intentionally violated constraints:
    Omitted required `type=channel` relationship.
- Branch 2:
  - Unsatisfied condition:
    `channelType` has an unsupported value.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=channel`, and `channelType=creator`.
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

### Behavior 5: search videos with video attributes

Behavior name:
search videos with video attributes

Successful execution:
- Result:
  This behavior returns videos matching video-only filters such as event status, captions, category, definition, dimension, duration, embeddability, license, syndication, or video type.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, and one or more video filters such as `eventType=live`, `videoCaption=closedCaption`, `videoCategoryId=10`, `videoDefinition=high`, `videoDuration=short`, `videoEmbeddable=true`, `videoLicense=youtube`, or `videoType=episode`.
- Constraints:
  All these filters require `type=video` exactly. `videoSyndicated` is validated and requires `type=video`, but the repository does not actually apply a syndication filter, despite the Swagger description.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A video-only filter is used without `type=video`.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `videoDuration=short`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation rejects video-only filters unless `type` is exactly `video`.
  - Intentionally violated constraints:
    Omitted required `type=video`.
- Branch 2:
  - Unsatisfied condition:
    A video filter has an unsupported enum value.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=video`, and `videoDefinition=ultra`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation only accepts `any`, `high`, and `standard` for `videoDefinition`.
  - Intentionally violated constraints:
    Video filter value is outside the implementation enum.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Performs video search constrained by video-specific metadata.

### Behavior 6: search videos by geographic area

Behavior name:
search videos by geographic area

Successful execution:
- Result:
  This behavior returns videos whose stored latitude and longitude fall within the requested circular area.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, `location={latitude},{longitude}`, and `locationRadius={number}{unit}`.
- Constraints:
  `location` and `locationRadius` must be supplied together. `locationRadius` units may be `m`, `km`, `ft`, or `mi`, and the converted radius must not exceed 1000 km. Only videos with stored coordinates can match.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Only one location parameter is supplied.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=video`, and `location=37.3565037,-5.9817521`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation requires `location` and `locationRadius` together.
  - Intentionally violated constraints:
    Omitted required `locationRadius`.
- Branch 2:
  - Unsatisfied condition:
    Location radius is invalid or too large.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=video`, `location=37.3565037,-5.9817521`, and `locationRadius=2000km`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Converted radius exceeds the implementation limit of 1000 km.
  - Intentionally violated constraints:
    Radius exceeds allowed distance.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches geotagged videos by distance from a coordinate.

### Behavior 7: search content-owner videos

Behavior name:
search content-owner videos

Successful execution:
- Result:
  This behavior returns videos owned by the content owner named by `onBehalfOfContentOwner`.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `forContentOwner=true`, `onBehalfOfContentOwner={contentOwnerId}`, and `type=video`.
- Constraints:
  `{contentOwnerId}` must match a seeded channel content owner, such as `contentOwner1` or `contentOwnerGoogle`. The request may also use supported filters such as `channelId`, `eventType`, `location`, `publishedAfter`, `publishedBefore`, `q`, `regionCode`, `relevanceLanguage`, `safeSearch`, `topicId`, `videoCaption`, and `videoCategoryId`. It must not combine with `forDeveloper`, `forMine`, or `relatedToVideoId`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `forContentOwner=true` is supplied without `onBehalfOfContentOwner`.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `forContentOwner=true`, and `type=video`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation treats content-owner search without an owner value as an invalid search filter.
  - Intentionally violated constraints:
    Omitted required `onBehalfOfContentOwner`.
- Branch 2:
  - Unsatisfied condition:
    Content owner does not exist in seeded data.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `forContentOwner=true`, `onBehalfOfContentOwner=missingOwner`, and `type=video`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation returns HTTP 404 for unknown content owners.
  - Intentionally violated constraints:
    `{contentOwnerId}` does not match any seeded channel content owner.
- Branch 3:
  - Unsatisfied condition:
    Content-owner search is combined with a forbidden video filter.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `forContentOwner=true`, `onBehalfOfContentOwner=contentOwner1`, `type=video`, and `videoDuration=short`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The implementation forbids `videoDefinition`, `videoDimension`, `videoDuration`, `videoLicense`, `videoEmbeddable`, `videoSyndicated`, and `videoType` with content-owner search.
  - Intentionally violated constraints:
    Used `videoDuration` with `forContentOwner=true`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches videos through the content-owner ownership filter.

### Behavior 8: search authenticated user's videos

Behavior name:
search authenticated user's videos

Successful execution:
- Result:
  This behavior returns videos owned by the implementation’s hard-coded authenticated user channel.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `forMine=true`, and `type=video`.
- Constraints:
  The source treats channel `UCxwvutsrqponmlkjihgfedc` as the authenticated user’s channel. `forMine=true` must not combine with `forContentOwner`, `forDeveloper`, or `relatedToVideoId`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `forMine=true` is used without `type=video`.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `forMine=true`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation requires `type=video` for `forMine`.
  - Intentionally violated constraints:
    Omitted required `type=video`.
- Branch 2:
  - Unsatisfied condition:
    `forMine=true` is combined with a mutually exclusive ownership filter.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `forMine=true`, `forDeveloper=true`, and `type=video`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation allows at most one of `forContentOwner`, `forDeveloper`, `forMine`, and `relatedToVideoId`.
  - Intentionally violated constraints:
    Combined mutually exclusive search modes.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches videos owned by the current user identity represented in the mock data.

### Behavior 9: search developer-uploaded videos

Behavior name:
search developer-uploaded videos

Successful execution:
- Result:
  This behavior returns videos marked in the seeded data as uploaded through the developer’s application or website.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet` and `forDeveloper=true`.
- Constraints:
  `forDeveloper=true` is mutually exclusive with `forContentOwner`, `forMine`, and `relatedToVideoId`. With no `type`, only developer-marked videos are returned because channel and playlist repositories suppress results when `forDeveloper=true`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Developer search is combined with another exclusive search mode.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `forDeveloper=true`, `forMine=true`, and `type=video`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Dependency validation rejects simultaneous exclusive modes.
  - Intentionally violated constraints:
    Combined `forDeveloper` and `forMine`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches the subset of videos flagged as developer-uploaded.

### Behavior 10: search videos related to an existing video

Behavior name:
search related videos

Successful execution:
- Result:
  This behavior returns videos related to the video identified by `relatedToVideoId`, based on shared topic IDs or the same category.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, `type=video`, and `relatedToVideoId={videoId}`.
- Constraints:
  `{videoId}` must match a seeded video ID, such as `YVm37gs92ME`. No documented endpoint can create a video, so the ID must come from seeded data or a prior search result. Implementation requires `type=video` and rejects combination with `forContentOwner`, `forDeveloper`, or `forMine`. Swagger says many other parameters are forbidden with `relatedToVideoId`, but the source does not enforce that; the related-video repository only applies `regionCode`, `relevanceLanguage`, and `safeSearch`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Related video ID does not exist.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=video`, and `relatedToVideoId=missingVideo`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Repository validation requires the related video to exist in seeded video data.
  - Intentionally violated constraints:
    `{videoId}` does not match any seeded video.
- Branch 2:
  - Unsatisfied condition:
    `relatedToVideoId` is used without `type=video`.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `relatedToVideoId=YVm37gs92ME`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Implementation dependency validation requires `type=video`.
  - Intentionally violated constraints:
    Omitted required `type=video`.
- Branch 3:
  - Unsatisfied condition:
    Related-video search is combined with another exclusive mode.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet`, `type=video`, `relatedToVideoId=YVm37gs92ME`, and `forMine=true`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    `relatedToVideoId` is mutually exclusive with `forContentOwner`, `forDeveloper`, and `forMine`.
  - Intentionally violated constraints:
    Combined `relatedToVideoId` with `forMine`.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Searches for videos related to a specific existing video.

### Behavior 11: page through search results

Behavior name:
page through search results

Successful execution:
- Result:
  This behavior retrieves a later or previous page of the same search result set.
- Endpoint sequence:
  Step 1: `GET /search` with query `part=snippet`, chosen search filters, and `maxResults={pageSize}`; read `nextPageToken` or `prevPageToken` from the response.
  Step 2: `GET /search` with the same query filters, the same `maxResults={pageSize}`, and `pageToken={tokenFromStep1}`.
- Constraints:
  `{tokenFromStep1}` must be the exact `nextPageToken` or `prevPageToken` produced by Step 1. Token format is `page-{number}`. The same filters and page size must be reused to page through the same logical result set.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `pageToken` is malformed.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `pageToken=abc`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    Pagination parsing expects a token like `page-2`.
  - Intentionally violated constraints:
    `pageToken` is not in implementation format.
- Branch 2:
  - Unsatisfied condition:
    `pageToken` refers to a page beyond the available result set.
  - Endpoint group:
    Step 1: `GET /search` with query `part=snippet` and `maxResults=5`; this is the call that should produce a valid `nextPageToken`.
    Step 2: `GET /search` with query `part=snippet`, `maxResults=5`, and `pageToken=page-999`.
  - Failure endpoint:
    `GET /search`
  - Why this fails:
    The second call asks for a page number greater than the number of pages computed for the result set.
  - Intentionally violated constraints:
    Did not reuse a token produced by Step 1.

Endpoint coverage:
- Covers:
  `GET /search`
- Distinct meaning:
  Uses response pagination tokens to navigate a search result set.

### Unclear or auxiliary endpoints

None. The only active documented and implemented endpoint is `GET /search`; all active behavior maps to that endpoint.