# Microsoft Foundry Cost Calculator

[![GitHub Pages](https://img.shields.io/badge/demo-GitHub%20Pages-0969da)](https://gabeincloud.github.io/foundry-cost-calculator/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Static HTML](https://img.shields.io/badge/app-static%20HTML%2FCSS%2FJS-orange.svg)](#uso-local)

Calculadora web estática para estimar costes orientativos de soluciones de IA basadas en **Microsoft Foundry** y **Azure OpenAI**.

Permite comparar modelos, tipos de despliegue, consumo mensual, uso estimado de caché, Batch API, PTU y costes adicionales de herramientas o servicios asociados.

> Esta herramienta está pensada para estimaciones, comparativas y conversaciones iniciales de arquitectura/FinOps. No sustituye a la página oficial de precios de Microsoft, Azure Pricing Calculator, Azure Retail Prices API, condiciones contractuales ni Azure Cost Management.

## Demo

La calculadora está publicada en GitHub Pages:

```text
https://gabeincloud.github.io/foundry-cost-calculator/
```

## Qué problema resuelve

Cuando se diseña una solución con modelos generativos, el coste no depende solo de “qué modelo uso”. También influyen:

- el número de consultas mensuales;
- cuántas llamadas al modelo hace cada consulta;
- cuántos tokens se envían al modelo;
- cuántos tokens devuelve el modelo;
- si parte del prompt puede beneficiarse de caché;
- si el workload puede ejecutarse en Batch;
- si conviene evaluar capacidad provisionada con PTU;
- el tipo de despliegue: Global, Data Zone o Regional;
- herramientas adicionales: búsqueda, grounding, conectores, memoria, observabilidad, logs, red, etc.

Esta calculadora reúne esos factores en una interfaz sencilla para hacer estimaciones rápidas y comparables.

## Para qué sirve

Puedes usarla para estimar escenarios como:

- chatbot interno de soporte;
- solución RAG sobre documentación;
- clasificación o extracción masiva de documentos;
- agentes con múltiples llamadas al modelo;
- agentes de código o automatización;
- comparación entre modelos grandes, mini y nano;
- análisis preliminar de coste entre pay-as-you-go, Batch y PTU.

## Qué calcula

La estimación principal usa una fórmula simplificada de consumo por tokens:

```text
coste mensual ≈
  tokens de entrada no cacheados × precio de entrada
+ tokens de entrada cacheados × precio de caché
+ tokens de salida × precio de salida
+ extras mensuales
```

La comparación con PTU se calcula aparte:

```text
coste PTU ≈ número de PTUs × precio PTU/hora × horas desplegadas
```

La herramienta muestra además:

- coste mensual estimado;
- coste aproximado por consulta;
- tokens mensuales estimados;
- desglose entre entrada, caché, salida y extras;
- comparativa de modelos usando el mismo patrón de consumo;
- avisos cuando una opción usa una tarifa aproximada o no está disponible.

## Modelos de coste incluidos

### 1. Standard / Pay-as-you-go

Pago por uso basado en tokens. Los tokens de entrada y los tokens de salida se facturan por separado. La tarifa depende del modelo, versión, deployment type, región, moneda, meter y condiciones comerciales.

Recomendado para:

- pilotos;
- prototipos;
- pruebas de modelos;
- cargas variables;
- escenarios donde todavía no se conoce el patrón real de uso.

Referencia oficial:

- Azure OpenAI Service pricing: https://azure.microsoft.com/pricing/details/azure-openai/
- Azure AI Foundry Models pricing: https://azure.microsoft.com/pricing/details/ai-foundry-models/aoai/

### 2. Prompt caching

Azure OpenAI puede aplicar caché automáticamente en modelos compatibles cuando el prompt es suficientemente largo y mantiene un prefijo repetido.

La calculadora **no activa la caché**. El porcentaje de caché solo estima qué parte de la entrada esperas que aparezca como `cached_tokens`.

Recomendación práctica:

1. Empieza con `0%`.
2. Ejecuta tráfico real.
3. Revisa `cached_tokens` en la respuesta de la API o en tu telemetría.
4. Ajusta la calculadora con el valor observado.

Buenas prácticas para mejorar la reutilización de caché:

- mantener instrucciones y contexto estable al inicio del prompt;
- evitar cambios innecesarios en el prefijo;
- colocar el contenido más variable al final;
- medir el valor real en telemetría, no asumirlo.

Referencia oficial:

- Prompt caching with Azure OpenAI: https://learn.microsoft.com/azure/foundry/openai/how-to/prompt-caching

### 3. Batch API

Batch está pensado para trabajos asíncronos y no interactivos. Es útil cuando no necesitas respuesta inmediata.

Casos habituales:

- clasificación de documentos;
- extracción de información;
- enriquecimiento de datos;
- generación offline;
- procesamiento nocturno;
- análisis masivo.

La calculadora modela Batch como descuento frente a Global Standard cuando el modelo y el deployment lo soportan.

No es apropiado para chat en tiempo real ni experiencias que requieren respuesta inmediata.

Referencia oficial:

- Azure OpenAI Batch API: https://learn.microsoft.com/azure/foundry/openai/how-to/batch

### 4. Provisioned Throughput / PTU

PTU es capacidad provisionada. Se factura por PTUs desplegadas y horas, aunque no se use toda la capacidad.

Puede ser útil para:

- producción;
- cargas estables;
- tráfico predecible;
- requisitos de latencia;
- throughput alto;
- escenarios donde la capacidad garantizada sea importante.

No existe un punto de equilibrio universal. Debe evaluarse con datos reales:

- requests por minuto;
- tokens medios de entrada;
- tokens medios de salida;
- tasa real de caché;
- latencia requerida;
- horas desplegadas;
- región y tipo de deployment;
- reservas mensuales o anuales, si aplican.

Referencias oficiales:

- Provisioned throughput: https://learn.microsoft.com/azure/foundry/openai/concepts/provisioned-throughput
- Provisioned throughput billing: https://learn.microsoft.com/azure/foundry/openai/concepts/provisioned-throughput-billing

## Tipos de despliegue

La calculadora permite comparar el impacto de distintos tipos de despliegue:

### Global

Microsoft puede procesar la inferencia en regiones disponibles globalmente. Suele ser la opción más económica y flexible.

### Data Zone

El procesamiento se mantiene dentro de una zona de datos definida por Microsoft, como EU o US, según disponibilidad del modelo.

### Regional

El procesamiento se asocia a una región concreta. Puede ser necesario por requisitos de residencia, latencia o control operativo, pero la disponibilidad y el precio dependen del modelo y de la región.

Referencia oficial:

- Deployment types for Microsoft Foundry Models: https://learn.microsoft.com/azure/foundry/foundry-models/concepts/deployment-types
- Region availability for Foundry Models: https://learn.microsoft.com/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure-region-availability

## Agentes y herramientas

Un agente no es solo una llamada al modelo. En una solución real conviene separar:

- tokens de entrada del modelo;
- tokens de salida del modelo;
- tokens cacheados;
- llamadas a herramientas;
- búsqueda documental o vectorial;
- grounding;
- conectores;
- memoria;
- almacenamiento;
- observabilidad y logs;
- red privada;
- seguridad;
- soporte y operación.

La calculadora incluye un campo de **extras mensuales** para añadir estos costes como línea separada.

Referencias oficiales:

- Microsoft Foundry pricing: https://azure.microsoft.com/pricing/details/microsoft-foundry/
- Foundry Agent Service pricing: https://azure.microsoft.com/pricing/details/foundry-agent-service/
- Foundry Tools pricing: https://azure.microsoft.com/pricing/details/foundry-tools/

## Buenas prácticas FinOps

Para usar la calculadora de forma responsable:

1. **Empieza con pay-as-you-go.** Mide uso real antes de comprometer capacidad.
2. **Usa el modelo más pequeño que cumpla requisitos.** No siempre el modelo más grande es la mejor opción económica.
3. **Separa entrada y salida.** La salida suele ser más cara que la entrada.
4. **Mide caché real.** Usa `cached_tokens`, no estimaciones optimistas.
5. **Controla reintentos y bucles.** Un agente mal acotado puede multiplicar llamadas.
6. **Define límites TPM/RPM.** Ayudan a controlar consumo y evitar sorpresas.
7. **Etiqueta recursos.** Usa equipo, aplicación, entorno y centro de coste.
8. **Separa costes de herramientas.** Search, logs, almacenamiento, conectores y red deben modelarse aparte.
9. **Configura presupuestos y alertas.** Usa Azure Cost Management.
10. **Reconcilia estimaciones contra consumo real.** Agrupa por recurso, meter y proyecto cuando aplique.

Referencia oficial:

- Plan and manage costs for Microsoft Foundry: https://learn.microsoft.com/azure/foundry/concepts/manage-costs
- Azure Pricing Calculator: https://azure.microsoft.com/pricing/calculator/
- Azure Retail Prices API: https://learn.microsoft.com/rest/api/cost-management/retail-prices/azure-retail-prices

## Actualización de precios

Los precios de la calculadora están incluidos como valores editables dentro del HTML. Esto permite:

- ajustar precios manualmente;
- exportar una tabla JSON;
- importar una tabla JSON actualizada;
- versionar precios en el repositorio.

Para un uso más riguroso, se recomienda mantener una tabla de precios versionada a partir de fuentes oficiales como:

- Azure OpenAI Service Pricing;
- Azure AI Foundry Models Pricing;
- Microsoft Foundry Pricing;
- Azure Retail Prices API;
- Azure Pricing Calculator;
- exportaciones de Azure Cost Management.

Metadatos recomendados para cada precio:

```json
{
  "model": "gpt-4o-mini",
  "model_version": "2024-07-18",
  "provider": "openai",
  "deployment_type": "global",
  "region": "global",
  "input_price_1m": 0.15,
  "cached_input_price_1m": 0.075,
  "output_price_1m": 0.60,
  "currency": "USD",
  "effective_date": "YYYY-MM-DD",
  "source": "official pricing page or Azure Retail Prices API",
  "meter_id": ""
}
```

## Limitaciones

Esta herramienta tiene limitaciones intencionadas:

- no consulta automáticamente APIs de Microsoft;
- no conoce precios contractuales privados;
- no sustituye a Azure Pricing Calculator;
- no sustituye a Azure Cost Management;
- no valida automáticamente disponibilidad por región/modelo en tiempo real;
- algunos precios Data Zone o Regional pueden estar aproximados;
- Batch no está disponible para todos los modelos, regiones o tipos de despliegue;
- PTU debe dimensionarse con datos reales;
- modelos de terceros pueden facturar mediante Azure Marketplace y tener condiciones comerciales distintas.

## Uso local

Este proyecto es una página HTML estática. No requiere backend, base de datos, build step ni instalación de paquetes.

Clona el repositorio:

```bash
git clone https://github.com/GabeinCloud/foundry-cost-calculator.git
cd foundry-cost-calculator
```

Abre el archivo:

```text
index.html
```

O levanta un servidor local:

```bash
python -m http.server 8000
```

Y abre:

```text
http://localhost:8000
```

## Publicación con GitHub Pages

Estructura recomendada:

```text
.
├── index.html
├── README.md
└── LICENSE
```

Para publicar:

1. Ve a **Settings**.
2. Entra en **Pages**.
3. Selecciona **Deploy from a branch**.
4. Elige la rama `main`.
5. Elige `/ (root)`.
6. Guarda y espera a que termine el workflow de Pages.

GitHub Pages sirve el archivo llamado exactamente `index.html` desde la fuente configurada.

## Privacidad y seguridad

La herramienta es estática y no debe contener secretos.

No publiques:

- claves de API;
- nombres de clientes;
- precios contractuales privados;
- datos internos de consumo;
- arquitecturas confidenciales;
- escenarios reales no anonimizados;
- información sensible de negocio.

Para uso privado, mantén el repositorio privado y abre el HTML localmente, o publícalo en una plataforma interna con autenticación.

## Estructura recomendada del repositorio

Para la rama publicada, se recomienda dejar únicamente:

```text
LICENSE
README.md
index.html
```

Evita mantener copias antiguas como:

```text
index (1).html
preview.html
```

salvo que estén documentadas como versiones de prueba o histórico.

## Roadmap sugerido

Ideas de mejora para próximas versiones:

- importar precios desde un JSON versionado;
- generar precios desde Azure Retail Prices API;
- añadir matriz explícita de disponibilidad por modelo, región y deployment type;
- añadir escenarios guardables;
- exportar resultados a CSV o JSON;
- añadir modo oscuro;
- añadir comparación por coste por tarea exitosa;
- añadir ejemplos de RAG, agentes y Batch con desglose de herramientas.

## Contribuciones

Las contribuciones son bienvenidas. Puedes abrir un issue o pull request para:

- corregir precios;
- añadir modelos;
- mejorar documentación;
- proponer escenarios;
- mejorar accesibilidad;
- añadir soporte para nuevas opciones de despliegue.

## Licencia

MIT License.

## Aviso

Herramienta independiente, sin afiliación con Microsoft. Los resultados son estimaciones para planificación y comparación. Valida siempre presupuestos finales contra fuentes oficiales de Microsoft, condiciones contractuales, Azure Pricing Calculator, Azure Retail Prices API y Azure Cost Management.
