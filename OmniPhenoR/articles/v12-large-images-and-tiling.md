# OmniPhenoR Large Images, Overlap-Aware Tiling, and Tiled Prediction

## 1. Purpose

Large close-range images, scanner images, greenhouse mosaics, and UAV
orthomosaics may be too large for convenient whole-image neural
inference. Tiling divides the image into manageable regions, optionally
overlaps neighboring tiles, predicts each tile, and reconstructs the
full output.

Tiling is not only a computational trick. Tile boundaries, overlap,
padding, spatial leakage, and merging rules can change a phenotype.
Version 0.2.0 therefore exposes those decisions explicitly.

## 2. Tile an RGB image

``` r

img <- pheno_data("leaf_rgb")
tt <- pheno_tile(
  img,
  tile_size=c(48,64),
  overlap=8,
  pad=TRUE
)
tt
#> <pheno_tiles>
#>   source: 96 x 128 x 3 
#>   tiles: 9  size: 48 x 64  overlap: 8 x 8
head(tt$index)
#> # A tibble: 6 × 7
#>    tile row_start row_end col_start col_end valid_height valid_width
#>   <int>     <int>   <int>     <int>   <int>        <int>       <int>
#> 1     1         1      48         1      64           48          64
#> 2     2         1      48        57     120           48          64
#> 3     3         1      48        65     128           48          64
#> 4     4        41      88         1      64           48          64
#> 5     5        41      88        57     120           48          64
#> 6     6        41      88        65     128           48          64
```

The index records source coordinates and the valid height/width of every
tile.

## 3. Exact reconstruction when tiles are unchanged

``` r

reconstructed <- pheno_merge_tiles(tt)
max(abs(reconstructed - img))
#> [1] 0
```

Overlapping pixels are averaged. If every tile contains the unchanged
source values, reconstruction should be exact to numerical precision.

## 4. Logical masks

``` r

mask <- pheno_data("leaf_mask")
mt <- pheno_tile(mask, tile_size=32, overlap=8)
mask_back <- pheno_merge_tiles(mt, logical_output=TRUE)
mean(mask_back == mask)
#> [1] 1
```

This frozen behavior is also checked by `pheno_validate("tiling")`.

## 5. Edge padding

If an image dimension does not align with tile size, the edge tile can
be padded.

``` r

tp <- pheno_tile(
  pheno_data("leaf_gray"),
  tile_size=c(35,37),
  overlap=c(5,7),
  pad=TRUE,
  pad_value=0
)
tail(tp$index)
#> # A tibble: 6 × 7
#>    tile row_start row_end col_start col_end valid_height valid_width
#>   <int>     <int>   <int>     <int>   <int>        <int>       <int>
#> 1    15        61      95        92     128           35          37
#> 2    16        62      96         1      37           35          37
#> 3    17        62      96        31      67           35          37
#> 4    18        62      96        61      97           35          37
#> 5    19        62      96        91     127           35          37
#> 6    20        62      96        92     128           35          37
```

`valid_height` and `valid_width` ensure padding does not become part of
the reconstructed source extent.

## 6. Non-padded edge tiles

``` r

tn <- pheno_tile(
  pheno_data("leaf_gray"),
  tile_size=35,
  overlap=5,
  pad=FALSE
)
sapply(tn$tiles, function(z) paste(dim(z), collapse="x"))[1:4]
#> [1] "35x35" "35x35" "35x35" "35x35"
```

Variable tile dimensions can be inconvenient for neural networks that
require fixed input size. Padding is therefore common for model
inference, but the padding value and valid region must be tracked.

## 7. Tiled probability prediction

A function model allows the full tiled workflow to run during package
documentation.

``` r

mock <- function(z) {
  exg <- pheno_rgb_indices(z,"ExG")[,,1]
  plogis(20*(exg-median(exg)))
}

pred <- pheno_predict_tiles(
  img,
  model=mock,
  tile_size=c(48,64),
  overlap=8,
  classes=c("background","leaf"),
  threshold=.5,
  device="cpu"
)
pred
#> <pheno_prediction>
#>   engine: tiled  device: cpu 
#>   classes: background, leaf 
#>   mask dimensions: 96 x 128 
#>   runtime: 0.06 s
range(pred$probability)
#> [1] 1.28253e-12 1.00000e+00
```

