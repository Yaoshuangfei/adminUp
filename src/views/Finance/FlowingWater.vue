<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true" :model="filters">
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
					<el-button type="primary" @click="getlist">查询</el-button>
					<el-button type="primary">导出</el-button>
				</el-form-item>
				<el-form-item label="店铺营业额：" style="margin-right: 40px">
				    {{totalMoney}}元
				</el-form-item>
				<el-form-item label="店铺可提现金额：" style="margin-right: 40px">
				    {{availableIncome}}元
				</el-form-item>
				<el-form-item label="已提现：" style="margin-right: 40px">
				    {{amount}}元
				</el-form-item>
				<el-form-item label="冻结金额：" style="margin-right: 40px">
				    {{frozenIncome}}元
				</el-form-item>
			</el-form>
		</el-col>
		<el-col :span="24">
			<el-col :span="4" style="margin-left: 20px">订单编号</el-col>
			<el-col :span="2">用户名</el-col>
			<el-col :span="3">手机号</el-col>
			<el-col :span="2">金额</el-col>
			<el-col :span="3">手续费</el-col>
			<el-col :span="3">交易时间</el-col>
			<el-col :span="3">本单利润</el-col>
			<el-col :span="3">店铺余额</el-col>
		</el-col>
		<el-col v-for="item in orderInformation" :span="24" style="margin-top: 20px;border:1px solid #ddd;">
			<el-col :span="24" style="height: 40px;line-height: 40px">
				<el-col :span="4" style="margin-left: 20px">{{item.tradeNo}}</el-col>
				<el-col :span="2">{{item.coreUserName}}</el-col>
				<el-col :span="3">{{item.coreUserMobile}}</el-col>
				<el-col :span="2">{{item.totalMoney}}</el-col>
				<el-col :span="3">{{item.serviceFee}}</el-col>
				<el-col :span="3">{{new Date(item.createTime).toLocaleString()}}</el-col>
				<el-col :span="3">{{item.mallProfits}}</el-col>
				<el-col :span="3">{{item.storeIncome+item.freezeIncome}}</el-col>
			</el-col>
			<el-col :span="24" style="border:1px solid #ddd;background: #eee;" v-if="item.maps.length !== 0" v-for="items in item.maps">
				<el-col :span="3" style="margin-left: 20px;margin-top: 20px;margin-bottom: 20px">分佣明细</el-col>
				<el-col :span="13">
					<el-col :span="24" style="margin-top: 10px;" v-for="itemfy in items.commissions">		
						<el-col :span="24" style="margin-top: 10px;" v-for="itemlx in itemfy.commissionsByType">
							<el-col :span="4">{{itemlx.userName}}</el-col>
							<el-col :span="4">{{itemlx.remark}}</el-col>
							<el-col :span="4">{{itemlx.quota}}</el-col>
						</el-col>
					</el-col>
				</el-col>
			</el-col>
		</el-col>
		<!--列表-->
		<!-- <el-table :data="orderInformation" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
			<el-table-column prop="tradeNo" label="订单号">
			</el-table-column>
			<el-table-column prop="remark" label="商品名">
			</el-table-column>
			<el-table-column prop="userName" label="用户名">
			</el-table-column>
			<el-table-column prop="mobile" label="手机号">
			</el-table-column>
			<el-table-column prop="quota" label="金额">
			</el-table-column>
			<el-table-column prop="payType" :formatter='formatterType' label="收入方式">
			</el-table-column>
			<el-table-column prop="type" :formatter='formatter' label="来源">
			</el-table-column>
			<el-table-column prop="createTime" :formatter='formatterTime' label="交易时间">
			</el-table-column>
		</el-table> -->

		<!--工具条-->
		<el-col :span="24" class="toolbar" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="订单详情" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form :model="orderDetails" label-width="160px" :rules="editFormRules" ref="editForm">
				<el-form-item label="订单号">
					<div>{{orderDetails.orderNumber }}</div>
					<!-- <el-input v-model="addForm.name" type="text" auto-complete="off"></el-input> -->
				</el-form-item>
				<el-form-item label="商品名称">
					<div>{{orderDetails.commodityName}}</div>
				</el-form-item>
				<el-form-item label="用户名">
					<div>{{orderDetails.userName }}</div>
				</el-form-item>
				<el-form-item label="实付金额">
					<div>{{orderDetails.amountPaid }}</div>
				</el-form-item>
				<el-form-item label="订单总价">
					<div>{{orderDetails.orderTotal }}</div>
				</el-form-item>
				<el-form-item label="订单状态">
					<div>{{orderDetails.orderStatus }}</div>
				</el-form-item>
				<el-form-item label="支付方式">
					<div>{{orderDetails.paymentMethod }}</div>
				</el-form-item>
				<el-form-item label="创建时间">
					<div>{{orderDetails.creationTime}}</div>
				</el-form-item>
				<el-form-item label="发货时间">
					<div>{{orderDetails.deliveryTime}}</div>
				</el-form-item>
				<el-form-item label="分佣详情">
					<div>{{orderDetails.deliveryTime}}</div>
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
				amount:'',
				availableIncome:'',
				totalMoney:'',
				frozenIncome:'',
				selectSubjectStatus: [
				{
					value:'',
					label:'所有状态'
				},{
					value:'0',
					label:'微信支付'
				},{
					value:'1',
					label:'支付宝支付'
				},{
					value:'2',
					label:'银联支付'
				},{
					value:'3',
					label:'余额支付'
				},{
					value:'4',
					label:'余额金豆混合支付'
				},{
					value:'5',
					label:'金豆支付'
				}],
				options: [{
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '用户名'
		        }, {
		          value: '3',
		          label: '电话'
		        }],
				filters: {
					name: '',
					status:'',
					type:''
				},
				users: [],
				total: 1,
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
			//性别显示转换
			formatSex: function (row, column) {
				return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
			},
			getlist(){
				const _this = this
				_this.listLoading = true
				_this.table = []
				const params = {
					pageNum:_this.page,
					pageSize:10,
					payType:this.filters.status,
					tradeNo:'',
					userName:'',
					mobile:'',
					sort:'4'
				}
				if(this.filters.type === '1'){
					params.tradeNo = this.filters.name
				}else if(this.filters.type === '2'){
					params.userName = this.filters.name
				}else if(this.filters.type === '3'){
					params.mobile = this.filters.name
				}
				// console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    // url:"http://192.168.0.115:8080/api/store/userCashFlow/selectFlowList",
                    url:baseUrl+"/api/store/userCashFlow/selectOrderMallFlowList",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	const info = data.data
                    	_this.orderInformation = info.list
                    	_this.total = info.total
                    	_this.listLoading = false
                    	console.log(_this.orderInformation[0].maps)
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
			},
			formatterTime(row, column){
				let curTime = row.createTime;
                curTime = new Date(curTime).toLocaleString()
                return curTime
			},
			//支付方式
			formatterType(row, column) {
				let type = ''
				if(row.payType === 0){
					type = '微信支付'
				}else if(row.payType === 1){
					type = '支付宝支付'
				}else if(row.payType === 2){
					type = '银联支付'
				}else if(row.payType === 3){
					type = '余额支付'
				}else if(row.payType === 4){
					type = '余额金豆混合支付'
				}else if(row.payType === 5){
					type = '金豆支付'
				}
				return type
			},
			formatter(row, column){
				let centype = ''
				if(row.type === 1){
					centype = '提现'
				}else if(row.type === 2){
					centype = '分佣'
				}else if(row.type === 3){
					centype = '业务充值'
				}else if(row.type === 4){
					centype = '余额充值 '
				}else if(row.type === 5){
					centype = '商品购买'
				}else if(row.type === 6){
					centype = '店铺身份购买'
				}else if(row.type === 7){
					centype = '平台身份购买'
				}else if(row.type === 8){
					centype = '补货'
				}else if(row.type === 9){
					centype = '金豆充值'
				}else if(row.type === 10){
					centype = '金豆支出 '
				}else if(row.type === 11){
					centype = '店铺收入'
				}else if(row.type === 12){
					centype = '手续费'
				}else if(row.type === 13){
					centype = '便付劵充值'
				}else if(row.type === 14){
					centype = '便付劵转赠 '
				}else if(row.type === 15){
					centype = '便付劵兑换金豆'
				}else if(row.type === 16){
					centype = '商家会员钱包转平台钱包'
				}
				return centype
			},
			getbaafList(){
				const _this = this
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/myStoreBAAf",
                    data:JSON.stringify({}),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	_this.amount = data.data.amount
                    	_this.availableIncome = data.data.availableIncome
                    	_this.totalMoney = data.data.totalMoney
                    	_this.frozenIncome = data.data.frozenIncome
                    }
                });
			}
		},
		mounted() {
			this.getlist();
			this.getbaafList()
		}
	}

</script>

<style>
	
</style>