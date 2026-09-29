# STACPlanet Extension Specification

- **Title:** PDSSP Planetary Data Schema for STACPlanet
- **Identifier:** <https://pdssp.github.io/stacplanet/v1.0.0/schema.json>
- **Field Name Prefix:** pdssp
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner:** @pdssp

This document describes the **STACPlanet** extension to the
[SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

The STACPlanet extension defines PDSSP-specific (Planetary Data System Small Bodies Node)
keywords, constraints, and extensions for planetary imagery and data products.
It applies to both STAC Items and Collections, with mandatory fields for solar system
targets, product types, and processing levels.

## Background

The Planetary Data System (PDS) archives and distributes scientific data from NASA
planetary missions, astronomy observations, and laboratory measurements.
The PDSSP extension enables the representation of PDS data products within the STAC
framework, facilitating interoperability between planetary science data and
modern geospatial cataloging systems.

This extension builds upon the following STAC extensions:

- [Solar System (ssys)](https://stac-extensions.github.io/ssys/v1.1.1/schema.json) -
  For solar system target information
- [Processing](https://stac-extensions.github.io/processing/v1.2.0/schema.json) -
  For processing level metadata
- [Product](https://stac-extensions.github.io/product/v1.0.0/schema.json) -
  For data product type classification
- [Scientific](https://stac-extensions.github.io/scientific/v1.0.0/schema.json) -
  For scientific metadata (Collections)
- [File](https://stac-extensions.github.io/file/v2.1.0/schema.json) -
  For file metadata (Items)
- [Version](https://stac-extensions.github.io/version/v1.2.0/schema.json) -
  For version information (Items)

## Examples

- [Item example](examples/item.json): Shows the basic usage of the extension in a
  STAC Item (a HiRISE Digital Terrain Model from MRO, produced by photogrammetry)
- [Collection example](examples/collection.json): Shows the basic usage of the
  extension in a STAC Collection (the HiRISE DTM collection)

## JSON Schema

- [STACPlanet Schema v1.0.0](json-schema/schema.json) - Main schema file

The schema is also available at:

- **Latest:** <https://pdssp.github.io/stacplanet/v1.0.0/schema.json>
- **Repository:** <https://github.com/pdssp/stacplanet>

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [ ] Assets
- [ ] Links

### Required Fields

#### For Items

| Field Name | Type | Description |
| ----------- | ---- | ----------- |
| `ssys:targets` | array | **REQUIRED**. List of solar system targets. At least one required. Free text (e.g. "Mars", "Deimos", "Mgs" for a spacecraft cross-calibration target) -- matches the real `ssys` v1.1.1 extension's own unconstrained field, not a short enum of common bodies |
| `ssys:target_class` | string | **REQUIRED**. Classification of the primary target -- the real `ssys` v1.1.1 enum: asteroid, dwarf_planet, planet, satellite, comet, exoplanet, interplanetary_medium, sample, sky, spacecraft, spacejunk, star, calibration |
| `version` | string | **REQUIRED**. Version identifier for the data product |
| `product:type` | string | **REQUIRED**. Type of the data product, from the [PDSSP Thesaurus](https://pdssp.github.io/pdssp-ontology/thesaurus/)'s Product Type concept scheme. Supported: Image, Image mosaic, Orthoimage, Anaglyph, Digital elevation model, Point cloud |
| `processing:level` | string | **REQUIRED**. Processing level. Supported: Ancillary, Raw, Calibrated, Derived |
| `proj:wkt2` | string | **REQUIRED when `stac_extensions` includes the `projection` extension** (in practice: whenever the Item has a non-null `geometry`, since that already requires `projection` -- see "Required STAC Extensions" below) |

#### For Collections

| Field Name | Type | Description |
| ----------- | ---- | ----------- |
| `ssys:targets` | array | **REQUIRED**, at the Collection's own top level (not nested in `properties` -- a STAC Collection has none -- nor in `summaries`: a collection's own target(s) are a fact about the collection itself, same status as its `license`). Same free-text field as the Item's own `ssys:targets` |
| `ssys:target_class` | string | **REQUIRED**, same top-level placement, same enum as the Item's own `ssys:target_class` |
| `summaries.product:type` | array | **REQUIRED**. Every `product:type` value this collection's items actually use, from the PDSSP Thesaurus's Product Type concept scheme |
| `summaries.processing:level` | array | **REQUIRED**. Every `processing:level` value this collection's items actually use |
| `proj:wkt2` | string | **REQUIRED when `stac_extensions` includes the `projection` extension** |

### Optional PDSSP-Specific Fields

The following fields are specific to the PDSSP extension. On a Collection
these are summarized as `summaries.pdssp:method` (an array), same
convention as `summaries.product:type`/`summaries.processing:level` above.

| Field Name | Type | Description |
| ----------- | ---- | ----------- |
| `pdssp:method` | string | How the product was acquired or produced (the technique), from the [PDSSP Thesaurus](https://pdssp.github.io/pdssp-ontology/thesaurus/)'s Method concept scheme. Supported: Panchromatic imaging, Multispectral imaging, Hyperspectral imaging, Visible light imaging, Infrared imaging, Thermal imaging, Radar imaging, Microwave imaging, Lidar, Aerial photography, Photogrammetry, Stereoscopic imaging, Remote sensing |

`pdssp:solar_longitude`/`pdssp:solar_distance`/`pdssp:map_resolution`/
`pdssp:map_scale` were removed (2026-09-29): solar-geometry fields belong
to the `ssys` extension and will be added there instead of duplicated
here; the two map-specific fields had no real consumer yet.

### Required STAC Extensions

Both Items and Collections **must** include the following in `stac_extensions`:

- `https://stac-extensions.github.io/ssys/v1.1.1/schema.json`
- `https://stac-extensions.github.io/processing/v1.2.0/schema.json`
- `https://stac-extensions.github.io/product/v1.0.0/schema.json`
- `https://pdssp.github.io/stacplanet/v1.0.0/schema.json`

Additionally:

- **Collections** must include:
  `https://stac-extensions.github.io/scientific/v1.0.0/schema.json`
- **Items with a non-null `geometry`** (every current PDSSP product type --
  Image, Image mosaic, Orthoimage, Anaglyph, Digital elevation model, Point
  cloud -- has one) **must** include:
  `https://stac-extensions.github.io/projection/v2.0.0/schema.json`, and
  must then also set `proj:wkt2`.
- **Collections that include the `projection` extension** must likewise
  set `proj:wkt2` at the Collection's own top level.
- **Items** may include:
  `https://stac-extensions.github.io/file/v2.1.0/schema.json`

## Implementation Notes

### Geometry Handling

For planetary data products:

- **Items with spatial footprint** (the normal case for every current
  PDSSP product type): include a valid GeoJSON geometry, the `projection`
  extension, and `proj:wkt2`
- **Items without spatial footprint** (e.g., spectra, time series -- not
  yet produced by any current PDSSP collection): use `geometry: null`,
  which relaxes the `projection`/`proj:wkt2` requirement

### Target Enumeration

`ssys:targets` is free text (e.g. "Mars", "Deimos", "Mgs" for a spacecraft
cross-calibration target, "Sky"/"Cal" for a non-body calibration frame),
matching the real `ssys` v1.1.1 extension's own unconstrained field --
not restricted to a short list of common bodies. `ssys:target_class` IS
constrained, to that same extension's own real enum: asteroid,
dwarf_planet, planet, satellite, comet, exoplanet,
interplanetary_medium, sample, sky, spacecraft, spacejunk, star,
calibration.

### Product Types

The `product:type` field categorizes data products, from the
[PDSSP Thesaurus](https://pdssp.github.io/pdssp-ontology/thesaurus/)'s
Product Type concept scheme -- deliberately NOT the EPN-TAP
`dataproduct_type` long-name vocabulary ("image"/"volume"/"map"/...):
that vocabulary is coarser than a planetary imaging archive needs (no
term distinguishes an orthoimage or an anaglyph from a plain image), and
mapping to/from EPN-TAP (or any other consumer's own vocabulary) is left
to whichever proxy needs it, not enforced here.

- **Image**: a single raster image, not yet mosaicked, ortho-rectified or elevation-derived
- **Image mosaic**: two or more images combined into one composite raster
- **Orthoimage**: an image geometrically corrected (ortho-rectified), typically by projecting it onto a digital elevation model
- **Anaglyph**: a red-cyan stereo composite built from two observations of the same site from different look angles
- **Digital elevation model**: a raster of elevation values, typically produced by stereo photogrammetry
- **Point cloud**: an unstructured set of 3D points, before being gridded into a digital elevation model

Currently scoped to what PDSSP's own imaging collections (CTX, HiRISE
EDR/RDR/DTM/anaglyph) actually produce; a non-imaging instrument
(spectrometer, in-situ sampler, ...) has no matching value yet.

### Processing Levels

The `processing:level` field indicates the processing stage:

- **Ancillary**: Ancillary or supplementary data
- **Raw**: Raw, uncalibrated data
- **Calibrated**: Calibrated data
- **Derived**: Derived or processed data products

### Methods

The `pdssp:method` field indicates how the product was acquired or
produced (as opposed to `product:type`'s "what" and `processing:level`'s
"how far processed") -- from the
[PDSSP Thesaurus](https://pdssp.github.io/pdssp-ontology/thesaurus/)'s
Method concept scheme:

- **Panchromatic imaging** / **Multispectral imaging** / **Hyperspectral imaging** /
  **Visible light imaging** / **Infrared imaging** / **Thermal imaging** /
  **Radar imaging** / **Microwave imaging** / **Lidar** / **Aerial photography**:
  reused from the [USGS Thesaurus](https://apps.usgs.gov/thesaurus/)'s own
  "methods" facet (`remote sensing` branch)
- **Photogrammetry**: deriving 3D geometry (e.g. a digital elevation
  model) by stereo-matching two or more overlapping images -- a PDSSP
  addition (USGS only carries "photogrammetry" as a synonym of "remote
  sensing" itself, not its own concept)
- **Stereoscopic imaging**: acquiring or combining two observations of
  the same site from different look angles for stereo (3D) viewing --
  e.g. a red-cyan anaglyph -- a PDSSP addition (not in USGS at all)
- **Remote sensing**: the generic fallback when no more specific method
  applies

Currently scoped to imaging/remote-sensing acquisition techniques; a
non-imaging instrument (spectrometer, in-situ sampler, ...) has no
matching value yet -- extending the enum for those is a follow-up, not
yet needed by any current PDSSP collection.

## Schema Deployment

The JSON schema is automatically deployed to GitHub Pages when a new release is published.

### Current Deployment

The v1.0.0 schema is available at:

```text
https://pdssp.github.io/stacplanet/v1.0.0/schema.json
```

### Creating a New Release

To deploy a new version of the schema:

1. Update the version in the schema's `$id` field
2. Update the CHANGELOG.md
3. Create a new Git tag and push it:

   ```bash
   git tag v1.1.0
   git push origin v1.1.0
   ```
4. Create a GitHub release for the tag

The GitHub Actions workflow `.github/workflows/publish.yaml` will automatically deploy
the `json-schema/` directory to the `gh-pages` branch under a directory named after
the release tag.

## Relation types

| Type | Description |
| ---- | ----------- |
| `about` | Links to external documentation or resources |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md).

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run
locally to verify that changes are valid. To run tests locally, you'll need `npm`,
which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once.
Just navigate to the root of this repository and run:

```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema:

```bash
npm test
```

This will check:

- Markdown formatting (via remark-lint)
- Example files validity against the schema

If the tests reveal formatting problems with the examples, you can fix them with:

```bash
npm run format-examples
```

## License

This work is licensed under the [CC0-1.0](LICENSE) license.

## Contact

For questions or support regarding the STACPlanet extension:

- GitHub Issues: <https://github.com/pdssp/stacplanet/issues>
- PDS Small Bodies Node: <https://pds-smallbodies.astro.umd.edu/>
