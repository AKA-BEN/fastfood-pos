<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs type="border-card">

            <!-- 点餐 -->
             <el-tab-pane label="点餐">
               <el-table :data="tableData" border style="width:100%;">
                 <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                 <el-table-column prop="count" label="数量" width="70"></el-table-column>
                 <el-table-column prop="price" label="价格" width="70"></el-table-column>
                 <el-table-column label="操作" width="100" fixed="right">
                   <template scope="scope">
                     <el-button type="text" size="small" @click="delOrderList(scope.row)">删除</el-button>
                     <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                   </template>
                 </el-table-column>
               </el-table>
               <div class="totalDiv">
                    <small>数量：</small>
                    <strong>{{totalCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                    <small>总计：</small>
                    <strong>{{totalMoney}}</strong> 元
               </div>
               <div class="div-btn">
                 <el-button type="warning" @click="putOrderList">挂单</el-button>
                 <el-button type="danger" @click="emptyOrderList">删除</el-button>
                 <el-button type="success" @click="checkout('tableData')">结账</el-button>
               </div>
             </el-tab-pane>

             <!-- 挂单 -->
             <el-tab-pane label="挂单">
                <el-table :data="putTableData" border style="width:100%;">
                 <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                 <el-table-column prop="count" label="数量" width="100"></el-table-column>
                 <el-table-column prop="price" label="价格" width="100"></el-table-column>
               </el-table>
                <div class="totalDiv">
                    <small>数量：</small>
                    <strong>{{putTotalCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                    <small>总计：</small>
                    <strong>{{putTotalMoney}}</strong> 元
               </div>
               <div class="div-btn">
                 <el-button type="success" @click="checkout('putTableData')">结账</el-button>
               </div>
             </el-tab-pane>

            <!-- 已结账 -->
             <el-tab-pane label="已结账">
                 <el-table :data = "checkoutData" style="width: 100%;">
                     <el-table-column type="expand">
                        <template slot-scope="props">
                            <el-form label-position="left" inline class="demo-table-expand">
                                <el-form-item v-for="(item,index) in props.row.goodsList" :key="index"  :label="item.goodsName">
                                    <span>{{item.count}}份/{{ item.price }}元</span>
                                </el-form-item>
                            </el-form>
                        </template>
                     </el-table-column>
                     <el-table-column label="订单号" prop="id"></el-table-column>
                     <el-table-column label="数量" prop="count"></el-table-column>
                     <el-table-column label="订单额" prop="amount"></el-table-column>
                 </el-table>
                <div class="totalDiv">
                    <small>总数量：</small>
                    <strong>{{checkoutCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                    <small>总金额：</small>
                    <strong>{{checkoutMoney}}</strong> 元
               </div>
             </el-tab-pane>

        </el-tabs>
      </el-col>
      <el-col :span="17">
        
        <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">        
                <ul>
                    <li v-for="item in oftenGoods" :key="item.goodsId" @click="addOrderList(item)">
                        <span>{{item.goodsName}}</span>
                        <span class="o-price">￥{{item.price}}元</span>
                    </li>
                </ul>
            </div>
        </div>

        <div class="goods-type">

          <el-tabs type="border-card">
              <el-tab-pane label="汉堡">
                  <ul class='cookList'>
                      <li v-for="item in type0Goods" :key="item.goodsId"  @click="addOrderList(item)">
                          <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                          <span class="foodName">{{item.goodsName}}</span>
                          <span class="foodPrice">￥{{item.price}}元</span>
                      </li>
                  </ul>
              </el-tab-pane>
              <el-tab-pane label="小食">
                  <ul class='cookList'>
                      <li v-for="item in type1Goods" :key="item.goodsId"  @click="addOrderList(item)">
                          <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                          <span class="foodName">{{item.goodsName}}</span>
                          <span class="foodPrice">￥{{item.price}}元</span>
                      </li>
                  </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                  <ul class='cookList'>
                      <li v-for="item in type2Goods" :key="item.goodsId"  @click="addOrderList(item)">
                          <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                          <span class="foodName">{{item.goodsName}}</span>
                          <span class="foodPrice">￥{{item.price}}元</span>
                      </li>
                  </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                  <ul class='cookList'>
                      <li v-for="item in type3Goods" :key="item.goodsId"  @click="addOrderList(item)">
                          <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                          <span class="foodName">{{item.goodsName}}</span>
                          <span class="foodPrice">￥{{item.price}}元</span>
                      </li>
                  </ul>
              </el-tab-pane>

          </el-tabs>
      </div>

      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  mounted: function () {
        var orderHeight = document.body.clientHeight;
        document.getElementById("order-list").style.height = orderHeight + 'px';
  },
  data: function(){
    return{
      tableData: [],
      putTableData: [],
      checkoutData: [],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalCount: 0,
      totalMoney: 0,
      putTotalCount: 0,
      putTotalMoney: 0,
      checkoutCount: 0,
      checkoutMoney: 0
    }
  },
  created: function(){

      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(response=>{
          this.oftenGoods = response.data;
      })
      .catch(err=>{
          console.log('请求错误：' + err);
      });

      axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response=>{
          this.type0Goods = response.data[0];
          this.type1Goods = response.data[1];
          this.type2Goods = response.data[2];
          this.type3Goods = response.data[3];
      })
  },
  methods: {

      addOrderList: function(goods){
          let isHave = false,
              x_order;
        //   判断列表是否已有商品
          for( let x = 0; x<this.tableData.length; x++ ){
              if( this.tableData[x].goodsId == goods.goodsId ){
                  isHave = true;
                  x_order = x;
                  break;
              }
          }
        //   添加到点餐列表
          if(isHave){
              this.tableData[x_order].count++;
          }else{
              let addGoods = goods;
              goods.count = 1;
              this.tableData.push(addGoods);
          }
        //   增加数量与价格
          this.totalCount++;
          this.totalMoney += goods.price;
      },

      delOrderList: function(goods){
        // 删除点餐行数据
          this.tableData = this.tableData.filter(o=>o.goodsId!=goods.goodsId);
          this.totalCount -= goods.count;
          this.totalMoney -= goods.count * goods.price;
      },

      emptyOrderList: function(){
        //   清空点餐列表
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
      },

      putOrderList(){
        //   挂单
          if( this.tableData.length <=0){
              this.$message.error('请勿空挂单！');
              return;
          }
        this.putTableData = this.tableData;
        this.putTotalCount = this.totalCount;
        this.putTotalMoney = this.totalMoney;
        this.$message({
            message: '挂单成功！',
            type: 'success'
        })

        // 清空点餐列表
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
      },

      checkout(typeList){
        //   结账
          if( this[typeList].length <=0){
              this.$message.error('请勿空结账！');
              return;
          }
          if(typeList == "tableData"){
            //   点餐
            this.checkout_update(this[typeList],this.totalCount,this.totalMoney);
            this.totalCount = 0;
            this.totalMoney = 0;
          }else{
            //   挂单
            this.checkout_update(this[typeList],this.putTotalCount,this.putTotalMoney);            
            this.putTotalCount = 0;
            this.putTotalMoney = 0;              
          }
          this[typeList] = [];
          this.$message({
              message: '结账成功！',
              type: 'success'
          })
      },

      checkout_update(goodsList,count,money){
        //   添加到已结账列表
          let checkoutList = {
              id: new Date().getTime(),
              count: count+'份',
              amount: money+'元',
              goodsList: goodsList
          }
          this.checkoutCount += count;
          this.checkoutMoney += money;
          this.checkoutData.push(checkoutList);
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos {
    font-size: 12px;
}
.pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
}
.div-btn{
  margin-top: 20px;
}
 .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:5px;
      background-color:#fff;
      cursor: pointer;
   }
  .o-price{
      color:#58B7FF; 
   }
   .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
 
   }
    .cookList li{
        cursor: pointer;
    }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .goods-type{
     padding-top: 30px;
     clear: both;
   }
   .totalDiv {
    height: auot;
    overflow: hidden;
    text-align: center;
    font-size: 16px;
    background-color: #fff;
    border-bottom: 1px solid #E5E9F2;
    padding: 10px;
}
.demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
</style>
