<template>
	<el-row class="container">
		<el-col :span="24" class="header">
			<el-col :span="10" class="logo" :class="collapsed?'logo-collapse-width':'logo-width'">
				{{collapsed?'':sysName}}
			</el-col>
			<el-col :span="4">
				<div class="tools" @click.prevent="collapse">
					<i class="fa fa-align-justify"></i>
				</div>
			</el-col>
			<el-col :span="10" class="userinfo">
				<el-dropdown trigger="hover">				
					<span id="name_span" class="el-dropdown-link userinfo-inner">admin</span>
					<el-dropdown-menu slot="dropdown">
						<el-dropdown-item @click.native="logout">退出登录</el-dropdown-item>
					</el-dropdown-menu>
				</el-dropdown>
			</el-col>
		</el-col>
		<el-col :span="24" class="main">
			<aside :class="collapsed?'menu-collapsed':'menu-expanded'">
				<!--导航菜单-->
				<el-menu :default-active="$route.path" class="el-menu-vertical-demo" @open="handleopen" @close="handleclose" @select="handleselect"
					 unique-opened router v-show="!collapsed"  id="sidebar-hook">
					<template v-for="(item,index) in $router.options.routes" v-if="!item.hidden">
						<el-submenu :index="index+''" v-if="!item.leaf">
							<template slot="title"><i :class="item.iconCls"></i>{{item.name}}</template>
							<el-menu-item v-for="child in item.children" :index="child.path" :key="child.path" v-if="!child.hidden &&child.pri&& child.pri.indexOf($userInfo.roleType)!=-1">{{child.name}}</el-menu-item>
						</el-submenu>
						<el-menu-item v-if="item.leaf&&item.children.length>0" :index="item.children[0].path"><i :class="item.iconCls"></i>{{item.children[0].name}}</el-menu-item>
					</template>
				</el-menu>
				<!--导航菜单-折叠后-->
				<ul class="el-menu el-menu-vertical-demo collapsed" v-show="collapsed" ref="menuCollapsed">
					<li v-for="(item,index) in $router.options.routes" v-if="!item.hidden" class="el-submenu item">
						<template v-if="!item.leaf">
							<div class="el-submenu__title" style="padding-left: 20px;" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)"><i :class="item.iconCls"></i></div>
							<ul class="el-menu submenu  submenu-scroll-hook" :class="'submenu-hook-'+index" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)"> 
								<li v-for="child in item.children" v-if="!child.hidden" :key="child.path" class="el-menu-item" style="padding-left: 40px;" :class="$route.path==child.path?'is-active':''" @click="$router.push(child.path)">{{child.name}}</li>
							</ul>
						</template>
						<template v-else>
							<li class="el-submenu">
								<div class="el-submenu__title el-menu-item" style="padding-left: 20px;height: 56px;line-height: 56px;padding: 0 20px;" :class="$route.path==item.children[0].path?'is-active':''" @click="$router.push(item.children[0].path)"><i :class="item.iconCls"></i></div>
							</li>
						</template>
					</li>
				</ul>
			</aside>
			<section class="content-container">
				<div class="grid-content bg-purple-light">
					<el-col :span="24" class="breadcrumb-container">
						<strong class="title" >{{$route.name}}</strong>
						<el-breadcrumb separator="/" class="breadcrumb-inner">
							<el-breadcrumb-item v-for="item in $route.matched" :key="item.path">
								{{ item.name }}
							</el-breadcrumb-item>
						</el-breadcrumb>
					</el-col>
					<el-col :span="24" class="content-wrapper">
						<transition name="fade" mode="out-in">
							<keep-alive><router-view></router-view></keep-alive>
						</transition>
					</el-col>
				</div>
				<div class="footer">
					<div class="page-footer-inner">
						 2017 &copy; Welcome to HangZhou DianZi University. 
					</div>
					<div class="scroll-to-top" @click="scrollToTop">
						<i class="fa fa-arrow-circle-up"></i>
					</div>
				</div>
			</section>
			
		</el-col>
	</el-row>
</template>

<script>

