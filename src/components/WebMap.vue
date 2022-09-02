<template>
  <div class="map-view">
    <label>lat</label>
    <input v-model="datax"/>
    <label>lon</label>
    <input v-model="datay"/>
  </div>

</template>

<script>
import ArcGISMap from '@arcgis/core/WebMap';
import MapView from '@arcgis/core/views/MapView';
import Search from '@arcgis/core/widgets/Search';
// import FeatureLayer from "@arcgis/core/layers/FeatureLayer"
import Legend from '@arcgis/core/widgets/Legend'
// import Editor from "@arcgis/core/widgets/Editor"
import GraphicsLayer from '@arcgis/core/layers/GraphicsLayer'
// import Sketch from '@arcgis/core/widgets/Sketch'
import GeoJSONLayer from '@arcgis/core/layers/GeoJSONLayer'
// import SimpleMarkerSymbol from '@arcgis/core/symbols/SimpleMarkerSymbol'
import SimpleFillSymbol from '@arcgis/core/symbols/SimpleFillSymbol'
// import rendererJsonUtils from '@arcgis/core/renderers/support/jsonUtils'
// import Popup from "@arcgis/core/widgets/Popup";
// import PopupTemplate from "@arcgis/core/PopupTemplate"
import ClassBreaksRenderer from '@arcgis/core/renderers/ClassBreaksRenderer'

//json
import datas from "@/assets/3273-kota-bandung-level-kewilayahan.json"

