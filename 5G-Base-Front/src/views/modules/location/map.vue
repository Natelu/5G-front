<template>
  <div>
      <!-- <el-form :inline="true">
        <el-form-item label-width="30px">
          <el-amap-search-box class="search-box"

                              :search-option="searchOption"
                              :on-search-result="onSearchResult">
          </el-amap-search-box>
        </el-form-item>
        <el-form-item>
          <el-button v-if="isAuth('operate:map:save')" type="primary" @click="AddLocation()">新增</el-button>
        </el-form-item>
      </el-form> -->

      <div class="amap-wrapper">
        <el-amap vid="amap" :plugin="plugin"  :center="center" :zoom ="zoom">
          <div v-if=" markersflag == true ">
              <!-- <el-amap-marker  v-for="(marker,idx) in markers" :key= "idx" :position="marker.marker" :events="marker.events"></el-amap-marker> -->
            <el-amap-marker  v-for="(marker,idx) in markers" :key= "idx" :position="marker.marker.position" :template="marker.marker.template" :events="marker.marker.events"></el-amap-marker>
            <el-amap-info-window v-if="windowitem" :position="windowitem.position" :visible="windowitem.visible" :content="windowitem.content"></el-amap-info-window>
          </div>
        </el-amap>
        <div class="toolbar">
        <span v-if="loaded">
          当前所在位置:  {{ locationinfor }}
        </span>
          <span v-else>正在定位</span>
        
        </div>
      </div>
    <!-- 弹窗, 新增Map -->
    <add-location v-if="addLocationVisible" ref="AddLocation" ></add-location>

  </div>
</template>

