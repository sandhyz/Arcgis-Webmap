<template>
  <div class="map-view">
    <p></p>
    <input type="text" v-model="datax" class="form-control" />
  </div>

</template>

<script>
import ArcGISMap from '@arcgis/core/WebMap';
import MapView from '@arcgis/core/views/MapView';
import Search from '@arcgis/core/widgets/Search';
import FeatureLayer from "@arcgis/core/layers/FeatureLayer"
// import Editor from "@arcgis/core/widgets/Editor"
import GraphicsLayer from '@arcgis/core/layers/GraphicsLayer'
import Sketch from '@arcgis/core/widgets/Sketch'

export default {
  name: 'WebMap',
  props: {
    msg: String
  },
  
  data: function() {
    return {
      datax: "",
      datay: ""
    }
  }
  ,

  mounted() {


    const myPointsFeatureLayer = new FeatureLayer({
      url: "https://services3.arcgis.com/fab0bdce65bf4865bdc0859d33a4f5a6/arcgis/rest/services/my_points/FeatureServer"
    });


    const graphicsLayer = new GraphicsLayer();

    const map = new ArcGISMap({
      portalItem: {
        id: this.msg
      },
      layers: [graphicsLayer]

    });

    const view = new MapView({
      map,
      container: this.$el,
      layers: [myPointsFeatureLayer, graphicsLayer]
    });



    const search = new Search({
      view
    })

    // const editor = new Editor({
    //   view: view
    // });



    view.when(() => {
      const sketch = new Sketch({
        layer: graphicsLayer,
        view: view,
      })
      view.ui.add(sketch, "top-right");

      sketch.on("create", function (event) {
        if (event.state === "complete") {
          this.datax = event.graphic.geometry.x;
          console.log(event.graphic.geometry.y);

        }
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
