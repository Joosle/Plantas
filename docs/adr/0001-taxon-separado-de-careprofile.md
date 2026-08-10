# Taxon y CareProfile son entidades separadas

La identificación de plantas degrada a **género** de forma rutinaria (~85 % de acierto frente a ~70 % en especie, según la investigación del issue [#5](https://github.com/Joosle/Plantas/issues/5)), y las fuentes de cuidado publican sus hechos al rango que les conviene — el `ks` de WUCOLS vive además en una taxonomía distinta de GBIF. Es decir: **el rango al que sabemos identificar y el rango al que existen los cuidados no coinciden, y ese es el caso normal, no el borde**. Por eso separamos `Taxon` (identidad en GBIF, a cualquier rango, con enlace al padre) de `CareProfile` (hechos citables, colgados del rango donde la fuente los tenga y resueltos subiendo el árbol).

## Considered Options

- **`Plant → Species → cuidados`**, la cadena obvia. Rechazada porque una planta identificada solo a género no tiene `Species` y el modelo se rompe el primer día — habría que inventar especies falsas o dejar la mitad de las plantas fuera del motor.
- **Un solo `Species` con campo `rank` y campos de cuidado anulables.** Una entidad menos, pero mezcla identidad y hechos en la misma fila: la procedencia de cada dato se difumina justo donde el mapa exige trazabilidad, y no permite que los cuidados vivan a un rango distinto del identificado.
- **Copiar los cuidados sobre la `Plant` al darla de alta.** Lectura trivial y offline puro, pero cada planta arrastra su copia vieja cuando la fuente mejora o cuando se corrige una identificación errónea.

## Consequences

`taxonId` es anulable en `Plant`: «sin identificar» es un estado de primera clase, reintentable, y nunca un requisito de alta. Resolver el `CareProfile` de una planta no es una lectura directa sino un recorrido hacia arriba por la cadena de taxones hasta encontrar hechos.
