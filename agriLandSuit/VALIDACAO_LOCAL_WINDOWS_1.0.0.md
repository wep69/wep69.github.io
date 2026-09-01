# Validação local detalhada no Windows — agriLandSuit 1.0.0

## Objetivo

Este roteiro certifica localmente a versão `agriLandSuit 1.0.0` em
Windows. O pacote já passou por validações estáticas, numéricas
independentes, auditoria de API, manifesto e integridade dos arquivos no
ambiente de montagem. Como esse ambiente não possui R, a certificação
executável precisa ser concluída em uma máquina Windows com R e Rtools.

O e-mail do criador/mantenedor já está registrado em `DESCRIPTION`:

``` text
walterufpb@yahoo.com.br
```

A validação deve ser realizada em duas passagens:

1.  **R-only**, demonstrando que Python não é necessário para instalar,
    carregar e utilizar o núcleo do pacote.
2.  **R + Python opcional**, exercitando os backends `reticulate` para
    fuzzy, MCDA e Monte Carlo numérico.

Não altere resultados, exemplos ou testes para fazer o check passar. Se
um teste falhar, registre a falha e corrija a causa.

------------------------------------------------------------------------

## 1. Preparar uma pasta curta e limpa

Evite caminhos longos, diretórios sincronizados e nomes contendo
caracteres especiais. Recomenda-se:

``` text
D:\temp\agriLandSuit_1.0.0
```

Coloque nessa pasta o ZIP ou o source snapshot fornecido e extraia o
pacote para:

``` text
D:\temp\agriLandSuit_1.0.0\agriLandSuit
```

No PowerShell:

``` powershell
New-Item -ItemType Directory -Force D:\temp\agriLandSuit_1.0.0 | Out-Null
Set-Location D:\temp\agriLandSuit_1.0.0
```

Confirme que existem pelo menos:

``` text
agriLandSuit\DESCRIPTION
agriLandSuit\NAMESPACE
agriLandSuit\R\
agriLandSuit\man\
agriLandSuit\tests\
agriLandSuit\vignettes\
agriLandSuit\tools\
```

------------------------------------------------------------------------

## 2. Instalar R e Rtools

Instale a versão corrente de **R 64-bit para Windows** e a versão de
**Rtools compatível com essa versão do R**. Não misture Rtools destinado
a outra série de R.

Depois da instalação, feche e reabra R/RStudio/PowerShell.

Em R:

``` r

R.version.string
R.version$platform
Sys.which(c("R", "Rscript", "make", "gcc", "g++"))
```

Resultados esperados:

- `R.version$platform` deve indicar Windows 64-bit;
- `R` e `Rscript` devem ser localizados;
- `make`, `gcc` e `g++` devem ser encontrados quando Rtools estiver
  configurado corretamente.

Opcionalmente, instale `pkgbuild` apenas para diagnosticar as
ferramentas de compilação:

``` r

install.packages("pkgbuild")
pkgbuild::check_build_tools(debug = TRUE)
```

Essa dependência não é necessária para o `agriLandSuit` em uso normal.

------------------------------------------------------------------------

## 3. Criar uma biblioteca R isolada para a validação

Isso reduz a chance de um pacote instalado anteriormente mascarar uma
dependência ausente.

No PowerShell:

``` powershell
New-Item -ItemType Directory -Force D:\temp\agriLandSuit_1.0.0\Rlib-core | Out-Null
$env:R_LIBS_USER = "D:\temp\agriLandSuit_1.0.0\Rlib-core"
```

Abra uma nova sessão R a partir desse PowerShell e confirme:

``` r

.libPaths()
```

A biblioteca `Rlib-core` deve aparecer como biblioteca do usuário.

------------------------------------------------------------------------

## 4. Instalar primeiro apenas o ambiente R

A primeira passagem deve comprovar que Python é opcional. Instale as
dependências R necessárias para execução, testes e documentação, mas
**não instale `reticulate` ainda nesta biblioteca isolada**, se ele não
estiver presente.

