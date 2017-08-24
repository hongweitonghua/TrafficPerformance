<template>
	<section>
		<!-- 工具条 -->
		<el-col :span = "24" class="toolbar" style = "padding-bottom:0">
			<el-form :inline = "true" :model = "filters">
				<el-form-item>
					<el-input v-model = "filters.carNum" placeholder = "船舶牌号"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type = "primary" class = "colorPrimary" v-on:click = "getCars">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button type = "primary" class = "colorPrimary" @click = "handleAdd">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!-- 列表 -->
		<el-table>
			<el-table-column type = "selection" width = "35"></el-table-column>
			<el-table-column type = "index" width = "45"></el-table-column>
			<el-table-column type = "carNum" label = "船舶牌号" width = "120" sortable></el-table-column>
			<el-table-column type = "operateNum" label = "营运证号码" width = "130" sortable></el-table-column>
			<el-table-column type = "operateStartTime" label = "营运证获取时间" width = "160" sortable></el-table-column>
			<el-table-column type = "carType" label = "船舶类型" width = "130" sortable></el-table-column>
			<el-table-column type = "carBrand" label = "船舶品牌" width = "120" sortable></el-table-column>
			<el-table-column type = "carModel" label = "船舶型号" width = "120" sortable></el-table-column>
			<el-table-column type = "energyCost" label = "综合燃料消耗量" width = "160" sortable></el-table-column>
			<el-table-column type = "energyCost" label = "综合燃料消耗量" width = "160" sortable></el-table-column>
		</el-table>
		<!-- 新增界面 -->
		<!-- <el-dialog title = "新增" v-model = "addFormVisible" :close-on-click-modal = "false">
			<el-form :model = "addForm" label-width = "80px" :rules = "addFormRules" ref = "addForm">
				<el-form-item label = "
				船舶牌号" prop = "carNum">
					<el-input v-model = "addForm.carNum" auto-complete = "off"></el-input>
				</el-form-item>
			</el-form>
		</el-dialog> -->
		<el-dialog v-model="addFormVisible" :close-on-click-modal="false" title = "新增">
			<el-form :inline = "true" :model = "addForm" label-width = "140px"  ref = "addForm">
				<el-form-item label = "船舶牌号" prop = "carNum">
					<el-input v-model = "addForm.carNum" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "营运证号码" prop = "operateNum">
					<el-input v-model = "addForm.operateNum" auto-complete = "off"></el-input>
				</el-form-item>
				
				<el-form-item label="船舶类型" prop="carType">
			        <el-select v-model="addForm.carType"  class = "input-in">
			          <el-option label="客车" value="keche"></el-option>
			          <el-option label="货车" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="船舶品牌" prop="carBrand">
			        <el-select v-model="addForm.carBrand"  class = "input-in">
			          <el-option label="品牌1" value="keche"></el-option>
			          <el-option label="品牌2" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="船舶型号" prop="carModel">
			        <el-select v-model="addForm.carModel"  class = "input-in">
			          <el-option label="型号1" value="model1"></el-option>
			          <el-option label="型号2" value="model2"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label = "生产企业" prop = "producer">
					<el-input v-model = "addForm.producer" auto-complete = "off"></el-input>
				</el-form-item>
		        <el-form-item label = "营运证获取时间" prop = "operateStartTime">
					<el-input v-model = "addForm.operateStartTime" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "综合燃料消耗量" prop = "energyCost">
					<el-input v-model = "addForm.energyCost" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "空载等速燃料消耗量" prop = "airEnergyCost">
					<el-input v-model = "addForm.airEnergyCost" auto-complete = "off"></el-input>
				</el-form-item>
				
				
			</el-form>
			<div slot = "footer" class = "dialog-footer" style="text-align:center">
				<el-button type = "primary" @click.native = "addSubmit" :loading = "addLoading">提交</el-button>
				<el-button type = "primary" @click.native =" addFormVisible = false">取消</el-button>
			</div>
		</el-dialog>


	</section>
</template>
<script>
	import util from '../../common/js/util'

	export default{
		data(){
			return {
				filters:{carNum:''},
				cars:[],
				total:0,
				page:1,
				listLoading:false,
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				addForm:{
					carNum:'',
					operateNum:'',
					operateStartTime:'',
					carType:'',
					carBrand:'',
					carModel:'',
					energyCost:'',
					airEnergyCost:'',
					producer:''
				}

			}
		},
		methods:{
			//获取船舶列表
			getCars(){
				let para = {
					page:this.page,
					carNum:this.filters.carNum
				};
				this.listLoading = true;
				getCarListPage(para).then((res)=>{
					this.total = res.data.total;
					this.cars = res.data.cars;
					this.listLoading = false;
				});
			},
			handleAdd:function(){

				this.addFormVisible = true;
				this.addForm = {
					carNum:'',
					operateNum:'',
					operateStartTime:'',
					carType:'',
					carBrand:'',
					carModel:'',
					energyCost:'',
					airEnergyCost:'',
					producer:''
					
				};
			},
			//新增
			addSubmit:function(){
				this.$refs.addForm.validate((valid) =>{
					if(valid){
						// this.$confirm('确认增加吗？','提示',{}).then(()=>{
							this.addLoading = true;
							let para = Object.assign({},this.addForm);
							addCarNum(para).then((res) =>{
								this.addLoading = false;
								this.$message({
									message:'添加成功',
									type:'success'

							});
								this.$refs['addForm'].resetFields();
								this.addFormVisible = false;
								this.getCarNum();
							});

						// })

					}
				});
			}






		},
		mounted(){}
	}
</script>

<style  scoped lang = "scss">
	@import '~scss_vars';

	.colorPrimary{
		color:#fff;
		background-color: $color-primary;
		border-color: $color-primary;
	}
	.colorPrimary:hover{
	color: #fff;
	background-color: $color-primary-hover;
    border-color: $color-primary-hover;
	}
	.input-in{
      width:190px;
    }
</style>