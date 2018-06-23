## Santander ProductRecommendation (Exploratory analysis)

## Load data and packages
``` R
df = fread("train_ver2.csv")

## df = fread("train_ver2.csv")
## Read 13647309 rows and 48 (of 48) columns from 2.135 GB file in 00:00:53
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
                    0                     0                     0                     0                     0                 13018 
           fecha_alta             ind_nuevo            antiguedad                indrel        ult_fec_cli_1t           indrel_1mes 
                    0                 13018                 13018                 13018                     0                     0 
          tiprel_1mes               indresi                indext              conyuemp         canal_entrada               indfall 
                    0                     0                     0                     0                     0                     0 
              tipodom              cod_prov               nomprov ind_actividad_cliente                 renta              segmento 
                13019                 56633                     0                 13018                864285                     0 
    ind_ahor_fin_ult1     ind_aval_fin_ult1      ind_cco_fin_ult1     ind_cder_fin_ult1      ind_cno_fin_ult1     ind_ctju_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_ctma_fin_ult1     ind_ctop_fin_ult1     ind_ctpp_fin_ult1     ind_deco_fin_ult1     ind_deme_fin_ult1     ind_dela_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_ecue_fin_ult1     ind_fond_fin_ult1      ind_hip_fin_ult1     ind_plan_fin_ult1     ind_pres_fin_ult1     ind_reca_fin_ult1 
                    0                     0                     0                     0                     0                     0 
    ind_tjcr_fin_ult1     ind_valo_fin_ult1      ind_viv_fin_ult1       ind_nomina_ult1     ind_nom_pens_ult1       ind_recibo_ult1 
                    0                     0                     0                  9081                  9081                     0 
                
```

### Impute missing values

#### Age
``` R
  ggplot(train, aes(x = age))+
    geom_bar(fill = 'steelblue', color = 'black')  
  train$age[(train$age < 20)] = mean(train$age[(train$age >= 20) & (train$age <= 30)], na.rm = T)
  train$age[(train$age > 100)] = mean(train$age[(train$age >= 30) & (train$age <= 100)], na.rm = T)
  train$age[is.na(train$age)] = median(train$age, na.rm = T)
  train$age = round(train$age)
```
#### Customer index (Ind_neuvo)
``` R
  table(train$ind_nuevo)
  active = train[is.na(train$ind_nuevo), ] %>% 
    select(ncodpers) %>% 
    group_by(ncodpers) %>%
    summarise(active = n())
  active$active[active$active > 6] 

  ## numeric(0) ##
```
```R
  train$ind_nuevo[is.na(train$ind_nuevo)] = 1	
```  

#### Customer seniority (antiguedad)

``` R
  table(train$antiguedad)   # -999999 detected. Should be fixed.
  train %>% filter(antiguedad == -999999) %>% select(ind_nuevo)
  train$antiguedad[train$antiguedad == -999999] = 0
  train[is.na(train$antiguedad)] %>% select(ind_nuevo) %>% summary()
  
##	ind_nuevo
	 Min.   :1  
	 1st Qu.:1  
	 Median :1  
	 Mean   :1  
	 3rd Qu.:1  
	 Max.   :1
```
``` R
train[is.na(train$antiguedad)] = min(train$antiguedad, na.rm = T)
```

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


