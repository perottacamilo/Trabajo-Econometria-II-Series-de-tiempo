install.packages("WDI")

library(tidyverse)
library(WDI)
library(skimr)
library(tidyr)

rm(list = ls())

wdi <- WDI(indicator = c(
  crecimiento_industria = "NV.IND.TOTL.KD.ZG",
  industria_pbi = "NV.IND.TOTL.ZS",
  tipo_cambio_oficial = "PA.NUS.FCRF",
  tipo_cambio_real = "PX.REX.REER",
  empleo_industrial = "SL.IND.EMPL.ZS",
  exportacion_manufacturas = "TX.VAL.MANF.ZS.UN",
  gasto_gobierno = "NE.CON.GOVT.KD.ZG",
  gasto_gobierno_pbi = "NE.CON.GOVT.ZS",
  gastos_totales_gob_pbi = "GC.XPN.TOTL.GD.ZS",
  valor_agregado_trabajador = "NV.IND.EMPL.KD",
  ied_pbi = "BX.KLT.DINV.WD.GD.ZS",
  ahorro_bruto_pbi = "NY.GNS.ICTR.ZS",
  ahorro_interno_bruto = "NY.GDS.TOTL.ZS",
  credito_privado_pbi = "FS.AST.PRVT.GD.ZS",
  credito_privado_bancos_pbi = "FD.AST.PRVT.GD.ZS"),
  country = "all",
  start = 1960,
  end = 2024)

view(wdi)
