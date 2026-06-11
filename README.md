# Calculadora de costes · Microsoft Foundry / Azure OpenAI

Estimador interactivo de costes mensuales para LLMs y agentes en Microsoft Foundry, pensado para conversaciones de pre-venta con perfiles de negocio. **v2 — correcciones FinOps aplicadas (jun 2026).**

## Qué incluye

- **31 modelos**: OpenAI (GPT-5.5, 5.4, 5.4 mini/Pro, 5.3-Codex, 5.2, 5.1, GPT-5 family + Codex/Pro, GPT-4.1, GPT-4o, o-series) y Anthropic (Claude Opus 4.8/4.7, Sonnet 4.6/4.5, Haiku 4.5 + legacy 3.x).
- **Disponibilidad por modelo**: flags de Data Zone EU / Regional EU / Batch no disponibles (el ticket avisa con ⚠ y aplica el precio correcto en vez de inventar un descuento).
- **Data Zone EU**: precio exacto cuando Microsoft lo publica (GPT-5: 1,38/11); si no, multiplicador editable marcado como "≈ aproximado".
- **Batch API**: −50% solo sobre Global Standard y solo en modelos compatibles (regla oficial).
- **Caché realista**: lectura y, para Claude, **escritura de caché** (1,25× entrada) por separado.
- **Panel PTU honesto**: coste = PTUs × $/PTU-hora × horas desplegadas, sin "break-even" inventados.
- **Tarifas versionables**: exporta/importa JSON con metadatos (fecha de verificación + fuente); regenerables desde la **Azure Retail Prices API** (sin autenticación).
- Glosario de negocio, presets de casos de uso, comparativa de modelos, conversión EUR orientativa.

## Mantener precios al día

1. Botón **Exportar JSON** → versiona `foundry-tarifas.json` en el repo.
2. Actualiza precios desde https://prices.azure.com/api/retail/prices (filtra por `serviceName eq 'Cognitive Services'` o el meter del modelo) o desde la página oficial.
3. Botón **Importar JSON** en la calculadora.

## Publicar gratis en GitHub Pages

Sube `index.html` al repo → Settings → Pages → Deploy from branch → main/(root).

## Limitaciones conocidas (honestas)

- Multiplicadores DZ/Regional son aproximación editable cuando no hay precio publicado por modelo — para cifras de contrato, usar la tarifa oficial del meter exacto.
- No cubre meters de herramientas por unidad propia (File Search, Code Interpreter por sesión, vector storage GB/día): usa el campo "extras" o añádelos aparte.
- Claude factura vía Azure Marketplace: Azure Prepayment/MACC y tagging pueden diferir.
- Reconcilia siempre contra **Azure Cost Management** (meters input/output por modelo).

Proyecto independiente sin afiliación con Microsoft.
