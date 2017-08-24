<template>
  <el-form :inline= "true":model="form" ref = "form" label-width="110px">
    <el-form-item label="组织机构代码" prop="code">
      <el-input v-model="form.code":disabled = "true"  class = "input-in" placeholder = "9438536563572"></el-input>
    </el-form-item>
    <el-form-item label="单位名称" prop="name">
      <el-input v-model="form.name":disabled = "true" class = "input-in" placeholder = "省交通厅xxx分局"></el-input>
    </el-form-item>
    <el-form-item label="所在地区" prop="region">
        <el-select v-model="form.region" placeholder="杭州市" class = "input-in">
          <el-option label="杭州市" value="shanghai"></el-option>
          <el-option label="衢州市" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="所处行业" prop="industry">
        <el-input v-model="form.industry" class = "input-in" placeholder = "交通运输"></el-input>
      </el-form-item>
      <el-form-item label="单位类型" prop="type">
        <el-select v-model="form.type" placeholder="选择单位的类型" class = "input-in">
          <el-option label="类型1" value="shanghai"></el-option>
          <el-option label="类型2" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="注册日期" prop="RegisDate">
        <el-date-picker v-model="form.RegisDate" type="date" class = "input-in" placeholder = "2017-07-19"></el-date-picker>
      </el-form-item>
     
      <el-form-item label="注册金额" prop="RegisAccount">
        <el-input v-model="form.RegisAccount" class = "input-in" placeholder = "100万"></el-input>
      </el-form-item>
      <el-form-item label="法人代表" prop="Boss">
        <el-input v-model="form.Boss" class = "input-in" placeholder = "xxx"></el-input>
      </el-form-item>
      <el-form-item label="联系电话" prop="tel">
        <el-input v-model="form.tel" class = "input-in" placeholder = "8788231"></el-input>
      </el-form-item>
      <el-form-item label="注册地址" prop="RegisAddress">
        <el-input v-model="form.RegisAddress" class = "input-in" placeholder = "浙江省杭州市新加坡工业园"></el-input>
      </el-form-item>
      <el-form-item label="注册地邮编" prop="RegisPostCode">
        <el-input v-model="form.RegisPostCode" class = "input-in" placeholder = "000000"></el-input>
      </el-form-item>
      <el-form-item label="经营地址" prop="SellAddress">
        <el-input v-model="form.SellAddress" class = "input-in" placeholder = "浙江省杭州市新加坡工业园"></el-input>
      </el-form-item>
      <el-form-item label="经营地邮编" prop="SellPostCode">
        <el-input v-model="form.SellPostCode" class = "input-in" placeholder = "000000"></el-input>
      </el-form-item>
       <el-form-item label="经营地电话" prop="SellTel">
        <el-input v-model="form.SellTel" class = "input-in" placeholder = "8788231"></el-input>
      </el-form-item>
      <el-form-item label="官方网址" prop="website">
        <el-input v-model="form.website" class = "input-in" placeholder = "www.baidu.com"></el-input>
      </el-form-item>
      <el-form-item label="传真号码" prop="Fax">
        <el-input v-model="form.Fax" class = "input-in" placeholder = "8788231"></el-input>
      </el-form-item>
      <el-form-item label="经营范围" prop="SellRange">
        <el-input v-model="form.website" class = "input-in" placeholder = "xxx"></el-input>
      </el-form-item>



  </el-form>
</template>

<script>
  import { mapGetters } from 'vuex'
  import { mapActions } from 'vuex'

  export default {
    data(){
      return {
        form:{
           code:'',
          name: '',
          region: '',
          industry:'',
          RegisDate:'',
          RegisAccount:'',
          Boss:'',
          tel:'',
          RegisAddress:'',
          RegisPostCode:'',
          SellAddress:'', 
          SellPostCode:'', 
          SellTel:'', 
          website:'', 
          Fax:'', 
          SellRange:'', 
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        }
      }
    },
    computed: {
  	// 使用对象展开运算符将 getters 混入 computed 对象中
    ...mapGetters([
      'getCount'
      // ...
    ])
    },
    methods: {
    ...mapActions([
      'increment', // 映射 this.increment() 为 this.$store.dispatch('increment')
      'decrement'
    ])
      //...mapActions({
      //  add: 'increment' // 映射 this.add() 为 this.$store.dispatch('increment')
      //})
    }
  }

</script>
<style scoped lang="scss">
  .input-in{
      width:190px;
    }
</style>