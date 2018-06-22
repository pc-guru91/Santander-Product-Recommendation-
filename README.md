## Santander ProductRecommendation (Exploratory analysis)

## Load data and packages
``` R
df = fread("train_ver2.csv")
```

```R
library(data.table)
library(dplyr)
library(ggplot2)
```

## Data cleaning

### Identify missing values
``` R
           fecha_dato              ncodpers          ind_empleado       pais_residencia                  sexo                   age 
                    0                     0                     0                     0                     0                 27734 
           fecha_alta             ind_nuevo            antiguedad                indrel        ult_fec_cli_1t           indrel_1mes 
                    0                 27734                 27734                 27734                     0                     0 
          tiprel_1mes               indresi                indext              conyuemp         canal_entrada               indfall 
                    0                     0                     0                     0                     0                     0 
              tipodom              cod_prov               nomprov ind_actividad_cliente                 renta              segmento 
                27735                 93591                     0                 27734               2794375                     0 
    ind_ahor_fin_ult1     ind_aval_fin_ult1      ind_cco_fin_ult1     ind_cder_fin_ult1      ind_cno_fin_ult1     ind_ctju_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_ctma_fin_ult1     ind_ctop_fin_ult1     ind_ctpp_fin_ult1     ind_deco_fin_ult1     ind_deme_fin_ult1     ind_dela_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_ecue_fin_ult1     ind_fond_fin_ult1      ind_hip_fin_ult1     ind_plan_fin_ult1     ind_pres_fin_ult1     ind_reca_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_tjcr_fin_ult1     ind_valo_fin_ult1      ind_viv_fin_ult1       ind_nomina_ult1     ind_nom_pens_ult1       ind_recibo_ult1 
                    0                     0                     0                 16063                 16063                     0 
                
```

### Impute missing values

#### Age

#### Customer index (Ind_neuvo)

#### Customer seniority (antiguedad)

#### First/primary customer (indrel)

#### Address type (tipodom)

#### Province code (cod_prov)

#### Activity index (ind_actividad_cliente)

#### Gross income of the household (renta)

#### Product: Payroll (ind_nomina_ult1)

#### Product: Pensions (ind_nom_pens_ult1)


## Data visualizations

### Bivariate analysis

### Multivariate analysis

### Correlation matrix

### Treemap

### Worldmap


