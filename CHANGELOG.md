# Changelog

All notable changes to avocado-bsp-imx93-frdm are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0]

### Added
- Rubicon run profiles: power, boot mode (microSD, eMMC, serial download)
  and reset, on the Rubicon's generic outputs.
- `avocado provision --rubicon --profile sd`: the hub powers the board
  off, takes its card through an SDWire3 (`sd_mux`), writes the image with
  stone's `sd` profile, hands the card back and boots from it.
- `avocado provision --rubicon --profile uuu-emmc`: the hub power-cycles the
  board into USB serial download (`1fc9:014e` on P2), uuu writes the eMMC,
  then the board boots from it. Needs only the power relay and `ssr1`/`ssr2`
  on SW1, no SDWire3.

## [0.1.0]

### Added
- Initial release: Board support for the i.MX 93 FRDM.
- CI via the shared `avocado-linux/actions` reusable workflows: PR build check
  (`test.yml`) and tag-driven package + publish to the Avocado feed (`release.yml`).