``` r

install.packages(c(
  "terra",
  "digest",
  "testthat",
  "roxygen2",
  "knitr",
  "rmarkdown",
  "jsonlite",
  "yaml",
  "units",
  "targets",
  "geotargets"
))
```

Confirme as dependências obrigatórias:

``` r

stopifnot(requireNamespace("terra", quietly = TRUE))
stopifnot(requireNamespace("digest", quietly = TRUE))
packageVersion("terra")
packageVersion("digest")
```

Confirme que `reticulate` não é necessário:

``` r

requireNamespace("reticulate", quietly = TRUE)
```

Se retornar `FALSE`, isso é aceitável e desejável nesta primeira
passagem.

------------------------------------------------------------------------

## 5. Conferir o DESCRIPTION antes de executar qualquer build

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
d <- read.dcf(file.path(pkg, "DESCRIPTION"))
d[, c("Package", "Version", "Date", "Title", "License", "Encoding")]
cat(d[, "Authors@R"], "\n")
```

Confirme manualmente:

``` text
Package: agriLandSuit
Version: 1.0.0
Date: 2026-08-31
License: GPL (>= 3)
Encoding: UTF-8
```

O `Authors@R` deve conter:

``` text
Walter Esfrain Pereira
ORCID 0000-0003-1085-0191
walterufpb@yahoo.com.br
roles aut, cre, cph

Magali Haidee Pereira Martinez
ORCID 0009-0009-5419-959X
role aut
```

Não prossiga se o e-mail ou a versão estiverem diferentes.

------------------------------------------------------------------------

## 6. Executar a validação estática independente

O validador estático usa Python apenas como linguagem utilitária do
script, não como backend científico do pacote. Se o comando `python` não
estiver disponível, use `py`.

No PowerShell:

``` powershell
Set-Location D:\temp\agriLandSuit_1.0.0\agriLandSuit
python .\tools\static_validate.py
```

ou:

``` powershell
py .\tools\static_validate.py
```

Resultado esperado:

``` text
STATIC VALIDATION PASSED
exports: 82
preserved 0.9.0 exports: 82
new 1.0.0 analytical exports: 0
```

O script também verifica:

- presença dos arquivos essenciais;
- versão `1.0.0`;
- e-mail do criador/mantenedor;
- ausência de exports duplicados;
- ausência de métodos S3 duplicados;
- existência das implementações para todos os exports;
- documentação Rd para todos os exports;
- preservação integral da API 0.9.0;
- presença dos dados sintéticos de demonstração;
- balanceamento estrutural básico dos arquivos R;
- ausência de marcadores provisórios nas superfícies de release.

Se houver qualquer `STATIC VALIDATION FAILED`, pare a certificação e
corrija a causa.

------------------------------------------------------------------------

## 7. Executar os validadores numéricos independentes

Ainda no PowerShell:

``` powershell
python .\tools\validate_core_reference_1.0.0.py
python .\tools\validate_multicrop_reference.py
python .\tools\validate_scenario_reference.py
python .\tools\validate_uncertainty_reference.py
```

ou substitua `python` por `py`.

Esses scripts verificam resultados de referência independentes para os
blocos científicos congelados. Nenhum resultado deve ser atualizado
apenas para fazer a validação passar.

------------------------------------------------------------------------

## 8. Regenerar a documentação roxygen2

Em R:

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
roxygen2::roxygenise(pkg)
```

Depois, feche a sessão R e execute novamente:

``` powershell
python D:\temp\agriLandSuit_1.0.0\agriLandSuit\tools\static_validate.py
```

Resultado esperado: **PASS** novamente.

Se `roxygenise()` alterar exports, nomes S3 ou documentação
inesperadamente, compare os arquivos antes de continuar. A API 1.0.0
deve permanecer com **82 exports**.

------------------------------------------------------------------------

