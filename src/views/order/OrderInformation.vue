<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true" :model="filters">
				<!-- <el-form-item>
					<el-input v-model="filters.name" placeholder="支付银行"></el-input>
				</el-form-item> -->
				<el-form-item label="状态">
					<el-select v-model="filters.status" clearable>
				      <el-option v-for="item in states" :label="item.label" :value="item.value">
				      </el-option>
				    </el-select>
				</el-form-item>
				<el-form-item label="搜索类型">
				    <el-select v-model="filters.type" clearable>
				      <el-option v-for="item in options" :label="item.label" :value="item.value">
				      </el-option>
				    </el-select>
				</el-form-item>
				<el-form-item>
				    <el-input v-model="filters.name"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" v-on:click="getlist">查询</el-button>
					<el-button type="primary">导出订单</el-button>
				</el-form-item>
			</el-form>
		</el-col>
		<el-col :span="24">
			<el-col :span="4" :offset="4">商品详情</el-col>
			<el-col :span="2">单价</el-col>
			<el-col :span="2">数量</el-col>
			<el-col :span="2">买家</el-col>
			<el-col :span="2" style="margin-left: 20px">订单总价</el-col>
			<el-col :span="2">状态</el-col>
		</el-col> <!-- v-for="item in selectSubjectStatus" -->
		<el-col :span="24" class="table_div" v-for="item in selectSubjectStatus">
			<el-col :span="24"  class="table_div_head">
				<el-col :span="6">订单编号：{{item.id}}</el-col>
				<el-col :span="5">下单时间：{{item.payTime}}</el-col>
				<el-col :span="1" :offset="9">{{item.totalMoney}}</el-col>
				<el-col :span="2" :offset="1">
					<router-link :to="{ name: '订单详情', params: { id: item.id }}">
						<el-button style="margin-top:-5px"  type="text">查看订单</el-button>
					</router-link>
				</el-col>
			</el-col>
			<el-col :span="24" v-for="items in item.orderGoods">
				<el-col :span="3">
					<img style="width: 100px;margin-left: 40px;margin-top: 30px" :src="items.picture">
				</el-col>
				<el-col :span="6" :offset="1" class="describe_fiast">
				{{items.productName}}
				</el-col>
				<el-col :offset="1" :span="3" class="describe">{{items.productPrice}}</el-col>
				<el-col :span="2" class="describe">{{items.quantity}}</el-col>
				<el-col :span="3" class="describe">{{item.consignee}}</el-col>
				<el-col :span="2" :offset="3" class="describe">{{items.orderStatus}}</el-col>
			</el-col>
		</el-col>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="background:#fff;">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
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
				states:[{
					value:'',
					label:'所有订单'
				},{
					value:1,
					label:'支付中'
				},{
					value:2,
					label:'支付成功'
				},{
					value:3,
					label:'支付失败'
				},{
					value:4,
					label:'已取消'
				},{
					value:5,
					label:'卖家已发货'
				},{
					value:6,
					label:'已收货'
				},{
					value:7,
					label:'已评价'
				},{
					value:8,
					label:'交易完成'
				},{
					value:9,
					label:'售后处理'
				},{
					value:10,
					label:'已删除'
				}],
				selectSubjectStatus: [],
				options: [{
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '快递单号'
		        }],
				filters: {
					name: '',
					status:'',
					type:''
				},
				users: [],
				total: 100,
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
				const params = {
					pageNum:this.page,
					size:10,
					storeId:state.storeId,
					orderStatus:this.filters.status,
					orderId:'',
					expno:''
				}
				if(this.filters.type !== ''){
					if(this.filters.type === '1'){
						params.orderId = this.filters.name
					}else if(this.filters.type === '2'){
						params.expno = this.filters.name
					}
				}
				
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/orderMall/selectListAll",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(info)
                    	_this.total = info.total
                    	_this.selectSubjectStatus = info.list
                    	console.log(_this.selectSubjectStatus)
                    	for(var i = 0;i<_this.selectSubjectStatus.length;i++){
                    		if(_this.selectSubjectStatus[i].payTime !== null){
		                		_this.selectSubjectStatus[i].payTime = new Date(_this.selectSubjectStatus[i].payTime).toLocaleString()
                    		}
		                	for(var x = 0;x<_this.selectSubjectStatus[i].orderGoods.length;x++){
		                		if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 1) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '支付中'
			                	}else if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 2) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '支付成功'
			                	}else if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 3) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '支付失败'
			                	}else if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 4) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '已取消'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 5) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '卖家已发货'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 6) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '已收货'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 7) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '已评价'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 8) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '交易完成'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 9) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '售后处理'
			                	}else  if(_this.selectSubjectStatus[i].orderGoods[x].orderStatus === 10) {
			                		_this.selectSubjectStatus[i].orderGoods[x].orderStatus = '已删除'
			                	}
		                	}
		                }
		                console.log(_this.selectSubjectStatus)
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
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
			//显示编辑界面
			seeBtn: function (index, row) {
				this.editFormVisible = true;
				this.orderDetails = Object.assign({}, row);
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
	.table_div{
		margin-top: 20px;
		width: 1200px;
		border: 1px solid #aaa;
		padding-bottom: 20px;
	}
	.table_div_head{
		padding-top:15px;
		padding-left: 40px;
		width: 1200px;
		height:50px;
		border-bottom: 1px solid #aaa;
		background-color: #eee;
		border-right: 1px solid #aaa;
	}
	.img_shangp{
		width:100px;
		height: 100px;
		border: 1px solid #aaa;
		margin-left: 40px;
		margin-top: 20px;
	}
	.describe_fiast{
		margin-top:40px;
	}
	.describe{
		margin-top: 60px;
	}
</style>