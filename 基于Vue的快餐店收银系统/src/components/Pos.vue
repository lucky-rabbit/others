<template>
	<div class="pos">
		<div>
		<!-- element标签 -->
		<!-- 产生的虚拟dom无法设置高度100%，可用vh代替，也可用原生方法 -->
			<el-row>
				<!-- 第一列占7格 -->
				<el-col :span='7' class='order'>
					<el-tabs>
				      <el-tab-pane label="点餐">
				        <el-table :data="tableData" border show-summary style="width: 100%" >
						    <el-table-column prop="goodsName" label="商品"  />
						    <el-table-column prop="count" label="量" width="50" />
						    <el-table-column prop="price" label="金额" width="70" />
						    <el-table-column  label="操作" width="100" fixed="right">
						        <template scope="scope">
						            <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
						            <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
						 
						        </template>
						    </el-table-column>
						</el-table>
						<div>数量：{{totalCount}}&nbsp;&nbsp;&nbsp;&nbsp;总价：{{totalMoney}}</div>

						<div>
							<el-button type="warning" >挂单</el-button>
							<el-button type="danger" @click='delAllGoods()'>删除</el-button>
							<el-button type="success" @click='checkout()'>结账</el-button>
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
				<!-- 第二列占17格 -->
				<el-col :span='17'>
					<div class="often-goods">
						<div class="title">常用商品</div>
						<div class="often-goods-list">
							<ul>
								<li v-for='goodsInfo in oftenGoods' @click="addOrderList(goodsInfo)">
									<span>{{goodsInfo.goodsName}}</span>
									<span class="o-price">￥{{goodsInfo.price}}元</span>
								</li>
							</ul>
						</div>
					</div>
					<div class="goods-type">
					    <el-tabs>
					        <el-tab-pane label="汉堡">
					            <ul class='cookList'>
								    <li v-for="goodsInfo in type0Goods" @click="addOrderList(goodsInfo)">
								        <span class="foodImg"><div><img :src='goodsInfo.goodsImg' width="40%"></div></span>
								        <span class="foodName">{{goodsInfo.goodsName}}</span>
								        <span class="foodPrice">￥{{goodsInfo.price}}元</span>
								    </li>
								</ul>
					        </el-tab-pane>
					            <el-tab-pane label="小食">
					            <ul class='cookList'>
								    <li v-for="goodsInfo in type1Goods" @click="addOrderList(goodsInfo)">
								        <span class="foodImg"><div><img :src='goodsInfo.goodsImg' width="40%"></div></span>
								        <span class="foodName">{{goodsInfo.goodsName}}</span>
								        <span class="foodPrice">￥{{goodsInfo.price}}元</span>
								    </li>
								</ul>
					        </el-tab-pane>
					        <el-tab-pane label="饮料">
					            <ul class='cookList'>
								    <li v-for="goodsInfo in type2Goods" @click="addOrderList(goodsInfo)">
								        <span class="foodImg"><div><img :src='goodsInfo.goodsImg' width="40%"></div></span>
								        <span class="foodName">{{goodsInfo.goodsName}}</span>
								        <span class="foodPrice">￥{{goodsInfo.price}}元</span>
								    </li>
								</ul>
					        </el-tab-pane>
					        <el-tab-pane label="套餐">
					            <ul class='cookList'>
								    <li v-for="goodsInfo in type3Goods" @click="addOrderList(goodsInfo)">
								        <span class="foodImg"><div><img :src='goodsInfo.goodsImg' width="40%"></div></span>
								        <span class="foodName">{{goodsInfo.goodsName}}</span>
								        <span class="foodPrice">￥{{goodsInfo.price}}元</span>
								    </li>
								</ul>
					        </el-tab-pane>					 
					    </el-tabs>
					</div>
				</el-col>
			</el-row>
		</div>
	</div>
</template>

<script type="text/javascript">

	import axios from 'axios'

	export default {
		name: 'Pos',
		data(){
			return{
				tableData: [],
			    oftenGoods:[],
	         	type0Goods:[],
	         	type1Goods:[], 
	         	type2Goods:[],
	         	type3Goods:[], 
	         	totalCount:0,
	         	totalMoney:0            	          	      
			}
		},
		created:function(){
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
	      			.then(response=>{
	         		this.oftenGoods=response.data;
      		})
		      .catch(error=>{
		          console.log(error);
		          alert('网络错误，不能访问');
		      })

		    axios.get('http://jspang.com/DemoApi/typeGoods.php')
		      .then(response=>{
		         console.log(response);
		         //this.oftenGoods=response.data;
		         this.type0Goods=response.data[0];
		         this.type1Goods=response.data[1];
		         this.type2Goods=response.data[2];
		         this.type3Goods=response.data[3];
		 
		      })
		      .catch(error=>{
		          console.log(error);
		          alert('网络错误，不能访问');
		      })
		},

		methods:{
      //添加订单列表的方法
      	addOrderList(goodsInfo){
            this.totalCount=0; //汇总数量清0
            this.totalMoney=0;
            let isHave=false;
            //判断是否这个商品已经存在于订单列表
            for (let i=0; i<this.tableData.length;i++){
                // console.log(this.tableData[i].goodsId);
                if(this.tableData[i].goodsId==goodsInfo.goodsId){
                    isHave=true; //存在
                }
            }
            //根据判断编写代码
            if(isHave){
                //存在就进行数量添加
                 let arr = this.tableData.filter(o =>o.goodsId == goodsInfo.goodsId);
                 arr[0].count++;
                 //console.log(arr);
            }else{
                //不存在就推入数组
                let newGoods={goodsId:goodsInfo.goodsId,goodsName:goodsInfo.goodsName,price:goodsInfo.price,count:1};
                 this.tableData.push(newGoods);
 
            }
 			this.getAllMoney()
      },
      //删除单个商品
      delSingleGoods(goodsInfo){
        this.tableData=this.tableData.filter(o => o.goodsId !=goodsInfo.goodsId);
 		this.getAllMoney()
      },
      //删除所有商品
      delAllGoods() {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        },
      //汇总数量和金额
	  getAllMoney(){
	    this.totalCount=0;
	    this.totalMoney=0;
	    if(this.tableData){
	        this.tableData.forEach((element) => {
	        this.totalCount+=element.count;
	        this.totalMoney=this.totalMoney+(element.price*element.count);   
	    });
	    }},	    	
	//结账
	checkout() {
    if (this.totalCount!=0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        //$message是element弹框信息
        this.$message({
            message: '结账成功，感谢你又为店里出了一份力!',
            type: 'success'
        });
 
    }else{
        this.$message.error('不能空结。老板了解你急切的心情！');
    }
 
	}

  }
}
		
	
</script>

<style type="text/css" scoped>
	.order{
		height: 100vh
	}
	.title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
	.often-goods-list ul{
		display: flex;
		flex-wrap: wrap;
	}
   .often-goods-list ul li{
      list-style: none;
      display: flex;
      width: 135px;
      border:1px solid #E5E9F2;
      padding:10px;
      margin: 5px;
      background-color:#fff;
   }
  .o-price{
      color:#58B7FF; 
   }
   .cookList{
   		display: flex;
		flex-wrap: wrap;
   }
   .cookList li{
   		display: flex;
   		flex-direction: column;
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auto;    
       background-color:#fff;
       padding: 2px;       
       margin: 2px;
 
   }
   .foodImg{
       width: 100%;
		display: flex;
   }
   .foodImg>div{
   		justify-content: center;
   }

</style>