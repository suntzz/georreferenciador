# Georreferenciador de puntos de venta

Página web estática (un solo `index.html`). Arrastras un Excel de puntos de venta y la página busca cada establecimiento, valida la ubicación con la dirección y la ciudad, lo muestra en un mapa y te deja descargar el Excel con las coordenadas.

- Todo se procesa en el navegador. El archivo no se sube a ningún servidor.
- Las columnas se detectan solas (`SAP`, `PDV`, `CIUDAD`, `DIRECCION`, `LATITUD`, `LONGIUTD`/`LONGITUD`…) y se pueden cambiar a mano.
- Si la latitud o la longitud dicen **`sin geo`**, el registro **se deja tal cual**: no se busca y conserva `sin geo` en ambas columnas. Su estado es `SIN_GEO`.
- Servicios disponibles: **Google Maps** (Places API (New) + Geocoding API, con tu API key) u **OpenStreetMap** (gratis, más lento y con menos cobertura).
- El modo prueba procesa solo los primeros 5 registros. Las consultas se guardan en el navegador, así que al repetir no se vuelven a pagar.

## Publicar en GitHub Pages

1. Crea un repositorio en GitHub, por ejemplo `georreferenciador`.
2. Sube `index.html` a la raíz del repositorio, con "Add file → Upload files".
3. Entra a **Settings → Pages**. En "Build and deployment" elige **Deploy from a branch**, rama `main` y carpeta `/ (root)`. Guarda.
4. En uno o dos minutos la página queda en `https://TU-USUARIO.github.io/georreferenciador/`.

Con la línea de comandos:

```bash
git init
git add index.html README.md
git commit -m "Georreferenciador de PDV"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/georreferenciador.git
git push -u origin main
```

## API key de Google (importante)

La clave **no** va en el código: cada persona la pega en la página y, si quiere, la recuerda en su navegador. Como la página es pública, protégela en Google Cloud → *APIs y servicios → Credenciales → tu clave*:

- **Restricciones de aplicaciones**: elige *Sitios web* y agrega `https://TU-USUARIO.github.io/*`. Para probar en tu equipo, agrega también `http://localhost:*/*`.
- **Restricciones de API**: deja solo *Places API (New)* y *Geocoding API*.
- Opcional: en *Cuotas* pon un límite diario para evitar sorpresas de facturación.

## Probar en tu equipo

```bash
python -m http.server 8765
```

Luego abre `http://localhost:8765/index.html`.

## Estados del resultado

| Estado | Significado |
|---|---|
| `GEORREFERENCIADO` | Sede identificada: coinciden nombre, municipio y dirección (placa, nomenclatura o centro comercial). También la dirección exacta cuando el local no figura en el mapa. |
| `REVISAR` | Varias sedes posibles, la dirección o la ciudad no coinciden, o solo hay una ubicación aproximada. El candidato aparece en *Observaciones* y en el mapa como punto claro. |
| `SIN_RESULTADO` | No hubo ninguna coincidencia confiable. |
| `SIN_GEO` | El registro venía marcado `sin geo` y se conservó igual. |
| `ERROR` | Falló el servicio (por ejemplo, una clave inválida). |

La página aplica las mismas reglas de validación que el script de Python (`../georreferenciar_pdv.py`). El script sigue siendo la versión más completa: para OpenStreetMap también usa geometría de vías, interpolación de placa y una plantilla de correcciones manuales.
