<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true" :model="filters">
				<el-form-item label="状态">
					<el-select v-model="filters.status" clearable>
				      <el-option v-for="item in selectSubjectStatus" :label="item.label" :value="item.value">
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
					<el-button type="primary" @click="getlist">查询</el-button>
					<el-button type="primary">导出</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="orderInformation" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
			<el-table-column prop="userId" label="用户ID">
			</el-table-column>
			<el-table-column prop="withdrawalsName" label="用户名">
			</el-table-column>
			<el-table-column prop="phone" label="手机号">
			</el-table-column>
			<el-table-column prop="withdrawalsPrice" label="提现金额">
			</el-table-column>
			<el-table-column prop="status" :formatter='formatterType' label="订单状态">
			</el-table-column>
			<el-table-column prop="createTime" :formatter='formatterTime' label="提现时间">
			</el-table-column>
			<el-table-column prop="reason" label="备注">
			</el-table-column>
			<el-table-column label="操作">
				<template scope="scope">
					<el-button type="text" v-if="scope.row.status !== 2" size="small" @click="TgBtn(scope.row)">打款</el-button>
					<el-button type="text" v-if="scope.row.status !== 2" size="small" @click="NogBtn(scope.row)">不通过 </el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
		<el-col :span="24" class="toolbar" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
		<!-- 通过 -->
		<el-dialog title="通过" v-model="tongVisible" :close-on-click-modal="false" >
			<el-form label-width="80px">
				<el-form-item label="备注">
					<el-input v-model="tonguoName"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="tonguoSubmit">确定</el-button>
				<el-button type="primary" @click.native="tongVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		<!-- NO通过 -->
		<el-dialog title="不通过" v-model="NotongVisible" :close-on-click-modal="false" >
			<el-form label-width="80px">
				<el-form-item label="备注">
					<el-input v-model="NotonguoName"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="NotonguoSubmit">确定</el-button>
				<el-button type="primary" @click.native="NotongVisible = false">关闭</el-button>
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
				tonguoName:'',
				tongVisible:false,
				NotongVisible:false,
				NotonguoName:'',
				selectSubjectStatus: [
				{
					value:'0',
					label:'全部'
				},{
					value:'1',
					label:'交易成功'
				},{
					value:'2',
					label:'交易失败'
				},{
					value:'3',
					label:'审核中付'
				}],
				options: [{
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '手机号'
		        }, {
		          value: '3',
		          label: '姓名'
		        }],
				filters: {
					name: '',
					status:'',
					type:''
				},
				total: 1,
				page: 1,
				listLoading: false,

				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {
				},
				orderInformation:[],
				withdrawalsId:''
			}
		},
		methods: {
			getlist(){
				const _this = this
				_this.table = []
				const params = {
					pageNum:_this.page,
					size:10,
					storeId:state.storeId,
					status:'',
					name:'',
					withdrawalsId:'',
					phone:4
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    // url:"http://192.168.0.115:8080/api/store/userCashFlow/selectFlowList",
                    url:baseUrl+"/api/withdrawals/selectListBySeller",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	const info = data.data
                    	_this.orderInformation = info.list
                    	_this.total = info.total
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
				if(row.status === 1){
					type = '提现成功'
				}else if(row.status === 2){
					type = '提现审核中'
				}else if(row.status === 3){
					type = '提现被拒绝'
				}
				return type
			},
			formatter(row, column){
				let type = ''
				if(row.centType === 1){
					type = '购买平台身份'
				}else if(row.centType === 2){
					type = '购买店铺身份'
				}else if(row.centType === 3){
					type = '购买产品'
				}else if(row.centType === 4){
					type = '补货 '
				}else if(row.centType === 5){
					type = '业务充值'
				}else if(row.centType === 6){
					type = '扫码支付'
				}
				return type
			},
			// 通过
			TgBtn(row){
				this.tongVisible = true
				this.withdrawalsId = row.withdrawalsId
				console.log(this.withdrawalsId)
			},
			tonguoSubmit(){
				const _this = this
				const params = {
					withdrawalsId:this.withdrawalsId,
					status:1,
					reason:this.tonguoName
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/withdrawals/update",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
                    		_this.getlist()
                    	}else{
                    		_this.$message.error(data.msg)
                    	}
                    	_this.tongVisible = false
                    }
                });
			},
			// 不通过
			NogBtn(row){
				this.NotongVisible = true
				this.withdrawalsId = row.withdrawalsId
				console.log(this.withdrawalsId)
			},
			NotonguoSubmit(){
				const _this = this
				const params = {
					withdrawalsId:this.withdrawalsId,
					status:3,
					reason:this.NotonguoName
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/withdrawals/update",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
                    		_this.getlist()
                    	}else{
                    		_this.$message.error(data.msg);
                    	}
                    	_this.NotongVisible = false
                    }
                });
			},
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
	
</style>