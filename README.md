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
  STAC Item (Gamma Ray Spectrometer data from Mars Odyssey)
- [Collection example](examples/collection.json): Shows the basic usage of the
  extension in a STAC Collection (THEMIS Visible Apparent Brightness Data)

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
| `ssys:targets` | array | **REQUIRED**. List of solar system targets. At least one required. Supported: Mercury, Venus, Mars, Moon, Titan |
| `ssys:target_class` | string | **REQUIRED**. Classification of the primary target. Supported: planet, satellite |
| `version` | string | **REQUIRED**. Version identifier for the data product |
| `product:type` | string | **REQUIRED**. Type of the data product. Supported: Cube, Spectrum, Spectral Cube, Image, Map, Volume, Time Series, Dynamic Spectrum, Catalog, Spatial Vector |
| `processing:level` | string | **REQUIRED**. Processing level. Supported: Ancillary, Raw, Calibrated, Derived |

#### For Collections

| Field Name | Type | Description |
| ----------- | ---- | ----------- |
| `ssys:targets` | array | **REQUIRED**. List of solar system targets. At least one required. Supported: Mercury, Venus, Mars, Moon, Titan |
| `ssys:target_class` | string | **REQUIRED**. Classification of the primary target. Supported: planet, satellite |
| `product:type` | string | **REQUIRED**. Type of the data product. Supported: Cube, Spectrum, Spectral Cube, Image, Map, Volume, Time Series, Dynamic Spectrum, Catalog, Spatial Vector |
| `processing:level` | string | **REQUIRED**. Processing level. Supported: Ancillary, Raw, Calibrated, Derived |

### Optional PDSSP-Specific Fields

The following fields are specific to the PDSSP extension:

| Field Name | Type | Description |
| ----------- | ---- | ----------- |
| `pdssp:solar_longitude` | number | Solar longitude of the target (in degrees) |
| `pdssp:solar_distance` | number | Distance from the Sun to the target (in AU) |
| `pdssp:map_resolution` | number | Spatial resolution of the map (in meters per pixel) |
| `pdssp:map_scale` | number | Scale of the map (dimensionless) |

### Required STAC Extensions

Both Items and Collections **must** include the following in `stac_extensions`:

- `https://stac-extensions.github.io/ssys/v1.1.1/schema.json`
- `https://stac-extensions.github.io/processing/v1.2.0/schema.json`
- `https://stac-extensions.github.io/product/v1.0.0/schema.json`
- `https://pdssp.github.io/stacplanet/v1.0.0/schema.json`

Additionally:

- **Collections** must include:
  `https://stac-extensions.github.io/scientific/v1.0.0/schema.json`
- **Items with null geometry** must include:
  `https://stac-extensions.github.io/projection/v1.0.0/schema.json` and
  `https://stac-extensions.github.io/version/v1.2.0/schema.json`
- **Items** may include:
  `https://stac-extensions.github.io/file/v2.1.0/schema.json`

## Implementation Notes

### Geometry Handling

For planetary data products:

- **Items with spatial footprint**: Include a valid GeoJSON geometry
- **Items without spatial footprint** (e.g., spectra, time series): Use `geometry: null`
  and include the `projection` and `version` extensions

### Target Enumeration

The `ssys:targets` field supports:

- Mercury
- Venus
- Mars
- Moon (Earth's moon)
- Titan (Saturn's moon)

### Product Types

The `product:type` field categorizes data products:

- **Cube**: 3D data cube
- **Spectrum**: Spectral data
- **Spectral Cube**: 3D spectral data cube
- **Image**: 2D image data
- **Map**: Cartographic map product
- **Volume**: 3D volume data
- **Time Series**: Temporal data series
- **Dynamic Spectrum**: Time-frequency spectrum
- **Catalog**: Data catalog or index
- **Spatial Vector**: Vector spatial data

### Processing Levels

The `processing:level` field indicates the processing stage:

- **Ancillary**: Ancillary or supplementary data
- **Raw**: Raw, uncalibrated data
- **Calibrated**: Calibrated data
- **Derived**: Derived or processed data products

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
