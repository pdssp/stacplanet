# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `pdssp:method` optional field (Item and Collection): how a product was
  acquired or produced, from the
  [PDSSP Thesaurus](https://pdssp.github.io/pdssp-ontology/thesaurus/)'s
  Method concept scheme -- reuses the USGS Thesaurus's own "methods"
  facet where it fits, adds Photogrammetry/Stereoscopic imaging where it
  doesn't.
- `proj:wkt2` now actually required (Item and Collection) when the
  `projection` extension is declared -- previously the profile required
  declaring `projection` (for a non-null-geometry Item) but never
  required `proj:wkt2` itself to be present.

### Changed (breaking)

- `product:type` enum replaced entirely: was the EPN-TAP `dataproduct_type`
  long-name vocabulary (Cube, Spectrum, Spectral Cube, Image, Map, Volume,
  Time Series, Dynamic Spectrum, Catalog, Spatial Vector), now the PDSSP
  Thesaurus's own Product Type concept scheme (Image, Image mosaic,
  Orthoimage, Anaglyph, Digital elevation model, Point cloud) -- too
  coarse for a planetary imaging archive's actual needs; mapping to/from
  EPN-TAP (or any other vocabulary) is left to whichever proxy needs it.
- `ssys:targets` relaxed from a 5-value enum (Mercury, Venus, Mars, Moon,
  Titan) to free text, matching the real `ssys` v1.1.1 extension's own
  unconstrained field -- the old enum rejected real PDSSP data (Deimos,
  Phobos, Jupiter, comets, spacecraft cross-calibration targets, ...).
- `ssys:target_class` enum replaced: was `planet`/`satellite` only, now
  the real `ssys` v1.1.1 extension's own full enum (asteroid,
  dwarf_planet, planet, satellite, comet, exoplanet,
  interplanetary_medium, sample, sky, spacecraft, spacejunk, star,
  calibration) -- confirmed live against that extension's own schema.
- Collection-level `ssys:targets`/`ssys:target_class` moved to the
  Collection's own top level. The previous schema required them (and
  `product:type`/`processing:level`) inside a non-standard top-level
  `properties` object -- a STAC Collection has no such object at all
  (only Items do). `product:type`/`processing:level`/`pdssp:method`
  instead move to `summaries` (the real STAC-correct place for a
  Collection's own per-item value distribution), as arrays.

### Removed

- `pdssp:solar_longitude`/`pdssp:solar_distance`/`pdssp:map_resolution`/
  `pdssp:map_scale` optional fields, and the conditional rule requiring
  `pdssp:map_resolution` when `product:type` was `"Map"` (no longer
  meaningful now that `product:type` no longer has a `"Map"` value at
  all). Solar-geometry fields belong to the `ssys` extension and will be
  added there instead of duplicated here; the map-specific fields had no
  real consumer yet.
- `version` STAC extension requirement for a null-geometry Item -- the
  plain `version` *property* (STAC Common Metadata core, not an
  extension) was already required unconditionally; no extension URL was
  ever needed to use it.

### Fixed

- Collection-level example (`examples/collection.json`) is no longer
  Gamma Ray Spectrometer / THEMIS data unrelated to any current PDSSP
  imaging collection -- replaced with a real HiRISE DTM
  collection/item pair, exercising every field this profile actually
  requires (including the new `proj:wkt2` requirement).

## [v1.0.0] - 2026-06-23

### Added

- Initial version of the STACPlanet extension for PDSSP planetary data
- JSON Schema defining PDSSP-specific fields and constraints
- Example STAC Item (Gamma Ray Spectrometer data from Mars Odyssey)
- Example STAC Collection (THEMIS Visible Apparent Brightness Data)
- Complete documentation in README.md
- GitHub Actions workflow for schema deployment

### Changed

- Updated package name from stac-extension-template to stacplanet
- Configured schema mapping for <https://pdssp.github.io/stacplanet/v1.0.0/schema.json>

### Fixed

- Corrected stac_extensions validation structure in schema (changed contains/allOf to allOf/contains)
- Added required properties field to Collection examples
- Fixed JSON formatting in collection.json

[Unreleased]: <https://github.com/pdssp/stacplanet/compare/v1.0.0...HEAD>
[v1.0.0]: <https://github.com/pdssp/stacplanet/tree/v1.0.0>
