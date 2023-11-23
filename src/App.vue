<template>
  <audio ref="myaudio" loop volume="0.3"></audio>
  <div ref="AnzeigeDiv" style="width:60em; height:60em;"></div>

  <n-form style="width: 30em;">
    <n-form-item>
      <transition>
      <n-card title="Current data point" v-if="dataPointSelector != -1">
        <h3>Date: </h3> <h5> {{ selectedDate }} </h5>
        <h3>Time: </h3> <h5> {{ selectedTime }} </h5>
        <h3>Position: </h3> <h5 >{{ selectedPos }} </h5>
        <h3>{{ selectedX }}: </h3> <h5> {{ selectedX_val }} </h5>
        <h3>{{ selectedY }}: </h3> <h5> {{ selectedY_val }} </h5>
        <h3>{{ selectedZ }}: </h3> <h5> {{ selectedZ_val }} </h5>
      </n-card>
      </transition>
    </n-form-item>
    <n-form-item label="X-Axis values" label-style="font-size: 20px">
      <n-select v-model:value="x_values" :options="lDataOptions"></n-select>
    </n-form-item>
    <n-form-item label="Y-Axis values" label-style="font-size: 20px">
      <n-select v-model:value="y_values" :options="lDataOptions"></n-select>
    </n-form-item>
    <n-form-item label="Z-Axis values" label-style="font-size: 20px">
      <n-select v-model:value="z_values" :options="lDataOptions"></n-select>
    </n-form-item>
    <n-form-item>
      <n-button @click="renderPlot" strong secondary type="primary" style="width: 100%;">update plot</n-button>
    </n-form-item>
    <n-form-item>
      <n-button @click="stopButton" strong secondary type="primary" style="width: 100%;">Reset selection</n-button>
    </n-form-item>
  </n-form>
  <n-layout-footer style="width: 100%; background-color: white;">
    <p style="font-size: 12px;">
      Built with <a href="https://vuejs.org/">Vue 3</a>, <a href="https://vitejs.dev/">Vite</a>, <a href="https://www.naiveui.com/en-US/os-theme/">NaiveUI</a> and <a href="https://plotly.com/graphing-libraries/">Plotly</a>, all of which are published under the <a href="https://opensource.org/license/mit/">MIT License</a>.
      Developed by the <a href="https://www.akustik.rwth-aachen.de/cms/Institut-fuer-Hoertechnik-und-Akustik/Forschung/~kbfdp/Research-in-architectural-design/?lidx=1">PAAD research group</a>.
    </p>
  </n-layout-footer>
</template>

