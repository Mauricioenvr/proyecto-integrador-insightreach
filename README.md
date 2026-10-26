# proyecto-integrador-insightreach

HENRY · DATA SCIENCE · PIM1
Proyecto Integrador — Conociendo al cliente 360°
README — Pasos y resultados
Avances 1, 2 y 3
Estudiante: Mauricio Vázquez


Contexto del proyecto
InsightReach es una empresa de marketing digital especializada en campañas personalizadas para negocios locales. Este proyecto simula el reto técnico de un candidato a Científico de Datos Junior: integrar múltiples fuentes de datos, analizarlas y generar insights accionables para optimizar la estrategia de segmentación de la empresa.
Este documento resume los pasos ejecutados y los resultados obtenidos en los Avances 1, 2 y 3 del Proyecto Integrador, desarrollados en Google Colab con Python, pandas, numpy, matplotlib y seaborn, e integrando el Yelp Fusion API.
Avance 1 — Análisis exploratorio de la base de clientes (Chicago)
Notebook: Avance_1_EDA.ipynb
Pasos ejecutados
Carga de la base de clientes (30,000 registros, 17 columnas) desde Google Drive.
Normalización de la columna ciudad_residencia (espacios, capitalización, y corrección de la abreviatura 'Nyc' a 'New York').
Filtrado por Chicago, obteniendo un subconjunto de 5,384 registros.
Detección y tratamiento de nulos: edad (0.43%) y promedio_gasto_comida (0.65%) imputados con la mediana; preferencias_alimenticias (4.98%) imputada como 'No especificado'; telefono_contacto y correo_electronico (~50% cada una) imputados como 'No disponible', dado que no son datos críticos para el análisis de comportamiento.
Detección de duplicados: ninguno, ni por fila completa ni por id_persona (verificado también sobre la base completa de 30,000 registros).
Detección de errores: edad con valores imposibles (mínimo -5, máximo 300, 29 registros) y frecuencia_visita con valores negativos (mínimo -3, 286 registros); ambos casos se marcaron como nulos y se imputaron con la mediana.
Análisis exploratorio con tablas y gráficos: distribución por género, estrato socioeconómico, edad, ingresos, gasto promedio y su relación con el estrato.
Resultados clave
Género casi equilibrado: 50.4% masculino / 49.6% femenino.
Estrato socioeconómico en Chicago: Medio (1,798), Alto (1,511), Bajo (1,148), Muy Alto (927).
Edad promedio de 48.7 años (rango 18-80 tras la limpieza); ingresos mensuales promedio de $5,202.
La proporción de nulos en teléfono y correo (~50%) es una limitación real de la base para contacto directo, documentada como hallazgo en vez de imputarse con datos ficticios.
Avance 2 — Integración con la API de Yelp (Miami)
Notebook: Avance_2_API_Yelp.ipynb
Pasos ejecutados
Conexión al Yelp Fusion API (endpoint /v3/businesses/search) usando una API key provista por el curso, almacenada de forma segura en Colab Secrets (nunca escrita en texto plano en el código).
Se seleccionó Miami como ciudad de búsqueda, siguiendo la muestra de referencia entregada para este avance.
Implementación de paginación (parámetro offset) para superar el límite de 50 resultados por petición, hasta agotar los resultados disponibles.
Extracción de campos: categoría (alias/title), id, nombre, precio, calificación, número de reseñas, distancia, coordenadas, dirección y ciudad; una fila por cada combinación negocio–categoría.
Resultado: 443 registros para Miami, de los cuales 272 reportan rango de precio y 171 no lo reportan.
Exportación del resultado a yelp_restaurants.csv en Google Drive, para reutilizarlo sin necesidad de volver a consultar la API.
Resultados clave
Calificaciones concentradas y altas (mayoría entre 4.0 y 4.6): mercado gastronómico maduro y bien valorado.
Se identificaron outliers dentro de varias categorías: un restaurante Mediterráneo (~2.9) y uno Asian Fusion (~3.2) muy por debajo de su categoría; un caso en New American (~3.6); y un Cocktail Bar con calificación perfecta (5.0), muy por encima de su categoría.
39% de los negocios (171 de 443) no reporta rango de precio en Yelp — limitación a considerar si se usa esta variable para segmentar recomendaciones.
No se observa que un precio más alto implique necesariamente mejor calificación.
Avance 3 — Patrones integrados y preguntas clave de negocio
Documentado como continuación de Avance_1_EDA.ipynb. A diferencia de los Avances 1 y 2, esta sección trabaja principalmente sobre la base de clientes completa (30,000 registros, las 10 ciudades), para poder comparar entre mercados antes de profundizar en Miami como caso de integración con Yelp.
1. Personas por ciudad
Ciudad
Clientes
Chicago
5,384
New York
4,769
Miami
3,186
San Diego
3,075
Dallas
2,602
Boston
2,547
Denver
2,523
Houston
2,212
Seattle
2,191
Phoenix
1,511


2. Distribución por estrato socioeconómico (base completa)
Estrato
Clientes
%
Medio
9,325
31.1%
Alto
9,038
30.1%
Bajo
6,161
20.5%
Muy Alto
5,476
18.3%


3. Ciudades con más gasto promedio en restaurantes
Ciudad
Gasto promedio
Miami
$39.82
New York
$37.12
Seattle
$36.86
Phoenix
$36.54
San Diego
$34.75
Denver
$34.26
Boston
$29.49
Dallas
$28.70
Houston
$26.02
Chicago
$25.53

