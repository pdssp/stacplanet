# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

[v1.0.0]: <https://github.com/pdssp/stacplanet/tree/v1.0.0>
