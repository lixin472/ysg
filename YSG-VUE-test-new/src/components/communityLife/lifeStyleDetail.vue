<template>
    <div class="common_nav_style">
        <div class="nav_mark"></div>
        <yd-navbar title="LIFESTYLE" fixed>
            <router-link :to="{path:'/lifeStyle'}" slot="left">
                <span class="back"></span>
            </router-link>
        </yd-navbar>
        <div class="webview">
            <iframe id="photo" :src="url" frameborder="0" scrolling="yes"></iframe>
        </div>
    </div>
</template>
<style>
</style>

<script>
	import { mapGetters } from 'vuex'
	import { mapState } from 'vuex'
	export default {
		data() {
			return {
				panoramicSrc:'',
				url:''
			};
		},
        mounted:function () {
		    this.url = this.$route.query.url
            //一级页面falg
            isHomePage(0)
        },
		created:function () {
		}
	};
</script>
