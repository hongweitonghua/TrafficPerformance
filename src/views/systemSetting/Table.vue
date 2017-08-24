<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" >
				<el-form-item>
					<el-button type="primary" @click="handleAdd" class="colorPrimary">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="users" stripe  highlight-current-row v-loading="listLoading" style="width: 100%;">
			<!-- <el-table-column type="selection" width="55">
			</el-table-column> -->
			<el-table-column type="index" width="60">
			</el-table-column>
			<el-table-column prop="name" label="姓名" width="120" sortable>
			</el-table-column>
			<el-table-column prop="roleName" label="所属单位编号" width="200" sortable>
			</el-table-column>
			<el-table-column prop="roleTypeName" label="角色" width="100" sortable>
			</el-table-column>
			<el-table-column prop="upAuth" label="注册日期" width="110" sortable>
			</el-table-column>
			<el-table-column prop="phone" label="电话号码" width="150" sortable>
			</el-table-column>
			<el-table-column prop="p1_p2" label="权限范围" min-width="180" sortable>
			</el-table-column>
			<el-table-column label="操作" width="150">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->

		<!--编辑界面-->
		<el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
			<el-form :inline = "true":model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
				<el-form-item label="用户名" prop="username">
					<el-input v-model="editForm.username" :disabled="true" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="姓名" prop="name">
					<el-input  id = 'ID_name' v-model="editForm.name"  auto-complete="off" class = "itemWidth"></el-input>
				</el-form-item>
				<el-form-item label="电话号码" prop="phone">
					<el-input v-model="editForm.phone" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="企业编号" prop="roleName">
					<el-input v-model="editForm.roleName" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="角色类型" prop="roleTypeName">
					<el-select v-model="editForm.roleTypeName" placeholder = "请选择"  class = "itemWidth">
						<el-option key = "交通厅" label = "交通厅" value = "交通厅"></el-option>
						<el-option key = "运管" label = "运管" value = "运管"></el-option>
						<el-option key = "港航" label = "港航" value = "港航"></el-option>
						<el-option key = "企业" label = "企业" value = "企业"></el-option>
					</el-select>
				</el-form-item>
				
				<el-form-item label="权限范围" prop="p1_p2_arr">
				<div class="bottom">
					<el-tooltip class="item" effect="dark" content="Left Top 提示文字" placement="left-start">
						<el-cascader
						@change="onCityChange"
						:options="cityOption"
						v-model="editForm.p1_p2_arr"
						:props="props"></el-cascader>
			    	</el-tooltip>
		    	</div>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="editFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
			</div>
		</el-dialog>

		<!--新增界面-->
		<el-dialog title="新增" v-model="addFormVisible" :close-on-click-modal="false">
			<el-form :inline= "true":model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
				<el-form-item label="用户名" prop="username">
					<el-input v-model="addForm.username" auto-complete="off" class = "itemWidth"></el-input>
				</el-form-item>
				<el-form-item label="姓名" prop="name">
					<el-input v-model="addForm.name" auto-complete="off" ></el-input>
				</el-form-item>
				<el-form-item label="密码" prop="password" type = "password">
					<el-input v-model="addForm.password" auto-complete="off" id = "password"></el-input>
				</el-form-item>
				<el-form-item label="电话号码" prop="phone">
					<el-input v-model="addForm.phone" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="企业编号" prop="roleName">
					<el-input v-model="addForm.roleName" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="角色类型">
					<el-select v-model="addForm.roleType" placeholder = "请选择"  class = "itemWidth">
						<el-option key = "交通厅" label = "交通厅" value = "交通厅"></el-option>
						<el-option key = "运管" label = "运管" value = "运管"></el-option>
						<el-option key = "港航" label = "港航" value = "港航"></el-option>
						<el-option key = "企业" label = "企业" value = "企业"></el-option>
					</el-select>
				</el-form-item>
				
				<el-form-item label="权限范围" prop="p1_p2">
				<div class="bottom">
					<el-tooltip class="item" effect="dark" content="Left Top 提示文字" placement="left-start">
						<el-cascader
						@change="onCityChangeAdd"
						:options="cityOption"
						v-model="addForm.p1_p2_arr"
						:props="props"></el-cascader>
			    	</el-tooltip>
		    	</div>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="addFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import util from '../../common/js/util'
	//import NProgress from 'nprogress'
	import { getUserListPage, removeUser, batchRemoveUser, editUser, addUser } from '../../api/api';

	export default {
		data() {
			return {
				users: [],
				listLoading: false,

				cityOption:[],
				props:{value: 'label',children: 'cities' },

				editFormVisible: false,//编辑界面是否显示
				editLoading: false,
				editFormRules: {
					// name: [
					// 	{ required: true, message: '请输入姓名', trigger: 'blur' }
					// ]
					userName: [
						{ required: true, message: '请输入用户名', trigger: 'blur' }
					],
					// roleName: [
					// 	{ required: true, message: '请输入企业编号', trigger: 'blur' }
					// ],
					roleTypeName: [
						{ required: true, message: '请输入角色类型', trigger: 'blur' }
					]
				},
				//编辑界面数据
				editForm: {
					id: 0,
					username:'',
					name: '',
					phone:'', 
					roleName:'',
					place1:'',
					place2:'',
					rolyType:'',
					p1_p2:'',
					p1_p2_arr:['',''],
					roleTypeName:''
				},

				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				addFormRules: {
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					],
					username: [
						{ required: true, message: '请输入用户名', trigger: 'blur' }
					],
					password: [
						{ required: true, message: '请输入密码', trigger: 'blur' }
					],
					roleName: [
						{ required: true, message: '如果是企业账户，请输入企业编号', trigger: 'blur' }
					],
					roleType: [
						{ required: true, message: '请输入角色类型', trigger: 'blur' }
					]

				},
				//新增界面数据
				addForm: {
					name: '',
					username:'',
					password: '',
					phone:'',
					roleName:'',
					place1:'',
					place2:'',
					roleType:'',
					p1_p2_arr:[]

				}
			}
		},
		methods: {
			setTest(){
				var n = document.getElementById('ID_name');
				console.log(n);
				n.setAttribute("disabled","true");
			},
			onCityChangeAdd(val){
				console.log(val);
				if(val[0]!='全省')
					this.addForm.place1 = val[0]
				else 
				   this.addForm.place1 = '';
				if(val[1]!='全市')
					this.addForm.place2 = val[1]
				else 
				   this.addForm.place2 = '';
			},
			onCityChange(val){
				console.log(val);
				if(val[0]!='全省')
					this.editForm.place1 = val[0]
				else 
				   this.editForm.place1 = '';
				if(val[1]!='全市')
					this.editForm.place2 = val[1]
				else 
				   this.editForm.place2 = '';
			},
			setCityInfo(){
				this.cityOption.splice(0,this.cityOption.length);
				var cities = this.cityinfo.cities;
				var counties = this.cityinfo.counties;
				for(var i=0;i<cities.length;i++){
					var tmpc = {};
					var tmpl = [];
					tmpc.label = cities[i];
					tmpc.value = cities[i];
					for(var j =0;j<counties[i].length;j++)
						tmpl.push({label:counties[i][j],value:counties[i][j]});
					tmpc.cities = tmpl;	
					this.cityOption.push(tmpc);
				}
				console.log(this.cityOption);
			},
			decoRoleType(roleType){
				if(!roleType) return '';
				if(roleType==='R_TRA') return '交通厅';
				else if(roleType==='R_LAN')  return '运管';
				else if(roleType==='R_WAT')  return '港航';
				else if(roleType==='R_ENT')  return '企业';
				else return roleType;
			},
			encoRoleType(roleTypeName){
				if(!roleTypeName) return '';
				if(roleTypeName==='交通厅') return 'R_TRA';
				else if(roleTypeName==='运管')  return 'R_LAN';
				else if(roleTypeName==='港航')  return 'R_WAT';
				else if(roleTypeName==='企业')  return 'R_ENT';
				else return roleTypeName;
			},
			decoPlace(place1,place2){
				if((!place1||place1=='')&&(!place2||place2=='')) return '全省 / 全市';
				else if(place1&&place2!=''&&(!place2||place2=='')) return place1 + ' / 全市';
				else if(place1&&place2&&place1!=''&&place2!='') return place1+' / '+place2;
			},
			encoPlaceArr(place1,place2){
				var placearr = [];
				if(!place1||place1=='')
					placearr[0] = '全省';
				else 
					placearr[0] = this.editForm.place1;
				if(!place2||place2=='')
					placearr[1] = '全市';
				else 
					placearr[1] = this.editForm.place2;
				return placearr;
			},
			setUserList(res){
				this.users.splice(0,this.users.length);
				var usersre = res.users;
				var _this = this;
				usersre.forEach(function(user) {
					user.roleTypeName = _this.decoRoleType(user.roleType);
					user.p1_p2 = _this.decoPlace(user.place1,user.place2);
					_this.users.push(user);
				});
			},
			//获取用户列表
			getUsers() {
				this.listLoading = true;
				var _this = this;
				$.get(this.Constant.ajaxAddress+this.Constant.getUserListAjax,
					{token:_this.$token}).done(function(data){
						_this.listLoading = false;
						console.log(data);
						_this.setUserList(data);
					});
				
			},
			//删除
			handleDel(index, row) {
				this.$confirm('确认删除该记录吗?', '提示', {
					type: 'warning'
				}).then(() => {
					this.listLoading = true;
					//NProgress.start();
					var _this = this;
					$.get(this.Constant.ajaxAddress+this.Constant.deleteUserAjax,
					{token:this.$token,username:row.username}).
						done(function(data){
							this.listLoading = false;
							if(data.errCode==20){
								 _this.$message('删除成功！');
								_this.getUsers();
							}else if(data.errCode==21){
								_this.$message.error('删除失败，未知错误！'); 
							}else if(data.errCode==44)
								_this.$router.push('/login');
					})
				}).catch(() => {

				});
			},
			//显示编辑界面
			handleEdit(index, row) {
				this.editFormVisible = true;
				this.editForm = Object.assign({}, row);
				this.editForm.p1_p2_arr = 
						this.encoPlaceArr(this.editForm.place1,this.editForm.place2);
				console.log('before edit editForm ',this.editForm);
				//this.setTest();
				
			},
			//显示新增界面
			handleAdd () {
				this.addFormVisible = true;
					this.addForm = {
					name: '',
					username:'',
					password: '',
					phone:'',
					roleName:'',
					place1:'',
					place2:'',
					roleType:'交通厅',
					p1_p2_arr:['全省','全市']
				}
			},
			//编辑
			editSubmit() {
				
				console.log('after edit editForm ',this.editForm);
				this.$confirm('确认提交吗？', '提示', {}).then(() => {
					this.editLoading = true;
					let para = Object.assign({}, this.editForm);
					var ui = {};
					ui.token = this.$token;
					ui.username = para.username;
					ui.roleType = this.encoRoleType(para.roleTypeName);
					ui.name = para.name;
					if(para.place1==='')
						delete ui.place1;
					else
						ui.place1 = para.place1;
					if(para.place2==='')
						delete ui.place2;
					else
						ui.place2 = para.place2;

					if(para.roleName==='')
						delete ui.roleName;
					else
						ui.roleName = para.roleName;
						
					if(para.phone==='')
						delete ui.phone;
					else
						ui.phone = para.phone;
					console.log('after edit ui ',ui);
					var _this = this;
					$.get(this.Constant.ajaxAddress+this.Constant.setUserInfoAjax,ui).
						done(function(data){
							console.log('ajax return ',data);
							_this.editLoading = false;							
							if(data.errCode==20){
								 _this.$message('修改成功！');
								_this.$refs['editForm'].resetFields();
								_this.editFormVisible = false;		
								_this.getUsers();
							}else if(data.errCode==21){
								_this.$message.error('修改失败，未知错误！'); 
							}else if(data.errCode==44)
								_this.$router.push('/login');
					});


				});
			},
			//新增
			addSubmit() {
				this.$confirm('确认提交吗？', '提示', {}).then(() => {
					this.addLoading = true;
					//NProgress.start();
					let para = Object.assign({}, this.addForm);
					console.log(para);
					var ui = {};
					ui.token = this.$token;
					ui.username = para.username;
					ui.roleType = this.encoRoleType(para.roleType);
					ui.name = para.name;
					ui.password = $.md5(para.password);
					if(para.place1==='')
						delete ui.place1;
					else
						ui.place1 = para.place1;
					if(para.place2==='')
						delete ui.place2;
					else
						ui.place2 = para.place2;

					if(para.roleName==='')
						delete ui.roleName;
					else
						ui.roleName = para.roleName;
						
					if(para.phone==='')
						delete ui.phone;
					else
						ui.phone = para.phone;
					console.log('after edit ui ',ui);

					var _this = this;
					$.get(this.Constant.ajaxAddress+this.Constant.regUserAjax,ui).
						done(function(data){
							console.log('ajax return ',data);
							_this.addLoading = false;							
							if(data.errCode==20){
								 _this.$message('添加用户成功！');
								_this.$refs['addForm'].resetFields();
								_this.addFormVisible = false;
								_this.getUsers();
							}else if(data.errCode==21){
								_this.$message.error('添加失败，未知错误！'); 
							}else if(data.errCode == 22){
								_this.$message.error('用户名重复，请修改用户名！'); 
							}
							else if(data.errCode==44)
								_this.$router.push('/login');
					});
				});
			},
		},
		mounted() {
			this.getUsers();
			this.setCityInfo();
			
		}
	}

</script>

<style scoped lang="scss">
@import '~scss_vars';

.colorPrimary{
	color: #fff;
	background-color: $color-primary;
    border-color: $color-primary;
}
.colorPrimary:hover{
	color: #fff;
	background-color: $color-primary-hover;
    border-color: $color-primary-hover;
}
.itemWidth{
	width:190px;
}

</style>