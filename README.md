## Santander ProductRecommendation (Exploratory analysis)

## Introduction




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

## Data cleaning Part 1: Identify N/A values

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
``` R
  table(train$indrel)
  train$indrel[is.na(train$indrel)] = 1 # Options are either 1 or 99. Replace N/A with most common value.
```

#### Address type (tipodom) & province code (cod_prov)
Since both tipodom and cod_prov don't seem to be too useful due to other relevant information available such as pais_residencia and nomprov. Therefore, we will remove these two columns from data set. 
``` R
train = train[, -c('tipodom', 'cod_prov')]
```

#### Activity index (ind_actividad_cliente)
  ``` R
  table(train$ind_actividad_cliente)
  train$ind_actividad_cliente[is.na(train$ind_actividad_cliente)] = median(train$ind_actividad_cliente, na.rm = T)
  ```
#### Gross income of the household (renta)
``` R
  summary(train$renta)
  train %>% filter(!is.na(renta)) %>% group_by(nomprov) %>% 
    summarise(median = median(renta)) %>% 
    ggplot(aes(reorder(x = nomprov, median), y = median))+
    geom_point(color = "steelblue3")
 
  incomes = train %>% 
    merge(train %>% group_by(nomprov) %>% summarise(med.income = median(renta, na.rm = T)), by = 'nomprov') %>% 
    select(nomprov, med.income) %>% 
    arrange(nomprov)
  
  train = train %>% arrange(nomprov)
  
  train$renta[is.na(train$renta)] = incomes$med.income[is.na(train$renta)]
```
#### Product: Payroll (ind_nomina_ult1)
``` R
  table(train$ind_nomina_ult1)
  train$ind_nomina_ult1[is.na(train$ind_nomina_ult1)] = 0
```
#### Product: Pensions (ind_nom_pens_ult1)
``` R
  table(train$ind_nom_pens_ult1)
  train$ind_nom_pens_ult1[is.na(train$ind_nom_pens_ult1)] = 0
```

## Data cleaning Part 2: Identify empty values
Now, we will replace empty values with either most observed values or create a new category called unknown. 

#### Customer's country residence (pais_residencia)
``` R
  table(train$pais_residencia)
  train$pais_residencia[train$pais_residencia == ""] = "unknown"
```
#### Customer's sex (sexo)
``` R
  table(train$sexo)
  train$sexo[train$sexo == ""] = "unknown"
```
#### Laste date as primary customer (ult_fec_cli_1t)
``` R
  table(train$ult_fec_cli_1t)
  train$ult_fec_cli_1t[train$ult_fec_cli_1t == ""] = "unknown"
```
#### Customer type at the beginning of the month (indrel_1mes)
``` R
  table(train$indrel_1mes)
  train$indrel_1mes[train$indrel_1mes == ""] = "1"
  train$indrel_1mes[train$indrel_1mes == "1.0"] = "1"
  train$indrel_1mes[train$indrel_1mes == "2.0"] = "2"
  train$indrel_1mes[train$indrel_1mes == "3.0"] = "3"
  train$indrel_1mes[train$indrel_1mes == "4.0"] = "4"
  train$indrel_1mes[train$indrel_1mes == "P"] = "5" #Converts a group that contains a letter to a number. 
```
#### Customer relation type at the beginning of the month (tiprel_1mes)
``` R
  table(train$tiprel_1mes)
  train$tiprel_1mes[train$tiprel_1mes == ""] = "A"
```
#### Residence index (indresi)
``` R
  table(train$indresi)
  train$indresi[train$indresi == ""] = "S"
```
####   #Foreign indes (indext)
``` R
  table(train$indext)
  train$indext[train$indext == ""] = "N"
```    
#### Spouse Index (conyuemp)
``` R
  table(train$conyuemp)
  train$conyuemp[train$conyuemp == ""] = "unknown"
```    
#### Type of channel (canal_entrada)
``` R
  table(train$canal_entrada)
  train$canal_entrada[train$canal_entrada == ""] = "unknown"
```  
#### Deceased index (indfall)
``` R
  table(train$indfall)
  train$indfall[train$indfall == ""] = "N"
```  
#### Province name (nomprov)
``` R
  table(nomprov)
  train$nomprov[train$nomprov==""] = "unknown"
```
#### Segmentation (segmento)
``` R
  table(train$segmento)  
  train$segmento[train$segmento == ""] = "02 - PARTICULARES"
```

## Data cleaning Part 3: Identify duplicate values
  ``` R
  distinct(train, fecha_dato, ncodpers) 
  ##
490     2015-01-28   675057
491     2015-01-28   670240
492     2015-01-28   668959
493     2015-01-28   668960
494     2015-01-28   668996
495     2015-01-28   670852
496     2015-01-28   652408
497     2015-01-28   652409
498     2015-01-28   651686
499     2015-01-28   654310
500     2015-01-28   654244
 [ reached getOption("max.print") -- omitted 5230695 rows ]
  ```
  
The results hows that there are no duplicate values.


## Data visualizations

### Bivariate analysis

### Multivariate analysis

### Correlation matrix

### Treemap

### Worldmap


