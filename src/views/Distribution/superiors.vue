<template>
	<section>
		<!--工具条-->
		<el-col :span="24" style="padding-bottom: 0px;background: #fff">
			<h3>我的分销商（{{list.length}}）- 上级  - {{name}}</h3>
		</el-col>
		<el-col :span="24">
			<el-col :span="4" style="margin-top: 20%;" class='bor_div_left'>{{name}}</el-col>
			<el-col :span="18" style="margin-left: 60px;margin-bottom: 10px;">
				<el-col :span="24" style="margin-top: 10px;" v-for="item in list">
						<el-col :span="4" class='bor_div'>
							<div @click="onDown(item.id)" style="cursor: pointer;">
								<el-col :span="24" v-if="item.coreUser !== null">{{item.coreUser.nickName}}</el-col>
								<el-col :span="24">{{item.shopRoleName}}</el-col>
								<el-col :span="24" v-if="item.coreUser !== null">{{item.coreUser.mobile}}</el-col>
							</div>
						</el-col>
						<el-col v-if="item.list.length > 0" :span="1" style="border:1px solid #ddd;margin-top: 20px;margin-left: 10px;"></el-col>
						<el-col :span="10" style="margin-left: 0px">
							<el-col :span="24" v-for="items in item.list" style="border-left:1px solid #aaa;padding-left: 15px;height:80px">
								<el-col :span="24" class='bor_div'>
									<div @click="onDown(items.id)" style="cursor: pointer;">
										<el-col :span="24" v-if="items.coreUser !== null">{{items.coreUser.nickName}}</el-col>
										<el-col :span="24">{{items.shopRoleName}}</el-col>
										<el-col :span="24" v-if="items.coreUser !== null">{{items.coreUser.mobile}}</el-col>
									</div>
								</el-col>
							</el-col>
						</el-col>
					<el-col v-if="item.list.length > 0" :span="4" :offset="5" style="border-top:1px solid #bbb;"></el-col>
				</el-col>
			</el-col>

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
				name:'',
				list:[],
				id:this.$route.params.id
			}
		},
		methods: {
			onDown(val){
				console.log(val)
				this.id = val
				this.list = []
				this.getlist()
			},
			getlist(){
				const _this = this
				const params = {
					id:this.id,
					lvl:3
				}
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+"/api/shopConfig/selectChildLst",
	                data:JSON.stringify(params),
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                	const info = data.data
	                  	console.log(info)
	                  	for(var i = 0;i<info.length;i++){
	                  		if(info[i].lvl === 0){
		                  		_this.name = info[i].coreUser.nickName
		                  	}
		                  	if(info[i].lvl === 1){
		                  		info[i].list = []
		                  		for(var x = 0;x<info.length;x++){
		                  			if(info[i].id === info[x].configId){
		                  				info[i].list.push(info[x])
		                  			}
		                  		}
		                  		_this.list.push(info[i])
		                  	}
	                  	}
	                  	console.log(_this.list)
	                }
	            })
			}
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
	.bor_div_left{
		border: 1px solid #aaa;
		border-radius: 10px;
		width:200px;
		height: 60px;
		line-height: 60px;
		text-align: center;
	}
	.bor_div{
		border: 1px solid #aaa;
		border-radius: 10px;
		width:200px;
		height: 60px;
		text-align: center;
	}
</style>