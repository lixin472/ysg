<template>
	<div class="home">
		<section class="index">
			<!--页面遮罩-->
			<div class="mark" v-show="markFlag" @click="hideSlide"></div>
			<!--导航菜单-->
			<yd-navbar title="" class="index_tab">
				<span slot="left" class="back" @click="goBack"></span>
				<span class="menu" @click="showMenu" slot="right"></span>
			</yd-navbar>
			<!--banner-->
			<scroller ref="scroller">
				<section class="index_top" :style="{backgroundImage: 'url(' + homeData.indexBackground + ')'}">
					<span class="logo"></span>
					<h4 class="user_name" v-if="fullName">{{fullName }}</h4>
					<h4 class="user_name" v-else><router-link :to="{path:'/loginforguest',query:{pageType:'s_home'}}">Visitor</router-link></h4>
					<ul class="weather_date">
						<li class="date">
							<div class="left"></div>
							<div class="right">
								<p>{{startTime}}</p>
								<!--<p>The remaining 14 days</p>-->
							</div>
						</li>
						<li class="weather">
							<div class="right">
								<img :src="weatherSrc" alt="">
							</div>
							<div class="left">
								<p>{{weather.weatherEn}}</p>
								<p>{{weather.temperatureFrom}} ~ {{weather.temperatureTo}}</p>
							</div>
						</li>
					</ul>
					<div class="smart_home">
            <!--@click="goNext(shortcutList[0].key)" <img style="width: .4rem;vertical-align: middle;display: inline-block;" src="../../assets/images/service-btn.png" alt="">-->
						<button type="button">{{servicesMsg}}</button>
					</div>
				</section>
				<!--快捷功能-->
				<section class="index_content">
					<div class="swiper-container">
						<div class="swiper-wrapper">
							<div class="swiper-slide" v-for="(item,index) in shortcutList" @click="goNext(item.key)">
								<img :src="item.imgSrc">
								<p>{{item.title}}</p>
							</div>
						</div>
             <!-- Add Arrows -->
    <div v-show="shortcutList.length>4" class="swiper-button-next"></div>
    <div v-show="shortcutList.length>4" class="swiper-button-prev"></div>
					</div>
				</section>
				<!--酒店详情-->
				<section class="app_content" style="background: #fff;padding-bottom: 1rem;">
					<h4>{{ hotelDetail.name }}</h4>
          <div v-if="hotelDetail.robot_pic != ''" class="map_content" @click="goLocation(2)">
						<img :src="hotelDetail.robot_pic">
					</div>
					<div class="map_content" @click="goLocation(1)">
						<img :src="hotelDetail.localpic">
					</div>
					<ul class="add">
						<li class="col-7">
							<p>{{home.address}}</p>
						</li>
						<li class="col-3"> </li>
					</ul>
					<section class="apart_profile">
						<h4>{{language.home.profile}}</h4>
						<P>{{hotelDetail.introduction}}</P>
						<!--<a href="#">more</a>-->
					</section>
					<section class="equipment">
						<div class="swiper-container">
							<div class="swiper-wrapper">
								<div class="swiper-slide" v-for="item in facListShow">
									<router-link :to="{path:'/facilities',query:{data:item}}">
										<img :src="item.imgSrc">
									</router-link>
								</div>
							</div>
               <div v-show="shortcutList.length>5" class="swiper-button-next"></div>
    <div v-show="shortcutList.length>5" class="swiper-button-prev"></div>
						</div>
              <!-- <div class="swiper-pagination"></div> -->
					</section>
					<section class="virtual">
						<h4><i></i> {{language.home.virtual}}</h4>
						<div class="swiper-container">
							<div class="swiper-wrapper">
								<div class="swiper-slide" v-for="(item,index) in hotelDetail.panoramicList">
									<router-link :to="{path:'/home/virtual', query:{src:item.panoramic, pageType:this.pageType,title:item.title}}"><img :src="item.pic"></router-link>
									<p style="text-align:center;font-size:0.24rem;color:#999;width:90%;overflow:hidden;padding-top:.2rem;">{{item.title}}</p>
								</div>
							</div>
						</div>
					</section>
					<section class="apartment">
						<h4>{{language.home.apartment}}</h4>
						<div class="swiper-container" id="test">
							<div class="swiper-wrapper">
								<div class="swiper-slide" v-for="(item, index) in hotelDetail.roomTypeList">
									<router-link :to="{path:'/appartmentStyle',query:{index:index,bookUrl:hotelDetail.bookurl}}">
										<img :src="item.pic">
									</router-link>
									<h5>{{item.title}}</h5>
									<ul class="apart_style">
										<li class="col-5"><i></i>{{language.apartment.maxPerson}} {{item.personcount}} {{language.apartment.person}}(s)</li>
										<li class="col-5"><i></i>{{item.bedtype}}</li>
									</ul>
									<ul class="apart_util">
										<li>
											<div class="col-5">{{language.apartment.fcount}}:</div>
											<div class="col-5">{{item.roomcount}}</div>
										</li>
										<li>
											<div class="col-5">{{language.apartment.size}}:</div>
											<div class="col-5">{{item.size}}</div>
										</li>
									</ul>
									<ul class="apart_btn">
										<li @click="openBooking(hotelDetail.bookurl)">
											{{language.apartment.book}}
										</li>
										<!--<li class="col-4">RESERVE</li>-->
									</ul>

								</div>

							</div>
						</div>
					</section>
					<section class="fire">
						<h4>{{language.home.fire}}</h4>
						<div class="swiper-container">
							<div class="swiper-wrapper">
								<div class="swiper-slide" v-for="item in hotelDetail.floorList">
									<router-link :to="{path:'/fireDetail', query:{info:item}}">
										<img :src="item.pic">
									</router-link>
								</div>
							</div>
						</div>
					</section>
					<section class="how">
						<h4>{{language.home.how}}</h4>
						<div class="how_phone">
							<yd-cell-group>
								<yd-cell-item arrow v-for="item in hotelDetail.trafficList" @click.native="goTraffic(item)">
									<img slot="icon" src="../../assets/images/icon-bike.png" v-if="item.introduct == 'Bicycle' || item.introduct == '自行车'|| item.introduct=='自転車'">
									<img slot="icon" src="../../assets/images/icon-car.png" v-else-if="item.introduct == 'Self-Driving' || item.introduct == '租车自驾服务' || item.introduct=='赁车自'">
									<img slot="icon" src="../../assets/images/icon-taxi.png" v-else-if="item.introduct == 'Taxi' || item.introduct == '出租车'|| item.introduct=='タクシー'">
									<img slot="icon" src="../../assets/images/icon-bus.png" v-else-if="item.introduct == 'Public bus' || item.introduct == '公交'|| item.introduct=='バス'">
									<img slot="icon" src="../../assets/images/icon-tbus.png" v-else-if="item.introduct == 'Subway' || item.introduct == '地铁'|| item.introduct=='地下鉄'">

									<span slot="left">{{item.introduct}}</span>
								</yd-cell-item>
							</yd-cell-group>

						</div>
					</section>

				</section>
			</scroller>
		</section>
	    <yd-actionsheet :items="myItems2" v-model="tabLanShow2" v-if="hotelid ==10"></yd-actionsheet>
    <yd-actionsheet :items="myItems" v-model="tabLanShow2" v-else></yd-actionsheet>
		<!--底部菜单、个人中心、 设置项-->
		<v-foot></v-foot>
		<v-menu v-show="menuFlag"></v-menu>
		<v-guestCenter v-show="centerFlag"></v-guestCenter>

	</div>