Miami lidera el gasto promedio; Chicago —la ciudad con más clientes— es, en contraste, la que menos gasta en promedio.
4. Frecuencia de visita vs. gasto promedio, según estrato
Se usó un gráfico de dispersión (scatterplot) en vez de barras o líneas, ya que se trata de dos variables numéricas más una categórica. El hallazgo principal es que la frecuencia de visita está fuertemente segmentada por estrato, con rangos que casi no se solapan: Bajo (0-3 visitas/mes), Medio (2-4), Alto (4-6), Muy Alto (4-10, el más amplio). El gasto por visita, en cambio, varía más libremente dentro de cada grupo.
5. Gasto mensual estimado vs. ingresos mensuales
Se derivó gasto_mensual_estimado = promedio_gasto_comida × frecuencia_visita, ya que la base no incluye directamente un campo de gasto mensual. La correlación con ingresos_mensuales es de 0.70 (positiva, moderada-fuerte): a mayor ingreso, mayor gasto en restaurantes, aunque con suficiente variabilidad para que otros factores (estrato, preferencias) también influyan en la segmentación.
6. Distribución de preferencias alimenticias por ciudad
Se usó un heatmap (10 ciudades × 7 categorías) por ser la forma más legible de mostrar dos variables categóricas cruzadas. Miami se distingue con 26.9% de sus clientes prefiriendo Mariscos, la proporción más alta entre las 10 ciudades, y la más baja en Carnes (16.0%). Houston, Dallas y New York lideran en preferencia por Carnes (31-32%).
7. Perfil de los clientes de mayor gasto (percentil 90 de gasto mensual estimado)
Métrica
Valor
Clientes en el grupo
2,986 (10% superior)
Umbral de gasto mensual (percentil 90)
$407.02
Gasto mensual promedio del grupo
$677.82
Estrato Muy Alto
94.3%
Estrato Alto
5.7%

Ciudades con más clientes de alto gasto: New York (521), Miami (445), Chicago (410), San Diego (348), Seattle (256). El perfil está dominado casi por completo por el estrato Muy Alto — el mejor predictor individual de este segmento.
Preferencias alimenticias del grupo de mayor gasto: Carnes (26.8%) y Vegetariano (24.7%) encabezan, seguidas de Mariscos (18.6%), Vegano (12.0%), Pescado (10.2%) y Otro (7.8%). A diferencia de lo que se podría suponer, el gasto alto no se concentra en una sola preferencia alimenticia — el grupo está repartido de forma relativamente pareja entre varias categorías, con Carnes apenas por encima de Vegetariano.
8. Ciudad con más membresías premium pagadas
Aquí es clave distinguir conteo absoluto de proporción: en número absoluto, Chicago lidera (2,215 clientes premium), pero esto refleja simplemente que Chicago tiene más clientes en total. En proporción dentro de cada ciudad —la métrica correcta para responder la pregunta— Miami lidera con 53.2% de sus clientes con membresía premium, la única ciudad donde supera la mitad de la base, seguida de San Diego (48.9%) y Seattle (46.7%). Chicago cae al 7º lugar en proporción (41.1%).
9. Consumo de alcohol vs. edad
Los clientes que consumen licor tienen una edad promedio menor (46.0 años) frente a quienes no consumen (53.6 años) — un patrón demográfico esperable, útil como dato de contexto, aunque de menor prioridad para decisiones de marketing frente a otros patrones identificados en este avance.
Preguntas clave de negocio adicionales
Más allá de la guía de referencia, se formularon dos preguntas adicionales para aprovechar variables aún no exploradas:
Pregunta: ¿Qué método de pago debería priorizar InsightReach en sus campañas en Miami?
Respuesta: Efectivo (38.3%), Tarjeta (32.3%) y App (27.4%) están relativamente repartidos, con Criptomoneda como marginal (2.0%). No hay un método dominante absoluto, por lo que las campañas digitales (vía App) tienen una audiencia sólida pero no deberían descartar canales tradicionales.
Pregunta: ¿Los restaurantes más populares (más reseñas) de Miami son también los mejor calificados?
Respuesta: No necesariamente: la correlación entre review_count y rating es de -0.18 (débil y negativa). Popularidad y calidad percibida son señales distintas — un negocio con muchas reseñas no garantiza una calificación más alta, por lo que ambas métricas deberían considerarse por separado al recomendar negocios.
Integración conceptual: oferta vs. demanda en Miami
Dado que Miami resultó ser el mercado prioritario en dos hallazgos independientes (mayor gasto promedio y mayor proporción de membresía premium), se comparó el perfil de los clientes de Miami (base propia) contra el panorama competitivo de restaurantes en Miami (Yelp), para identificar brechas entre lo que el cliente busca y lo que el mercado ofrece.
Dimensión
Demanda (clientes Miami)
Oferta (restaurantes Miami, Yelp)
Brecha
Mariscos / Seafood
26.9%
5.2%
≈ 21.7 pts
Segmento premium (membresía vs. precio muy alto)
53.2%
1.5%
≈ 51.7 pts

La brecha del segmento premium es la más amplia de las dos, y coincide con el hallazgo del punto 7 (el gasto mensual más alto se concentra en el estrato Muy Alto). Esto sugiere que el mercado de restaurantes de alta gama en Miami está sub-representado frente a la demanda real de InsightReach en esa ciudad — la oportunidad de mayor impacto identificada en este avance.


Fin del documento — Avances 1, 2 y 3. Las recomendaciones derivadas de estos hallazgos se documentan por separado en Recomendaciones.pdf.
