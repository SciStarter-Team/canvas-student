<template>
<div class="map-box" ref="map_box"></div>
</template>

<script>
export default {
    name: 'Map',
    props: ['dataUrl'],

    data: function(){
        return {
            map: null
        }
    },

    computed: {

    },

    methods: {

    },

    mounted: function() {
        var ctx = this;

        var mapboxgl = require('mapbox-gl/dist/mapbox-gl.js');

        mapboxgl.accessToken = 'pk.eyJ1Ijoic2Npc3RhcnRlciIsImEiOiJja2p5YXMwbWowYzB5Mm9xdW53bDZ2NW5xIn0.pPgmuk78JrY58BdzkdzOvw';

        var map = new mapboxgl.Map({
            container: ctx.$refs.map_box,
            style: 'mapbox://styles/mapbox/streets-v11',
        });

        ctx.map = map;

        map.on('load', () => {
            map.loadImage(
                'https://docs.mapbox.com/mapbox-gl-js/assets/custom_marker.png',
                function (error, image) {
                    if (error) throw error;
                    map.addImage('custom-marker', image);
                    map.addSource('points', {type: "geojson", data: ctx.dataUrl});
                    map.addLayer({
                        'id': 'points',
                        'type': 'symbol',
                        'source': 'points',
                        'layout': {
                            'icon-image': 'custom-marker',
                            // get the title name from the source's "title" property
                            // 'text-field': ['get', 'title'],
                            // 'text-font': [
                            //     'Open Sans Semibold',
                            //     'Arial Unicode MS Bold'
                            // ],
                            // 'text-offset': [0, 1.25],
                            // 'text-anchor': 'top'
                        }
                    });

                    if(navigator.geolocation) {
                        navigator.geolocation.getCurrentPosition(function(loc) {
                            map.flyTo({center: [loc.coords.longitude, loc.coords.latitude], zoom: 6});
                        }, function() {
                            map.zoomTo(0);
                        });
                    }
                    else {
                        map.zoomTo(0);
                    }
                }
            );
        });
    }
}
</script>

<style lang="scss">
@import '../assets/css/CanvasVariables.scss';

.cbtn-primary > i {
    vertical-align: middle;
}

.map-box {
    width: 100%;
    height: 400px;
}
</style>