export default {
  name: 'WebMap',
  props: {
    msg: String
  },

  data: function () {
    return {
      datax: "",
      datay: ""
    }
  }
  ,

  mounted() {

    //warna warni


    // let template = new PopupTemplate({
    //   title: "Population by Gender",
    // });
    //contoh data
    // const geojsonlayer = new GeoJSONLayer({
    //   url: "https://raw.githubusercontent.com/tryfatur/geojson-bandung/master/3273-kota-bandung-level-kewilayahan.json",
    //   copyright: "USGS Earthquakes",
    //   PopupTemplate: template
    // });

    // const geojson = datas

    //     const datajson = {


    // "type": "FeatureCollection",
    // "features": [
    //   {
    //     "type": "Feature",
    //     "properties": {},
    //     "geometry": {
    //       "type": "Point",
    //       "coordinates": [
    //         107.67288208007812,
    //         -6.965278395972943
    //       ]
    //     }
    //   },
    //   {
    //     "type": "Feature",
    //     "properties": {},
    //     "geometry": {
    //       "type": "Point",
    //       "coordinates": [
    //         107.6443862915039,
    //         -7.025594153831843
    //       ]
    //     }
    //   },
    //   {
    //     "type": "Feature",
    //     "properties": {},
    //     "geometry": {
    //       "type": "Point",
    //       "coordinates": [
    //         107.69004821777344,
    //         -7.0126456653825455
    //       ]
    //     }
    //   },
    //   {
    //     "type": "Feature",
    //     "properties": {},
    //     "geometry": {
    //       "type": "Point",
    //       "coordinates": [
    //         107.56301879882812,
    //         -7.034112699606202
    //       ]
    //     }
    //   }
    // ]


    // };

    // const blob = new Blob([JSON.stringify(datajson)], {
    // type: "application/json"
    // });

    // // URL reference to the blob
    // const url = URL.createObjectURL(blob);
    // // create new geojson layer using the blob url
    // const geojsonlayer2 = new GeoJSONLayer({
    // url
    // });

    const datajson = datas;
    // console.log(datas.features[0].properties.nama_wilayah)
    // function createSymbol(color) {
    //       return {
    //         type: "simple-fill",
    //         color: color,
    //         outline: {
    //           width: 0.3,
    //           color: [255, 255, 255, 0.2]
    //         }
    //       };
    //     }

    const less35 = {
      type: "simple-fill", // autocasts as new SimpleFillSymbol()
      color: "#00b6f1",
      style: "solid",
      outline: {
        width: 0.2,
        color: [255, 255, 255, 0.5]
      }
    };

    const less50 = {
      type: "simple-fill", // autocasts as new SimpleFillSymbol()
      color: "#d9bf0d",
      style: "solid",
      outline: {
        width: 0.2,
        color: [255, 255, 255, 0.5]
      }
    };

    const more50 = {
      type: "simple-fill", // autocasts as new SimpleFillSymbol()
      color: "#6a28c7",
      style: "solid",
      outline: {
        width: 0.2,
        color: [255, 255, 255, 0.5]
      }
    };

    const more75 = {
      type: "simple-fill", // autocasts as new SimpleFillSymbol()
      color: "#c44245",
      style: "solid",
      outline: {
        width: 0.2,
        color: [255, 255, 255, 0.5]
      }
    };

    // const colors = [ "#00b6f1", "#d9bf0d", "#6a28c7", "#c44245", "#b9a087", "#ab579d", "#78aea0", "#1e8553" ];
    const renderer = new ClassBreaksRenderer({
      field: "data",
      normalizationField: "",

      legendOptions: {
        title: "nama_wilayah"
      },
      defaultSymbol: {
        type: "simple-fill", // autocasts as new SimpleFillSymbol()
        color: "black",
        style: "backward-diagonal",
        outline: {
          width: 1,
          color: [50, 50, 50, 0.6]
        }
      },
      defaultLabel: "no data",
      classBreakInfos: [
        {
          minValue: 0,
          maxValue: 1,
          symbol: less35,
          label: "data <= 1" // label for symbol in legend
        },
        {
          minValue: 2,
          maxValue: 3,
          symbol: less50,
          label: "data <= 3" // label for symbol in legend
        },
        {
          minValue: 4,
          maxValue: 6,
          symbol: more50,
          label: "data <= 6" // label for symbol in legend
        },
        {
          minValue: 7,
          maxValue: 100,
          symbol: more75,
          label: "data => 7" // label for symbol in legend
        }
      ]
    })

    const blob = new Blob([JSON.stringify(datajson)], {
      type: "application/json"
    });

    // URL reference to the blob
    const url = URL.createObjectURL(blob);
    // create new geojson layer using the blob url
    const geojsonlayer2 = new GeoJSONLayer({
      url,
      outFields: ["nama_wilayah", "id_wilayah", "data"
      ],
      renderer: renderer,
      opacity: 0.85,
      popupTemplate: { // autocast as esri/PopupTemplate
        title: "Nama Wilayah {nama_wilayah}  ",
        content: "data yang telah diverifikasi {data}"
      },

    });
    console.log(geojsonlayer2)


    // Set the renderer on a layer



    // const editor = new Editor({
    //   view: view
    // });





    const graphicsLayer = new GraphicsLayer();

    const map = new ArcGISMap({
      portalItem: {
        id: this.msg
      },
      layers: [graphicsLayer]

    });

    // map.add(geojsonlayer);  // adds the layer to the map
    map.add(geojsonlayer2);
    // map.add(geojsonlayer3);


    const view = new MapView({
      map,
      container: this.$el,
      layers: [graphicsLayer]
    });



    const search = new Search({
      view
    })




    view.when(() => {
      // add sketch
      // const sketch = new Sketch({
      //   layer: graphicsLayer,
      //   view: view,
      // })
      // view.ui.add(sketch, "top-right");

      // sketch.on("create", function (event) {
      //   if (event.state === "complete") {
      //     this.datax = event.graphic.geometry.x;
      //     console.log(event.graphic.geometry.y);

      //   }
      // });




      const featureLayer = geojsonlayer2;


      const legend = new Legend({
        view: view,
        layerInfos: [
          {
            layer: featureLayer,
            title: "Status Data"
          }
        ]
      });

      // Add widget to the bottom right corner of the view
      view.ui.add(legend, "bottom-left");





      view.on("click", (event) => {

        let data = view.hitTest(event);
        console.log(data)

        // for(let {layer} of results){
        // }

          // var pixel = view.getE
        // let nama_kecamatan, parkName;

        // const getjson = view.getEventPixel(event.originalEvent);

        // console.log(getjson)

        // view.popup.open({
        //   title: "tes",
        //   location: event.mapPoint // Set the location of the popup to the clicked location

        // });

        //  if (getjson.length > 0) {

        //   const nama_kecamatan = getjson[0].get("nama_kecamatan");
        //   const parkName = getjson[0].get("PARK_NAME");

        // } else {
        //   Popup.hide();
        // }

      });


      // view.popup.autoOpenEnabled = false;
      view.on("click", (event) => {
        // Get the coordinates of the click on the view
        // around the decimals to 3 decimals
        let lat = Math.round(event.mapPoint.latitude * 1000) / 1000;
        let lon = Math.round(event.mapPoint.longitude * 1000) / 1000;

        this.datax = lat
        this.datay = lon

        console.log([event.mapPoint.latitude, event.mapPoint.longitude])

        new SimpleFillSymbol({
          layer: graphicsLayer,
          view: view,
        }
          
        );

        //         let symbol = {
        //   type: "simple-marker",  // autocasts as new SimpleMarkerSymbol()
        //   style: "square",
        //   color: "blue",
        //   size: "8px",  // pixels
        //   outline: {  // autocasts as new SimpleLineSymbol()
        //     color: [ 255, 255, 0 ],
        //     width: 3  // points
        //   }
        // };
        // var marker = new SimpleMarkerSymbol();
        // marker.setSize(12)
        // var marker = { type: "simple-marker" };


        // view.popup.open({
        //   // Set the popup's title to the coordinates of the clicked location
        //   title: "Reverse geocode: [" + lon + ", " + lat + "]",
        //   location: event.mapPoint // Set the location of the popup to the clicked location
        // });


      });

    })


    view.ui.add(search, "top-right")
    // view.ui.add(editor, "top-right");
    view.when(() => { console.log('view ready'); })

  }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url("https://js.arcgis.com/4.24/esri/themes/light/main.css");

.map-view {
  /* width: 50%; */
  height: 70%;
  width: 70%;
  /* display: block; */
  /* margin-left: auto;
  margin-right: auto; */
}
</style>
