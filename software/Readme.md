Building
--------

You need to have the Pico SDK installed, as well as a recent CMake and arm-none-eabi-gcc.


```bash
mkdir build
cd build
PICO_SDK_PATH=path/to/sdk cmake -DPICO_COPY_TO_RAM=1 ..
make -j$(nproc)
```

This will build all the example apps. You can then do e.g.

```bash
cp apps/sprite_bounce/sprite_bounce.uf2 /media/${USER}/RPI-RP2/
```

To flash a DVI board plugged into your system. Note the `PICO_COPY_TO_RAM=1` is important -- some of the apps will not run if this is not passed, because they use the SSI in fast DMA streaming mode. Others might underperform if they have large image assets (larger than the XIP cache) in flash.

Support for Different Boards
----------------------------

The DVI code is configured with which pins and which PIO state machines to use for signal generation, defined by a `dvi_serialiser_cfg` struct which looks like this:

```
static const struct dvi_serialiser_cfg picodvi_dvi_cfg = {
	.pio = pio0,
	.sm_tmds = {0, 1, 2},
	.pins_tmds = {10, 12, 14},
	.pins_clk = 8,
	.invert_diffpairs = true
};
```

Serial configurations for a handful of boards are listed in [common_dvi_pin_configs.h](include/common_dvi_pin_configs.h). Feel free to raise a PR to add your board to this file. All of the example apps use the `DVI_DEFAULT_SERIAL_CONFIG` from this header to select their pin configuration, which has a default value of `pico_sock_cfg`. You can pass `-DDVI_DEFAULT_SERIAL_CONFIG=some_other_board` to `cmake` to override this default, so that the example apps will be built with a different serial configuration.

As well as the DVI serial configuration, you may also want to set the `PICO_BOARD` flag for your target board to pick up things like UART and LED pin settings.

