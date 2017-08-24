<template>
  <el-form :inline= "true":model="form" ref = "form" label-width="110px">
  	<el-form-item label="组织机构代码" prop="code">
      <el-input v-model="form.code":disabled = "true"  class = "input-in" placeholder = "9438536563572"></el-input>
    </el-form-item>
    <el-form-item label="单位名称" prop="name">
      <el-input v-model="form.name":disabled = "true" class = "input-in" placeholder = "省交通厅xxx分局"></el-input>
    </el-form-item>
    <el-form-item label="从事业务类型" prop="region">
        <el-select v-model="form.type" placeholder="客运" class = "input-in">
          <el-option label="客运" value="keyun"></el-option>
          <el-option label="货运" value="huoyun"></el-option>
        </el-select>
    </el-form-item>
    <el-form-item label="业务能力规模" prop="scale">
        <el-input v-model="form.scale" class = "input-in" placeholder = "小型"></el-input>
    </el-form-item>
      
     
    <el-form-item label="年生产总量" prop="yearAccount">
        <el-input v-model="form.yearAccount" class = "input-in" placeholder = "100万"></el-input>
    </el-form-item>
    <el-form-item label="年效益量" prop="yearProfit">
        <el-input v-model="form.yearProfit" class = "input-in" placeholder = "70万"></el-input>
    </el-form-item>
      <el-form-item label="年总油耗" prop="yearOil">
        <el-input v-model="form.yearOil" class = "input-in" placeholder = "20吨"></el-input>
      </el-form-item>
      <el-form-item label="油耗成本" prop="yearCost">
        <el-input v-model="form.yearCost" class = "input-in" placeholder = "10万"></el-input>
      </el-form-item>
      <el-form-item label="年总耗气" prop="yearGas">
        <el-input v-model="form.yearGas" class = "input-in" placeholder = "200L"></el-input>
      </el-form-item>
      <el-form-item label="耗气成本" prop="yearGasCost">
        <el-input v-model="form.yearGasCost" class = "input-in" placeholder = "100万"></el-input>
      </el-form-item>
      <el-form-item label="年总耗电" prop="yearElectric">
        <el-input v-model="form.yearElectric" class = "input-in" placeholder = "300万"></el-input>
      </el-form-item>
       <el-form-item label="电力成本" prop="yearElectricCost">
        <el-input v-model="form.yearElectricCost" class = "input-in" placeholder = "150万"></el-input>
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
          type: '',
          scale:'',
          RegisDate:'',
          yearAccount:'',
          yearProfit:'',
          yearOil:'',
          yearCost:'',
          yearGas:'',
          yearGasCost:'', 
          yearElectric:'', 
          yearElectricCost:'', 
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