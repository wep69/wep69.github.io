# FREEZE 1.0.0 — OmniPhenoR

## Artefato congelado

| Campo | Valor |
|----|----|
| Filename | `OmniPhenoR_1.0.0.tar.gz` |
| Size | 4,243,898 bytes (4.05 MB) |
| SHA-256 | `19f19ebbe353d6cab20430534d13058a762504531a2900879fb9bf166af89172` |
| Date | 2026-08-24 |
| Version | **1.0.0** (stable integrated release) |
| Repo | <https://github.com/wep69/OmniPhenoR> |
| Site | <https://wep69.github.io/OmniPhenoR/> |

> Nota: hash anterior (`fbae569c...`) referia-se ao build anterior à
> inclusão dos campos `URL`/`BugReports` no DESCRIPTION (GUIA §7.9). O
> pipeline completo foi re-executado após a mudança: suite
> F0/W2/S0/P388, check –as-cran Status OK, instalação exata + smoke
> tests PASS.

## Ambiente de validação

| Item | Valor |
|----|----|
| OS | Windows 11 x64 (ucrt) |
| R | 4.6.0 (2026-04-24 ucrt) |
| Rtools | 45 (gcc 14.2.0) |
| Pandoc | via Quarto 1.10.7 / pandoc tools |
| Git | 2.55.0 |
| Suggests backends | 25/25 instalados (incl. torch CPU, magick, terra, arrow, duckdb, sf, nlme/lme4/glmmTMB/emmeans) |

## Resultados da validação final (fonte + tarball exato)

| Camada | Resultado |
|----|----|
| Estrutura da fonte (DESCRIPTION/NAMESPACE/API CSV) | PASS — 223 exports; 20 S3; API ledger íntegro |
| Block registry | PASS — 240 blocos (32/40/50/58/60 por geração) |
| testthat completo | PASS — FAIL 0 / WARN 2\* / SKIP 0 / PASS 388 |
| Controles numéricos (protocolo §16–39) | PASS — 20 controles mestres |
| Fixture verification (`verify_image_fixtures.R`) | PASS |
| Novos image-file fixtures tests | PASS — F0/S0/P32 (+2 WARN benignos\*) |
| Vinhetas | PASS — 44/44 Rmd renderizam; 44/44 HTML instalados |
| `R CMD build` | OK |
| `R CMD check --as-cran` (tarball exato) | **Status: OK** — 0 ERROR / 0 WARNING / 0 NOTE de pacote |
| Instalação do tarball exato em biblioteca isolada | PASS |
| Smoke tests no pacote instalado | PASS — versão, exports, blocos, vinhetas, fixtures+SHA-256, core, escala, YOLO |

\* WARN = `[rast] unknown extent` do terra ao ler TIFFs sintéticos sem
georreferenciamento — comportamento esperado e documentado.

## Correções aplicadas durante a validação (registradas no NEWS.md)

1.  [`pheno_diff()`](https://wep69.github.io/OmniPhenoR/reference/pheno_diff.md):
    chave inexistente `file_id` → detecção real por path+SHA-256.
2.  [`pheno_snapshot()`](https://wep69.github.io/OmniPhenoR/reference/pheno_snapshot.md):
    congela estado do disco (`refresh=TRUE`) — habilita auditoria.
3.  Testes: `$function` (palavra reservada), registry 72→240, tampering
    test adicionado.
4.  `print.pheno_series()` defensivo para séries subconjunto.
5.  Vinheta v43: reshaping long explícito +
    [`library(OmniPhenoR)`](https://wep69.github.io/OmniPhenoR/) em
    todas as vinhetas que faltavam (33).
6.  Documentação: `man/` regenerado integralmente via roxygen2 (223 Rd ↔︎
    223 exports).
7.  [`pheno_read()`](https://wep69.github.io/OmniPhenoR/reference/pheno_read.md)
    (magick): decodificação via `image_raster()` — `image_data()`
    corrompia PNGs 8-bit em builds IM 6.9 (verificado contra libpng).
8.  [`pheno_detection_to_sf()`](https://wep69.github.io/OmniPhenoR/reference/pheno_detection_to_sf.md):
    default `crs=NULL`→`NA_character_` para sf atual.
9.  Qualificação
    [`utils::capture.output()`](https://rdrr.io/r/utils/capture.output.html)/[`utils::str()`](https://rdrr.io/r/utils/str.html)
    e 40+ linhas de exemplos requebradas (≤100 col) após dano de wrapper
    diagnosticado por gates (parse/aspas/corrupção-de-split).
10. Fixtures: `rgb_reference_values.csv` com precisão plena (17
    dígitos); scripts de verificação/testes com comparações
    whitespace-safe.

## Fixtures físicas de imagem (patch integrado)

| Item                          | Status                           |
|-------------------------------|----------------------------------|
| Physical image files          | 10                               |
| Ground-truth files            | 9                                |
| Fixture SHA-256 verification  | PASS                             |
| Scale truth (100 px = 10 mm)  | PASS                             |
| YOLO round-trip (12 boxes)    | PASS                             |
| Perfect detection (P=R=IoU=1) | PASS                             |
| PNG I/O via magick            | PASS                             |
| TIFF I/O via terra            | PASS (labels 0:5; tiny 3 bandas) |
| Core sem magick/terra         | PASS (skip_if_not_installed)     |

## Regra pós-hash

Qualquer alteração posterior em `DESCRIPTION`, `NAMESPACE`, `R/`,
`man/`, `tests/`, `vignettes/`, `inst/` ou `data/` invalida este SHA-256
e exige repetição integral do pipeline (roxygen → tests → controles →
build → check → install → hash).
