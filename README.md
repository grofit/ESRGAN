# Fork of [BlueAmulet's fork](https://github.com/BlueAmulet/ESRGAN) of [ESRGAN by Xinntao](https://github.com/xinntao/ESRGAN)

This fork ports features over from my ESRGAN-Bot repository and adds a few more. It natively allows:

* In-memory splitting/merging functionality (fully seamless, recently revamped again)
* Seamless texture preservation (both tiled and mirrored)
* Model chaining
* Transparency preservation (3 different modes)
* 1-bit transparency support (with half transparency as well)
* Both new-arch and old-arch models
* SPSR models
* On-the-fly interpolation

To change the tile size for the split/merge functionality, use the `--tile_size` argument. This was recently changed to scale with the scale of the model used to more consistently not run out of VRAM, so you may need to play around with it to find your new maximum value.

To set your textures to seamless, use the `--seamless` flag. For mirrored seamless, use the `--mirror` flag. You can also add pixel-replication padding using `--replicate` and alpha padding using `--alpha_padding`. You cannot use more than one of these at once.

To chain models, simply put one model name after another with a `>` in between (you can also use `+` if using bash to avoid issues), such as `1xDeJpeg.pth>4xESRGAN.pth` **note: To use model chaining, model names must be the complete full name without the path included, and the models must be present in your `/models` folder. You can still use full model paths to upscale with a single model.**

For on-the-fly interpolation, you use this syntax: `<model1_name>:<##>&<model2_name>:<##>`, where the model name is the path to the model and ## is the numerical percentage to interpolate by. For example, `model1:50&model2:50` would interpolate model1 and model2 by 50 each. The numbers should add up to 100. If you have trouble using `:` or `&`, either try putting the interpolation string in quotes or use `@` or `|` respectively (`"model1@50|model2@50"`).

To use 1 bit binary alpha transparency, set the `--binary_alpha` flag to True. When using `--binary_alpha` transparency, provide the optional `--alpha_threshold` to specify the alpha transparency threshold. 1 bit binary transparency is useful when upscaling images that require that the end result has 1 bit transparency, e.g. PSX games. If you want to include half transparency, use `--ternary_alpha` instead, which allows you to set the `--alpha_boundary_offset` threshold.

The default alpha mode is now 0 (ignore alpha). There are also now 3 other modes to choose from:

* `--alpha_mode 1`: Fills the alpha channel with both white and black and extracts the difference from each result.
* `--alpha_mode 2`: Upscales the alpha channel by itself, as a fake 3 channel image (The IEU way) then combines with result.
* `--alpha_mode 3`: Shifts the channels so that it upscales the alpha channel along with other regular channels then combines with result.

To process images in reverse order, use `--reverse`. If needed, you can also skip existing files by using `--skip_existing`.

Examples:

* `python upscale.py 4xBox.pth --seamless`
* `python upscale.py 1xSSAntiAlias9x.pth>4xBox.pth --tile_size 512`
* `python upscale.py 4xBox.pth --binary_alpha --alpha_threshold .2`
* `python upscale.py /models/4xBox.pth`

If you want a GUI for ESRGAN and if you're on Windows, check out [Cupscale](https://github.com/n00mkrad/cupscale/). It implements most of this fork's features as well as other utilities around it.
