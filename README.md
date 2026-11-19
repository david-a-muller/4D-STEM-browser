# 4D-STEM Browser

A lightweight viewer for exploring 4D-STEM data sets. It's mainly intended for my Nanocharacterization class: students can open and work with data sets in a web browser without installing any code. Everything runs in a single `index.html` file, and data is processed locally in the browser. Nothing is uploaded.

**Live version:** open the GitHub Pages link for this repository, or download `index.html` and open it in any modern browser.

## What it does

The window has two panels:

- **Diffraction pattern** (left) shows the pattern at the selected probe position, or the mean or max pattern over the scan. A detector drawn on it can be a **point, circle, annulus or rectangle**. Drag the detector to move it and drag its edge to resize it.
- **Virtual image** (right) is the image formed by that detector. Click or drag in it to choose the probe position, or draw a rectangle to average patterns over a region. The arrow keys step the probe.

### Imaging modes

- **Integrated intensity**: virtual bright-field, annular dark-field and dark-field images from any detector shape.
- **Centre of mass**: CoM x, CoM y and |CoM|.
- **iDPC**: the integrated CoM field, giving the projected phase. It includes a curl-minimising fit for the detector–scan rotation and flip.
- **Tilt-corrected bright field (tcBF)**: each bright-field pixel's image is shifted back by Δf·θ and the shifted images are summed, with optional upsampling. A cross-correlation fit estimates the defocus and the detector rotation.
- **Aberration-corrected bright field (ACBF)**: tcBF plus correction of each pixel's residual contrast-transfer phase (defocus and Cs), combined with a regularised (Wiener) inverse. The result is a phase image whose transfer has no zero crossings out to 2α.

The tcBF and ACBF modes are based on:

> D. Ma, G. Li, D. A. Muller, S. E. Zeltmann (2026). *Information in 4D-STEM: Where it is, and How to Use it.* Ultramicroscopy **283**, 114351. https://doi.org/10.1016/j.ultramic.2026.114351

ACBF here uses the weak-phase-object approximation and corrects only defocus and spherical aberration.

### Display and export

- Linear, square-root and log scaling, percentile contrast sliders, several colour maps, and a choice of sharp pixels or smooth interpolation.
- A **power spectrum** inset for the virtual image. Hovering over it reads out the spatial frequency and d-spacing.
- **Save** as PNG, as PDF with a caption recording the detector and imaging settings, or as CSV holding the raw values.
- **Copy detector as NumPy code** reproduces the current virtual image in Python.

## Supported file formats

| Format | Notes |
|---|---|
| HDF5 / EMD / py4DSTEM (`.h5`, `.hdf5`, `.emd`) | Lists the 3D and 4D datasets so you can choose one. 4D data is read as (scan rows, scan cols, k rows, k cols). Needs an internet connection the first time, because the HDF5 reader (h5wasm) loads from a CDN. |
| TIFF stacks (`.tif`, `.tiff`) | One diffraction pattern per page. Uncompressed, LZW or deflate; ImageJ stacks work. You enter the scan shape. |
| EMPAD `.raw` | 128×130 float32 frames, with the two metadata rows dropped. The scan size is read from file names like `scan_x256_y256.raw`. Other raw binary layouts can be set up by hand. |
| NumPy `.npy` | 3D or 4D, C-ordered arrays. |

Because data is held in browser memory, files up to roughly 1–2 GB are practical.

## Demo data sets

Simulated data sets can be loaded from the **Load demo data** menu:

- **WS₂ monolayer**, 80 kV: at 21.4 mrad (ptychography conditions), at 11 mrad (a lattice of partially overlapping disks), and at 21.4 mrad with 20 nm underfocus (for tcBF and ACBF). W and S vacancies are included.
- **Single W atom**, noise-free, with the probe 200 Å underfocus. Try tcBF, ACBF and the Δf fit.
- **Two single W atoms**, noise-free: one in the focal plane and one 20 nm below it. This shows depth sectioning with tcBF and ACBF.
- **Polycrystal**: two grains rotated 30° in plane, a vacuum hole, and an amorphous inclusion with an electrostatic field around it.

The WS₂ and two-atom data are simulated from a probe passing through a phase object, with Fresnel propagation between slices for the two-atom case. They are teaching examples, not quantitative simulations.

## Conventions

- Detector and image coordinates are x = column and y = row.
- Defocus follows the Kirkland sign convention: Δf > 0 is underfocus, with χ = πλk²(½Csλ²k² − Δf).
- The detector rotation is the angle from detector axes to scan axes. The optional flip transposes the detector axes before rotating.

## Authors

Written by David A. Muller together with Claude (Anthropic).

## License

MIT License: see [LICENSE](LICENSE). If you use the tcBF or ACBF modes in published work, please cite the Ultramicroscopy paper above.