The final object retains the merged probability array, mask,
uncertainty, total tile-inference runtime, tile size, and overlap in
provenance.

## 8. Why overlap helps

Convolutional models can be less reliable near a tile edge because the
receptive field has less surrounding context. Overlap lets a pixel be
predicted in multiple tile positions. Averaging probabilities can reduce
seam artifacts.

The correct overlap depends on model architecture, receptive field,
padding behavior, image resolution, and the size of biological
structures. A fixed overlap should be validated rather than copied from
another study without inspection.

## 9. Overlap also creates leakage risk during training

If overlapping tiles from the same large image are randomly split
between training and test sets, nearly identical pixels can appear in
both partitions. This can strongly inflate performance. Leakage in
scientific machine learning can produce overoptimistic conclusions
([Kapoor and Narayanan 2023](#ref-Kapoor2023_Leakage)).

The safe sequence is:

**split independent plots/images/fields first -\> then derive training
tiles inside each partition.**

Do not derive all tiles first and randomly split them unless tile-level
independence is genuinely the scientific target.

## 10. Tiling a field-like image

``` r

canopy <- pheno_data("canopy_rgb")
ct <- pheno_tile(canopy, tile_size=c(40,40), overlap=10)
ct
#> <pheno_tiles>
#>   source: 120 x 160 x 3 
#>   tiles: 20  size: 40 x 40  overlap: 10 x 10
```

For real orthomosaics, spatially neighboring tiles may share plants and
environmental gradients even without direct overlap. Plot, block, field,
or flight-level splitting can be more appropriate.

## 11. Connection to field orthomosaics

`FIELDimageR` provides field-trial orthomosaic tools in R ([Matias et
al. 2020](#ref-Matias2020)). OmniPhenoR does not duplicate its full
field-image functionality. The 0.2.0 tiling layer focuses on generic
image chunking and prediction reconstruction, while existing `terra` and
spatial-linkage functions preserve integration with georeferenced
workflows.

## 12. Raster extraction remains separate

Tiling a raster for model inference and extracting plot-level values are
different operations. After a full prediction surface is reconstructed,
use spatial polygons and explicit IDs to summarize the result at
experimental-unit level.

``` r

# Conceptual production sequence:
# raster <- pheno_read("orthomosaic.tif")
# prediction <- pheno_predict_tiles(rgb_array, trained_model, tile_size=512, overlap=64)
# prediction_raster <- convert_prediction_to_spatraster(prediction, raster_geometry)
# plot_traits <- pheno_spatial_extract(prediction_raster, plot_polygons)
```

Georeferencing and raster conversion are intentionally not inferred from
a plain RGB array.

## 13. Merge probabilities before thresholding

When possible, overlapping semantic-segmentation tiles should merge
continuous probabilities and only then apply the final threshold.
Thresholding each tile first discards information and can make seams
harder to resolve.

[`pheno_predict_tiles()`](https://wep69.github.io/OmniPhenoR/reference/pheno_predict_tiles.md)
follows the probability-first approach.

## 14. Multiclass tiled prediction

For K classes, each tile can return an H x W x K probability array. The
merger averages each class channel separately and assigns the
maximum-probability class after reconstruction.

``` r

truth <- pheno_data("leaf_mask")
lesion <- pheno_data("lesion_mask")

multi_fun <- function(z) {
  h <- dim(z)[1]; w <- dim(z)[2]
  # A real tiled adapter would compute probabilities from the tile itself.
  array(rep(c(.7,.2,.1), h*w), c(h,w,3))
}
```

For teaching simplicity this function is not used as a biological
prediction. The important contract is the class-probability array.

## 15. Tile size and biological scale

A tile should be large enough to contain the context needed for the
target. Detecting a whole leaf, disease pattern, plant row, or canopy
gap can require different spatial context. A very small tile may force
the model to classify ambiguous fragments without knowing the
surrounding organ or plot.

## 16. Tile size and hardware

Larger tiles increase memory demand. Smaller tiles increase the number
of inference calls and may increase seam effects. GPU memory, CPU
memory, image size, channels, model width, precision, and batch size
interact. Benchmark under representative deployment conditions.

``` r

pheno_benchmark(
  img,
  list(
    tile_32=function(z) pheno_merge_tiles(pheno_tile(z,32,overlap=4)),
    tile_48=function(z) pheno_merge_tiles(pheno_tile(z,48,overlap=8))
  ),
  repetitions=1,
  device="cpu"
)
#> # A tibble: 2 × 8
#>   method  repetition elapsed_sec output_mb memory_delta_mb gpu_memory_mb
#>   <chr>        <int>       <dbl>     <dbl>           <dbl>         <dbl>
#> 1 tile_32          1      0.0200     0.281           0.300            NA
#> 2 tile_48          1      0.0100     0.281           0                NA
#> # ℹ 2 more variables: input_dimensions <chr>, device <chr>
```

## 17. Batch size

[`pheno_predict_tiles()`](https://wep69.github.io/OmniPhenoR/reference/pheno_predict_tiles.md)
in 0.2.0 uses a transparent tile-by-tile inference loop. This emphasizes
correctness and backend compatibility rather than maximum throughput.
Advanced `torch` workflows may batch tiles directly, but must preserve
tile coordinates and merging logic.

## 18. Edge artifacts and QC

Inspect predictions at tile seams, image borders, plot boundaries,
high-contrast shadows, and partially visible objects. A global IoU can
hide localized seam failures.

A useful validation can stratify pixels or objects by distance to the
nearest tile boundary and test whether error increases near seams.

## 19. Large-image segmentation versus object detection

Semantic tiling predicts class labels for pixels. It does not identify
distinct overlapping objects. Counting individual fruits, leaves,
flowers, or insects may require instance segmentation or object
detection, which is outside the built-in 0.2.0 neural core and is a
logical target for later external backends.

## 20. Reproducibility record

For a tiled model workflow retain:

- source image/raster hash;
- image dimensions and spatial resolution;
- tile height/width;
- overlap;
- edge padding and value;
- class order;
- model ID and weights hash;
- preprocessing;
- probability merging rule;
- threshold or multiclass decision rule;
- runtime/device;
- spatial split strategy;
- final plot/plant linkage.

## 21. Frozen validation

``` r

pheno_validate("tiling")
#> # A tibble: 2 × 6
#>   domain check                              estimate target tolerance pass 
#>   <chr>  <chr>                                 <dbl>  <dbl>     <dbl> <lgl>
#> 1 tiling overlap merge exact reconstruction        0      0     1e-12 TRUE 
#> 2 tiling logical tile reconstruction               1      1     0     TRUE
```

The checks establish exact reconstruction for unchanged numeric tiles
and logical masks. They do not validate a trained model’s tile-edge
behavior.

## 22. Three applied examples

### 22.1 High-resolution detached leaf scanner

Tiling may be necessary for a very large scanned leaf. Overlap can
protect fine lesion boundaries, and the final severity should be
compared with full-resolution annotation.

### 22.2 Greenhouse bench mosaic

Split by plant or tray before making tiles. Avoid letting neighboring
tiles from one plant enter separate partitions.

### 22.3 UAV field orthomosaic

Split by plot, flight, field, or environment according to the intended
claim. Reconstruct probabilities in image space, preserve
georeferencing, then summarize by experimental polygons.

## 23. Final perspective

Tiling should preserve, not weaken, the measurement chain. A defensible
large-image workflow keeps spatial identity, merges probabilities
transparently, prevents overlapping regions from leaking across
evaluation partitions, and validates whether tile edges or context
limits alter the final phenotype.

## References

Kapoor, Sayash, and Arvind Narayanan. 2023. “Leakage and the
Reproducibility Crisis in Machine-Learning-Based Science.” *Patterns* 4
(9): 100804. <https://doi.org/10.1016/j.patter.2023.100804>.

Matias, Filipe Inacio, Maria V. Caraza-Harter, and Jeffrey B. Endelman.
2020. “FIELDimageR: An r Package to Analyze Orthomosaic Images from
Agricultural Field Trials.” *The Plant Phenome Journal* 3 (1): e20005.
<https://doi.org/10.1002/ppj2.20005>.