<script>
  import AddLocation from './map-add'
  import Response from './bases'

  export default {

    data () {
      let self = this;
      let Bases = Response
      return {
        choesLocation:'',
        dataForm: {
          citycode:'',
          adcode:'',
          businessAreas:[{
            name:'',
            id:'',
            location:{
              P:'',
              O:'',
              lng:'',
              lat:''
            }
          }],
          neighborhoodType:'',
          neighborhood:'',
          building:'',
          buildingType:'',
          township:'',
          province:'',
          city:'',
          district:'',
          street:'',
          streetNumber:''
        },
        // 地图搜索
        searchOption: {
          city: '济南',
          citylimit: true
        },
        addLocationVisible: false,
        //地图
        locationinfor:'',
        center: [116.882774, 36.624876],
        zoom:15, // 放大比列
        lng: 0,
        lat: 0,
        loaded: false,
        plugin: [{
          pName: 'Geolocation',
          events: {
           init (o) {
              // o 是高德地图定位插件实例
            //  self.clearmarkers()
              o.getCurrentPosition((status, result) => {
                if (result && result.position) {
                  // console.log("当前地址:"+result.formattedAddress)
                  self.locationinfor = result.formattedAddress;
                  self.choesLocation = result.formattedAddress;
                  self.dataForm = JSON.parse(JSON.stringify(result.addressComponent, null, 4));
                  //businessAreas可能为null
                  self.lng = result.position.lng;
                  self.lat = result.position.lat;
                  self.searchOption.city = self.dataForm.city
                  self.center = [self.lng, self.lat];
                  // let marker = [self.lng, self.lat];
                  let marker = {
                    position: [self.lng, self.lat],
                    template: `<img src="./static/img/blue.gif">`,
                    events:{
                      click() {
                        self.windows.forEach(window => {
                          window.visible = false;
                        });
                      }
                    }
                  }
                  self.markers = [{
                    marker: marker
                  }
                  ];
                  // console.log(self.markers)
                  let resp = Bases.Response.response.bussinessAreas;
                  console.log(resp)
                  self.showBases(resp) 
                  self.markersflag = true;
                  self.loaded = true;
                }
                
              });
            },
          }
        }],
        markersflag: false,
        // markers: [],
        //地图窗口
        windows: [],
        windowitem: '',
        // self.showBases(Response);
      }
    },
    components: {
      AddLocation
    },
    mounted:function () {
      //this.getDataList()
      // this.showBases(Response);
    },
    methods: {
      //基站展示

      showBases(bases){
        let self = this;
        let posLength = bases.length;
        self.clearmarkers();
        for(var i = 0; i < posLength; i++){
          let base = bases[i];
          let tempPos = base.location;
          let healthy = base.health;
          let icon = healthy ? './static/img/blue.gif': './static/img/red.gif'
          let marker = {
            position: [tempPos.lng, tempPos.lat],
            template: `<img src="` + icon + '">',
            events: {
                click () {
                  self.windows.forEach(window => {
                    window.visible = false;
                  });
                  // console.logn(this)
                  self.dataForm = base;
                  console.log(self.dataForm)
                  self.showWindows(self.dataForm)
                  // self.setdataForm(tempPos.lng, tempPos.lat)
                  // self.window = self.windows[i];
                  // console.log('click bases')
                  // console.log(self.windows)
                  // self.$nextTick(() => {
                  //   self.window.visible = true;
                  // });
                  // console.log(self.windows[0])
                 /* let windowItem = self.windows[0];
                  self.windowitem = windowItem;*/

                }
              }
          };
          self.markers.push({
            marker: marker
          });
        };
      },
      //地图搜索
      onSearchResult (pois) {
        this.clearmarkers()
        let latSum = 0;
        let lngSum = 0;
        let markerNum = 2;
        let windows = [];
        if (pois.length > 0) {
          if (pois.length < markerNum) {
            markerNum = pois.length
          }
          for (let i = 0;i<markerNum;i++){
            let poi = pois[i]
            console.log("[poi.lng:" + poi.lng)
            console.log("[poi.lat:" + poi.lat)
            let {lng, lat} = poi;
            lngSum += lng;
            latSum += lat;
            this.markers.push({
              marker : [poi.lng, poi.lat],
              events: {
                click () {
                  /*self.windows.forEach(window => {
                    window.visible = false;
                  });
                  self.window = self.windows[i];
                  self.$nextTick(() => {
                    self.window.visible = true;
                  });*/
                  console.log(self.windows[0])
                 /* let windowItem = self.windows[0];
                  self.windowitem = windowItem;*/

                }
              }
            });
          }
          let center = {
            lng: lngSum /markerNum ,
            lat: latSum /markerNum
          };
          this.center = [center.lng, center.lat];
          this.markersflag = true;
          this.setdataForm(center.lng, center.lat);
        }
      },
      clearmarkers (){
        this.markersflag = false
        this.markers = [];
      },
      showWindows (positionObj) {
        this.windows = []
        // this.dataForm = self.dataForm
        // this.choesLocation = self.choesLocation
        // let postionObj = this.dataFormn;
        // console.log(positionObj)
        let statusFlag = positionObj.health ? `<span style="color:#bbb">正常运行</span>` : `<span style="color:red">发生故障</span>`
        this.windows.push({
          // position:  this.center,
          position: [positionObj.location.lng, positionObj.location.lat],
          content: `<div class="prompt">
                              <h3 style="text-align: center">所选基站信息</h3>
                              <p>名称: ${positionObj.name}</p>
                              <p>id: ${positionObj.id}</p>
                              <p>设备状态: ${statusFlag}</p>
                              <p>状态详情：${positionObj.status}</p>
                            </div>`,
          visible: false
        });
        // ${positionObj.status}
        this.windowitem = this.windows[0];
        this.$nextTick(() =>{
          this.windowitem.visible = true;
        });
        console.log(this.windowitem)
        console.log(this.windowitem.visible)
      },
      // 新增 / 修改
      AddLocation () {
        this.addLocationVisible = true
        this.$nextTick(() => {
          this.$refs.AddLocation.init(this.dataForm,this.choesLocation)
        })
      },

      setdataForm(lng,lat){
        let tself = this;
        var geocoder = new AMap.Geocoder({
          radius: 1000,
          extensions: "all"
        });
        geocoder.getAddress([lng ,lat], (status, result) =>{
          if (status === 'complete' && result.info === 'OK') {
            if (result && result.regeocode) {
              console.log("当前搜索地址:" + result.regeocode.formattedAddress)
              console.log("当前搜索详情:" + JSON.stringify(result.regeocode.addressComponent, null, 4))
              self.locationinfor =result.regeocode.formattedAddress
              self.choesLocation = result.regeocode.formattedAddress
              self.dataForm = JSON.parse(JSON.stringify(result.regeocode.addressComponent, null, 4));
              if (self.dataForm.businessAreas.length == 0){
                let supply = {
                  name: '',
                  id: '',
                  location: {
                    P: '',
                    O: '',
                    lng: lat,
                    lat: lng
                  }
                }
                self.dataForm.businessAreas.push(supply)
              }
              console.log("修改搜索详情:" + JSON.stringify(self.dataForm, null, 4))
              tself.showWindows()
            }
          }
        });
      }
    }
  }



</script>
<style>
  .amap-wrapper {
    position:absolute;
    /* top:75px; */
    /* left:25px; */
    width:100%;
    height:100%;
  }
  .search-box {
    position:absolute;
    height: 55px;
  }
  .prompt {
    background: white;
    width: 180px;
    height: 170px;
    text-align: left;
    line-height:14px;
  }
</style>