## 9. Instalar o pacote diretamente da pasta para um primeiro smoke test

No PowerShell, a partir de `D:\temp\agriLandSuit_1.0.0`:

``` powershell
R CMD INSTALL --preclean --clean .\agriLandSuit
```

Em uma nova sessão R:

``` r

library(agriLandSuit)
packageVersion("agriLandSuit")
```

Resultado esperado:

``` text
[1] '1.0.0'
```

Confirme que o núcleo não precisa de Python:

``` r

agri_engine("r")
python_backend_status()
```

`agri_engine("r")` deve retornar `"r"`.
[`python_backend_status()`](https://wep69.github.io/agriLandSuit/reference/python_backend_status.md)
pode indicar módulos Python indisponíveis sem impedir o uso do pacote.

------------------------------------------------------------------------

## 10. Executar toda a suíte testthat em modo R-only

Na sessão R:

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
res <- testthat::test_local(pkg, reporter = "progress")
res
```

Os testes Python usam `skip_if_not_installed()`/checagens de módulos e
devem ser ignorados quando o backend opcional não estiver disponível.

Os grupos críticos incluem:

``` text
test-api-compatibility-1.0.0.R
test-release-assets-1.0.0.R
test-fuzzy-membership.R
test-constraints.R
test-aggregation.R
test-mcda-ahp.R
test-mcda-topsis.R
test-scenarios.R
test-monte-carlo.R
test-crop-probability.R
test-reproducibility-fingerprint.R
```

Critério de aprovação: **zero falhas e zero erros** nos testes
executados. Skips de Python são aceitáveis nesta passagem.

------------------------------------------------------------------------

## 11. Verificar os dados de demonstração

Após instalar/carregar o pacote:

``` r

library(agriLandSuit)

env_file <- system.file("extdata", "demo_environment_grid.csv", package = "agriLandSuit")
prof_file <- system.file("extdata", "demo_crop_profiles.csv", package = "agriLandSuit")

stopifnot(file.exists(env_file), file.exists(prof_file))

env <- read.csv(env_file)
prof <- read.csv(prof_file)

stopifnot(nrow(env) == 80L)
stopifnot(all(prof$source == "synthetic demonstration"))
```

Esses dados são exclusivamente didáticos. Não devem ser citados como
limites ou recomendações agronômicas reais.

------------------------------------------------------------------------

## 12. Executar um smoke test espacial e de reprodutibilidade

``` r

library(terra)
library(agriLandSuit)

r <- rast(nrows = 2, ncols = 2, xmin = 0, xmax = 2, ymin = 0, ymax = 2)
values(r) <- 1:4
names(r) <- "x"

x <- land_data(climate = r)
land_validate(x)

fp1 <- land_fingerprint(x)
man <- land_manifest(x)

bundle <- tempfile("agriLandSuit_bundle_")
land_export(x, bundle, format = "bundle")
y <- land_import(bundle)
fp2 <- land_fingerprint(y)

stopifnot(identical(as.character(fp1), as.character(fp2)))
chk <- reproducibility_check(y, man)
stopifnot(isTRUE(chk$ok))
```

Também inspecione manualmente:

``` r

print(man)
print(chk)
```

------------------------------------------------------------------------

## 13. Renderizar as dez vinhetas

Primeiro confirme a quantidade:

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
vigs <- list.files(file.path(pkg, "vignettes"), pattern = "\\.Rmd$", full.names = TRUE)
basename(vigs)
stopifnot(length(vigs) == 10L)
```

Renderize:

``` r

for (v in vigs) {
  message("Rendering: ", basename(v))
  rmarkdown::render(v, quiet = FALSE)
}
```

A sequência deve começar por:

``` text
00-complete-workflow.Rmd
```

Nenhuma vinheta deve terminar com erro. Verifique especialmente warnings
relacionados a arquivos temporários, caminhos absolutos, internet ou
Python.

------------------------------------------------------------------------

## 14. Criar o ambiente Python opcional

Essa etapa é **opcional para o usuário final**, mas recomendada para
certificar a release completa.

Use um Python 3.11 ou superior para compatibilidade com o backend PyMCDM
auditado na release.

No PowerShell, supondo que `py` esteja disponível:

``` powershell
py -3.11 -m venv D:\temp\agriLandSuit_1.0.0\py-agriLandSuit
D:\temp\agriLandSuit_1.0.0\py-agriLandSuit\Scripts\python.exe -m pip install --upgrade pip
D:\temp\agriLandSuit_1.0.0\py-agriLandSuit\Scripts\python.exe -m pip install numpy scikit-fuzzy pymcdm
```

Não é necessário instalar `PyMC`, `ArviZ`, `AHPy`, `PyAEZ`, `LSAPy` ou
`terraflow-agro` para certificar os backends efetivamente ativos da
1.0.0.

Instale agora `reticulate` no R:

``` r

install.packages("reticulate")
```

Antes de carregar `reticulate`, em uma sessão R nova:

``` r

Sys.setenv(
  RETICULATE_PYTHON = "D:/temp/agriLandSuit_1.0.0/py-agriLandSuit/Scripts/python.exe"
)

library(reticulate)
py_config()
```

Verifique os módulos:

``` r

py_module_available("numpy")
py_module_available("skfuzzy")
py_module_available("pymcdm")
```

Todos devem retornar `TRUE`.

------------------------------------------------------------------------

## 15. Verificar os backends opcionais do agriLandSuit

``` r

library(agriLandSuit)

python_backend_status("fuzzy")
python_backend_status("mcda")
python_backend_status("sampling")
```

Para os componentes ativos da 1.0.0, confirme disponibilidade de:

``` text
numpy
scikit-fuzzy / import skfuzzy
pymcdm
```

Também confira:

``` r

agri_engine("python", "fuzzy")
agri_engine("python", "mcda")
agri_engine("python", "sampling")
```

Resultado esperado em todos os três casos:

``` text
"python"
```

------------------------------------------------------------------------

## 16. Reexecutar todos os testes com Python configurado

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
testthat::test_local(pkg, reporter = "progress")
```

Desta vez, preste atenção especial a:

``` text
test-python-fuzzy.R
test-python-mcda.R
test-python-uncertainty.R
```

Critério de aprovação:

- zero falhas;
- zero erros;
- testes de paridade R/Python executados, e não simplesmente ignorados
  por ausência de módulos.

Se os testes Python forem ignorados, rode novamente
[`reticulate::py_config()`](https://rstudio.github.io/reticulate/reference/py_config.html)
e
[`python_backend_status()`](https://wep69.github.io/agriLandSuit/reference/python_backend_status.md)
e confirme que o R está realmente ligado ao ambiente correto.

------------------------------------------------------------------------

## 17. Registrar o ambiente Python utilizado

No PowerShell:

``` powershell
D:\temp\agriLandSuit_1.0.0\py-agriLandSuit\Scripts\python.exe --version > PYTHON_VERSION_1.0.0.txt
D:\temp\agriLandSuit_1.0.0\py-agriLandSuit\Scripts\python.exe -m pip freeze > PYTHON_REQUIREMENTS_VALIDATED_1.0.0.txt
```

Esses arquivos devem ser arquivados junto ao log da validação, mas não
precisam virar dependências obrigatórias do pacote.

------------------------------------------------------------------------

## 18. Construir o tarball oficial com R CMD build

Abra um PowerShell novo, preferencialmente na raiz:

``` powershell
Set-Location D:\temp\agriLandSuit_1.0.0
R CMD build agriLandSuit
```

O comando deve produzir:

``` text
agriLandSuit_1.0.0.tar.gz
```

Esse arquivo é diferente do `source_snapshot.tar.gz` montado
previamente. **O tarball produzido por `R CMD build` é o candidato que
deve ser certificado.**

Confirme sua existência:

``` powershell
Get-Item .\agriLandSuit_1.0.0.tar.gz
```

------------------------------------------------------------------------

## 19. Executar R CMD check –as-cran

Ainda na mesma pasta:

``` powershell
R CMD check --as-cran agriLandSuit_1.0.0.tar.gz
```

O diretório esperado é:

``` text
agriLandSuit.Rcheck
```

Leia integralmente:

``` text
agriLandSuit.Rcheck\00check.log
```

No PowerShell:

``` powershell
Get-Content .\agriLandSuit.Rcheck\00check.log
```

Para localizar mensagens relevantes:

``` powershell
Select-String -Path .\agriLandSuit.Rcheck\00check.log -Pattern "ERROR|WARNING|NOTE|Status:"
```

### Critério de certificação

A meta final é:

``` text
Status: OK
```

ou, no mínimo, **0 ERROR e 0 WARNING**, com cada NOTE analisado
individualmente e documentado antes de qualquer submissão.

Não ignore NOTEs automaticamente. Avalie especialmente:

- dependências em `Suggests`;
- exemplos/vinhetas que usam pacotes opcionais;
- conectividade com internet;
- tamanho do pacote;
- arquivos executáveis ou ocultos;
- portabilidade dos caminhos;
- tempo dos exemplos e testes;
- mensagens sobre `terra`;
- comportamento quando Python não está disponível;
- documentação de funções e argumentos;
- UTF-8 e caracteres especiais;
- metadados `Authors@R`/ORCID/e-mail.

------------------------------------------------------------------------

## 20. Inspecionar arquivos gerados pelo check

Além de `00check.log`, revise quando existirem:

``` text
00install.out
00_pkg_src\
examples_and_tests\
vign_test\
```

No PowerShell:

``` powershell
Get-ChildItem .\agriLandSuit.Rcheck -Recurse | Select-Object FullName, Length
```

Não distribua o diretório `.Rcheck` como parte do pacote final.

------------------------------------------------------------------------

## 21. Instalar o tarball que efetivamente passou no check

Após corrigir qualquer problema e reconstruir, instale exatamente o
tarball aprovado:

``` powershell
R CMD INSTALL --preclean .\agriLandSuit_1.0.0.tar.gz
```

Em uma sessão R limpa:

``` r

library(agriLandSuit)
stopifnot(packageVersion("agriLandSuit") == "1.0.0")
```

Execute novamente um smoke test mínimo:

``` r

membership_triangular(c(0, 0.5, 1), c(0, 0.5, 1))
python_backend_status()
```

Se Python não estiver configurado nessa sessão, o segundo comando deve
apenas reportar indisponibilidade dos módulos opcionais, sem impedir o
carregamento do pacote.

------------------------------------------------------------------------

## 22. Registrar sessionInfo e capacidades

Em R:

``` r

writeLines(
  capture.output(sessionInfo()),
  "D:/temp/agriLandSuit_1.0.0/SESSION_INFO_1.0.0.txt"
)

writeLines(
  capture.output(capabilities()),
  "D:/temp/agriLandSuit_1.0.0/R_CAPABILITIES_1.0.0.txt"
)
```

Opcionalmente registre as versões principais:

``` r

pkgs <- c(
  "agriLandSuit", "terra", "digest", "testthat", "roxygen2",
  "knitr", "rmarkdown", "reticulate", "targets", "geotargets"
)

vers <- vapply(pkgs, function(p) {
  if (requireNamespace(p, quietly = TRUE)) as.character(packageVersion(p)) else NA_character_
}, character(1))

write.csv(
  data.frame(package = pkgs, version = vers),
  "D:/temp/agriLandSuit_1.0.0/R_PACKAGE_VERSIONS_1.0.0.csv",
  row.names = FALSE
)
```

------------------------------------------------------------------------

## 23. Calcular o SHA-256 do tarball certificado

No PowerShell:

``` powershell
Get-FileHash .\agriLandSuit_1.0.0.tar.gz -Algorithm SHA256
```

Salve o resultado:

``` powershell
Get-FileHash .\agriLandSuit_1.0.0.tar.gz -Algorithm SHA256 |
  Format-List |
  Out-File .\SHA256_CERTIFIED_LOCAL_1.0.0.txt
```

Esse hash é o identificador do **tarball local certificado**.

Não o confunda com:

- SHA-256 do ZIP de montagem;
- SHA-256 do `source_snapshot.tar.gz` produzido antes de `R CMD build`;
- hashes internos do `FREEZE_MANIFEST_1.0.0.json`.

------------------------------------------------------------------------

## 24. Arquivar evidências da validação

Ao final, recomenda-se manter uma pasta como:

``` text
D:\temp\agriLandSuit_1.0.0\validation_evidence\
```

contendo:

``` text
agriLandSuit_1.0.0.tar.gz
SHA256_CERTIFIED_LOCAL_1.0.0.txt
SESSION_INFO_1.0.0.txt
R_CAPABILITIES_1.0.0.txt
R_PACKAGE_VERSIONS_1.0.0.csv
PYTHON_VERSION_1.0.0.txt                    [se Python foi validado]
PYTHON_REQUIREMENTS_VALIDATED_1.0.0.txt     [se Python foi validado]
00check.log
00install.out                               [se disponível]
```

Também é útil guardar uma cópia do `DESCRIPTION` e do `cran-comments.md`
usados exatamente no build certificado.

------------------------------------------------------------------------

## 25. Critérios finais para declarar a 1.0.0 validada

A versão pode ser tratada como localmente certificada quando todos os
itens abaixo forem verdadeiros:

`DESCRIPTION` identifica `agriLandSuit 1.0.0`.

`Authors@R` contém `walterufpb@yahoo.com.br` para o `cre`.

Rtools é compatível com a versão de R utilizada.

`static_validate.py` retorna PASS.

validadores numéricos independentes retornam PASS.

[`roxygen2::roxygenise()`](https://roxygen2.r-lib.org/reference/roxygenize.html)
termina sem quebrar a API.

o pacote mantém 82 exports.

todos os testes R-only executáveis passam.

as 10 vinhetas renderizam.

dados sintéticos são encontrados e preservam o schema esperado.

fingerprint/export/import/reproducibility smoke test passa.

backend Python permanece opcional.

se o backend Python for certificado, os testes de paridade
fuzzy/MCDA/Monte Carlo passam.

`R CMD build` produz `agriLandSuit_1.0.0.tar.gz`.

`R CMD check --as-cran` retorna 0 ERROR e 0 WARNING.

cada NOTE foi examinada e justificada/corrigida.

o tarball aprovado instala em uma sessão limpa.

[`sessionInfo()`](https://rdrr.io/r/utils/sessionInfo.html) foi
registrado.

o SHA-256 do tarball local aprovado foi congelado.

------------------------------------------------------------------------

## 26. Sequência curta recomendada

Para uso operacional, a ordem é:

``` text
1. Extrair fonte em D:\temp
2. Confirmar DESCRIPTION/e-mail
3. Instalar R + Rtools compatível
4. Criar biblioteca R limpa
5. Instalar dependências sem reticulate
6. static_validate.py
7. validadores numéricos Python independentes
8. roxygenise()
9. static_validate.py novamente
10. R CMD INSTALL da pasta
11. testthat R-only
12. renderizar 10 vinhetas
13. smoke test de proveniência
14. configurar Python opcional
15. testar paridade R/Python
16. R CMD build
17. R CMD check --as-cran
18. corrigir e repetir até aprovação
19. instalar o tarball aprovado
20. registrar sessionInfo + versões
21. calcular e congelar SHA-256
```

O ponto de certificação é sempre o **tarball criado por `R CMD build`
que efetivamente passou pelo `R CMD check --as-cran`**.
