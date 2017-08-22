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
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="orderInformation" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
			<el-table-column prop="goodsId" label="商品编号">
			</el-table-column>
			<el-table-column prop="coreUser.realName" label="买家">
			</el-table-column>
			<el-table-column prop="coreUser.mobile" label="电话">
			</el-table-column>
			<el-table-column prop="goods.name" label="商品名称">
			</el-table-column>
			<el-table-column prop="crate" :formatter='formatterType' label="评价">
			</el-table-column>
			<el-table-column label="操作">
				<template scope="scope">
					<el-button type="text" size="small" @click="seeBtn(scope.row)">查看</el-button>
					<el-button type="text" size="small" @click="handleEdit(scope.row)">回复</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
		<el-col :span="24" class="toolbar" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>

		<!--编辑界面-->
		<el-dialog title="订单详情" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form :model="orderDetails" label-width="160px">
				<el-form-item label="商品编号">
					<div>{{orderDetails.goodsId }}</div>
				</el-form-item>
				<!-- <el-form-item label="买家">
					<div>{{orderDetails.coreUser.realName}}</div>
				</el-form-item>
				<el-form-item label="电话">
					<div>{{orderDetails.coreUser.mobile}}</div>
				</el-form-item>
				<el-form-item label="商品名称">
					<div>{{orderDetails.goods.name }}</div>
				</el-form-item> -->
				<el-form-item label="数量：">
					<div>{{orderDetails.orderTotal }}</div>
				</el-form-item>
				<el-form-item label="评价：">
					<div>{{orderDetails.content }}</div>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="editFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		<!-- 回复 -->
		<el-dialog title="回复" v-model="hfInfo" :close-on-click-modal="false" >
			<el-form :model="orderDetails" label-width="160px">
				<el-form-item label="商品编号">
					<div>{{editForm.goodsId }}</div>
				</el-form-item>
				<<!-- el-form-item label="买家">
					<div>{{editForm.coreUser.realName}}</div>
				</el-form-item>
				<el-form-item label="电话">
					<div>{{editForm.coreUser.mobile}}</div>
				</el-form-item>
				<el-form-item label="商品名称">
					<div>{{editForm.goods.name }}</div>
				</el-form-item> -->
				<el-form-item label="数量：">
					<div>{{editForm.orderTotal }}</div>
				</el-form-item>
				<el-form-item label="评价：">
					<div>好评</div>
					<div v-if="editForm.crate === 1">好评</div>
					<div v-if="editForm.crate === 2">中评</div>
					<div v-if="editForm.crate === 3">差评</div>
				</el-form-item>
				<el-form-item>
					<div>editForm.content</div>
					<img width="100px" :src="editForm.picImgs">
				</el-form-item>
				<el-form-item label="回复内容：">
					<el-input  v-model="deco" type="textarea" :rows="2" auto-complete="off"></el-input>
				</el-form-item>
				<!-- <el-form-item label="图片">
					<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
					<button type="button" class="el-button el-button--primary el-button--small">
						<span>点击上传</span>
					</button>
				</el-form-item>
	            <el-col :span="24" v-for="item in CommodityPictures">
	              <img style="position: relative;left: 120px;top: -77px;" @click="deldetImg(item)" src="../../assets/delet.png">
	              <img :src="item" style="width:100px;height: 100px;border: 1px solid #f0f0f0;">
	            </el-col> -->
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">保存</el-button>
				<el-button type="primary" @click.native="hfInfo = false">关闭</el-button>
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
				hfInfo:false,
				formData: new FormData(),
				CommodityPictures:[],
				selectSubjectStatus: [{
					value:1,
					label:'好评'
				},{
					value:2,
					label:'中评'
				},{
					value:3,
					label:'差评'
				}],
				options: [{
		          value: '1',
		          label: '商品编号'
		        }, {
		          value: '2',
		          label: '手机号'
		        }, {
		          value: '3',
		          label: '昵称'
		        }, {
		          value: '4',
		          label: '真实姓名'
		        }],
				filters: {
					name: '',
					status:'',
					type:''
				},
				total: 1,
				page: 1,
				listLoading: false,
				editFormVisible: false,//编辑界面是否显示
				editLoading: false,
				//编辑界面数据
				editForm: {
				},
				deco:'',
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {
					coreUser:{},
					goods:{}
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
				_this.table = []
				const params = {
					pageNum:_this.page,
					size:10,
					storeId:state.storeId,
					goodsId:'',
					mobile:'',
					nickName:'',
					realName:'',
					crate:this.filters.status
				}
				if(this.filters.type === '1'){
					params.goodsId = this.filters.name
				}else if(this.filters.type === '2'){
					params.mobile = this.filters.name
				}else if(this.filters.type === '3'){
					params.nickName = this.filters.name
				}else if(this.filters.type === '4'){
					params.realName = this.filters.name
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/ordersEvaluate/selectListOfGoods",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	const info = data.data
                    	// _this.orderInformation = info.list
                    	_this.total = info.total
                    }
                });
			},
			// 查看
			seeBtn(row){
				this.editFormVisible = true
				this.orderDetails = row
				console.log(this.orderDetails)
			},
			// 回复
			handleEdit(row){
				this.hfInfo = true
				this.editForm = row
			},
			editSubmit(){
				console.log(this.deco)
				const params = {
					evaluateId:this.editForm.id,
					replyContent:this.deco,
					replyPicture:''
				}
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/evaluateReply/add",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	_this.getlist()
                    }
                });
			},
			// /删除图片
	        deldetImg(src){
	          this.CommodityPictures.splice(jQuery.inArray(src, this.CommodityPictures),1)
	        },
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
			},
			formatterType(row, column){
				let type = ''
				if(row.crate === 1){
					type = '好评'
				}else if(row.crate === 2){
					type = '中评'
				}else if(row.crate === 3){
					type = '差评'
				}
                return type
			}
		},
		mounted() {
			this.getlist();
		}
	}

</script>

<style>
	
</style>