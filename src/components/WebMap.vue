<template>
  <div class="map-view">
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
// import rendererJsonUtils from '@arcgis/core/renderers/support/jsonUtils'
// import Popup from "@arcgis/core/widgets/Popup";
import PopupTemplate from "@arcgis/core/PopupTemplate";

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
    let template = new PopupTemplate({
      title: "Population by Gender",
    });
    //contoh data
    const geojsonlayer = new GeoJSONLayer({
      url: "https://raw.githubusercontent.com/tryfatur/geojson-bandung/master/3273-kota-bandung-level-kelurahan.json",
      copyright: "USGS Earthquakes",
      PopupTemplate: template
    });

    const datajson = {


      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Point",
            "coordinates": [
              107.67288208007812,
              -6.965278395972943
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Point",
            "coordinates": [
              107.6443862915039,
              -7.025594153831843
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Point",
            "coordinates": [
              107.69004821777344,
              -7.0126456653825455
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Point",
            "coordinates": [
              107.56301879882812,
              -7.034112699606202
            ]
          }
        }
      ]


    };

    const blob = new Blob([JSON.stringify(datajson)], {
      type: "application/json"
    });

    // URL reference to the blob
    const url = URL.createObjectURL(blob);
    // create new geojson layer using the blob url
    const geojsonlayer2 = new GeoJSONLayer({
      url
    });

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

    map.add(geojsonlayer);  // adds the layer to the map
    map.add(geojsonlayer2);

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




      const featureLayer = geojsonlayer;


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



      view.on("click", () => {
        // let nama_kecamatan, parkName;

        // const getjson = view.getFeaturesAtPixel(event.pixel, {
        //   layerFilter: (layer) => layer === geojsonlayer
        // });

        console.log(geojsonlayer)

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


      view.popup.autoOpenEnabled = false;
      view.on("click", (event) => {
        // Get the coordinates of the click on the view
        // around the decimals to 3 decimals
        let lat = Math.round(event.mapPoint.latitude * 1000) / 1000;
        let lon = Math.round(event.mapPoint.longitude * 1000) / 1000;

        console.log([lat, lon])

        view.popup.open({
          // Set the popup's title to the coordinates of the clicked location
          title: "Reverse geocode: [" + lon + ", " + lat + "]",
          location: event.mapPoint // Set the location of the popup to the clicked location
        });


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
