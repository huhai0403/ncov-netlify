<template>
  <div class="main">
    <div class="swiper-container">
      <!-- Additional required wrapper -->
      <div class="swiper-wrapper">
        <!-- Slides -->
        <div class="swiper-slide">
          <template v-if="indexData">
            <div class="index-box">
              <p class="index-title">疫情总览</p>
              <div class="index-wrap" v-if="indexData">
                <div class="index-item">
                  <span>现存确诊</span>
                  <h3>{{indexData[0].currentConfirmedCount}}</h3>
                  <i>较昨日{{indexData[0].currentConfirmedIncr}}</i>
                </div>
                <div class="index-item">
                  <span>现存疑似</span>
                  <h3>{{indexData[0].suspectedCount}}</h3>
                  <i>较昨日{{indexData[0].suspectedIncr}}</i>
                </div>
                <div class="index-item">
                  <span>现存重症</span>
                  <h3>{{indexData[0].seriousCount}}</h3>
                  <i>较昨日{{indexData[0].seriousIncr}}</i>
                </div>
                <div class="index-item">
                  <span>累计确诊</span>
                  <h3>{{indexData[0].confirmedCount}}</h3>
                  <i>较昨日{{indexData[0].confirmedIncr}}</i>
                </div>
                <div class="index-item">
                  <span>累计死亡</span>
                  <h3>{{indexData[0].deadCount}}</h3>
                  <i>较昨日{{indexData[0].deadIncr}}</i>

                </div>
                <div class="index-item">
                  <span>累计治愈</span>
                  <h3>{{indexData[0].curedCount}}</h3>
                  <i>较昨日{{indexData[0].curedIncr}}</i>
                </div>
              </div>
            </div>
            <template>
              <div class="txt-item" v-if="indexData[0].remark1">{{indexData[0].remark1}}</div>
              <div class="txt-item" v-if="indexData[0].remark2">{{indexData[0].remark2}}</div>
              <!--<div class="txt-item" v-if="indexData[0].remark3">{{indexData[0].remark3}} </div>-->
              <!--<div class="txt-item" v-if="indexData[0].note1">{{indexData[0].note1}} </div>-->
              <div class="txt-item" v-if="indexData[0].note2">{{indexData[0].note2}}</div>
              <div class="txt-item" v-if="indexData[0].note3">{{indexData[0].note3}}</div>
            </template>
          </template>
          <div class="weui-loadmore" style="margin-top: 20vh" v-else>
            <i class="weui-loading"></i>
            <span class="weui-loadmore__tips">正在加载</span>
          </div>

        </div>
        <div class="swiper-slide">
          <div class="table-head ">
            <p>地区</p>
            <p>现存确诊</p>
            <p>累计确诊</p>
            <p>死亡</p>
            <p>治愈</p>
          </div>
          <div class="table-scr" v-if="areaData">
            <div class="table-box" v-if="item.countryEnglishName==='China'" v-for="(item,index) in areaData"
                 :key="index">
              <div class="table-top">
                <p><i :class="checkedTableName===item.provinceShortName?'tj active':'tj'"
                      @click="checkedTableName===item.provinceShortName?checkedTableName='':checkedTableName=item.provinceShortName"></i>{{item.provinceShortName}}
                </p>
                <p>{{item.currentConfirmedCount}}</p>
                <p>{{item.confirmedCount}}</p>
                <p>{{item.deadCount}}</p>
                <p>{{item.curedCount}}</p>
              </div>
              <template v-if="checkedTableName===item.provinceShortName">
                <div v-for="(city,cIndex) in item.cities" :key="cIndex" class="table-top table-child">
                  <p>{{city.cityName}}</p>
                  <p>{{city.currentConfirmedCount}}</p>
                  <p>{{city.confirmedCount}}</p>
                  <p>{{city.deadCount}}</p>
                  <p>{{city.curedCount}}</p>
                </div>
              </template>

            </div>
          </div>
          <div class="weui-loadmore" style="margin-top: 20vh" v-else>
            <i class="weui-loading"></i>
            <span class="weui-loadmore__tips">正在加载</span>
          </div>
        </div>
        <div class="swiper-slide">
          <div class="weui-cell weui-cell_active weui-cell_select news-search">
            <div class="weui-cell__bd">
              <select class="weui-select" v-model="newProvince">
                <option v-for="(item,index) in provinceData" :key="index" :value="item.name">{{item.name}}</option>
              </select>
            </div>
          </div>
          <div class="weui-loadmore" style="margin-top: 20vh" v-if="!newsList.length">
            <i class="weui-loading"></i>
            <span class="weui-loadmore__tips">正在加载</span>
          </div>
          <div class="news-box" v-else>
            <div class="txt-item" v-for="(item,index) in newsList" :key="index">
              <p>{{(index+1)+':'+item.title}}</p>
              <span v-if="item.provinceName">地点：{{item.provinceName}}</span>
              <span v-if="item.infoSource">来源：{{item.infoSource}}</span>
              <span>{{item.summary}}</span>
              <i>发布时间：{{backFormatTime(item.pubDate)}}</i>
            </div>
          </div>

        </div>

      </div>
      <!-- If we need pagination -->
      <div class="swiper-pagination"></div>
    </div>

  </div>

