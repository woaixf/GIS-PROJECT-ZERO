<template>
    <div id="map-container" style="height: 100vh; width: 100vw">
    </div>
</template>

<script>
    import polygons from '../data/polygons';
    import $ from 'leaflet-ant-path';
    import axios from 'axios';
    import Provider from 'leaflet.chinatmsproviders'


    export default {

        name: "LeafletMap",
        data() {
            return {
                fieldInfo : ''
            }
        },
        methods:{
            getPolygon(pList) {
                var area = 0;
                var x = 0;
                var y = 0;
                for (var i = 1; i <= pList.length; i++) {
                    var lat = pList[i % pList.length][0];
                    var lng = pList[i % pList.length][1];
                    var nextLat = pList[i - 1][0];
                    var nextLng = pList[i - 1][1];
                    var temp = (lat * nextLng - lng * nextLat) / 2;
                    area += temp;
                    x += (temp * (lat + nextLat)) / 3;
                    y += (temp * (lng + nextLng)) / 3;
                }
                x = x / area;
                y = y / area;
                return [x, y];
            }
        },
        mounted(){
            var map = L.map('map-container',{
                minZoom:5,
                maxZoom:18,
                attributionControl:false
            }).setView([31.339, 121.651], 12);
            L.tileLayer.chinaProvider('TianDiTu.Normal.Map',{maxZoom:18,minZoom:5,key:'431bcc240096c8857af225e0a30e9930'}).addTo(map);
            L.tileLayer.chinaProvider('TianDiTu.Normal.Annotion',{maxZoom:18,minZoom:5,key:'431bcc240096c8857af225e0a30e9930'}).addTo(map);
            // L.marker([31.32960,121.68509],{title:'#00ff00'}).addTo(map).bindPopup("31.336770325716444,121.6369557390499").openPopup();

            //通过坐标转换算法在地图上添加标记，输入GPS坐标，转换成2.5维地图坐标
            // polygons.forEach(p=>{p.latlngs.forEach(l=>{L.marker(convertPoint(l)).addTo(map)})});
            var mypop = L.popup();
            map.on('click', function(e) {
                var content = '你临幸了这个点：<br>';
                content += e.latlng.toString();
                mypop.setLatLng(e.latlng)
                    .setContent(content)
                    .openOn(map);
            });
            var ploy_group;
            var ploy = [];
            var ploy_group2;
            var ploy2 = [];

            polygons.forEach(p=>{
                var p2 = L.polygon(p.latlngs, {
                    color: "green",
                    weight: 1,className:"p2p"
                })
                    .bindTooltip(p.name);
                ploy2.push(p2);
                console.log(p2);
            });
            ploy_group2 = new L.layerGroup(ploy2).addTo(map);
            map.addLayer(ploy_group2);
            map.on("zoomend",function (e) {
                let zoom = map.getZoom();
                console.log(zoom);
                if (zoom>15){
                    map.removeLayer(ploy_group2);
                    polygons.forEach(p=>{
                        var p1 = L.polygon(p.latlngs, {
                            color: "green",
                            weight: 1,className:"p1p"
                        })
                            .bindTooltip(p.name);
                        ploy.push(p1);
                    });
                    ploy_group = new L.layerGroup(ploy);
                    map.addLayer(ploy_group);
                   // ploy_group = new L.layerGroup(ploy).addTo(map);
                }else{
                    map.removeLayer(ploy_group);
                    map.addLayer(ploy_group2);
                }
            })
        }
    };
</script>

<style>
    #map-container{
        cursor: default;
        background-color: #fff;
    }
    p1p{
        opacity: 0;
    }
    p1p:hover{
        opacity: 1 !important;
    }
    p2p{
        opacity: 1;
    }
    p2p:hover{
        opacity: 0 !important;
    }
    .leaflet-ant-path{
        opacity: 1 !important;
    }
    .leaflet-tip{
        color: #0f0  !important;
        border-width: 1.5px !important;
        font-size: 16px !important;
        padding: 2px 9px !important;
        background-color: rgb(20, 114, 249) !important;
    }
    .leaflet-tip2{
        color: #f55  !important;
        border-width: 1.5px !important;
        font-size: 16px !important;
        padding: 2px 9px !important;
        background-color: rgb(220, 214, 29) !important;
    }
    /*.leaflet-tooltip{*/
    /*    border-width: 1.5px !important;*/
    /*    color: #fff  !important;*/
    /*    font-size: 16px !important;*/
    /*    padding: 2px 9px !important;*/
    /*    background-color: rgb(237, 125, 49) !important;*/
    /*}*/
</style>
