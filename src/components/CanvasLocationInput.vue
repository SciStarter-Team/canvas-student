<template>
<div class="ss-map">
  <!-- <el-row v-if="field_types.indexOf('region') > -1"> -->
  <!--   <el-col> -->
  <!--     <el-select -->
  <!--         v-if="editable" -->
  <!--         v-model="selected" -->
  <!--         placeholder="Or search civil regions by name" -->
  <!--         value-key="name" -->
  <!--         filterable -->
  <!--         remote -->
  <!--         :remote-method="search" -->
  <!--         :loading="loading" -->
  <!--     > -->
  <!--       <el-option -->
  <!--           v-for="item in filtered_completions" -->
  <!--           :key="item.name" -->
  <!--           :label="item.name" -->
  <!--           :value="item" -->
  <!--       > -->
  <!--       </el-option> -->
  <!--     </el-select> -->
  <!--   </el-col> -->
  <!-- </el-row> -->
  <el-row v-if="field_types.indexOf('latlon') > -1">
    <el-col :span="12">
      <el-input v-model="latitude" placeholder="latitude"></el-input>
    </el-col>
    <el-col :span="12">
      <el-input v-model="longitude" placeholder="longitude"></el-input>
    </el-col>
  </el-row>
  <div class="display" ref="display"></div>
  <el-popover
      v-if="editable && mode != 'point'"
      placement="top"
      trigger="click"
      width="275"
  >
    <el-row>
      <el-col :xs="24" :md="24" class="u-mb">
        <img alt="zoom in and zoom out icons" :src="SciStarter.static('img/map/map-resize-icons.png')" class="u-fl-l u-mr-sm u-mb-sm map-icon">
        <p class="b-muted b-small">Use these tools to Zoom-in or Zoom-out. You can also drag on the map to move it.</p>
      </el-col>
      <el-col :xs="24" :md="24" class="u-mb">
        <img alt="draw a polygon and draw a rectangle icons" :src="SciStarter.static('img/map/map-draw-icons.png')" class="u-fl-l u-mr-sm u-mb-sm map-icon">
        <p class="b-muted b-small">Draw a new place on the map with the Polygon or Rectangle tools. A polygon is created via a series of clicks. Your input is automatically saved after you confirm the polygon.</p>
      </el-col>
      <el-col :xs="24" :md="24" class="u-mb">
        <img alt="edity layers and delete layers icons" :src="SciStarter.static('img/map/map-edit-icons.png')" class="u-fl-l u-mr-sm u-mb-sm map-icon">
        <p class="b-muted b-small">To edit of an existing shape, click on the Edit Layer icon (pencil in a box) then select that shape. Use the Delete Layer (trashcan) to delete an existing shape. (Each shape belongs to each own layer.)</p>
      </el-col>
    </el-row>
    <a slot="reference">How to draw on the map</a>
  </el-popover>
</div>
</template>

