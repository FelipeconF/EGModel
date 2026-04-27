# Trabajo Final - Crecimiento Económico - ITAM 2026 I

Replicación numérica de las Figuras 14 y 15 de **Bergoeing, Kehoe, Kehoe y Soto (2002)**, *"A Decade Lost and Found: Mexico and Chile in the 1980s"*, Review of Economic Dynamics 5, 166–205.

## Archivos

| Archivo | Descripción |
|---|---|
| `Proyecto.m` | Script principal. Carga parámetros, calibración, datos, corre los 4 experimentos y genera las figuras. |
| `func_cpo.m` | Función con las condiciones de primer orden del modelo (sistema de 3T ecuaciones). |
| `compute_ss.m` | Función auxiliar que calcula el estado estacionario para una (A, τ) dadas. |
| `compute_aggregates.m` | Función auxiliar que extrae Y, K/Y, L/N, Y/N de la solución. |
| `Trabajo_Final_Bergoeing.docx` | Ensayo (5 páginas) discutiendo los resultados. |
| `Trabajo_Final_Bergoeing.pdf` | Versión PDF del ensayo. |
| `figura14_mexico.png` | Figura 14 replicada (México: Base Case y Tax Reform). |
| `figura15_chile.png` | Figura 15 replicada (Chile: Base Case y Tax Reform). |

## Cómo correr el código

1. Abre MATLAB en el directorio donde están los 4 archivos `.m`.
2. Ejecuta `Proyecto.m`.
3. El script:
   - Imprime el `k0` calibrado para cada país.
   - Corre `fsolve` 4 veces (México BC, México TR, Chile BC, Chile TR).
   - Genera las dos figuras (Figura 14 y Figura 15).
   - Imprime un resumen numérico al final.

## Nota importante sobre los datos

El script tiene incrustadas series **aproximadas** de PTF (digitalizadas de la Figura 13 del paper) y de las series observadas (Y/N, K/Y, L/N) digitalizadas de las Figuras 14 y 15. Si dispones del archivo `Decade_Lost_and_Found_Data.xls`, puedes reemplazar los arreglos `A_MEX_data` y `A_CHL_data` (líneas 53-60 de `Proyecto.m`) con las series exactas:

- **PTF detrended 1.4% México**: columna **C.39** del archivo.
- **PTF detrended 1.4% Chile**: columna **C.36** del archivo.

Análogamente, las series observadas pueden cargarse de:

- **Y/N detrended 2% México**: columna **C.14**.
- **Y/N detrended 2% Chile**: columna **C.13**.
- **K/Y**: cociente entre **C.5/C.7** (capital) y **C.1/C.2** (PIB real).
- **L/N**: cociente entre **C.9/C.11** (insumo laboral) y la población 15-64.

Sustitución sugerida con `readmatrix`:

```matlab
T_MEX = readmatrix('Decade_Lost_and_Found_Data.xls','Sheet','MEX','Range','...');
A_MEX_data = T_MEX(:,39).';   % columna C.39
A_MEX_obs  = A_MEX_data / A_MEX_data(1);   % normalización 1980 = 1
```

## Calibración

| Parámetro | Valor | Justificación |
|---|---|---|
| α (capital share) | 0.30 | BKKS (2002), p. 184 |
| δ (depreciación) | 0.05 | BKKS (2002), p. 185 |
| β (descuento) | 0.94 | calibrado para K/Y(1980) ≈ 1.9 |
| γ_Méx (peso consumo) | 0.30 | BKKS (2002), p. 188 |
| γ_Chl (peso consumo) | 0.28 | BKKS (2002), p. 188 |
| τ pre-1988 | 0.40 | "high distortion" pre-reforma |
| τ post-1988 | 0.12 | reforma fiscal (BKKS, p. 188) |
| T (horizonte) | 101 | suficiente para alcanzar EE |

## Estructura del modelo

El consumidor representativo maximiza:

$$\max \sum_{t=1980}^\infty \beta^{t-1980} \left[ \gamma \log C_t + (1-\gamma)\log(\bar N - L_t) \right]$$

sujeto a:

$$C_t + K_{t+1} - (1-\delta)K_t = w_t L_t + (1-\tau_t)(r_t - \delta)K_t + T_t$$

Tecnología: $Y_t = A_t K_t^\alpha L_t^{1-\alpha}$.

Las CPO son: Euler intertemporal, FOC intratemporal del trabajo y la restricción de recursos. Se resuelven 3T ecuaciones para 3T incógnitas {C_t, K_t, L_t} con `fsolve`.
