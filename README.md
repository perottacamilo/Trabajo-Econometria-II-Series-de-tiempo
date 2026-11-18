# Contrabando y exportaciones de cacao en Ghana: 1980-2024

Análisis de series de tiempo sobre si el incentivo al contrabando hacia Costa de Marfil explica las exportaciones de cacao de Ghana después de las reformas de 1983.

Trabajo práctico de **Fenómenos Dinámicos Multivariados** (Econometría II, cátedra Mirta González), Facultad de Ciencias Económicas, Universidad de Buenos Aires.

**Autor:** Camilo Perotta

---

## Pregunta de investigación

Bulíř (1998) mostró que, entre 1950 y 1996, el ratio entre el precio al productor de Ghana y el de Costa de Marfil ayudaba a explicar la oferta de cacao registrada en Ghana: cuando el precio marfileño era mucho más alto, el cacao se contrabandeaba y las exportaciones oficiales caían.

En 1983 Ghana inició el Programa de Recuperación Económica (ERP), que subió el precio pagado a los productores y redujo esa brecha. Este trabajo evalúa si, para **1980-2024**, el incentivo al contrabando sigue explicando las exportaciones, controlando por el precio internacional y el precio al productor.

## Resultados principales

- **Corto plazo:** los correlogramas cruzados (con preblanqueo) no muestran rezagos significativos del incentivo sobre las exportaciones, salvo uno aislado (k = −11) con signo contrario al esperado. Por eso no se estima un modelo de función de transferencia.
- **Causalidad de Granger:** el incentivo no causa ni es causado por las exportaciones. En los precios hay algunos rechazos, pero cambian con los rezagos y con el uso de logaritmos.
- **Largo plazo (test de límites de Pesaran, Shin y Smith):** el estadístico F supera el límite superior al 1% en los cuatro modelos estimados, pero el coeficiente de largo plazo del incentivo **no es significativo** cuando se controla por la guerra civil de Costa de Marfil (TC 2004) y solo lo es cuando se la excluye.
- **Conclusión:** para 1980-2024 no hay evidencia estadística robusta de que el incentivo explique las exportaciones de Ghana. Con solo 45 observaciones y resultados sensibles a las variables de intervención, esto no implica que el diferencial de precios haya perdido importancia económica.

El detalle está en el paper: [`paper/"El_contrabando_de_cacao_en_Ghana_1980-2024.pdf"`] (paper/El_contrabando_de_cacao_en_Ghana_1980-2024.pdf)

## Estructura del repositorio

```
.
├── <proyecto>.Rproj            # abrir el proyecto desde acá
├── raw/                        # datos crudos (ver "Datos")
│   ├── datos_exportacion.csv
│   ├── datos_produccion.csv
│   ├── deflactor_pbi_usd.csv
│   ├── derivados_cocoa.csv
│   └── derivados_cocoa_2.xlsx
│   └── exportacion_2.xlsx
│   └── precio_anual_por_kg.xlsx
│   └── precio_productor.xlsx
│   └── precio_productor_tipo_cambio.xlsx
│   └── produccion_2.xlsx
│   └── tipo_cambio.xlsx
├── input/                      # bases que genera el script (entrada para EViews)
│   ├── datos.csv               #   las 4 series del análisis
│   ├── tabla_completa.csv      #   todas las variables de ambos países
│   ├── datos_ghana.csv
│   └── datos_cdi.csv
├── output/
│   └── grafico/precios_productores_gh_cdi.png
├── auxiliar/                   # algunos textos de referencia
│   └── "The Price Incentive to Smuggle and the Cocoa Supply in Ghana, 1950-96 - Aleš Bulíř"
│   └── "FAOSTAT y “Distortions to Agricultural Incentives in Ghana”, Jonathan Brooks, Andre Croppenstedt y Emmanuel Aggrey-Fynn"
│   └── "FAO - Trade reforms and food security – country case studies and synthesis."
├── eviews/
│   └── Datos Cacao
└── paper/
│   └── "El contrabando de cacao en Ghana 1980-2024"
└── presentacion/
│   └── "Contrabando de cacao en Ghana 1980-2024"
```

## Datos

Todas las series son anuales, 1980-2024 (45 observaciones).

| Variable | Descripción | Unidad | Fuente |
|---|---|---|---|
| `exportacion_total_gh` | Exportaciones totales de Ghana: granos de cacao más derivados (manteca, pasta y polvo/torta) expresados en equivalente grano | toneladas | FAOSTAT; factores de conversión del ICCO |
| `precio_productor_usd_gh` | Precio pagado al productor en Ghana | USD por tonelada | FAOSTAT; para los años sin dato en USD, precio local dividido por el tipo de cambio |
| `precio_internacional_real_gh` | Precio internacional del cacao en términos reales | USD reales por tonelada | Banco Mundial, Commodity Markets (precios anuales reales) |
| `incentivo_contrabando` | Precio al productor de Ghana / precio al productor de Costa de Marfil, ambos en USD | ratio | Cálculo propio con datos de FAOSTAT y tipos de cambio |

