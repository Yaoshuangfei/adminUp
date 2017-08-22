<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true">
				<el-form-item label="商品名称">
				     <el-input v-model="name"></el-input>     
				</el-form-item>
				<el-form-item>
					<el-button type="primary" v-on:click="getlist">查询</el-button>
					<!-- <el-button type="primary" v-on:click="getUsers">发布新品</el-button> -->
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-col :span="24">
			<el-col :span="4" :offset="4">商品名称</el-col>
			<el-col :span="2">价格</el-col>
			<el-col :span="2" style="margin-left: 20px">库存</el-col>
			<el-col :span="2" style="margin-left: 40px">操作</el-col>
		</el-col> <!-- v-for="item in selectSubjectStatus" -->
		<el-col :span="24" class="table_div" v-for="item in selectSubjectStatus">
			<el-col :span="24"  class="table_div_head">
				<el-col :span="6">订单编号：{{item.goodsNo}}</el-col>
				<el-col :span="5">下单时间：{{item.createTime}}</el-col>
				<!-- <el-col :span="3" :offset="10">
					<router-link :to="{ name: '订单详情', params: { id: 0 }}">
						<el-button style="margin-top:-5px"  type="text">查看下级</el-button>
					</router-link>
				</el-col> -->
			</el-col>
			<el-col :span="24">
				<el-col :span="3" >
					<img style="width: 100px;margin-left:40px;margin-top: 20px " :src="item.carouselPicture">
				</el-col>
				<el-col :span="4" :offset="3" class="describe">
				{{item.name}}
				</el-col>
				<el-col :offset="1" :span="3" class="describe">{{item.price}}</el-col>
				<el-col :span="2" class="describe">{{item.storage}}</el-col>
				<el-col :span="7" class="describe" :offset="1">
					<el-button type="text" @click="seeBtn(item.id)">查看</el-button>
					<el-button type="text" @click="editBtn(item.id)">编辑</el-button>
					<el-button type="text" @click="topBtn(item.id)">上架</el-button>
					<el-button type="text" @click="deleteBtn(item.id)">删除</el-button>
				</el-col>
			</el-col>
		</el-col>

		<!--工具条-->
		<el-col :span="18" class="toolbar" style="background:#fff;">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--查看界面-->
		<el-dialog title="查看" v-model="seeFormVisible" :close-on-click-modal="false" >
			<el-form :model="orderDetails" label-width="160px">
				<el-form-item label="订单号">
					<div>{{orderDetails.goodsNo }}</div>
					<!-- <el-input v-model="addForm.name" type="text" auto-complete="off"></el-input> -->
				</el-form-item>
				<el-form-item label="商品名称">
					<div>{{orderDetails.name}}</div>
				</el-form-item>
				<el-form-item label="宝贝属性">
					<div v-for="item in orderDetails.goodsData"><span>{{item.key}}</span> <span style="margin-left: 50px">{{item.value}}</span></div>
				</el-form-item>
				<el-form-item label="计量单位">
					<div>{{orderDetails.unit }}</div>
				</el-form-item>
				<el-form-item label="规格报价">
					<div>{{orderDetails.price }}</div>
				</el-form-item>
				<el-form-item label="重量">
					<div>{{orderDetails.weight }}</div>
				</el-form-item>
				<el-form-item label="商品描述">
					<div>{{orderDetails.goodsDesc }}</div>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="seeFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		<!--编辑 界面-->
		<el-dialog title="编辑" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form :model="seeForm" label-width="160px">
				<!-- <el-form-item label="订单号">
					<el-input v-model="seeForm.goodsNo" type="text" auto-complete="off"></el-input>
				</el-form-item> -->
				<el-form-item label="商品名称">
					<!-- <div>{{seeForm.name}}</div> -->
					<el-input v-model="seeForm.name" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<!-- <el-form-item label="宝贝属性">
					<el-col :span="24" v-for="item in seeForm.goodsData">
						<el-col :span="6"><span>{{item.key}}</span></el-col>
						<el-col :span="14"><el-input v-model="item.value" type="text" auto-complete="off"></el-input> </el-col>
					</el-col>
				</el-form-item> -->
				<el-form-item label="计量单位">
					<!-- <div>{{seeForm.unit }}</div> -->
					<el-input v-model="seeForm.unit" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="规格报价">
					<!-- <div>{{seeForm.price }}</div> -->
					<el-input v-model="seeForm.price" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="重量">
					<!-- <div>{{seeForm.weight }}</div> -->
					<el-input v-model="seeForm.weight" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="商品描述">
					<!-- <div>{{seeForm.goodsDesc }}</div> -->
					<el-input v-model="seeForm.goodsDesc" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click="editSubmit">保存</el-button>
				<el-button type="primary" @click.native="editFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import { state } from '../../vuex/state'
	import util from '../../common/js/util'
	import {baseUrl} from '../../api/api';

	export default {
		data() {
			return {
				seeForm:{},
				selectSubjectStatus: [],
				name:'',
				total: 0,
				page: 1,
				seeFormVisible: false,//编辑界面是否显示
				editFormVisible: false,
				orderDetails: {}
			}
		},
		methods: {
			getlist(){
				const _this = this
				_this.selectSubjectStatus = []
				const params = {
					pageNum:this.page,
					size:10,
					name:this.name,
					saleStatus:2,//状态
					storeId:state.storeId
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goods/selectListOfSeller",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(info)
                    	_this.total = info.total
                    	_this.selectSubjectStatus = info.list
                    	for(var i = 0;i<_this.selectSubjectStatus.length;i++){
		                	_this.selectSubjectStatus[i].createTime = new Date(_this.selectSubjectStatus[i].createTime).toLocaleString()
		                	if(_this.selectSubjectStatus[i].saleStatus === 1) {
		                		_this.selectSubjectStatus[i].saleStatus = '销售中'
		                	}else if(_this.selectSubjectStatus[i].saleStatus === 2) {
		                		_this.selectSubjectStatus[i].saleStatus = '已下架'
		                	}else{
		                		_this.selectSubjectStatus[i].saleStatus = '已删除'
		                	}
		                }
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
			},
			// 查看
			seeBtn(id) {
				const _this = this
				this.seeFormVisible = true;
				const params = {
					id:id,
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goods/selectPC",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(data)
                    	_this.orderDetails = info
                    	_this.orderDetails.goodsData = JSON.parse(_this.orderDetails.goodsData)
                    	console.log(_this.orderDetails.goodsData)
                    }
                });
			},
			//编辑
			editBtn(id) {
				this.editFormVisible = true
				const _this = this
				const params = {
					id:id,
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goods/selectPC",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(data)
                    	_this.seeForm = info
                    	// _this.seeForm.goodsData = JSON.parse(_this.seeForm.goodsData)
                    	console.log(_this.seeForm.goodsData)
                    }
                });
			},
			editSubmit() {
				const _this = this
				console.log(_this.seeForm)
				const params = _this.seeForm
				// params.goodsData = JSON.stringify(params.goodsData)
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goods/update",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(data)
                    }
                });
			},
			// 下架
			topBtn(id) {
				const _this = this
				const params = {
					id:id,
					saleStatus:1
				}
				console.log(params)
				this.$confirm('确认上架该商品吗?', '提示', {
					type: 'warning'
				}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/goods/updateStatus",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	const info = data.data
	                    	_this.$message({
								message: data.msg,
								type: 'success'
							});
							_this.getlist()
	                    }
	                });
				}).catch(() => {

				});
			},
			deleteBtn(id) {
				const _this = this
				const params = {
					id:id,
				}
				console.log(params)
				this.$confirm('确认删除该商品吗?', '提示', {
					type: 'warning'
				}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/goods/delete",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	console.log(data)
	                    	_this.$message({
								message: data.msg,
								type: 'success'
							});
							_this.getlist()
	                    }
	                	});
				}).catch(() => {

				});
			}
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
	
</style>