import {getCookie,delCookie,setCookie} from '../common/js/Cookie.js';



	export default {
		data() {
			return {
				sysName:'浙江省能耗统计',
				collapsed:false,

				form: {
					name: '',
					region: '',
					date1: '',
					date2: '',
					delivery: false,
					type: [],
					resource: '',
					desc: ''
				},


			}
		},

		methods: {
			onSubmit() {
				console.log('submit!');
			},
			handleopen() {
				//console.log('handleopen');
				console.log(this.$route.path);
			},
			handleclose() {
				//console.log('handleclose');
			},
			handleselect: function (a, b) {
			},
			//退出登录
			logout: function () {
				var _this = this;
				this.$confirm('确认退出吗?', '提示', {
					type: 'info'
				}).then(() => {
				_this.$router.push('/login');
            $.get(this.Constant.ajaxAddress+"/logout.json",
				{ username:_this.$userInfo.username,
				  token:_this.$token}).done(function(data){
					if(data.errCode==12){
						_this.$userInfo = null;
						_this.$token = null;
						sessionStorage.removeItem('user');

						_this.$router.push('/login');
					}else{
						window.alert("登出失败")
					}
				})
				}).catch(() => {

				});
			},
			//折叠导航栏
			collapse:function(){
				this.collapsed=!this.collapsed;
			},
			showMenu(i,status){
				this.$refs.menuCollapsed.getElementsByClassName('submenu-hook-'+i)[0].style.display=status?'block':'none';
			},
			scrollToTop(){
     			$('.content-container').animate({scrollTop: '0px'}, 500);   
			}

		},
		mounted() {

			 document.getElementById('name_span').innerHTML = this.$userInfo.username;

			console.log($('#sidebar-hook'));
			$('#sidebar-hook').niceScroll({
   					cursorcolor: "#6bc5a4",//#CC0071 光标颜色E4F1F1
				    cursoropacitymax: 1, //改变不透明度非常光标处于活动状态（scrollabar“可见”状态），范围从1到0
				    touchbehavior: false, //使光标拖动滚动像在台式电脑触摸设备
				    cursorwidth: "2px", //像素光标的宽度
				    cursorborder: "0", // 游标边框css定义
				    cursorborderradius: "5px",//以像素为光标边界半径
				    autohidemode: true, //是否隐藏滚动条
				    hidecursordelay: 400
			});
			
			$('.submenu-scroll-hook').each(function(){
			    //计算ul隐藏的高度是否大于屏幕高度，如果是，则出现滚动条
			    $(this).niceScroll({
   					cursorcolor: "#65CEA7",//#CC0071 光标颜色
				    cursoropacitymax: 1, //改变不透明度非常光标处于活动状态（scrollabar“可见”状态），范围从1到0
				    touchbehavior: false, //使光标拖动滚动像在台式电脑触摸设备
				    cursorwidth: "1px", //像素光标的宽度
				    cursorborder: "0", // 游标边框css定义
				    cursorborderradius: "5px",//以像素为光标边界半径
				    autohidemode: false //是否隐藏滚动条
				});
			});

			$('.content-container').niceScroll({
   					cursorcolor: "#5AB6DF",//#CC0071 光标颜色
				    cursoropacitymax: 1, //改变不透明度非常光标处于活动状态（scrollabar“可见”状态），范围从1到0
				    touchbehavior: false, //使光标拖动滚动像在台式电脑触摸设备
				    cursorwidth: "5px", //像素光标的宽度
				    cursorborder: "0", // 游标边框css定义
				    cursorborderradius: "5px",//以像素为光标边界半径
				    background: "#999",
				    autohidemode: true, //是否隐藏滚动条
				    hidecursordelay: 400
			});

			//滚到顶部出现隐藏
			$('.content-container').scroll(function() {   
			        if($('.content-container').scrollTop() >= 100){
			            $('.scroll-to-top').fadeIn(300); 
			        }else{    
			            $('.scroll-to-top').fadeOut(300);    
			        }  
			    });
			}
	}

</script>

<style scoped lang="scss">
	@import '~scss_vars';
	
	.container {
		position: absolute;
		top: 0px;
		bottom: 0px;
		width: 100%;
		& .el-button--primary {
    		color: #fff;
   			background-color: red;
    		border-color: red;
		}
		.header {
			height: 60px;
			line-height: 60px;
			background: $color-primary;
			color:#fff;
			.userinfo {
				text-align: right;
				padding-right: 35px;
				float: right;
				color:#fff;
				.role{
					margin-right: 10px;
				}
				.userinfo-inner {
					cursor: pointer;
					color: #fff;
					img {
						width: 40px;
						height: 40px;
						border-radius: 20px;
						margin: 10px 0px 10px 10px;
						float: right;
					}
				}
			}
			.logo {
				//width:230px;
				height:60px;
				font-size: 22px;
				padding-left:20px;
				padding-right:20px;
				border-color: rgba(238,241,146,0.3);
				border-right-width: 1px;
				border-right-style: solid;
				/*background: #424f63;*/
				img {
					width: 40px;
					float: left;
					margin: 10px 10px 10px 18px;
				}
				.txt {
					color:#fff;
				}
			}
			.logo-width{
				width:230px;
			}
			.logo-collapse-width{
				width:60px
			}
			.tools{
				padding: 0px 23px;
				width:14px;
				height: 60px;
				line-height: 60px;
				cursor: pointer;
			}
		}
		.main {
			display: flex;
			// background: #324057;
			position: absolute;
			top: 60px;
			bottom: 0px;
			overflow: hidden;
			aside {
				flex:0 0 230px;
				width: 230px;
				// position: absolute;
				// top: 0px;
				// bottom: 0px;
				.el-menu{
					height: 100%;

				}
				.collapsed{
					width:60px;
					.item{
						position: relative;
					}
					.submenu{
						position:absolute;
						top:0px;
						left:60px;
						z-index:99999;
						height:auto;
						display:none;
					}
					.submenu-scroll-hook{
						max-height: 600px;
					}
				}
			}
			.menu-collapsed{
				flex:0 0 60px;
				width: 60px;
			}
			.menu-expanded{
				flex:0 0 230px;
				width: 230px;
			}
			.content-container {
				// background: #f1f2f7;
				display: flex;
				flex-direction: column;
				flex:1;
				position: relative;
				// position: absolute;
				// right: 0px;
				// top: 0px;
				// bottom: 0px;
				// left: 230px;
				overflow-y: scroll;
				padding: 20px;
				.grid-content{
					flex: 1;
					.breadcrumb-container {
						//margin-bottom: 15px;
						.title {
							width: 200px;
							float: left;
							color: #475669;
						}
						.breadcrumb-inner {
							float: right;
						}
					}
					.content-wrapper {
						background-color: #fff;
						box-sizing: border-box;
					}
					
				}
				
				.footer{
					width: 100%;
					.page-footer-inner{
						margin-top: 20px;
						float: left;
					}
					.scroll-to-top{
						float: right;
						margin-top: 10px;
						padding: 0px 23px;
					    cursor: pointer;
						.fa-arrow-circle-up{
							font-size: 34px;
							line-height: 24px;
							color: #20a0ff;
						}
					}
				}
			}
			
		}

	}
</style>