**Notas sobre las series:**
- Un ratio **menor a 1** significa que el productor marfileño recibe más que el ghanés (más incentivo a contrabandear). Se espera un efecto positivo del ratio sobre las exportaciones registradas.
- Los derivados se convierten a equivalente grano con los factores de ICCO (2010), artículo 34: manteca ×1,33; pasta ×1,25; polvo y torta ×1,18.
- Para Ghana se usa el tipo de cambio del mercado negro, porque antes del ERP la brecha con el tipo oficial era muy grande (Brooks, Croppenstedt y Aggrey-Fynn). Para Costa de Marfil se usa el tipo de cambio oficial, ya que el franco CFA está atado al franco francés (y al euro).
- La variable exportada de R precio_productor_usd_gh fue deflactada en EViews usando la serie de datos de deflactor de PBI de EE.UU.  (deflactor del PBI de EE.UU. dolares constantes 2015, Banco Mundial, `NY.GDP.DEFL.ZS`): en EViews la variable pasa a llamarse 'precio_productor_real'.

Otras fuentes consultadas: Banco Mundial (World Development Indicators), ICCO (precios diarios e informe anual 2024-2025), FRED (tipo de cambio de Costa de Marfil), FAO y Dordunoo sobre el mercado cambiario de Ghana.

## Metodología

1. **Orden de integración:** test de Dickey-Fuller aumentado (ADF).
2. **Dinámica propia de cada serie:** modelos ARMA estimados por Máxima Verosimilitud (por la cantidad limitada de observaciones y por problemas de convergencia con medias móviles), con variables de intervención (VI) hasta obtener residuos ruido blanco.
3. **Correlogramas cruzados** con preblanqueo (se filtran las exportaciones con los componentes ARMA de cada X).
4. **VAR bivariados y test de causalidad de Granger**, con tests LM de autocorrelación y verificación de raíces.
5. **Test de límites (bounds test) de Pesaran, Shin y Smith** sobre un modelo ARDL.

Nivel de significatividad: 5% en todos los tests.

### Modelos finales por serie

| Serie | Componentes |
|---|---|
| Exportaciones | Constante, tendencia determinística y VI 2023-24. Sin AR/MA (el residuo ya es ruido blanco) |
| Precio internacional real | Constante, VI 2024 y AR(2) |
| Precio al productor real | Constante, tendencia, VI 1983 y MA(1) |
| Incentivo al contrabando | Constante, SLS, TC 2004 y AR(3) + MA(2) |

### Variables de intervención

| VI | Tipo | Qué representa |
|---|---|---|
| SLS (1983-2000) | Cambio de pendiente: vale 0 hasta 1982, crece de 1 a 18 entre 1983 y 2000 y queda constante | Cierre gradual de la brecha de precios durante las reformas del ERP |
| TC 2004 | Cambio transitorio, decaimiento γ = 0,5 (elegido por AIC) | Caída del precio marfileño durante la guerra civil de Costa de Marfil |
| VI 1983 | Variable de intervencion aditiva | Aumento del precio al productor en Ghana al comienzo del ERP |
| VI 2024 | Variable de intervencion aditiva | Salto del precio internacional por la escasez mundial de cacao |
| VI 2023-24 | Variable de intervencion aditiva | Caída de las exportaciones por la crisis reciente del cacao en África Occidental |

## Cómo reproducir

### 1. Preparar los datos (R)

1. Clonar el repositorio.
2. Abrir el archivo `.Rproj` (necesario para que funcionen las rutas relativas `raw/`, `input/` y `output/`).
3. Instalar los paquetes, si hace falta:
   ```r
    install.packages(c("tidyr", "readxl", "haven", "AER", "modelsummary", "janitor", "tidyverse", "ggtext", "scales"))
   ```
4. Ejecutar `datos_cacao.R`. Genera los CSV en `input/` y el gráfico de precios al productor en `output/grafico/`.

### 2. Estimaciones (EViews)

Los tests y modelos (ADF, ARMA con variables de intervención, correlogramas cruzados, VAR, Granger y bounds test) se estimaron en **EViews 12** a partir de `input/datos.csv`. **[COMPLETAR]** Indicar dónde están el workfile y los programas, o cómo reproducir las estimaciones paso a paso.

## Software

- R (tidyverse, janitor, readxl, scales, ggtext)
- EViews 12

## Referencias

- Bulíř, A. (1998). *The Price Incentive to Smuggle and the Cocoa Supply in Ghana, 1950-96.* IMF Working Paper WP/98/88.
- Pesaran, M. H., Shin, Y. y Smith, R. J. (2001). Bounds testing approaches to the analysis of level relationships. *Journal of Applied Econometrics*, 16(3), 289-326.
- González, M. *Fenómenos Dinámicos Multivariados.* Material de cátedra, FCE-UBA.
- Brooks, J., Croppenstedt, A. y Aggrey-Fynn, E. *Distortions to Agricultural Incentives in Ghana.*
- ICCO. *Preparation of a successor agreement to the International Cocoa Agreement, 2010*, art. 34.

La lista completa de fuentes de datos está en la sección de referencias del paper.