<script>
import _ from 'lodash';
import L from 'leaflet';
export default {
    name: "LocationInput",
    props: ['value', 'mode', 'editable', 'fields', 'cluster', 'defaults'],

    data: function() { return {
        map: null,
        drawn: null,
        clusters: null,
        marker: null,
        selected: '',
        completions: [],
        loading: false,
        updating: false,
        field_types: this.fields ? this.fields : ['region'],
        latitude: '',
        longitude: '',
        render_popup: null,
    }},

    watch: {
        value: function() {
            if(this.editable) {
                if(this.marker !== null && this.mode == 'point') {
                    var val = JSON.parse(this.value);
                    this.marker.setLatLng({lat: val.coordinates[1], lng: val.coordinates[0]});
                    //this.zoom();
                }
                else {
                    this.update(this.value, this.render_popup);
                }
            }
            else if(this.mode == "datapoints") {
                this.update(this.value, this.render_popup);
            }
        },

        selected: function() {
            this.update(this.selected.geometry);
            this.input();
        },

        latitude: function() {
            if(this.updating) return;
            this.updating = true;
            try {
                this.marker.setLatLng({lat: this.latitude, lng: this.longitude});
                this.zoom();
            } catch(x) { null; }
            this.updating = false;
        },

        longitude: function() {
            if(this.updating) return;
            this.updating = true;
            try {
                this.marker.setLatLng({lat: this.latitude, lng: this.longitude});
            } catch(x) { null; }
            this.zoom();
            this.updating = false;
        },
    },

    computed: {
        filtered_completions: function() {
            return this.completions.filter(item => item.geometry.coordinates.length > 0);
        }
    },

    mounted: function() {
        var ctx = this;
        var render_popup = null;

        // if(!!ctx.$slots.default && ctx.$slots.default.length > 0) {
        //     var template = SciStarter.template_from_vnodes(ctx.$slots.default);
        //     render_popup = function(feature, layer) {
        //         layer.bindPopup(SciStarter.replace(template, feature.properties, ctx.defaults));
        //     }
        // }

        ctx.render_popup = render_popup;

        ctx.map = L.map(ctx.$refs.display);

        // var osm = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        //     attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
        // });

        // ctx.map.addLayer(osm);

        var mapbox = L.tileLayer(
            //'https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token={accessToken}',
            'https://api.mapbox.com/styles/v1/mapbox/{id}/tiles/256/{z}/{x}/{y}@2x?access_token={accessToken}',
            {
                id: 'satellite-streets-v9',
                accessToken: 'pk.eyJ1Ijoic2Npc3RhcnRlciIsImEiOiJjamJmOGZvMncxNDloMnJxd3g2anVlaWF6In0.YU5-XE0w1nRwZN1lilerdw',
                attribution: '&copy; <a href="https://www.mapbox.com/map-feedback/">Mapbox</a>, <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
            }
        );

        ctx.map.addLayer(mapbox);

        ctx.drawn = new L.GeoJSON();

        ctx.clusters = L.markerClusterGroup({
            maxClusterRadius: 15, // Can be a e.g. function(zoom_level) {return 15;}
            showCoverageOnHover: false,
            //disableClusteringAtZoom: 17,
            spiderLegPolylineOptions: { weight: 2.0, color: "#47a8d4", opacity: 1.0 }
        });

        if(ctx.cluster) {
            ctx.map.addLayer(ctx.clusters);
        }

        ctx.map.addLayer(ctx.drawn);

        if(ctx.mode == 'point' && !ctx.value) {
            ctx.marker = L.marker([-97, 38]).addTo(ctx.drawn);
        }
        else {
            ctx.update(ctx.value, render_popup);
        }

        if(ctx.mode == 'point') {
            ctx.drawn.eachLayer(function(layer) {
                ctx.marker = layer;
            });
        }

        ctx.map.setView([39.8333333,-105.585522], 4);

        if(ctx.editable) {
            if(ctx.mode == 'poly') {
                var control = new L.Control.Draw({
                    edit: {
                        featureGroup: ctx.drawn
                    },
                    draw: {
                        polyline: false,
                        polygon: ctx.mode == 'poly',
                        rectangle: ctx.mode == 'poly',
                        circle: false,
                        marker: ctx.mode == 'point',
                        circlemarker: false
                    }
                });

                ctx.map.addControl(control);

                ctx.map.on('draw:created', function(evt) {
                    ctx.drawn.addLayer(evt.layer);
                    ctx.input();
                });

                ctx.map.on('draw:edited', function() {
                    ctx.input();
                });

                ctx.map.on('draw:deleted', function() {
                    ctx.input();
                });
            }
            else if(ctx.mode == 'point') {
                ctx.map.on('click', function(evt) {
                    ctx.marker.setLatLng(evt.latlng);
                    ctx.input();
                });
            }
        }


        ctx.zoom();

        var shape = ctx.$refs.display.offsetWidth + ctx.$refs.display.offsetHeight;
        var repeat = window.setInterval(function() {
            if(ctx.$refs.display === undefined) {
                window.clearInterval(repeat);
                return;
            }
            var cur_shape = ctx.$refs.display.offsetWidth + ctx.$refs.display.offsetHeight;
            if(cur_shape != shape) {
                shape = cur_shape;
                ctx.map.invalidateSize();
            }
        }, 100);
    },

    methods: {
        // search: _.debounce(function(stem) {
        //     var ctx = this;

        //     ctx.loading = true;
        //     SciStarter.get('geo_search', {stem: stem}).then(function(data) {
        //         ctx.completions = data.matches || [];
        //         ctx.loading = false;
        //     });
        // }, 1500),

        update: function(json, render_popup) {
            if(typeof(json) == 'string') {
                json = JSON.parse(json);
            }

            if(!json) {
                json = {'type': 'MultiPolygon', 'coordinates': []};
            }

            if(this.mode == 'poly') {
                json = this.decompose_multipolygon(json);
            }

            if(!!this.cluster && this.mode == 'datapoints') {
                this.clusters.clearLayers();
                for(var i = 0; i < json.length; i++) {
                    var l = L.circleMarker(
                        [json[i].geometry.coordinates[1], json[i].geometry.coordinates[0]],
                        {color: "#47a8d4", radius: 8}
                    ).addTo(this.clusters);
                    if(render_popup) {
                        render_popup(json[i], l);
                    }
                }
            }
            else {
                this.drawn.clearLayers();
                this.drawn.addData(json);
                if(render_popup) {
                    this.drawn.eachLayer(function(layer) {
                        render_popup(layer.feature, layer);
                    });
                }
            }

            this.zoom();
        },

        decompose_multipolygon: function(multi) {
            var ret = [];

            if(multi.type == 'Polygon') {
                ret.push(multi);
                return ret;
            }

            multi.coordinates.forEach(function(poly) {
                ret.push({type: 'Polygon', 'coordinates': poly})
            });

            return ret;
        },

        input: function() {
            if(this.mode == 'poly') {
                var result = {'type': 'MultiPolygon', 'coordinates': []};
                this.drawn.eachLayer(function(layer) {
                    result['coordinates'].push(layer.toGeoJSON().geometry.coordinates);
                });
                if(result['coordinates'].length == 0) {
                    this.$emit('input', null);
                }
                else {
                    this.$emit('input', JSON.stringify(result));
                }
            }
            else {
                var ll = this.marker.getLatLng();
                this.latitude = ll.lat;
                this.longitude = ll.lng;
                this.$emit('input', JSON.stringify(this.marker.toGeoJSON().geometry));
            }
        },

        zoom: function() {
            var bounds = this.drawn.getBounds();

            if(_.isEmpty(bounds)) {
                this.map.setView([34.813803317113155,-36.56250000000001], 3);
                // this.map.setView([39.8333333,-105.585522], 4);
                // this.map.setView([36, -22], 1);
            } else {
                var width = bounds.getWest() - bounds.getEast();
                var height = bounds.getSouth() - bounds.getNorth();

                if(width == 0 || height == 0) {
                    this.map.setView(bounds.getCenter(), 6);
                } else {
                    this.map.fitBounds(bounds);
                }
            }
        },
    },
}
</script>

<style lang="scss">
.ss-map {
  .display {
    height: 320px;
    width: 100%;
  }
}
</style>
