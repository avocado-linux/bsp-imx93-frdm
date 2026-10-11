# bsp-imx93-frdm

Board support for the i.MX 93 FRDM

## Using this extension

`bsp-imx93-frdm` is an [Avocado](https://avocadolinux.org) extension — a reusable fragment of
build- and runtime-configuration that you compose into your own Avocado project. To use it,
declare it as a package-sourced extension in your `avocado.yaml` and add it to a runtime:

```yaml
extensions:
  avocado-bsp-imx93-frdm:
    source:
      type: package
      version: "*"        # or pin an exact version

runtimes:
  my-runtime:
    extensions:
      - avocado-bsp-imx93-frdm
```

Then install and build:

```sh
avocado install   # fetches + installs the SDK, extensions and runtime deps from your config
avocado build     # builds the SDK compile steps, extensions and runtime images
```

`avocado install` pulls the extension from your target's package feed and merges its
config into your project; `avocado build` then produces the runtime.

## Rubicon

The BSP ships Rubicon run profiles (`rubicon.run` in `avocado.yaml`), so
`avocado provision --rubicon` and `avocado rubicon run` work with no Rubicon
config in the project. They expect this wiring of the Rubicon's generic
outputs:

| Rubicon | Function | FRDM-IMX93 |
| --- | --- | --- |
| `power` (D2) | power relay | on the AC side of the board's USB-C PD adapter (switching VBUS alone leaves a stale PD contract) |
| `ssr1` (D3) | photo relay | across SW1-1, BOOT_MODE0 |
| `ssr2` (D7) | photo relay | across SW1-2, BOOT_MODE1 |
| `ssr3` (D8) | photo relay | across P19, SYS_nRST |
| USB port | SDWire3 | its tongue in the microSD slot (P13) |

Leave SW1 all off: the relays set the boot mode (SW1[3:0] 0011 microSD,
0010 eMMC, 0001 USB serial download).

### Writing the image to the microSD card

```sh
avocado provision --rubicon <board> --profile sd
```

The hub powers the board off, takes the card through the SDWire3, and
stone's `sd` profile writes the runtime image to it (`fwup`, the device
passed as `AVOCADO_PROVISION_DEVICE`). It then hands the card back to the
board, sets microSD boot and powers it on.

The board's consoles are on its USB-C debug port (a CH342 USB serial
bridge), which the hub does not read yet, so this profile has no boot
check.

Wired differently? Define a run profile of the same name in the project:
it replaces the BSP's whole.
