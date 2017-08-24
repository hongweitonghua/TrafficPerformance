<template>
	<section>
		<!-- 工具条 -->
		<el-col :span = "24" class="toolbar" style = "padding-bottom:0">
			<el-form :inline = "true" :model = "filters">
				<el-form-item>
					<el-input v-model = "filters.carNum" placeholder = "车辆牌号"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type = "primary" class = "colorPrimary" v-on:click = "getCars">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button type = "primary" class = "colorPrimary" @click = "handleAdd">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!-- 列表  v-for ="item in carItems"  {{item.carNum}}-->
		<el-table :data = "carData">
			<el-table-column fixed type = "selection" width = "35" text-align = "center"></el-table-column>
			<el-table-column fixed type = "index" width = "40"></el-table-column>
			<el-table-column fixed type = "carNum" label = "车辆牌号" width = "120" sortable prop = "carNum" text-align = "center"></el-table-column>
			<el-table-column type = "carType" label = "车辆类型" width = "120" sortable prop = "carType" text-align = "center"></el-table-column>
			<el-table-column type = "carBrand" label = "车辆品牌" width = "120" sortable prop = "carBrand" text-align = "center"></el-table-column>
			<el-table-column type = "carModel" label = "车辆型号" width = "120" sortable prop = "carModel"></el-table-column>
			<el-table-column type = "energyCost" label = "综合燃料消耗量" width = "155" sortable prop = "energyCost"></el-table-column>
			<el-table-column type = "airEnergyCost" label = "空载等速燃料消耗量" width = "185" sortable prop = "airEnergyCost"></el-table-column>
			<el-table-column type = "operateNum" label = "营运证号码" width = "130" sortable prop = "operateNum"></el-table-column>
			<el-table-column type = "operateStartTime" label = "营运证获取时间" width = "155" sortable prop = "operateStartTime"></el-table-column>
			<el-table-column fixed = "right" label="操作" width="150">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!-- 新增界面 -->
		<el-dialog v-model="addFormVisible" :close-on-click-modal="false" title = "新增" class = "boxWidth">
			<el-form :inline= "true":model = "addForm" label-width = "110px" ref = "addForm">
				<el-form-item label = "车辆牌号" text-align = "center" prop = "carNum">
					<el-input v-model = "addForm.carNum" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "营运证号码" prop = "operateNum">
					<el-input v-model = "addForm.operateNum" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label="车辆类型" prop="carType">
			        <el-select v-model="addForm.carType"  class = "input-in">
			          <el-option label="客车" value="keche"></el-option>
			          <el-option label="货车" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="车辆品牌" prop="carBrand">
			        <el-select v-model="addForm.carBrand"  class = "input-in">
			          <el-option label="品牌1" value="keche"></el-option>
			          <el-option label="品牌2" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="车辆型号" prop="carModel">
			        <el-select v-model="addForm.carModel"  class = "input-in">
			          <el-option label="型号1" value="model1"></el-option>
			          <el-option label="型号2" value="model2"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label = "生产企业" prop = "producer">
					<el-input v-model = "addForm.producer" auto-complete = "off"></el-input>
				</el-form-item>
		        <el-form-item label = "营运证获取时间" text-align = "center" prop = "operateStartTime">
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

		<!-- 编辑界面 -->
		<el-dialog v-model="editFormVisible" :close-on-click-modal="false" title = "编辑" >
			<el-form :inline= "true":model = "editForm" label-width = "140px" ref = "editForm">
				<el-form-item label = "车辆牌号" prop = "carNum">
					<el-input v-model = "editForm.carNum" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "营运证号码" prop = "operateNum">
					<el-input v-model = "editForm.operateNum" auto-complete = "off"></el-input>
				</el-form-item>
				
				<el-form-item label="车辆类型" prop="carType">
			        <el-select v-model="editForm.carType"  class = "input-in">
			          <el-option label="客车" value="keche"></el-option>
			          <el-option label="货车" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="车辆品牌" prop="carBrand">
			        <el-select v-model="editForm.carBrand"  class = "input-in">
			          <el-option label="品牌1" value="keche"></el-option>
			          <el-option label="品牌2" value="huoche"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label="车辆型号" prop="carModel">
			        <el-select v-model="editForm.carModel"  class = "input-in">
			          <el-option label="型号1" value="model1"></el-option>
			          <el-option label="型号2" value="model2"></el-option>
			        </el-select>
		        </el-form-item>
		        <el-form-item label = "生产企业" prop = "producer">
					<el-input v-model = "editForm.producer" auto-complete = "off"></el-input>
				</el-form-item>
		        <el-form-item label = "营运证获取时间" prop = "operateStartTime">
					<el-input v-model = "editForm.operateStartTime" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "综合燃料消耗量" prop = "energyCost">
					<el-input v-model = "editForm.energyCost" auto-complete = "off"></el-input>
				</el-form-item>
				<el-form-item label = "空载等速燃料消耗量" prop = "airEnergyCost">
					<el-input v-model = "editForm.airEnergyCost" auto-complete = "off"></el-input>
				</el-form-item>
			</el-form>
			<div slot = "footer" class = "dialog-footer" style="text-align:center">
				<el-button type = "primary" @click.native = "editSubmit" :loading = "editLoading">提交</el-button>
				<el-button type = "primary" @click.native =" editFormVisible = false">取消</el-button>
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
				},
				//当前用户列表
				carData:[
					{carNum:'001',operateNum:'002',operateStartTime:'2016-05-03',carType:'中型',carModel:'客车',carBrand:'金杯',energyCost:'100W',airEnergyCost:'100L'},
					{carNum:'003',operateNum:'004',operateStartTime:'2016-05-04',carType:'小型',carModel:'货车',carBrand:'一汽',energyCost:'90W',airEnergyCost:'90L'}
				],
				carDataEdit:[
					{carNum:'001修',operateNum:'002',operateStartTime:'2016-05-03',carType:'中型',carModel:'客车',carBrand:'金杯',energyCost:'100W',airEnergyCost:'100L'}
					
				],
				editLoading:false,
				editFormVisible:false,
				editForm:{
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
			//获取车辆列表
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
							this.carData.push({carNum:'002',operateNum:'004',operateStartTime:'2017-05-03',carType:'中型',carModel:'客车',carBrand:'金杯',energyCost:'100W',airEnergyCost:'100L'})
							this.addLoading = false;
							this.$message({
								message:'添加成功',
								type:'success'
							});
							this.$refs['addForm'].resetFields();
							this.addFormVisible = false;
					}
				});
			},
			handleEdit:function(index,row){
				this.editFormVisible = true;
				this.editForm = Object.assign({},row);

			},
			editSubmit:function(){
				this.$refs.editForm.validate((valid)=>{
					if(valid){
						this.editLoading = true;
						let para = Object.assign({},this.editForm);
						this.carData.push({carNum:'002修',operateNum:'004',operateStartTime:'2017-05-03',carType:'中型',carModel:'客车',carBrand:'金杯',energyCost:'100W',airEnergyCost:'100L'})
						this.editLoading = false;
						this.$message({
							message:'修改成功',
							type:'success'
						});
						this.$refs['editForm'].resetFields();
						this.editFormVisible = false;
						
					}
				})
			},
			deleteIndexElement:function(arr,index){
				var arrBefore = arr.slice(0,index);
				var arrAfter = arr.slice(index+1,arr.length);
				return arrBefore.concat(arrAfter);
			},
			deleteIndexElement:function(arr,index){
				let arrBefore = arr.slice(0,index);
				let arrAfter = arr.slice(index+1,arr.length);
				return arrBefore.concat(arrAfter);
			},
			handleDel:function(index,row){
				this.$confirm('确认删除该记录吗？','提示',{type:'warning'}).then(()=>{
					this.listLoading = true;
					let para = {id:row.id};
					// let arrBefore = this.carData.slice(0,index);
					// let arrAfter = this.carData.slice(index+1,this.carData.length);
					// this.carData = arrBefore.concat(arrAfter);
					this.carData.splice(index,1)
					this.listLoading = false
					this.$message({
						message:"删除成功",
						type:'success'
					});					
				})
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
    /*.boxWidth{border:1px solid red;}*/
    .boxWidth .el-dialog{width: 60% !important;}
</style>