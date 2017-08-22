<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true">
				<router-link :to="{ name: '补货', params: { id: 0 }}">
					<el-button  type="primary">增加</el-button>
				</router-link>
			</el-form>
		</el-col>
		<el-row style="margin-left: 40px">
		    <el-col :xs="10" :sm="10" :md="10" :lg="10" v-for="item in table" style="margin-bottom: 20px">
		    	<div class="replenishment">
		    		<el-row style="margin-left: 10px">
					    <el-col :xs="10" :sm="10" :md="10" :lg="10">
					    	<div style="border: 1px solid #ddd;width: 150px;height: 150px;margin-left: 40px;margin-top: 20px">
					    		{{item.icon}}
					    	</div>
					    </el-col>
					    <el-col :xs="12" :sm="12" :md="12" :lg="12">
					    	<el-col :xs="12" :sm="12" :md="12" :lg="24" style="height: 60px;margin-top: 20px">
					    		{{item.name}}
					    	</el-col>
					    	<el-col :xs="12" :sm="12" :md="12" :lg="24" style="height: 50px">
					    		联创补货每次不低于200盒
					    	</el-col>
					    	<el-col :xs="12" :sm="12" :md="12" :lg="24">
					    		<el-button type="text" v-on:click="seeBtn(item)">查看</el-button>
					    		<el-button type="text" v-on:click="getlist">修改</el-button>
					    		<el-button type="text" v-on:click="getlist">删除</el-button>
					    	</el-col>
					    </el-col>
					</el-row>
		    	</div>
		    </el-col>
		</el-row>
	</section>
</template>

<script>
	import { state } from '../../vuex/state'
	import util from '../../common/js/util'
	import {baseUrl , editUser, addUser } from '../../api/api';

	export default {
		data() {
			return {
				table:[]
			}
		},
		methods: {
			getlist(){
				const _this = this
				_this.table = []
				const params = {
					storeId:state.storeId,
					introType:1
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectOne",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	_this.table = info.shopRoles
                    	console.log(info.shopRoles)
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
			},
			seeBtn(row){
				console.log(row)
			}
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
.replenishment{
	border: 1px solid #ddd;
	width: 600px;
	height:200px;
}
</style>