<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class="pos-order" id="order-list">
       <el-tabs>
        <el-tab-pane label="点餐">
           <el-table :data="tableData">
             <el-table-column prop="goodsName" label="商品名称">
             </el-table-column>
             <el-table-column prop="count" label="数量" width="50">
             </el-table-column>
             <el-table-column prop="price" label="金额" width="70">
             </el-table-column>
             <el-table-column label="操作" fixed="right" width="100">
              <template scope="scope">
             
              <el-button type="text" size="small" @click="delGoods(scope.row)">删除</el-button>
              <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
              </template>
             </el-table-column>
           </el-table>
          <div class="div-total">
              <span>数量:{{totalNum}}</span>
             <span>总价:{{totalPrice}}元</span>
           </div>
           <div class="div-btn">
              <el-button type="warning" size="small">挂单</el-button>
              <el-button type="danger" size="small" @click="delAllGoods()">删除</el-button>
              <el-button type="success" size="small" @click="checkout()">结账</el-button>
           </div>
         </el-tab-pane>
        <el-tab-pane label="挂单">
          挂单
        </el-tab-pane>
        <el-tab-pane label="外卖">
             外卖
        </el-tab-pane>

       </el-tabs>
      </el-col>
      <el-col :span='17'>
        <div class="often-goods"> 
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goods-type">
        <el-tabs>
         <el-tab-pane label="汉堡">
           <div>
             <ul class='cookList'>
                  <li v-for="goods in type0Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
           </div>
         </el-tab-pane>
          <el-tab-pane label="饮料">
           <div>
             <ul class='cookList'>
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
           </div>
         </el-tab-pane>
          <el-tab-pane label="小吃">
           <div>
             <ul class='cookList'>
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
           </div>
         </el-tab-pane>
          <el-tab-pane label="套餐">
           <div>
             <ul class='cookList'>
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
           </div>
         </el-tab-pane>
      

       </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'pos',
  data(){
     return{
        tableData:[],
        oftenGoods:[ ],
        type0Goods:[ ],
        type1Goods:[ ],
        type2Goods:[ ],
        type3Goods:[ ],
        totalNum:0,
        totalPrice:0,
     }
  },
  created:function(){
      axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods")
      .then(response=>{
          this.oftenGoods=response.data;
      })
      .catch(error=>{
        alert("网络错误！");
      });
      axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods")
      .then(response=>{
          this.type0Goods=response.data[0];
            this.type1Goods=response.data[1];
            this.type2Goods=response.data[2];
            this.type3Goods=response.data[3];
      })
      .catch(error=>{
        alert("网络错误！");
      })
  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeight+"px";
  },
  methods:{
    //模拟结账
    checkout(){
        if(this.totalNum!=0){
          this.tableData=[];
          this.totalNum=0;
          this.totalPrice=0;
          this.$message({
            message:"结账成功！感谢有你！",
            type:'success'
          })
        }else{
          this.$message.error('不能空结账！你好皮啊！');
        }
    },
    delGoods(goods){
    this.tableData=this.tableData.filter(o=>o.goodsId!= goods.goodsId);
    this.computTotal();
    },
    delAllGoods(){
      this.tableData=[];
      this.totalNum=0;
      this.totalPrice=0;
    },
    computTotal(){
      this.totalPrice=0;
      this.totalNum=0;
      if(this.tableData){
        this.tableData.forEach(el=>{
        this.totalNum+=el.count;
        this.totalPrice+=el.count*el.price;
        })
      }
    },
    addOrderList(goods){
    
      ///判断商品是否存在于订单列表
       let isHave=false;
       for(let i=0;i<this.tableData.length;i++){
         if(this.tableData[i].goodsId==goods.goodsId){
                isHave=true;
         }
       }
      ///////////
      if(isHave){
        //改变列表中商品数量
        let arr = this.tableData.filter(o=>o.goodsId==goods.goodsId);
        arr[0].count++;

      }else{
        let newGoods = {
              goodsId:goods.goodsId,
              goodsName:goods.goodsName,
              price:goods.price,
              count:1};
        this.tableData.push(newGoods);
      }
      this.computTotal();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn{
  margin-top: 10px;
}
.div-total{
  margin-top: 10px;
  background-color:#ffffff;
  padding:10px;
}
.div-total span{
  display:inline-block;
  padding:10px;
}
.title{
  height: 20px;
  border-bottom: 1px solid #c1c1cc;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li{
  list-style: none;
  float: left;
  border:1px solid #c1c1c1;
  padding: 10px;
  margin: 10px;
  background-color: #ffffff;
}
.o-price{
  color: #5887ff;
}
.goods-type{
  padding: 10px;
  clear: both;
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
       cursor:pointer;
 
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40px;;
       height: 60px;
       overflow: hidden;
   }
   .foodName{
       font-size: 16px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 12px;
       padding-left: 10px;
       padding-top:10px;
   }
</style>
