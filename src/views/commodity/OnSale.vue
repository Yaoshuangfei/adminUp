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
					<!-- <el-button type="primary" >发布新品</el-button> -->
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-col :span="24">
			<el-col :span="4" :offset="4">商品名称</el-col>
			<el-col :span="2">价格</el-col>
			<el-col :span="2">库存</el-col>
			<el-col :span="4" style="margin-left: 20px">操作</el-col>
			<!-- <el-col :span="2">状态</el-col> -->
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
				<el-col :span="5" class="describe">
					<el-button type="text" @click="seeBtn(item.id)">查看</el-button>
					<el-button type="text" @click="downBtn(item.id)">下架</el-button>
				</el-col>
			</el-col>
		</el-col>

		<!--工具条-->
		<el-col :span="18" class="toolbar" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="订单详情" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form :model="orderDetails" label-width="100px" :rules="editFormRules" ref="editForm">
				<el-form-item label="订单号">
					<div>{{orderDetails.goodsNo }}</div>
				</el-form-item>
				<el-form-item label="商品名称">
					<div>{{orderDetails.name}}</div>
				</el-form-item>
				<el-form-item label="宝贝属性">
					<div v-for="item in orderDetails.goodsData"><span>{{item.key}}</span> <span style="margin-left: 50px">{{item.value}}</span></div>
				</el-form-item>
				<!-- <el-form-item label="商品货号">
					<div>{{orderDetails.goodsNo }}</div>
				</el-form-item> -->
				<el-form-item label="计量单位">
					<div>{{orderDetails.unit }}</div>
				</el-form-item>
				<el-form-item label="规格报价">
					<div>{{orderDetails.price }}</div>
				</el-form-item>
				<el-form-item label="重量">
					<div>{{orderDetails.weight}}</div>
				</el-form-item>
				<el-form-item label="商品描述">
					<div>{{orderDetails.goodsDesc}}</div>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<!-- <el-button type="primary" @click.native="editSubmit" :loading="editLoading">保存</el-button> -->
				<el-button type="primary" @click.native="editFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
	</section>
</template>

<script>
	import { state } from '../../vuex/state'
	import util from '../../common/js/util'
	import {baseUrl , editUser, addUser } from '../../api/api';

	export default {
		data() {
			return {
				radio: '0',
				checked: true,
				value:'',
				value1:'',
				value2:'',
				selectSubjectStatus: [],
				options: [{
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '快递单号'
		        }],
				name: '',
				users: [],
				total: 0,
				page: 1,
				listLoading: false,
				sels: [],//列表选中列

				editFormVisible: false,//编辑界面是否显示
				editLoading: false,
				editFormRules: {
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					]
				},
				//编辑界面数据
				editForm: {
					id: 0,
					username: '',
					sex: -1,
					age: 0,
					birth: '',
					addr: ''
				},
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {
				},
				orderInformation:[]
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
					saleStatus:1,
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
                    	console.log(data)
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
				this.getlist()
			},
			seeBtn(id) {
				const _this = this
				this.editFormVisible = true;
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
                    	_this.orderDetails = info
                    	_this.orderDetails.goodsData = JSON.parse(_this.orderDetails.goodsData)
                    	console.log(_this.orderDetails.goodsData)
                    }
                });
			},
			downBtn(id){
				const _this = this
				const params = {
					id:id,
					saleStatus:2
				}
				console.log(params)
				this.$confirm('确认下架该商品吗?', '提示', {
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
			//获取用户列表
			getUsers() {
				let para = {
					page: this.page,
					name: this.filters.name
				};
				this.listLoading = true;
				//NProgress.start();
				getUserListPage(para).then((res) => {
					this.total = res.data.total;
					this.users = res.data.users;
					this.listLoading = false;
					//NProgress.done();
				});
			},
			//删除
			handleDel: function (index, row) {
				this.$confirm('确认删除该记录吗?', '提示', {
					type: 'warning'
				}).then(() => {
					this.listLoading = true;
					//NProgress.start();
					let para = { id: row.id };
					removeUser(para).then((res) => {
						this.listLoading = false;
						//NProgress.done();
						this.$message({
							message: '删除成功',
							type: 'success'
						});
						this.getUsers();
					});
				}).catch(() => {

				});
			},
			//显示新增界面
			handleAdd: function () {
				this.addFormVisible = true;
				this.addForm = {
					name: '',
					sex: -1,
					age: 0,
					birth: '',
					addr: ''
				};
			},
			//编辑
			editSubmit: function () {
				this.$refs.editForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(() => {
							this.editLoading = true;
							//NProgress.start();
							let para = Object.assign({}, this.editForm);
							para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
							editUser(para).then((res) => {
								this.editLoading = false;
								//NProgress.done();
								this.$message({
									message: '提交成功',
									type: 'success'
								});
								this.$refs['editForm'].resetFields();
								this.editFormVisible = false;
								this.getUsers();
							});
						});
					}
				});
			},
			//新增
			addSubmit: function () {
				this.$refs.addForm.validate((valid) => {
					if (valid) {
						this.$confirm('确认提交吗？', '提示', {}).then(() => {
							this.addLoading = true;
							//NProgress.start();
							let para = Object.assign({}, this.addForm);
							para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
							addUser(para).then((res) => {
								this.addLoading = false;
								//NProgress.done();
								this.$message({
									message: '提交成功',
									type: 'success'
								});
								this.$refs['addForm'].resetFields();
								this.addFormVisible = false;
								this.getUsers();
							});
						});
					}
				});
			},
			selsChange: function (sels) {
				this.sels = sels;
			},
			//批量删除
			batchRemove: function () {
				var ids = this.sels.map(item => item.id).toString();
				this.$confirm('确认删除选中记录吗？', '提示', {
					type: 'warning'
				}).then(() => {
					this.listLoading = true;
					//NProgress.start();
					let para = { ids: ids };
					batchRemoveUser(para).then((res) => {
						this.listLoading = false;
						//NProgress.done();
						this.$message({
							message: '删除成功',
							type: 'success'
						});
						this.getUsers();
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