<script setup>
  import {onMounted, ref} from 'vue';
  import { NSelect, NButton, NForm, NFormItem, NCard, NLayout, NLayoutFooter} from 'naive-ui';
  import Plotly from 'plotly.js-dist';

  import rawData from './data/data.json'

  // Defining empty arrays for every property of the data
  let date_array = [];
  let day_array = [];
  let time_array = [];
  let position_array = [];
  let snippet_array = [];
  let mean_FluctuationStrength = [];
  let std_FluctuationStrength = [];
  let mean_Level = [];
  let std_Level = [];
  let mean_LevelA = [];
  let std_LevelA = [];
  let mean_Loudness = [];
  let std_Loudness = [];
  let mean_Roughness = [];
  let std_Roughness = [];
  let mean_Sharpness = [];
  let std_Sharpness = [];
  let mean_Tonality = [];
  let std_Tonality = [];
  let soilTemperture_5Cm_Celsius = [];
  let foliage = [];
  let soilTemperture_100Cm_Celsius = [];
  let airTemperature_140CmAboveGround_Celsius = [];
  let airTemperture_2MAboveGround_Celsius = [];
  let timecategory = [];
  let solarIrradiance_WPerM2 = [];
  let cloudiness = [];
  let brightness = [];
  let atmosphericPressure_meanSea_level_hPa = [];
  let atmosphericPressureAtElevationOfLocation_222MAboveMeanSea_level = [];
  let relativeHumidity_2MAboveGround_percent = [];
  let relativeHumidity_140CmAboveGround_percent = [];
  let windVelocity_10MAboveRoof_mPerS = [];
  let windDirection_10MAboveRoof_degrees = [];
  let surfaceWetness = [];
  let time_1_array = [];
  let time2_array = [];
  let descriptions = [];
  
  let markerSymbols = [];
  
  // populating each array
  rawData.forEach( ( obj ) => {
    date_array.push( obj.date );
    day_array.push( obj.day );
    time_array.push( obj.time );
    position_array.push( obj.position );
    snippet_array.push( obj.snippet );
    mean_FluctuationStrength.push( obj.mean_FluctuationStrength_snippet );
    std_FluctuationStrength.push( obj.std_FluctuationStrength_snippet );
    mean_Level.push( obj.mean_Level_snippet);
    std_Level.push( obj.std_Level_snippet );
    mean_LevelA.push( obj.mean_LevelA_snippet );
    std_LevelA.push( obj.std_LevelA_snippet );
    mean_Loudness.push( obj.mean_Loudness_snippet );
    std_Loudness.push( obj.std_Loudness_snippet );
    mean_Roughness.push( obj.mean_Roughness_snippet );
    std_Roughness.push( obj.std_Roughness_snippet );
    mean_Sharpness.push( obj.mean_Sharpness_snippet );
    std_Sharpness.push( obj.std_Sharpness_snippet );
    mean_Tonality.push( obj.mean_Tonality_snippet );
    std_Tonality.push( obj.std_Tonality_snippet );
    soilTemperture_5Cm_Celsius.push( obj.soilTemperture_5Cm__Celsius_ );
    foliage.push( obj.foliage );
    soilTemperture_100Cm_Celsius.push( obj.soilTemperture_100Cm__Celsius_ );
    airTemperature_140CmAboveGround_Celsius.push( obj.airTemperature_140CmAboveGround__Celsius_ );
    airTemperture_2MAboveGround_Celsius.push( obj.airTemperture_2MAboveGround__Celsius_ );
    timecategory.push( obj.timecategory );
    solarIrradiance_WPerM2.push( obj.solarIrradiance_WPerM2_ );
    cloudiness.push( obj.cloudiness );
    brightness.push( obj.brightness );
    atmosphericPressure_meanSea_level_hPa.push( obj.atmosphericPressure_meanSea_level__hPa_ );
    atmosphericPressureAtElevationOfLocation_222MAboveMeanSea_level.push( obj.atmosphericPressureAtElevationOfLocation_222MAboveMeanSea_level );
    relativeHumidity_2MAboveGround_percent.push( obj.relativeHumidity_2MAboveGround__percent_ );
    relativeHumidity_140CmAboveGround_percent.push( obj.relativeHumidity_140CmAboveGround__percent_ );
    windVelocity_10MAboveRoof_mPerS.push( obj.windDirection_10MAboveRoof__degrees_ );
    windDirection_10MAboveRoof_degrees.push( obj.windDirection_10MAboveRoof__degrees_ );
    surfaceWetness.push( obj.surfaceWetness );
    time_1_array.push( obj.time__1 );
    time2_array.push( obj.time2 );

    const sDesc = "<br>Date: " + String( obj.date ) + "<br>Time: " + obj.time + "<br>Position: " + obj.position;
    descriptions.push( sDesc );

    markerSymbols.push( 'circle' );
  });

  let colorList = [];

  // Data array containing selectable measures, their corresponding position inside the array and the data
  let lDataOptions = [
    { label: 'date_array', value: 0, array: date_array },
    { label: 'position', value: 1, array: position_array },
    { label: 'mean fluctuation strength', value: 2, array: mean_FluctuationStrength },
    { label: 'std fluctuation strength', value: 3, array: std_FluctuationStrength },
    { label: 'mean Level dB', value: 4, array: mean_Level },
    { label: 'std Level dB', value: 5, array: std_Level },
    { label: 'mean Level dBA', value: 6, array: mean_LevelA },
    { label: 'std Level dBA', value: 7, array: std_LevelA },
    { label: 'mean Loudness', value: 8, array: mean_Loudness },
    { label: 'std Loudness', value: 9, array: std_Loudness },
    { label: 'mean Roughness', value: 10, array: mean_Roughness },
    { label: 'std Roughness', value: 11, array: std_Roughness },
    { label: 'mean Sharpness', value: 12, array: mean_Sharpness },
    { label: 'std Sharpness', value: 13, array: std_Sharpness },
    { label: 'mean Tonality', value: 14, array: mean_Tonality },
    { label: 'std Tonality', value: 15, array: std_Tonality },
    { label: 'soil temperture (5Cm, Celsius)', value: 16, array: soilTemperture_5Cm_Celsius },
    { label: 'foliage', value: 17, array: foliage },
    { label: 'soil temperture (100Cm, Celsius)', value: 18, array: soilTemperture_100Cm_Celsius },
    { label: 'air temperature (140Cm above ground, Celsius)', value: 19, array: airTemperature_140CmAboveGround_Celsius },
    { label: 'air temperture (2m above ground, Celsius)', value: 20, array: airTemperture_2MAboveGround_Celsius },
    { label: 'time category', value: 21, array: timecategory },
    { label: 'solar Irradiance (WPerM2)', value: 22, array: solarIrradiance_WPerM2 },
    { label: 'cloudiness', value: 23, array: cloudiness },
    { label: 'brightness', value: 24, array: brightness },
    { label: 'atmospheric pressure mean (Sea level, hPa)', value: 25, array: atmosphericPressure_meanSea_level_hPa },
    { label: 'atmospheric pressure (Elevation of location: 222m above mean sea level)', value: 26, array: atmosphericPressureAtElevationOfLocation_222MAboveMeanSea_level },
    { label: 'relative humidity (2m above ground, percent)', value: 27, array: relativeHumidity_2MAboveGround_percent },
    { label: 'relative humidity (140cm above ground, percent)', value: 28, array: relativeHumidity_140CmAboveGround_percent },
    { label: 'wind velocity (10m above roof, m/s)', value: 29, array: windVelocity_10MAboveRoof_mPerS },
    { label: 'wind direction (10m above roof, degrees)', value: 30, array: windDirection_10MAboveRoof_degrees },
    { label: 'surface wetness', value: 31, array: surfaceWetness },
    //{ label: 'time_1_array', value: 32, array: time_1_array },
    { label: 'day time', value: 32, array: time2_array },
    //{ label: 'day_array', value: 34, array: day_array },
    //{ label: 'time_array', value: 35, array: time_array },
    //{ label: 'snippet_array', value: 36, array: snippet_array },
  ];

  let x_values = ref( lDataOptions.find( obj => obj.label == 'air temperature (140Cm above ground, Celsius)').value ) // ref( 19 ); 
  let y_values = ref( lDataOptions.find( obj => obj.label == 'std Roughness').value ) // ref( 11 );
  let z_values = ref( lDataOptions.find( obj => obj.label == 'day time').value ) // ref( 33 );

  // References to DOM elements PLOT and AUDIO
  const myaudio = ref(null);
  const AnzeigeDiv = ref(null);

  // Reference to last clicked data point. -1 if none selected
  let dataPointSelector = ref(-1);

  // References to Date, Time and Pos info. Used to make Info Card in DOM reactive
  let selectedDate = ref( "" );
  let selectedTime = ref( "" );
  let selectedPos = ref( "" );

  // Reference to selected X, Y and Z measures. Used to make Info Card in DOM reactive
  let selectedX = ref( "" );
  let selectedY = ref( "" );
  let selectedZ = ref( "" );

  // Reference to value of currently selected point in X, Y and Z. Again for the Info Card
  let selectedX_val = ref( "" );
  let selectedY_val = ref( "" );
  let selectedZ_val = ref( "" );

  function renderPlot( )
  {
    for( let i = 0; i < lDataOptions[x_values.value].array.length; i++ )
    {
      const xValueList = lDataOptions[x_values.value].array;
      const yValueList = lDataOptions[y_values.value].array;
      const zValueList = lDataOptions[z_values.value].array;

      const MIN_RGB_VAL = 20;
      const MAX_RGB_VAL = 235;

      let r_val = mapValueToRange( xValueList[i], Math.min(...xValueList), Math.max(...xValueList), MIN_RGB_VAL, MAX_RGB_VAL );
      let g_val = mapValueToRange( yValueList[i], Math.min(...yValueList), Math.max(...yValueList), MIN_RGB_VAL, MAX_RGB_VAL );
      let b_val = mapValueToRange( zValueList[i], Math.min(...zValueList), Math.max(...zValueList), MIN_RGB_VAL, MAX_RGB_VAL );

      colorList.push( `rgb(${r_val}, ${g_val}, ${b_val})` );
    }

    let trace = {
      x: lDataOptions[x_values.value].array,
      y: lDataOptions[y_values.value].array,
      z: lDataOptions[z_values.value].array,
      mode: 'markers',
      type: 'scatter3d',
      name: 'test',
      marker: { symbol: markerSymbols, color: colorList, opacity: 1, line: { width: 1 } },
      text: descriptions
    }
  
    const layout = {
      autosize: true,
      showlegend: false,
      scene: {
          xaxis: {
              type: 'linear',
              zeroline: true,
              title: lDataOptions[x_values.value].label,
          },
          yaxis: {
              type: 'linear',
              zeroline: true,
              title: lDataOptions[y_values.value].label,
          },
          zaxis: {
              zeroline: true,
              title: lDataOptions[z_values.value].label,
          },
      },
    };

    selectedX.value = lDataOptions[ x_values.value ].label;
    selectedY.value = lDataOptions[ y_values.value ].label;
    selectedZ.value = lDataOptions[ z_values.value ].label;

    Plotly.newPlot( AnzeigeDiv.value, [trace], layout, { displayModeBar: false } );

    AnzeigeDiv.value.on('plotly_click', ( data ) => {      
      const idx = data.points[0].pointNumber;

      if( dataPointSelector.value == -1 || dataPointSelector.value != idx )
      {
        const date_day_time_string = "20" + String( date_array[idx] ) + "_" + day_array[idx] + "_" + time_array[idx]
        const audioPath = "../IHTApark-ambient-recordings/data/audio/" + date_day_time_string + "/" + date_day_time_string + "_Pos" + String( position_array[idx] ) + "/" + date_day_time_string + "_Pos" + String( position_array[idx] ) + "_1.wav" 
        
        playAudio( audioPath );

        dataPointSelector.value = idx;
        selectedDate.value = String(date_array[ dataPointSelector.value ]).slice(0, 2) + "." + String(date_array[ dataPointSelector.value ]).slice(2, 4) + "." + String(date_array[ dataPointSelector.value ]).slice(4, 6);
        selectedTime.value = time_array[ dataPointSelector.value ];
        selectedPos.value = position_array[ dataPointSelector.value ];
        selectedX_val.value = lDataOptions[ x_values.value ].array[ dataPointSelector.value ];
        selectedY_val.value = lDataOptions[ y_values.value ].array[ dataPointSelector.value ];
        selectedZ_val.value = lDataOptions[ z_values.value ].array[ dataPointSelector.value ];

        markerSymbols.forEach( ( elem, i, array ) => {
          array[i] = 'circle';
        });

        markerSymbols[idx] = 'diamond-open';
      }
      else
      {
        markerSymbols.forEach( ( elem, i, array ) => {
          array[i] = 'circle';
        });
        stopAudio( );
      } 
    });
  }

  function playAudio( file )
  {
    let Player = myaudio.value;

    Player.src = file;
    Player.play( );
  }

  function stopButton( )
  {
    markerSymbols.forEach( ( elem, i, array ) => {
          array[i] = 'circle';
    });

    const update = {
      marker: { symbol: markerSymbols, color: colorList, opacity: 1, line: { width: 1 } },
    }

    Plotly.restyle( AnzeigeDiv.value, update, [0] );
    stopAudio( );
  }

  function stopAudio( )
  {
    let Player = myaudio.value;
    Player.pause( );

    dataPointSelector.value = -1;
    selectedDate.value = "";
    selectedTime.value = "";
    selectedPos.value = "";
    selectedX_val.value = "";
    selectedY_val.value = "";
    selectedZ_val.value = "";
  }

  function mapValueToRange(value, minValue, maxValue, newMinValue, newMaxValue) {
    // Calculate the percentage of where the value is in the original range
    const percentage = (value - minValue) / (maxValue - minValue);

    // Map the percentage to the new range
    const newValue = (percentage * (newMaxValue - newMinValue)) + newMinValue;

    return newValue;
  }

  onMounted( ( ) => {
    document.addEventListener( 'mouseup', ( e ) => {
      const update = {
        marker: { symbol: markerSymbols, color: colorList, opacity: 1, line: { width: 1 } },
      }

      Plotly.restyle( AnzeigeDiv.value, update, [0] );
    })
    renderPlot( );
  });

</script>

<style scoped>
h3 {
  font-weight: bold;
}

h5 {
  font-size: 18px;
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>