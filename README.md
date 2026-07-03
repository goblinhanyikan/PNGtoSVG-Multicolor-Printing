For those that don't want to download the tool, you can also access it here:
https://goblinhanyikan.github.io/PNGtoSVG-Multicolor-Printing/

# PNGtoSVG-Multicolor-Printing
Experimental tool that converts png files to svg, then creates seperate stl files for each colors, allowing the user to print PNG files with multicolor capabilities. The app is open source and under the MIT license.

DISCLAIMER: This app is made with the assistance of AI. If you are avoiding AI for ethical reasons, I totally understand.

A tiny, dependency-free PNG → SVG converter that runs entirely in the browser.
No upload, no backend, no build step — open `index.html` and it works.

**Live tool:** enable GitHub Pages on this repo (see below) and it's hosted for free on GitHub's own servers.

## How it works

1. **Quantize** — the image is downscaled for performance, then its colors are
   reduced to a small palette using k-means++ clustering.
2. **Mask** — for each palette color, a binary mask of "this pixel is this
   color" is built.
3. **Trace** — each mask's boundary (including holes) is traced by walking
   pixel-grid edges into closed loops. Collinear points are merged away, so
   straight edges collapse to a single line segment instead of one per pixel.
4. **Compose** — each color becomes one `<path>` with `fill-rule="evenodd"`,
   which makes holes render correctly with no extra bookkeeping.

The output is intentionally rectilinear/pixel-accurate rather than curve-fit
— it suits icons, logos, pixel art, and flat illustrations best. Photos will
convert but look posterized/blocky, which is a property of this approach, not
a bug.

## Using it

- **Mode** — `Full color` runs k-means palette extraction as above.
  `Monochrome` instead thresholds the image into a single ink color, useful
  for line art, stencils, or laser/vinyl-cutter files.
- **Palette size** *(full color only)* — how many colors to keep. Fewer
  colors = simpler, smaller SVG; more colors = closer to the original.
- **Threshold / Invert / Ink color** *(monochrome only)* — threshold sets the
  luminance cutoff between ink and blank; invert flips which side is filled;
  ink color sets the fill.
- **Max working size** — the image is downscaled to this before tracing.
  Larger values preserve more detail but are slower and produce heavier SVGs.
- **Alpha cutoff** — pixels with alpha below this are treated as transparent
  and left untraced.
- **Corner smoothing** — at `0` every corner stays a sharp pixel-accurate
  right angle. Raising it rounds each corner by cutting it back and bridging
  the cut with a curve, which softens the "staircase" look of traced pixel
  edges. Large values on small shapes are automatically clipped so opposite
  corners never overlap.

  Smoothing only ever rounds corners on the **outer silhouette** — the edge
  facing background/transparency (or a hole's edge, which also faces open
  space). Any corner that sits on a **seam between two colors** is left
  exactly where it is. Rounding both sides of a shared seam independently
  would pull each color back from the same point and open a gap between
  them — harmless in a browser preview, but a real defect if you're cutting
  or printing the result (a seam between two colors/materials needs to stay
  a closed line, not a gap). This holds even at three- and four-way
  junctions where multiple colors meet at a single point.

Everything runs client-side; your image never leaves your machine.

## Language

The language toggle (top right) switches the interface between English and
Turkish. Your choice is remembered in the browser for next time.

## 3D print friendly mode

Check "3D print friendly" and the tool switches into a workflow for
multi-color/multi-material printing:

- The palette is locked to a fixed color count you choose with a **2–8
  slider** instead of the continuous palette-size slider (fewer, larger
  regions trace and print more reliably than a large open palette).
- After tracing, a **Print layers** section appears below with one card per
  color: a small preview, its hex value, and its own **Download** button.
- Each of those files is a *complete, standalone* SVG with the exact same
  `viewBox`/width/height as the full combined image — only that one color's
  shape is filled in, everything else is empty. That means if you import
  all of them into the same slicer project without moving or rescaling
  them, they land in register and reassemble into the original picture,
  ready to be extruded as separate parts/colors. The colored SVG files
  can also be exported as STL, 3MF, or be bundled in an uncompressed ZIP file.
  Current version works most reliably when the user downloads the stls for each color seperately. 
- There's no bundled "download all" — grab the ones you need individually,
  since not every project uses every color.
- Seams between colors are never rounded (see above), so adjacent layers
  still meet exactly with no gap between them even with smoothing on.

## Hosting on GitHub Pages

1. Push this folder to a new GitHub repository.
2. In the repo, go to **Settings → Pages**.
3. Under "Build and deployment", set **Source** to `Deploy from a branch`,
   branch `main`, folder `/ (root)`.
4. Save. GitHub will publish it at `https://<your-username>.github.io/<repo-name>/`.

## Attributions

This project includes earcut v3.2.3, © Mapbox, ISC License.

## License

MIT — see [LICENSE](./LICENSE).
