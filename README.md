# Ejemplo de Uso de la API de FDX LEGACY

## Descripción

Este proyecto es un ejemplo de cómo utilizar la API de FDX para obtener datos y visualizarlos en un gráfico utilizando Chart.js. El código HTML proporcionado incluye un formulario de selección para diferentes sitios y un gráfico que muestra los niveles de datos obtenidos de la API.

## Contenido

El archivo `index.html` contiene la estructura principal de la página, el formulario de selección de sitios, y el contenedor del gráfico. Además, incluye los scripts necesarios para obtener los datos de la API y renderizar el gráfico.

### Respuesta de la API
La respuesta de la API devuelve los siguientes parámetros:

- id_reading: ID de la lectura.
- id: ID del sitio.
- id_probe: ID de la sonda.
- id_site: ID del sitio.
- distancia: Distancia medida.
- nivel: Nivel medido.
- temperatura: Temperatura medida.
- humedad: Humedad medida.
- hora: Hora de la lectura.
- senal: Señal de la lectura.
- lac: LAC (Location Area Code).
- cid: CID (Cell ID).
- red: Tipo de red.
- bateria: Nivel de batería.
- ip: Dirección IP.
- chip: Chip utilizado.
- telefono: Número de teléfono asociado.
- tem: Temperatura.
- met: Medida.
- npm: Nombre del punto de medición.
- valh: Valor alto.
- vall: Valor bajo.
- ct: Tiempo de ciclo.
- dts: Datos.
- serie: Número de serie.
- modelo: Modelo del dispositivo.
- created_at: Fecha de creación del registro.
- updated_at: Fecha de la última actualización del registro.
- marca: Marca del dispositivo.
- id_modbus: ID Modbus.
- low: Valor bajo.
- high: Valor alto.
- alarmLevel: Nivel de alarma.
- tem1: Temperatura 1.
- met1: Medida 1.
- last: Última lectura.
- cota: Cota.
- user: Usuario.
- updated_last: Última actualización.
- contador: Contador.
- name: Nombre del sitio.
- address: Dirección del sitio.
- latitude: Latitud.
- longitude: Longitud.
- cota_terreno: Cota del terreno.
- desborde: Desborde.
- predesborde: Predesborde.
- deleted_at: NO APLICA.



## Estructura del Código

### HTML

El código HTML contiene:

- Un encabezado con las referencias a Bootstrap y Chart.js.
- Un cuerpo que incluye un formulario de selección de sitios y un contenedor para el gráfico.

```HTML
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html" charset="UTF-8" />
  <title>Chart Example</title>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      padding: 20px;
    }
    #chart {
      background-color: white;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="form-group">
      <select id="siteSelect" class="form-control">
        <option value="47">RN3 y Rio Matanza</option>
        <option value="49">AU Ezeiza-Cañuelas y Arr. Cañuelas</option>
        <option value="50">Arroyo Morales-Calle Manuel Castilla Hidalgo</option>
        <option value="51">Río Matanza-Riachuelo. AU Riccheri</option>
      </select>
    </div>
    <div class="card">
      <div class="card-body">
        <canvas id="chart"></canvas>
      </div>
    </div>
  </div>

```

##JavaScript
El script JavaScript realiza las siguientes funciones:

fetchDataAndCreateChart: Esta función se encarga de obtener los datos de la API y crear el gráfico.
Calcula la fecha de inicio y fin (últimos 7 días).
Construye la URL de la API con los parámetros necesarios.
Realiza una petición fetch a la API.
Procesa los datos recibidos y los utiliza para actualizar el gráfico.

```javascript

function fetchDataAndCreateChart(siteId) {
  const endDate = new Date();
  const startDate = new Date(endDate.getTime() - 7 * 24 * 60 * 60 * 1000);
  const formattedStartDate = formatDateTime(startDate);
  const formattedEndDate = formatDateTime(endDate);

  const user = "fernandez.mauro@inta.gob.ar";

  const apiUrl = `https://apis.fdx-ingenieria.com.ar/api_new?user=${user}&site_id=${siteId}&query=filter_site&date=${formattedStartDate}@${formattedEndDate}`;

  fetch(apiUrl)
    .then(response => response.json())
    .then(data => {
      const labels = data.map(entry => entry.hora).reverse();
      const values = data.map(entry => entry.nivel).reverse();

      const ctx = document.getElementById('chart').getContext('2d');
      if (window.myChart) {
        window.myChart.destroy();
      }
      window.myChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: labels,
          datasets: [{
            label: 'Nivel',
            data: values,
            borderColor: 'blue',
            fill: false
          }]
        },
        options: {
          responsive: true,
          scales: {
            x: {
              display: true,
              title: {
                display: true,
                text: 'Hora'
              }
            },
            y: {
              display: true,
              title: {
                display: true,
                text: 'Nivel'
              }
            }
          }
        }
      });
    });
}
```
### formatDateTime: Esta función formatea la fecha y hora en un formato compatible con la API.

```javascript

function formatDateTime(dateTime) {
  const year = dateTime.getFullYear();
  const month = String(dateTime.getMonth() + 1).padStart(2, '0');
  const day = String(dateTime.getDate()).padStart(2, '0');
  const hours = String(dateTime.getHours()).padStart(2, '0');
  const minutes = String(dateTime.getMinutes()).padStart(2, '0');
  const seconds = String(dateTime.getSeconds()).padStart(2, '0');
  return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}

```

### Event Listener: Se añade un event listener al select para actualizar el gráfico cada vez que se selecciona un sitio diferente.

```javascript

const siteSelect = document.getElementById('siteSelect');
siteSelect.addEventListener('change', function() {
  const selectedSiteId = this.value;
  fetchDataAndCreateChart(selectedSiteId);
});

// Fetch data and create the initial chart with the default site
const defaultSiteId = siteSelect.value;
fetchDataAndCreateChart(defaultSiteId);

```
## USO
Clona el repositorio y abre el archivo index.html en tu navegador web.
Selecciona un sitio del menú desplegable para ver los datos correspondientes en el gráfico.

