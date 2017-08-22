<template>
	<section>
		<el-col :span="24" style="margin-top: 80px;margin-left: 60px;">
			<el-col :span="2" style="margin-top: 8px">最低提现额度</el-col>
			<el-col :span="4">
				<el-input v-model="minPrice"></el-input>
			</el-col>
			<el-col :span="1" style="margin-top: 8px">元</el-col>
		</el-col>
		<el-col :span="24" style="margin-top: 40px;margin-left: 60px;">
			<el-col :span="2" style="margin-top: 8px">提现时间</el-col>
			<el-col :span="20">
				<el-radio-group v-model="roleId" @change = "click">
			    	<el-radio-button  v-for="item in ruleAll" :label="item.id">{{item.name}}</el-radio-button>
			    </el-radio-group>
			</el-col>
		</el-col>
		<el-col :span="24" style="margin-top: 40px;margin-left: 60px;">
			<el-col :span="2" style="margin-top: 8px">手续费</el-col>
			<el-col :span="4">
				<el-input v-model="adminDiscount" type="number"></el-input>
			</el-col>
			<el-col :span="1"  style="margin-top: 8px">%</el-col>
		</el-col>
		<el-col :span="24" style="margin-top: 40px;margin-left: 200px">
			<el-button type="primary" @click="onSubmit">保存</el-button>
			<el-button type="primary" @click="eidtSubmit">修改</el-button>
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
				minPrice:'',
				adminDiscount:'',
				roleId:'',
				allocationId:'',
				ruleAll:[
					{
						id:1,
						name:'星期一'
					},
					{
						id:2,
						name:'星期二'
					},
					{
						id:3,
						name:'星期三'
					},
					{
						id:4,
						name:'星期四'
					},
					{
						id:5,
						name:'星期五'
					},
					{
						id:6,
						name:'星期六'
					},
					{
						id:7,
						name:'星期七'
					}
				]
			}
		},
		methods: {
			click(){

			},
			onSubmit(){
				const _this = this
				const params = {
					withdrawalsWeek:this.roleId,
					adminDiscount:parseInt(this.adminDiscount),
					minPrice:parseInt(this.minPrice),
					storeId:state.storeId
				}
				console.log(params)
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+"/api/commissionAllocation/add/update",
	                data:JSON.stringify(params),
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	console.log(data)
	                  	_this.getlist()
	                }
	            });
			},
			eidtSubmit(){
				const _this = this
				const params = {
					withdrawalsWeek:this.roleId,
					adminDiscount:parseInt(this.adminDiscount),
					minPrice:parseInt(this.minPrice),
					storeId:state.storeId,
					allocationId:this.allocationId
				}
				console.log(params)
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+"/api/commissionAllocation/add/update",
	                data:JSON.stringify(params),
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	console.log(data)
	                  	_this.getlist()
	                }
	            });
			},
			getlist(){
				const _this = this
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+"/api/commissionAllocation/selectOne",
	                data:JSON.stringify({}),
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	console.log(data.data)
	                  	if(data.data === null){
	                  		return
	                  	}
	                  	_this.roleId = data.data.withdrawalsWeek
	                  	_this.adminDiscount = data.data.adminDiscount
	                  	_this.minPrice = data.data.minPrice
	                  	_this.allocationId = data.data.allocationId
	                }
	            });
			}
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
	.agen_div{
		border: 1px solid #aaa;
		width:200px;
		height: 300px;

	}
	.agen_div div:nth-child(1){
		border: 1px solid #aaa;
		width:100px;
		height: 100px;
		border-radius: 50px;
		margin-left: 45px;
		margin-top: 20px;
	}
	.agen_div div:nth-child(2){
		width: 200px;
		text-align: center;
		margin-top: 20px;
	}
	.agen_div div:nth-child(3){
		width: 200px;
		text-align: center;
		margin-top: 20px;
	}
	.agen_div div:nth-child(4){
		width: 200px;
		text-align: center;
		margin-top: 20px;
	}
</style>