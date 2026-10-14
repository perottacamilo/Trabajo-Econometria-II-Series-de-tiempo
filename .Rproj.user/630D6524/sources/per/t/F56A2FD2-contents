library(tidyr)
library(readxl)
library(haven)      
library(AER)
library(modelsummary) 
library(tidyverse)   
library(janitor)

setwd()

rm(list = ls())

precio_productor <- read.csv("raw/precio_productor.csv") %>% clean_names() 
datos_exportacion <- read.csv("raw/exportacion_2.csv") %>% clean_names()
precio_anual_real <- read_excel("raw/precio_anual_por_kg.xlsx", sheet = "Annual Prices (Real)",
                                skip = 8,
                                col_names = c("year", "precio_kg_real")) %>% clean_names()
derivados <- read.csv("raw/derivados_cocoa_2.csv") %>% clean_names()
precio_productor_2 <- read.csv("raw/precio_productor_tipo_cambio.csv") %>% clean_names()

precio_productor_2 <- precio_productor_2 |> 
  mutate(precio_usd = precio_local/tipo_cambio)

precio_productor <- precio_productor |> 
  select(-fuente_usd)

precio_productor_completo <- bind_rows(precio_productor, precio_productor_2) %>%
  arrange(area, year)

#Ordenar aquellas tablas que no esten en formato ancho
#Juntar todas en una sola tabla

datos_exportacion <- datos_exportacion %>% 
  rename(value_export = value) |> 
  filter(year >= 1980 & year <= 2024)

derivados <- derivados %>% 
  select(area, year, item, value) %>% 
  pivot_wider(names_from = item,
              values_from = value) %>% 
  rename(derivado1 = "Cocoa butter, fat and oil",
         derivado2 = "Cocoa paste not defatted",
         derivado3 = "Cocoa powder and cake") %>% 
  mutate(total_derivados = (derivado1 * 1.33) + (derivado2 * 1.25) + (derivado3 * 1.18))

#Poner los derivados segun su ponderacion
#Armar una variable de exportacion total con datos_expo + derivados ponderados
#Butter 1.33, paste 1.25, torta, 1.18

precio_anual_real <- precio_anual_real %>%
  filter(year >= 1980 & year <= 2024)


datos_cocoa <- datos_exportacion %>%
  left_join(derivados, by = c("year", "area")) %>% 
  left_join(precio_anual_real, by = "year") %>% 
  left_join(precio_productor_completo, by = c("year", "area"))

datos_cocoa_2 <- datos_cocoa %>% 
  select(area, year, value_export, derivado1, derivado2, derivado3, 
         precio_kg_real, precio_local, tipo_cambio, precio_usd, total_derivados)

datos_cocoa_2 <- datos_cocoa_2 %>% 
  mutate(total_export = total_derivados + value_export,
         precio_internacional_real = precio_kg_real *1000,
         ln_total_export = log(total_export),
         ln_precio_productor_usd = log(precio_usd),
         ln_tipo_cambio = log(tipo_cambio),
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
  mutate(smuggling_incentive = precio_usd_gh/precio_usd_cdi) |> 
  rename(exportacion_total_gh = total_export_gh, incentivo_contrabando = smuggling_incentive)

datos_regresion <- tabla_completa |> 
  select(exportacion_total_gh, , precio_usd_gh, precio_internacional_real_gh, incentivo_contrabando) |> 
  rename(precio_productor_usd_gh = precio_usd_gh)

datos_ghana <- datos_cocoa_2 %>% 
  filter("Ghana" == area)

datos_cdi <- datos_cocoa_2 %>% 
  filter("Côte d'Ivoire" == area)

write.csv(datos_ghana, "input/datos_ghana.csv", row.names = FALSE)
write.csv(datos_cdi, "input/datos_cdi.csv", row.names = FALSE)
write.csv(datos_regresion, "input/datos.csv", row.names = FALSE)
write.csv(tabla_completa, "input/tabla_completa.csv", row.names = FALSE)

