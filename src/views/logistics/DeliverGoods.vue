<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true" :model="filters">
				<!-- <el-form-item>
					<el-input v-model="filters.name" placeholder="支付银行"></el-input>
				</el-form-item> -->
				<!-- <el-form-item label="状态">
					<el-select v-model="filters.status" clearable>
				      <el-option v-for="item in selectSubjectStatus" :label="item.label" :value="item.value">
				      </el-option>
				    </el-select>
				</el-form-item> -->
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
					<el-button type="primary" v-on:click="getUsers">导出订单</el-button>
				</el-form-item>
			</el-form>
		</el-col>
		<el-col :span="24">
			<el-col :span="4" :offset="4">商品详情</el-col>
			<el-col :span="2">单价</el-col>
			<el-col :span="2">数量</el-col>
			<el-col :span="2">买家</el-col>
			<el-col :span="2" style="margin-left: 20px">订单总价</el-col>
			<el-col :span="2">操作</el-col>
		</el-col>
		<el-col :span="24" class="table_div" v-for="item in selectSubjectStatus">
			<el-col :span="24"  class="table_div_head">
				<el-col :span="6">订单编号：{{item.id}}</el-col>
				<el-col :span="6">下单时间：{{item.createTime}}</el-col>
				<el-col :span="3" :offset="4" v-if="item.coreUser === null">/</el-col>
				<el-col :span="3" :offset="4" v-if="item.coreUser !== null">{{item.coreUser.nickName}}</el-col>
				<el-col :span="1" style="margin-left: 20px">{{item.totalMoney}}</el-col>
				<el-col :span="2" style="margin-left:60px">
					<router-link :to="{ name: '订单详情', params: { id: item.id }}">
						<el-button style="margin-top:-5px"  type="text">查看订单</el-button>
					</router-link>
				</el-col>
			</el-col>
			<el-col :span="24" v-for="items in item.orderGoods">
				<el-col :span="3" >
					<img style="width: 100px;margin-left:40px;margin-top: 20px " :src="items.picture">
				</el-col>
				<el-col :span="6" :offset="1" class="describe">
				{{items.productName}}
				</el-col>
				<el-col :offset="1" :span="3" class="describe">{{items.productPrice}}</el-col>
				<el-col :span="2" :offset="1"  class="describe">{{items.payTime}}</el-col>
				<el-col :span="1" :offset="5" class="describe">
					<el-button type="text" v-on:click="fahuoBtn(items)">发货</el-button>
				</el-col>
			</el-col>
			<el-col :span="24" style="border-top: 1px solid #aaa;height: 30px;line-height: 50px">地址：{{item.provinceName}}{{item.cityName}}{{item.countyName}}{{item.address}}</el-col>
		</el-col>
		<!--工具条-->
		<el-col :span="18" class="toolbar" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="发货" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-col :span="24">
				<el-col :span="24" style="margin-bottom: 20px">
					<el-select v-model="keyvalue" clearable>
				      <el-option v-for="item in optionKey" :label="item.label" :value="item.value">
				      </el-option>
				    </el-select>
				</el-col>
				<el-col :span="24" style="margin-bottom: 20px">
					<el-input v-model='expno' @change="keyup"></el-input>
				</el-col>
			</el-col>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="upSubmit" :loading="editLoading">确认</el-button>
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
				keyvalue: '',
				expno:'',
				ddId:'',
				dpId:'',
				checked: true,
				value:'',
				value1:'',
				value2:'',
				selectSubjectStatus: [],
				options: [{
		          value: '0',
		          label: '全部'
		        }, {
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '快递单号'
		        }],
		        optionKey:[],
				filters: {
					name: '',
					status:'',
					type:''
				},
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
				orderInformation:[{
					orderNumber :'145877458784524c',
					courierNumber :'145877458784524c',
					userName:'吸引力量',
					amountPaid :'300',
					orderTotal :'900',
					orderStatus :'待付款',
					paymentMethod :'微信支付',
					creationTime:'2017-09-08 17:09',
					deliveryTime:'2017-09-08 17:09',
					commodityName:'雨花说'
				}]
			}
		},
		methods: {
			fahuoBtn(row){
				this.optionKey = []
				this.keyvalue =''
				this.expno = ''
				console.log(row)
				this.editFormVisible = true
				this.ddId = row.id
				this.dpId = row.storeId
			},
			keyup(){
				const _this = this
				const params = {
					expno:this.expno
				}
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/orderMall/orderTracesByJson",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data.data)
                    	_this.optionKey = []
                    	if(data.data !== null && data.data !== []){
                    		for(var i = 0;i<data.data.length;i++){
                    			const obj = {}
                    			obj.value = data.data[i].ShipperCode
                    			obj.label = data.data[i].ShipperName
                    			_this.optionKey.push(obj)
                    			_this.keyvalue = obj.value
                    		}
                    	}
                    	console.log(_this.optionKey)
                    }
                });
			},
			upSubmit() {
				console.log(this.keyvalue)
				console.log(this.expno)
				let express = ''
				for(var i = 0;i<this.optionKey.length;i++){
					if(this.keyvalue === this.optionKey[i].value){
						express = this.optionKey[i].label
					}
				}
				const _this = this
				const params = {
					id:this.ddId,
					storeId:this.dpId,
					expno:this.expno,
					expressName:express,
					expressCode:this.keyvalue,
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/orderMall/delivery",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	_this.editFormVisible = false
                    	_this.getlist()
                    }
                });
			},
			getlist(){
				const _this = this
				this.selectSubjectStatus = []
				const params = {
					pageNum:this.page,
					size:10,
					storeId:state.storeId,
					orderStatus:2,
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
                    	_this.total = info.total
                    	_this.selectSubjectStatus = info.list
                    	console.log(_this.selectSubjectStatus)
                    	for(var i = 0;i<_this.selectSubjectStatus.length;i++){
                    		_this.selectSubjectStatus[i].createTime = new Date(_this.selectSubjectStatus[i].createTime).toLocaleString()
                    	}
                    }
                });
			},

			delivery(value,expno,expressName,expressCode){
				const _this = this
				const params = {
					id:value,
					storeId:state.storeId,
					expno:expno,
					expressName:expressName,
					expressCode:expressCode
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/orderMall/delivery",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
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

</style>