</template>

<script>
  import Swiper from 'swiper';
  import 'swiper/css/swiper.min.css'
  import 'swiper/js/swiper.min.js'

  export default {
    name: 'App',
    data() {
      return {
        indexData: null,
        newNum: 10,
        newProvince: '全国',
        provinceData: [{"code": "10", "name": "全国"}, {"code": "11", "name": "北京市"}, {
          "code": "12",
          "name": "天津市"
        }, {"code": "13", "name": "河北省"}, {"code": "14", "name": "山西省"}, {
          "code": "15",
          "name": "内蒙古自治区"
        }, {"code": "21", "name": "辽宁省"}, {"code": "22", "name": "吉林省"}, {"code": "23", "name": "黑龙江省"}, {
          "code": "31",
          "name": "上海市"
        }, {"code": "32", "name": "江苏省"}, {"code": "33", "name": "浙江省"}, {"code": "34", "name": "安徽省"}, {
          "code": "35",
          "name": "福建省"
        }, {"code": "36", "name": "江西省"}, {"code": "37", "name": "山东省"}, {"code": "41", "name": "河南省"}, {
          "code": "42",
          "name": "湖北省"
        }, {"code": "43", "name": "湖南省"}, {"code": "44", "name": "广东省"}, {
          "code": "45",
          "name": "广西壮族自治区"
        }, {"code": "46", "name": "海南省"}, {"code": "50", "name": "重庆市"}, {"code": "51", "name": "四川省"}, {
          "code": "52",
          "name": "贵州省"
        }, {"code": "53", "name": "云南省"}, {"code": "54", "name": "西藏自治区"}, {"code": "61", "name": "陕西省"}, {
          "code": "62",
          "name": "甘肃省"
        }, {"code": "63", "name": "青海省"}, {"code": "64", "name": "宁夏回族自治区"}, {"code": "65", "name": "新疆维吾尔自治区"}],
        newsList: [],
        errNum: 0,
        areaData: null,
        checkedTableName: ''
      }
    },
    components: {},
    methods: {
      initSwiper() {
        let mySwiper = new Swiper('.swiper-container', {
          // Optional parameters
          direction: 'vertical',
          loop: false,

          // If we need pagination
          pagination: {
            el: '.swiper-pagination',
            clickable :true,
            type: 'custom',
            renderCustom: function (swiper, current, total) {
              var paginationHtml = "";
              var arr = ['疫情总览', '各地数据', '新闻资讯']
              for (var i = 0; i < total; i++) {
                let txt = arr[i]
                if(i == (current - 1)) {
                  paginationHtml += '<span class="swiper-pagination-bullet swiper-pagination-bullet-active">'+txt+'</span>';
                } else {
                  paginationHtml += '<span class="swiper-pagination-bullet">'+txt+'</span>';
                }
                // paginationHtml = '<span class="swiper-pagination-bullet">'+arr[current-1]+'</span>';
              }

              return paginationHtml;
              // return '<span class="swiper-pagination-bullet">疫情总览</span><span class="swiper-pagination-bullet">各地数据</span><span class="swiper-pagination-bullet">新闻资讯</span>'
            }
          },

          // Navigation arrows
          // navigation: {
          //   nextEl: '.swiper-button-next',
          //   prevEl: '.swiper-button-prev',
          // },

          // And if we need scrollbar
          // scrollbar: {
          //   el: '.swiper-scrollbar',
          // },
        });
        console.log(mySwiper)
      },
      getIndexData() {
        let that = this;
        this.$axios({
          url: 'http://lab.isaaclin.cn/nCoV/api/overall?latest=1',
          method: 'GET',
        }).then(function (res) {
          console.log(res);
          if (res.status === 200 && res.data.success) {
            that.indexData = res.data.results;
            sessionStorage.setItem('indexData', JSON.stringify(that.indexData))
            console.log(that.indexData)
          } else {
            alert(JSON.stringify(res.data))
          }
        }).catch(function (error) {
          if (that.errNum < 5) {
            that.getIndexData();
            that.errNum++
          } else {
            alert(JSON.stringify(error))
          }

        })
      },
      getNews() {
        let that = this;
        let url = 'http://lab.isaaclin.cn/nCoV/api/news?num=20'
        if (this.newProvince !== '全国' && this.newProvince) {
          url = url + '&province=' + this.newProvince
        }
        this.$axios({
          url: url,
          method: 'GET',
        }).then(function (res) {
          console.log(res);
          if (res.status === 200 && res.data.success) {
            that.newsList = res.data.results;
            sessionStorage.setItem('newsList', JSON.stringify(that.newsList))
            console.log(that.newsList)
          } else {
            alert(JSON.stringify(res.data))
          }
        }).catch(function (error) {
          if (that.errNum < 5) {
            that.getNews();
            that.errNum++
          } else {
            alert(JSON.stringify(error))
          }
        })
      },
      backFormatTime(time) {
        let date = new Date(time);
        let y = date.getFullYear();
        let m = date.getMonth() + 1;
        m = m < 10 ? ('0' + m) : m;
        let d = date.getDate();
        d = d < 10 ? ('0' + d) : d;
        let h = date.getHours();
        h = h < 10 ? ('0' + h) : h;
        let minute = date.getMinutes();
        let second = date.getSeconds();
        minute = minute < 10 ? ('0' + minute) : minute;
        second = second < 10 ? ('0' + second) : second;
        return y + '-' + m + '-' + d + ' ' + h + ':' + minute + ':' + second;
      },
      getAreaData() {
        let that = this;
        this.$axios({
          url: 'http://lab.isaaclin.cn/nCoV/api/area?latest=1',
          method: 'GET',
        }).then(function (res) {
          console.log(res);
          if (res.status === 200 && res.data.success) {
            that.areaData = res.data.results;
            sessionStorage.setItem('areaData', JSON.stringify(that.areaData))
            console.log(that.areaData)
          } else {
            alert(JSON.stringify(res.data))
          }
        }).catch(function (error) {
          if (that.errNum < 5) {
            that.getAreaData();
            that.errNum++
          } else {
            alert(JSON.stringify(error))
          }

        })
      },
    },
    watch: {
      newProvince(newVal) {
        console.log(newVal);
        this.newProvince = newVal;
        sessionStorage.setItem('newProvince',newVal)
        this.newsList = [];
        this.getNews();

      }
    },
    created() {
    },
    mounted() {
      this.initSwiper();
      if (!sessionStorage.getItem('indexData')) {
        this.getIndexData();
      } else {
        this.indexData = JSON.parse(sessionStorage.getItem('indexData'));
        console.log(this.indexData)

      }
      if (sessionStorage.getItem('newsList') && sessionStorage.getItem('newProvince')) {
        this.newsList = JSON.parse(sessionStorage.getItem('newsList'));
        this.newProvince = sessionStorage.getItem('newProvince')
        console.log(this.newsList)
        console.log(this.newProvince)
      } else {
        this.newProvince = '全国';
        sessionStorage.setItem('newProvince','全国')
        this.getNews();
      }
      if (!sessionStorage.getItem('areaData')) {
        this.getAreaData();
      } else {
        this.areaData = JSON.parse(sessionStorage.getItem('areaData'));
        console.log(this.areaData)
      }
    },
  }
</script>

<style>

</style>
