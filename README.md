# tcc

## Passos para refazer as amostras e rodar `att_gt`

1. **Defina a semente e recrie as amostras** após ter ajustado o `id` para ser numérico e construído as variáveis derivadas:

```r
set.seed(123)
Pme_100k <- Pme[sample(.N, 100000)]
Pme_200k <- Pme[sample(.N, 200000)]
```

2. **(Opcional) Remova linhas com dados faltantes** nas variáveis usadas pelo modelo para evitar avisos sobre observações descartadas:

```r
Pme_100k_clean <- Pme_100k[complete.cases(ln_emprego, time_c, id, gname_IMP1)]
```

3. **Execute o `att_gt`** usando a amostra (limpa, se aplicável):

```r
att_imp1 <- att_gt(
  yname   = "ln_emprego",
  tname   = "time_c",
  idname  = "id",
  gname   = "gname_IMP1",
  data    = Pme_100k_clean,  # ou Pme_100k se não limpar NAs
  xformla = ~ 1,
  panel   = TRUE,
  allow_unbalanced_panel = TRUE,
  control_group = "notyettreated",
  anticipation = 0,
  est_method = "ipw",
  bstrap = FALSE,
  cband = FALSE
)
```

4. **Repita para a outra amostra**, se quiser comparar resultados:

```r
att_imp1_200k <- att_gt(
  yname   = "ln_emprego",
  tname   = "time_c",
  idname  = "id",
  gname   = "gname_IMP1",
  data    = Pme_200k,        # ou Pme_200k[complete.cases(...)]
  xformla = ~ 1,
  panel   = TRUE,
  allow_unbalanced_panel = TRUE,
  control_group = "notyettreated",
  anticipation = 0,
  est_method = "ipw",
  bstrap = FALSE,
  cband = FALSE
)
```

5. **Cheque rapidamente NAs** se preferir inspecionar antes de rodar o modelo:

```r
colSums(is.na(Pme_100k[, .(ln_emprego, time_c, id, gname_IMP1)]))
```
