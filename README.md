# Google-Earth-Engine
Precipitação e Temperatura no Google Earth Engine 


var ERA5_TP = ee.ImageCollection('ECMWF/ERA5_LAND/DAILY_AGGR')
  .select('total_precipitation_sum')
  .filterDate('2022-01-01', '2023-12-31'); // Filtrar de 1970 a 2023


var ERA5_TP_mm = ERA5_TP.map(function(image) {
  return image.multiply(1000).copyProperties(image, ['system:time_start']);
});


var ERA5_T2M = ee.ImageCollection('ECMWF/ERA5_LAND/MONTHLY_AGGR')
  .select(['temperature_2m_max', 'temperature_2m_min'])
  .filterDate('1970-01-01', '2023-12-31'); // Filtrar de 1970 a 2023


var ERA5_T2M_Celsius = ERA5_T2M.map(function(image) {
  return image.subtract(273.15).copyProperties(image, ['system:time_start']);
});


var precipitacaoMensalChart = ui.Chart.image.series({
  imageCollection: ERA5_TP_mm,
  region: geometriaEstado,
  reducer: ee.Reducer.mean(),
  scale: 1000,
  xProperty: 'system:time_start'
}).setOptions({
  title: 'Precipitação Mensal Média (1970-2023)',
  vAxis: {
    title: 'Precipitação (mm)',
    titleTextStyle: {fontSize: 14, italic: false, bold: true},
    gridlines: {count: 6}
  },
  hAxis: {
    title: 'Ano-Mês',
    titleTextStyle: {fontSize: 14, italic: false, bold: true},
    format: 'yyyy-MM',
    gridlines: {count: 12},
    viewWindow: {
      min: new Date(2022, 0, 1).getTime(),
      max: new Date(2023, 11, 31).getTime()
    }
  },
  series: {
    0: {
      color: '#1f77b4',
      lineWidth: 2,
      pointSize: 4,
      dataOpacity: 1.0
    }
  },
  backgroundColor: {fill: 'transparent'},
  chartArea: {backgroundColor: 'transparent'},
  legend: {position: 'none'}
});


var temperaturaChart = ui.Chart.image.series({
  imageCollection: ERA5_T2M_Celsius,
  region: geometriaEstado,
  reducer: ee.Reducer.mean(),
  scale: 1000,
  xProperty: 'system:time_start'
}).setOptions({
  series: {
    0: {color: '#ff7f0e', lineWidth: 2, pointSize: 4, dataOpacity: 1.0}, // Temperatura Máxima
    1: {color: '#2ca02c', lineWidth: 2, pointSize: 4, dataOpacity: 1.0}  // Temperatura Mínima
  },
  title: 'Temperatura Máxima e Mínima (1970-2023)',
  vAxis: {
    title: 'Temperatura (°C)',
    titleTextStyle: {fontSize: 14, italic: false, bold: true},
    gridlines: {count: 6}
  },
  hAxis: {
    title: 'Ano-Mês',
    titleTextStyle: {fontSize: 14, italic: false, bold: true},
    format: 'yyyy-MM',
    gridlines: {count: 12},
    viewWindow: {
      min: new Date(2022, 0, 1).getTime(),
      max: new Date(2023, 11, 31).getTime()
    }
  },
  backgroundColor: {fill: 'transparent'},
  chartArea: {backgroundColor: 'transparent'},
  legend: {position: 'none'}
});

print(precipitacaoMensalChart);
print(temperaturaChart);
