<template>
	<section>

		<el-table :data="dataDict" stripe style="width: 100%;">
			<el-table-column type="index" label="序号" width="100" sortable>
			</el-table-column>
			<el-table-column prop="name" label="类型名称" width="200" sortable>
			</el-table-column>
			<el-table-column prop="typeS" label="分类方案" width="450" sortable>
			</el-table-column>
			
			<el-table-column label="操作" min-width="200">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--编辑界面-->
		<el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false">
			<el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
				<el-form-item label="类型名称" prop="name">
					<el-input v-model="editForm.name":disabled = "true" auto-complete="off" ></el-input>
				</el-form-item>
				<el-form-item label="分类含义">
					<el-input type="textarea" v-model="editForm.typeS"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click.native="editFormVisible = false">取消</el-button>
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import util from '../../common/js/util'
	//import { removeUser, batchRemoveUser, editUser, addUser } from '../../api/api';
	export default {
		data() {
			return {
				dataDict: [],
				editFormVisible: false,//编辑界面是否显示
					editLoading: false,
					editFormRules: {
						name: [
							{ required: true, message: '请输入字典名称', trigger: 'blur' }
						]
				},
				//编辑界面数据
					editForm: {
						index: 0,
						typeName:'',
						name: '',
						typeS: '',

					},
					
					
			}
		},
		methods: {
			//显示编辑界面
			handleEdit: function (index, row) {
				this.editFormVisible = true;
				this.editForm = Object.assign({}, row);
				//console.log(index, row);
				console.log(this.editForm);
			},
			
			//编辑
			editSubmit: function () {

				var _this = this;
				this.$confirm('确认提交吗?', '提示', {
					type: 'info'
				}).then(() => {
					this.editLoading = true;
					$.get(this.Constant.ajaxAddress+this.Constant.setDataDictAjax,
					{ token:_this.$token,typeName:_this.editForm.typeName,
					  name:_this.editForm.name,typeS:_this.editForm.typeS}).done(function(data){
						  _this.$message('修改成功！');
						  _this.editLoading = false;
						if(data.errCode==20){
							_this.editFormVisible = false;
							_this.getDataFromService();
						}else if(data.errCode==21){
							_this.$message.err('修改失败！');
						}else if(data.errCode==44)
							_this.$router.push('/login');
					})
				}).catch(() => {

				});
			},
			

			getDataFromService(){
				var _this = this;
				$.get(this.Constant.ajaxAddress+this.Constant.getDataDictAjax,
					{token:_this.$token}).done(function(data){
						console.log(data);
						if(data.errCode==20){

							_this.dataDict.splice(0,_this.dataDict.length);
							var dcs = data.dicts;
							for(var i = 1;i<=dcs.length;i++)
								_this.dataDict.push({index:i,typeName:dcs[i-1].typeName,name:dcs[i-1].name,typeS:dcs[i-1].typeS});

						}else if(data.errCode==21){
							window.alert("提交失败")
						}else if(data.errCode==44)
							_this.$router.push('/login');
					});
			}
		},

		mounted:function(){
			this.getDataFromService();		
		},
		updated:function(){
			//console.log("update");
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
</style>