</template>

<script>
import { Actionsheet, Scroller } from "vux";
import { mapState } from "vuex";
import { mapGetters } from "vuex";
import foot from "../foot.vue";
import menu from "../common/menu.vue";
import guestCenter from "../common/menu/guestCenter.vue";
export default {
  data() {
    return {
      menuFlag: false,
      centerFlag: false,
      menuSlide: false,
      centerSlide: false,
      homeData: {},
      fullName: "",
      startTime: "",
      weather: {},
      shortcutList: [], //按钮组
      localshortcutList: [], //本地按钮组
      markFlag: false,
      actionSheet: false,
      lat: "",
      lng: "",
      idType: "",
      myItems: [],
      myItems2: [],
      details: {},
      pageType: "",
      distance: "",
      facList: [],
      facListShow: [],
      sHeightPx: "400px",
      weatherSrc: "",
      hotelid: "",
      pageFlag: false,
      servicesMsg:'快捷服务'
    };
  },
  created: function() {
    this.tabLanShow2 = this.tabLanShow;
    //设置当前状态
    localStorage.stateFlag = 2;
    //判断是登录主页还是查询主页
    if (this.$route.query.hotelid) {
      localStorage.searchFlag = "s_home";
      this.hotelid = this.$route.query.hotelid;
      localStorage.HOTELID = this.$route.query.hotelid;
    } else {
      localStorage.searchFlag = "home";
      this.hotelid = localStorage.HOTELID;
      this.pageFlag = true;
    }

    //判断个人中心图标显示
    if (this.$route.query.page == "unlogin" || localStorage.spage == 1) {
      localStorage.spage = 1;
      this.pageFlag = true;
    } else if (this.$route.query.page == "search" || localStorage.spage == 2) {
      localStorage.spage = 2;
      this.pageFlag = true;
    } else {
      this.pageFlag = false;
    }

    if (localStorage.searchFlag == "home") {
      this.pageFlag = false;
    }

    //获取物业详情
    this.fullName = localStorage.FULLNAME;
    //初始化数据
    this.localshortcutList = [
      {
        key: "reservation",
        title: "",
        imgSrc: require("../../assets/images/icon-book.png"),
        linkTo: "/home/homebooking"
      },
      {
        key: "tanslate",
        title: "",
        imgSrc: require("../../assets/images/icon-translate.png"),
        linkTo: "/translate"
      },
      {
        key: "service",
        title: "",
        imgSrc: require("../../assets/images/icon-service.png"),
        linkTo: "/home/service"
      },
      {
        key: "flight",
        title: "",
        imgSrc: require("../../assets/images/icon-airport.png"),
        linkTo: "#"
      },
      {
        key: "phone",
        title: "",
        imgSrc: require("../../assets/images/icon-phone.png"),
        linkTo: "/phone"
      },
      {
        key: "guestmail",
        title: "",
        imgSrc: require("../../assets/images/icon-phone.png"),
        linkTo: "#"
      },
      {
        key: "smarthome",
        title: "",
        imgSrc: require("../../assets/images/icon-smart.png"),
        linkTo: "#"
      },
      {
        key: "news",
        title: "",
        imgSrc: require("../../assets/images/icon-news-add.png"),
        linkTo: "/ssr"
      },
      {
        key: "bills",
        title: "",
        imgSrc: require("../../assets/images/icon-bill.png"),
        linkTo: "/mybill"
      },
      {
        key: "invoice",
        title: "",
        imgSrc: require("../../assets/images/icon-receipt.png"),
        linkTo: "/invoices"
      },
      {
        key: "lifeservice",
        title: "",
        imgSrc: require("../../assets/images/icon-lifestyle.png"),
        linkTo: ""
      },
      {
        key: "shop",
        title: "",
        imgSrc: require("../../assets/images/Shopping@2x.png"),
        linkTo: ""
      },
      {
        key: "breakfast",
        title: "",
        imgSrc: require("../../assets/images/breakfast.png"),
        linkTo: "/breakfast"
      }
    ];
    this.myItems = [
      {
        label: "English",
        method: () => {
          this.hideAction("en");
        }
      },
      {
        label: "中文",
        method: () => {
          this.hideAction("zh");
        }
      },
      {
        label: "Cancel",
        method: () => {
          _this.$store.dispatch("changeLanguage");
        }
      }
    ];
    this.myItems2 = [
      {
        label: "English",
        method: () => {
          this.hideAction("en");
        }
      },
      {
        label: "中文",
        method: () => {
          this.hideAction("zh");
        }
      },
      {
        label: "日本語の",
        method: () => {
          this.hideAction("jp");
        }
      },
      {
        label: "Cancel",
        method: () => {
          _this.$store.dispatch("changeLanguage");
        }
      }
    ];
    this.facList = [
      {
        icon: "tv",
        imgSrc: require("../../assets/images/Group 5 Copy@2x.png")
      },
      {
        icon: "restaurant",
        imgSrc: require("../../assets/images/dining room@2x.png")
      },
      { icon: "wifi", imgSrc: require("../../assets/images/wifi@2x.png") },
      { icon: "Gymnasium", imgSrc: require("../../assets/images/gym@2x.png") },
      {
        icon: "swimmingpool",
        imgSrc: require("../../assets/images/icon-swimmingsool.png")
      },
      {
        icon: "residentslounge",
        imgSrc: require("../../assets/images/icon-laug.png")
      },
      {
        icon: "Reading Room",
        imgSrc: require("../../assets/images/icon-reading.png")
      },
      {
        icon: "Children's Play Room",
        imgSrc: require("../../assets/images/icon-child.png")
      },
      {
        icon: "Sauna Room",
        imgSrc: require("../../assets/images/icon-sauan.png")
      },

      { icon: "Yoga studio", imgSrc: require("../../assets/images/yoga.png") },
      {
        icon: "Video Room",
        imgSrc: require("../../assets/images/yingyuan.png")
      },
      { icon: "Massage / SPA", imgSrc: require("../../assets/images/spa.png") },
      {
        icon: "Business Center",
        imgSrc: require("../../assets/images/businesscenter.png")
      },
      {
        icon: "Golf simulator",
        imgSrc: require("../../assets/images/golf.png")
      },
      {
        icon: "Meeting Room",
        imgSrc: require("../../assets/images/meetingroom.png")
      },
      {
        icon: "Outdoor Terrace",
        imgSrc: require("../../assets/images/garden.png")
      }
    ];

    //设置首页标识
    localStorage.menuFlag = "home";
    //获取登录身份
    this.idType = this.$route.query.type;
    //设置底部菜单状态
    localStorage.footFlag = 1;
    //获取当前日期
    var m = new Array(
      "Jan",
      "Feb",
      "Mar",
      "Apr",
      "May",
      "Jun",
      "Jul",
      "Aug",
      "Spt",
      "Oct",
      "Nov",
      "Dec"
    );
    var tDay = new Date();
    this.startTime = m[tDay.getMonth()] + " " + tDay.getDate();
    //初始化语言
    this.$store.dispatch("firstLanguage");
    let params = {
      hotelid: this.hotelid,
      lang: localStorage.LANGUAGE
    };
    let params1 = {
      id: this.hotelid
    };

    this.$store.dispatch("getHotelListById", params1).then(res => {
      if (res.code == 0) {
        let data = res.data.list[0];
        if (data.invoice_id) {
          localStorage.INVOICEID = data.invoice_id;
        } else {
          localStorage.INVOICEID = "";
        }
        //是否显示机器人洗衣功能
        localStorage.WASHING_MACHINE = data.washing_machine;
      }
    });
    this.$store.dispatch("getHome", params).then(res => {
      if (res.code == 0) {
        //全部数据
        this.homeData = this.home.data;
        //天气数据  TODO 增加天气图标的判断--------------------未完成
        this.weather = this.home.data.wetherInfo;
        if (true) {
          this.weatherSrc = require("../../assets/images/icon-w-cloud.png");
        }

        //经纬度
        this.lat = this.home.data.lat;
        this.lng = this.home.data.lng;

        //整理按钮结果集
        for (var item in this.home.data.shortcutList) {
          for (var i in this.localshortcutList) {
            if (
              this.home.data.shortcutList[item].key ==
              this.localshortcutList[i].key
            ) {
              this.localshortcutList[i].title = this.home.data.shortcutList[
                item
              ].title;
              this.shortcutList.push(this.localshortcutList[i]);
            }
          }
        }

        $(document).ready(function() {
          setTimeout(function() {
            new Swiper(".index_content .swiper-container", {
              pagination: ".swiper-pagination",
              slidesPerView: 4,
              paginationClickable: true,
              spaceBetween: 0
              // navigation: {
              //   nextEl: ".swiper-button-next",
              //   prevEl: ".swiper-button-prev"
              // }
            });
          }, 300);
        });
      } else {
        this.$dialog.toast({ mes: res.msg, timeout: 1000 });
      }
    });
    var _this = this;
    $(document).ready(function() {
      $(".mask-actionsheet").click(function() {
        _this.$store.dispatch("changeLanguage");
      });
      $(".actionsheet-action").click(function() {
        _this.$store.dispatch("changeLanguage");
      });
    });

    //获取物业详情
    let params2 = {
      hotelid: this.hotelid,
      lang: localStorage.LANGUAGE
    };
    this.$store.dispatch("getHotelDetail", params2).then(res => {
      this.details = this.hotelDetail;
      this.lat = this.hotelDetail.lat;
      this.lng = this.hotelDetail.lng;
      // this.hotelDetail.trafficList.reverse();

      localStorage.bookUrl = this.hotelDetail.bookurl;

      //重组图标数据
      for (var item in this.hotelDetail.facilitiesList) {
        for (var i in this.facList) {
          if (
            this.hotelDetail.facilitiesList[item].icon == this.facList[i].icon
          ) {
            this.facList[i].pdf = this.hotelDetail.facilitiesList[item].pdf;
            this.facList[i].video = this.hotelDetail.facilitiesList[item].video;
            this.facList[i].detail = this.hotelDetail.facilitiesList[
              item
            ].detail;
            this.facList[i].name = this.hotelDetail.facilitiesList[item].name;
            this.facListShow.push(this.facList[i]);
          }
        }
      }
      //初始化swiper
      $(document).ready(function() {
        setTimeout(function() {
          var swiper = new Swiper(".equipment .swiper-container", {
            pagination: {
              el: ".swiper-pagination",
              type: "fraction"
            },
            slidesPerView: 5,
            paginationClickable: true,
            spaceBetween: 0
          });
          var swiper3 = new Swiper(".apartment .swiper-container", {
            pagination: ".swiper-pagination",
            slidesPerView: 1.1,
            paginationClickable: true,
            spaceBetween: 15
          });
          var swiper4 = new Swiper(".fire .swiper-container", {
            pagination: ".swiper-pagination",
            slidesPerView: 1.2,
            paginationClickable: true,
            spaceBetween: 15
          });
          var swiper2 = new Swiper(".virtual .swiper-container", {
            pagination: ".swiper-pagination",
            slidesPerView: 3,
            paginationClickable: true,
            spaceBetween: 0
          });
        }, 300);
      });
    });

    $(function(){
        $(".navbar-center").css('marginLeft',0);
    });
  },
  mounted: function() {
    //一级页面falg
    isHomePage(1);
  },
  methods: {
    //显示菜单
    showMenu: function() {
      this.menuFlag = true; //显示菜单
      this.centerFlag = false; //隐藏个人中心
      $(".index").css({
        position: "absolute",
        height: "100%",
        width: "100%",
        overflow: "hidden",
        "z-index": "350"
      });
      if (this.menuSlide == false) {
        $(".index").css({
          left: "-5rem"
        });
        $("footer").css({
          left: "-5rem"
        });

        $(".mark").css({
          //						'left':'-5rem',
          opacity: 1
        });
        this.menuSlide = true;
        this.markFlag = true;
      }
    },
    //显示个人中心
    showCenter: function() {
      $(".index").css({
        position: "absolute",
        height: "100%",
        width: "100%",
        overflow: "hidden",
        "z-index": "350"
      });
      this.menuFlag = false; //隐藏菜单
      this.centerFlag = true; //显示个人中心
      if (this.centerSlide == false) {
        $(".index").css({
          left: "4.5rem"
        });
        $("footer").css({
          left: "4.5rem"
        });
        $(".mark").css({
          opacity: 1
        });
        this.centerSlide = true;
        this.markFlag = true;
      }
    },
    //点击页面，收起抽屉效果
    hideSlide: function() {
      //如果是菜单展开，收起菜单
      if (this.menuSlide) {
        $(".index").css({
          left: 0
        });
        $("footer").css({
          left: 0
        });
        $(".mark").css({
          opacity: 0
        });
        this.menuSlide = false;
        this.markFlag = false;
        //					this.menuFlag = false;
      }
      //如果是个人中心展开，收起个人中心
      if (this.centerSlide) {
        $(".index").css({
          left: 0
        });
        $("footer").css({
          left: 0
        });
        $(".mark").css({
          opacity: 0
        });
        this.centerSlide = false;
        this.markFlag = false;
        //					this.centerFlag = false;
      }
    },
    hideAction: function(key) {
      let _this = this;
      //更新本地语言标识
      localStorage.LANGUAGE = key;
      let params1 = {
        token: localStorage.TOKEN,
        platform: localStorage.platform,
        identity: localStorage.identity,
        lang: key
      };
      this.$store.dispatch("tabLanguage", params1);
      this.$store.dispatch("updateLanguage", key);
      this.$store.dispatch("changeLanguage");
      //更新首页数据
      let params = {
        hotelid: this.hotelid,
        lang: localStorage.LANGUAGE
      };
      this.$store.dispatch("getHome", params).then(function(res) {
        _this.shortcutList = [];
        //整理按钮结果集
        for (var item in _this.home.data.shortcutList) {
          for (var i in _this.localshortcutList) {
            if (
              _this.home.data.shortcutList[item].key ==
              _this.localshortcutList[i].key
            ) {
              _this.localshortcutList[i].title =
                _this.home.data.shortcutList[item].title;
              _this.shortcutList.push(_this.localshortcutList[i]);
            }
          }
        }
      });

      //更新物业详情数据
      let params2 = {
        hotelid: this.hotelid,
        lang: localStorage.LANGUAGE
      };
      this.$store.dispatch("getHotelDetail", params2);

      if (this.tabLanShow2 == true) {
        this.$store.dispatch("changeLanguage2");
      }

      //跟新自主服务标识
      if(key!='zh'){
        this.servicesMsg = 'Services';
      }else{
        this.servicesMsg = '快捷服务';
      }
    },
    cancel: function() {
      if (this.tabLanShow2 == true) {
        this.$store.dispatch("changeLanguage2");
      }
    },
    //地图导航
    goLocation: function(val) {
      // if (localStorage.HOTELID == 1 || localStorage.HOTELID == 7) {
      //   if (localStorage.TOKEN) {
      //     // this.$router.push("/shopping");
      //     this.$router.push({ path: "/buy", query: { info: "home" } });
      //   } else {
      //     this.$router.replace("/loginforguest");
      //   }
      // } else {
      //   this.$router.push("/map");
      // }
        //机器人购物
		if(val===2){
			//判断是否登录
			if (localStorage.TOKEN) {
				let goPath = "/shopping";
        //console.log("=======HotileId:"+localStorage.HOTELID);//21,7
        if(localStorage.HOTELID==21||localStorage.HOTELID==7){
            localStorage.NEWTYPE=0;
            goPath = "/robotDelivery";
        }
        this.$router.push({ path: goPath, query: { info: "home" } });
			}else{
				this.$router.replace("/loginforguest");
			}
		}else{
			this.$router.push("/map");
		}
    },
    goNext: function(key) {
      let _this = this;
      switch (key) {
        case "control":
          _this.$router.replace("#");
          break;
        case "reservation":
          openBooking(localStorage.bookUrl);
          //						_this.$router.replace('/home/homebooking')
          break;
        case "tanslate":
          _this.$router.push("/translate");
          break;
        case "service":
          if (localStorage.TOKEN) {
            if (localStorage.idType == 1) {
              if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
                openFile(localStorage.serviceUrl);
              } else {
                openUrl(localStorage.serviceUrl, " ");
              }
            } else {
              alert("没有权限进入该模块");
            }
            //							_this.$router.replace('/home/service')
          } else {
            _this.$router.replace("/loginforguest");
          }
          break;
        case "flight":
          _this.$router.push("#");
          break;
        case "phone":
          _this.$router.push("/phone");
          break;
        case "bills":
          _this.$router.push("/mybill");
          break;
        case "invoice":
          if (localStorage.TOKEN) {
            _this.$router.push("/invoices");
          } else {
            _this.$router.replace("/loginforguest");
          }
          break;
        case "news":
          _this.$router.push("/ssr");
          break;
        case "lifeservice":
          _this.$router.push("/lifeStyle");
          break;
        case "shop":
          if (localStorage.TOKEN) {
            _this.$router.push({
              path: "/shopping",
              query: { pageFlag: "s_home" }
            });
          } else {
            _this.$router.replace("/loginforguest");
          }
          break;
        case "breakfast":
          if (localStorage.TOKEN) {
            _this.$router.push({
              path: "/breakfast",
              query: { pageFlag: "s_home" }
            });
          } else {
            _this.$router.replace("/loginforguest");
          }
          break;
      }
    },
    goBack: function() {
      if (this.$route.query.page == "unlogin" || localStorage.spage == 1) {
        this.$router.push("/unlogin");
      } else if (
        this.$route.query.page == "search" ||
        localStorage.spage == 2
      ) {
        this.$router.push("/search");
      }
    },
    goTraffic: function(item) {
      this.$router.push({ path: "/home/trafficDetail", query: { info: item } });
    },
    openBooking: function(url) {
      openBooking(url);
    }
  },
  components: {
    "v-foot": foot,
    "v-menu": menu,
    "v-guestCenter": guestCenter,
    Actionsheet
  },
  computed: {
    ...mapState({
      language: state => state.language.language,
      home: state => state.home.home,
      tabLanShow2: state => state.language.tabLanShow2,
      hotelDetail: state => state.home.hotelDetail
    })
  }
};
</script>
<style>

</style>
