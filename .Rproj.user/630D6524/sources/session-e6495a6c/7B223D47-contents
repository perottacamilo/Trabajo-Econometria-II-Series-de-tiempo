library(tidyr)
library(readxl)
library(haven)      
library(AER)
library(modelsummary) 
library(tidyverse)   
library(janitor)

rm(list = ls())

precio_productor <- read.csv("raw/precio_productor.csv") %>% clean_names() 
datos_exportacion <- read.csv("raw/datos_exportacion.csv") %>% clean_names()
datos_produccion <- read.csv("raw/datos_produccion.csv") %>% clean_names()
precio_anual_nominal <- read_excel("raw/precio_anual_por_kg.xlsx", 
                    sheet = "Annual Prices (Nominal)",
                    skip = 8,
                    col_names = c("year", "precio_kg_nominal")) %>% clean_names()
precio_anual_real <- read_excel("raw/precio_anual_por_kg.xlsx", sheet = "Annual Prices (Real)",
                                skip = 8,
                                col_names = c("year", "precio_kg_real")) %>% clean_names()
tipo_cambio <- read_excel("raw/tipo_cambio.xlsx") %>% clean_names()
derivados <- read.csv("raw/derivados_cocoa.csv") %>% clean_names()


#Ordenar aquellas tablas que no esten en formato ancho
#Juntar todas en una sola tabla

datos_exportacion <- datos_exportacion %>% 
  rename(value_export = value)

datos_produccion <- datos_produccion %>% 
  rename(total_production = value)

derivados <- derivados %>% 
  select(area, year, item, value) %>% 
  pivot_wider(names_from = item,
              values_from = value) 
derivados <- derivados %>% 
  rename(derivado1 = "Cocoa butter, fat and oil",
         derivado2 = "Cocoa paste not defatted",
         derivado3 = "Cocoa powder and cake")
  

  precio_anual_nominal <- precio_anual_nominal %>%
    filter(year >= 1991 & year <= 2024)
  
  
  precio_anual_real <- precio_anual_real %>%
    filter(year >= 1991 & year <= 2024)
  
  tipo_cambio <- tipo_cambio %>% 
    pivot_longer(cols = "x1991_yr1991":"x2024_yr2024",
                 names_to = "year",
                 values_to = "tipos_cambio")
  
  tipo_cambio <- tipo_cambio %>%
    rename(area= country_code,
           series_name = series_name) 
 
   tipo_cambio <- tipo_cambio %>%
    select(area, year, tipos_cambio, series_name) %>% 
    pivot_wider(names_from = series_name,
                values_from = tipos_cambio)
   
   tipo_cambio <- tipo_cambio %>%
     filter(!is.na(area)) %>% 
     select(-`NA`) %>%
     mutate(year = as.numeric(substr(year, 2, 5)))
   
#Poner los derivados segun su ponderacion
#Armar una variable de exportacion total con datos_expo + derivados ponderados
   #Butter 1.33, paste 1.25, torta, 1.18
   derivados <- derivados %>% 
     mutate(total_derivados = (derivado1 * 1.33) + (derivado2 * 1.25) + (derivado3 * 1.18))
   
   
   datos_cocoa <- datos_exportacion %>%
  left_join(datos_produccion, by = c("year", "area")) %>% 
  left_join(derivados, by = c("year", "area")) %>% 
  left_join(precio_anual_nominal, by = "year") %>% 
  left_join(precio_anual_real, by = "year") %>% 
  left_join(precio_productor, by = c("year", "area")) %>% 
  left_join(tipo_cambio, by = c("year", "area")) 
   
  datos_cocoa_2 <- datos_cocoa %>% 
  select(area, year, value_export, total_production, derivado1, derivado2, derivado3,
         precio_kg_nominal, precio_kg_real, precio_local, tipo_cambio, precio_usd, total_derivados)
   
   datos_cocoa_2 <- datos_cocoa_2 %>% 
   mutate(total_export = total_derivados + value_export,
          precio_internacional_nominal = precio_kg_nominal * 1000,
          precio_internacional_real = precio_kg_real *1000,
          ln_production = log(total_production),
          ln_total_export = log(total_export),
          ln_precio_productor_usd = log(precio_usd),
          ln_tipo_cambio = log(tipo_cambio),
          ln_precio_internacional_nominal = log(precio_internacional_nominal),
          ln_precio_internacional_real = log(precio_internacional_real)) 

   tabla_completa <- datos_cocoa_2 %>%
     mutate(area = case_when(
       area == "Ghana" ~ "gh",
       area == "Côte d'Ivoire" ~ "cdi",
       TRUE ~ area
     )) %>%
     pivot_wider(
       id_cols = year,
       names_from = area,
       values_from = -c(year, area), 
       names_glue = "{.value}_{area}") %>% 
     mutate(smuggling_incentive = precio_usd_gh/precio_usd_cdi)
                 
datos_regresion <- tabla_completa |> 
  select(ln_total_export_gh, ln_precio_productor_usd_gh, 
         ln_precio_internacional_real_gh, smuggling_incentive)
  
datos_ghana <- datos_cocoa_2 %>% 
  filter("Ghana" == area)

datos_cdi <- datos_cocoa_2 %>% 
  filter("Côte d'Ivoire" == area)
  
write.csv(datos_ghana, "input/datos_ghana.csv", row.names = FALSE)
write.csv(datos_cdi, "input/datos_cdi.csv", row.names = FALSE)
write.csv(datos_regresion, "input/datos_regresion.csv", row.names = FALSE)
write.csv(tabla_completa, "input/tabla_completa.csv", row.names = FALSE)
