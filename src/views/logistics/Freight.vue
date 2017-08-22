<template>
	<section>
		<el-col :span="24" v-show="template">
			<!--工具条-->
			<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
				<el-form :inline="true">
					<el-form-item>
						<el-button type="primary" v-on:click="addTemplate">新增运费模板</el-button>
					</el-form-item>
				</el-form>
			</el-col>
			<el-col :span="24" style="margin-bottom: 20px">件数模板</el-col>
			<!--列表-->
			<el-col v-for="item in ceshiarry" style="margin-bottom: 40px">
				<el-col :span="24" style="border: 1px solid #ddd;height: 40px;line-height: 40px">
					<el-col :span="1" :offset="1" style="">
						{{item.name}}
					</el-col>
					<el-col :span="7" :offset="15" style="">
						最后编辑时间：2017-09-09 09:09:09 <span class="btncolor">设为默认的运费模板 | </span> <!-- <span class="btncolor">修改 | </span> --> <span class="btncolor" @click="deldetBtn(item)">删除</span>
					</el-col>
				</el-col>
				<el-table :data="item.fareCarries" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
					<el-table-column prop="mode" :formatter='formatter' label="运送方式">
					</el-table-column>
					<el-table-column prop="data1" label="运送到">
					</el-table-column>
					<el-table-column prop="firstHeavy" label="首件（个）">
					</el-table-column>
					<el-table-column prop="firstPrice" label="运费（元）">
					</el-table-column>
					<el-table-column prop="addHeavy" label="续件（个）">
					</el-table-column>
					<el-table-column prop="addPrice" label="运费（元）">
					</el-table-column>
				</el-table>
			</el-col>
		</el-col>
		

		<el-col :span="24" v-show="addtemplate">
			<el-col :span="24"><h3>新增运费模板</h3></el-col>
			<el-col :span="24">
				<el-col :span="2" class="left_temp">模板名称：</el-col>
				<el-col :span="3"><el-input v-model="addfrom.name" type="text"></el-input></el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" class="left_temp">宝贝地址：</el-col>
				<el-col :span="8">
					<el-cascader
				    :options="options"
				    v-model="addfrom.areaId"
				    @change="handleChange">
				  </el-cascader>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" class="left_temp">发货时间：</el-col>
				<el-col :span="6">
					<el-select v-model="addfrom.deliverTime" placeholder="请选择">
					    <el-option
					      v-for="item in times"
					      :key="item.value"
					      :label="item.label"
					      :value="item.value">
					    </el-option>
					  </el-select>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" class="left_temp">是否包邮：</el-col>
				<el-col :span="6">
					<el-radio class="radio" v-model="addfrom.isFree" label="0">自定义运费</el-radio>
  					<el-radio class="radio" v-model="addfrom.isFree" label="1">卖家承担运费</el-radio>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" class="left_temp">计件方式：</el-col>
				<el-col :span="6">
					<el-radio class="radio" v-model="addfrom.valuationType" label="0">按件数</el-radio>
  					<el-radio class="radio" v-model="addfrom.valuationType" label="1">安重量</el-radio>
  					<el-radio class="radio" v-model="addfrom.valuationType" label="2">按体积</el-radio>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" class="left_temp">运送方式：</el-col>
				<el-col :span="6" style="line-height: 30px">除指定地区外，其余地区的运费采用“默认运费”</el-col>
			</el-col>
			<el-col :span="24">
				<el-col :span="2" :offset="2"> <el-checkbox v-model="checked" @change="showKD">快递</el-checkbox></el-col>
			</el-col>
			<!-- ------------------------------------------快递--------------------------------------------------------- -->
			<el-col :span="24" class="top_margin" v-show="kd_table">
				<el-col :span="20" :offset="2" style="border:1px solid #ddd">
					<el-col :span="24" class="top_margin">
						<el-col :span="2" class="left_temp">默认运费：</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValue.freight"></el-input></el-col>
						<el-col :span="1" class="left_temp">kg内，</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValue.element"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元，</el-col>
						<el-col :span="1" class="left_temp">每增加</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValue.zfreight"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">kg，</el-col>
						<el-col :span="1" class="left_temp">增加运费</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValue.zelement"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元</el-col>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-table :data="fareCarries" >
					      <el-table-column prop="data" label="运送到" width="280">
					      	<template scope="scope">
					      		{{scope.row.data}}
					      		<el-button type="text" size="small" @click="kdEditBtn(scope.row)">编辑</el-button>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstHeavy" label="首重(kg)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstHeavy" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstPrice" label="首费(元)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="addHeavy" label="续重(kg)" width="190">
						      <template scope="scope">
						      		<el-input v-model="scope.row.addHeavy" type="number"></el-input>
								</template>
					      </el-table-column>
					      <el-table-column prop="addPrice" label="续费(元)" width="200">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.addPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column label="操作">
							<template scope="scope">
								<el-button type="text" size="small" @click="deldetKDsubmit">删除</el-button>
							</template>
						</el-table-column>
					    </el-table>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-button type="text" size="small" @click="addKDsubmit">为指定地区设置运费</el-button>
						<!-- <el-button type="text" size="small" @click="handleEdit(scope.$index, scope.row)">批量操作</el-button> -->
					</el-col>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" :offset="2"> <el-checkbox v-model="ems" @change="showEMS">EMS</el-checkbox></el-col>
			</el-col>
			<!-- ------------------------------------------EMS--------------------------------------------------------- -->
			<el-col :span="24" class="top_margin" v-show="EMS_table">
				<el-col :span="20" :offset="2" style="border:1px solid #ddd">
					<el-col :span="24" class="top_margin">
						<el-col :span="2" class="left_temp">默认运费：</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValueEMS.freight"></el-input></el-col>
						<el-col :span="1" class="left_temp">kg内，</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValueEMS.element"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元，</el-col>
						<el-col :span="1" class="left_temp">每增加</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValueEMS.zfreight"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">kg，</el-col>
						<el-col :span="1" class="left_temp">增加运费</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValueEMS.zelement"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元</el-col>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-table :data="fareCarriesEMS" >
					      <el-table-column prop="data" label="运送到" width="280">
					      	<template scope="scope">
					      		{{scope.row.data}}
					      		<el-button type="text" size="small" @click="kdEditBtn(scope.row)">编辑</el-button>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstHeavy" label="首重(kg)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstHeavy" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstPrice" label="首费(元)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="addHeavy" label="续重(kg)" width="190">
						      <template scope="scope">
						      		<el-input v-model="scope.row.addHeavy" type="number"></el-input>
								</template>
					      </el-table-column>
					      <el-table-column prop="addPrice" label="续费(元)" width="200">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.addPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column label="操作">
							<template scope="scope">
								<el-button type="text" size="small" @click="handldeldet(scope.$index, scope.row)">删除</el-button>
							</template>
						</el-table-column>
					    </el-table>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-button type="text" size="small" @click="addEMSsubmit">为指定地区设置运费</el-button>
						<!-- <el-button type="text" size="small" @click="handleEdit(scope.$index, scope.row)">批量操作</el-button> -->
					</el-col>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" :offset="2"> <el-checkbox v-model="pingyou" @change="showPY">平邮</el-checkbox></el-col>
			</el-col>
			<!-- ------------------------------------------平邮--------------------------------------------------------- -->
			<el-col :span="24" class="top_margin" v-show="py_table">
				<el-col :span="20" :offset="2" style="border:1px solid #ddd">
					<el-col :span="24" class="top_margin">
						<el-col :span="2" class="left_temp">默认运费：</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValuePY.freight"></el-input></el-col>
						<el-col :span="1" class="left_temp">kg内，</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValuePY.element"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元，</el-col>
						<el-col :span="1" class="left_temp">每增加</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValuePY.zfreight"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">kg，</el-col>
						<el-col :span="1" class="left_temp">增加运费</el-col>
						<el-col :span="3"><el-input type="text" v-model="defaultValuePY.zelement"></el-input></el-col>
						<el-col :span="1" style="line-height: 30px">元</el-col>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-table :data="fareCarriesPY" >
					      <el-table-column prop="data" label="运送到" width="280">
					      	<template scope="scope">
					      		{{scope.row.data}}
					      		<el-button type="text" size="small" @click="kdEditBtn(scope.row)">编辑</el-button>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstHeavy" label="首重(kg)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstHeavy" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="firstPrice" label="首费(元)" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.firstPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="addHeavy" label="续重(kg)" width="190">
						      <template scope="scope">
						      		<el-input v-model="scope.row.addHeavy" type="number"></el-input>
								</template>
					      </el-table-column>
					      <el-table-column prop="addPrice" label="续费(元)" width="200">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.addPrice" type="number"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column label="操作">
							<template scope="scope">
								<el-button type="text" size="small" @click="handldeldet(scope.$index, scope.row)">删除</el-button>
							</template>
						</el-table-column>
					    </el-table>
					</el-col>
					<el-col :span="20" class="top_margin" style="margin-left: 10px;">
						<el-button type="text" size="small" @click="addPYsubmit">为指定地区设置运费</el-button>
						<!-- <el-button type="text" size="small" @click="handleEdit(scope.$index, scope.row)">批量操作</el-button> -->
					</el-col>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin">
				<el-col :span="2" :offset="1"> <el-checkbox v-model="addfrom.specifyMailStatus">按指定条件包邮可选</el-checkbox></el-col>
			</el-col>
			<el-col :span="24" class="top_margin" v-show="false">
				<el-col :span="16" :offset="1">
					<el-table :data="tableData" >
					      <el-table-column prop="name" label="选择地区" width="280">
					      	<template scope="scope">
					      		{{scope.row.name}}
					      		<el-button type="text" size="small" @click="kdEditBtn(scope.row)">编辑</el-button>
							</template>
					      </el-table-column>
					      <el-table-column prop="name" label="选择运送方式" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.name" type="text"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="address" label="选择快递" width="180">
					      	<template scope="scope">
					      		<el-input v-model="scope.row.name" type="text"></el-input>
							</template>
					      </el-table-column>
					      <el-table-column prop="date" label="设置包邮条件" width="190">
						      <template scope="scope">
						      		<el-input v-model="scope.row.name" type="text"></el-input>
								</template>
					      </el-table-column>
					      <el-table-column label="操作">
							<template scope="scope">
								<el-button type="text" size="small" @click="handldel(scope.$index, scope.row)">删除</el-button>
							</template>
						</el-table-column>
					    </el-table>
				</el-col>
			</el-col>
			<el-col :span="24" class="top_margin"><el-button type="primary" v-on:click="preservation">保存</el-button></el-col>
		</el-col>
		<el-dialog title="选择区域" v-model="regionDiv" :close-on-click-modal="false" >
		<!-- check-strictly  父子联动  :expand-on-click-node="false" 点击箭头展开-->
			<el-tree :data="treeLisy" show-checkbox    @check-change="handleNodeClick"  node-key="id" ref="tree"  highlight-current style="border: none">
			</el-tree>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="dequerSubmit">提交</el-button>
				<!-- <el-button type="primary" @click.native="regionDiv = false">关闭</el-button> -->
			</div>
		</el-dialog>
		<!--工具条-->
		<el-col :span="24" class="toolbar" v-show="template" style="background:#fff;">
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
	</section>
</template>

<script>
	import { state } from '../../vuex/state'
	import util from '../../common/js/util'
	import {baseUrl , editUser, addUser,cityData3,cityData2} from '../../api/api';

	export default {
		data() {
			return {
				kd_table:false,
				EMS_table:false,
				py_table:false,
				regionDiv:false,
				ceshiarry:[
					{name:1},
					{name:2}
				],
				defaultValue:{
					freight:'',
					element:'',
					zfreight:'',
					zelement:'',
					data:[],
					mode:0,
					isDefault:0
				},
				defaultValueEMS:{
					freight:'',
					element:'',
					zfreight:'',
					zelement:'',
					data:[],
					mode:0,
					isDefault:0
				},
				defaultValuePY:{
					freight:'',
					element:'',
					zfreight:'',
					zelement:'',
					data:[],
					mode:0,
					isDefault:0
				},
				addfrom:{
					name:'',
					areaId:[],
					deliverTime:'',
					isFree:'0',
					valuationType:'0',
					specifyMailStatus:false
				},
				fareCarries:[],
				fareCarriesEMS:[],
				fareCarriesPY:[],
				isareaId:'',
				fareIpps:[{
					ippType:'',
					ippData:'',
					ippNum:'',
					ippPrice:'',
					transportType:''
				}],
				template:true,
				addtemplate:false,
				radio: '1',
				piece:'1',
				checked: false,
				pingyou:false,
				ems:false,
				tableData:[{
					name:'1'
				}],
				value:'',
				value1:'',
				value2:'',
				selectSubjectStatus: [
				{
					value:'0',
					label:'全部'
				},{
					value:'1',
					label:'待付款'
				},{
					value:'2',
					label:'待发货'
				},{
					value:'3',
					label:'已发货'
				},{
					value:'4',
					label:'待评价'
				},{
					value:'5',
					label:'退货'
				}],
				options: cityData3,
				treeLisy: cityData2,
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
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {
				},
				orderInformation:[],
				times:[{
		          value: '4',
		          label: '4小时内'
		        }, {
		          value: '8',
		          label: '8小时内'
		        }, {
		          value: '12',
		          label: '12小时内'
		        }, {
		          value: '16',
		          label: '16小时内'
		        }, {
		          value: '20',
		          label: '20小时内'
		        }, {
		          value: '24',
		          label: '1天内'
		        }, {
		          value: '48',
		          label: '2天内'
		        }, {
		          value: '144',
		          label: '12天内'
		        }, {
		          value: '225',
		          label: '15天内'
		        }, {
		          value: '408',
		          label: '17天内'
		        }, {
		          value: '480',
		          label: '20天内'
		        }, {
		          value: '600',
		          label: '25天内'
		        }],
		        role_arr:[],
		        role_arrinit:[],
		        roleName_arr:[],
		        roleName_arrinit:[],
		        initobj :{},
		        province:'',
		        city:'',
		        area:'',
		        areaId:'',
		        cityId:'',
		        provinceId:''
			}
		},
		methods: {
			// 地区树勾选回调 
			handleNodeClick(row,index){
				console.log(row)
				this.role_arr = []
				this.roleName_arr = []
				if(index === true){
					this.role_arrinit.push(row.id)
					this.roleName_arrinit.push(row.label)
					var json = {};
					for(var i = 0; i < this.role_arrinit.length; i++){
					  if(!json[this.role_arrinit[i]]){
					   this.role_arr.push(this.role_arrinit[i]);
					   this.roleName_arr.push(this.roleName_arrinit[i]);
					   json[this.role_arrinit[i]] = 1;
					   json[this.roleName_arrinit[i]] = 1;
					  }
					}
					this.role_arrinit = this.role_arr
					this.roleName_arrinit = this.roleName_arr
				}else if(index ===false){
					Array.prototype.indexOf = function(val) {
						for (var i = 0; i < this.length; i++) {
							if (this[i] == val) return i;
						}
						return -1;
					};
					Array.prototype.remove = function(val) {
						var index = this.indexOf(val);
						if (index > -1) {
							this.splice(index, 1);
						}
					};
					this.role_arrinit.remove(row.id);
					this.role_arr = this.role_arrinit
					this.roleName_arrinit.remove(row.label);
					this.roleName_arr = this.roleName_arrinit
				}
				// this.fareCarries.data = this.role_arr
				// this.role_arr.join(',')
				// this.initobj.data = this.roleName_arr.join(',')
			},
			// 确认选着地区
			dequerSubmit(){
				// let _this = this
				this.initobj.data = this.roleName_arr.join(',')
				this.initobj.id = this.role_arr.join(',')
				console.log(this.initobj.id)
				this.regionDiv = false
				this.$refs.tree.setCheckedKeys([]);//清空勾选
				// const params = {
				// 	accountId:'1',
				// 	accessToken:'1',
				// 	orgId:_this.fatherRoleID,
				// 	resourcesIds:this.role_arr.join(',')
				// }
			},
			// 显示快递
			showKD(){
				if(this.checked === false){
					this.kd_table = false
				}else{
					this.kd_table = true
				}
			},
			// 添加快递运输方式
			addKDsubmit(){
				const obj = {
					id:[],
					mode:0,
					isDefault:0,
					data:'',
					firstHeavy:'',
					firstPrice:'',
					addHeavy:'',
					addPrice:''
				}
				this.fareCarries.push(obj)
			},
			// 添加EMS运输方式
			addEMSsubmit(){
				const obj = {
					mode:1,
					isDefault:0,
					data:'',
					firstHeavy:'',
					firstPrice:'',
					addHeavy:'',
					addPrice:''
				}
				this.fareCarriesEMS.push(obj)
			},
			// 添加平邮运输方式
			addPYsubmit(){
				const obj = {
					mode:2,
					isDefault:0,
					data:'',
					firstHeavy:'',
					firstPrice:'',
					addHeavy:'',
					addPrice:''
				}
				this.fareCarriesPY.push(obj)
			},
			// 显示地区树
			kdEditBtn(row){
				console.log(row)
				this.initobj = row
				this.regionDiv = true
			},
			deldetKDsubmit(){

			},
			// 显示EMS编辑
			showEMS(){
				if(this.ems === false){
					this.EMS_table = false
				}else{
					this.EMS_table = true
				}
			},
			// 显示平邮编辑
			showPY(){
				if(this.pingyou === false){
					this.py_table = false
				}else{
					this.py_table = true
				}
			},
			handldeldet(index ,row){
				console.log(row)
			},
			//新增模板
			addTemplate() {
				this.template = false
				this.addtemplate = true
			},
			 handleChange(value) {
		        console.log(value);
		        console.log(this.addfrom.areaId)
		        this.isareaId = value[value.length-1]
		        console.log(this.isareaId)
		        console.log(cityData3)
		        let arry = []
		        let arry1 = []
		        this.areaId = value[0]
		        this.cityId = value[1]
		        this.provinceId = value[2]
		        for(var i = 0;i<cityData3.length;i++){
		        	if(value[0] === cityData3[i].value){
		        		console.log(cityData3[i].label)
		        		arry = cityData3[i].children
		        	}
		        }
		        for(var i = 0;i<arry.length;i++){
		        	if(value[1] === arry[i].value){
		        		console.log(arry[i].label)
		        		arry1 = arry[i].children
		        	}
		        }
		        for(var i = 0;i<arry1.length;i++){
		        	if(value[2] === arry1[i].value){
		        		console.log(arry1[i].label)
		        	}
		        }
		    },
		    // 删除运费模板
		    deldetBtn(row){	
		    	const _this = this
				this.selectSubjectStatus = []
				const params = {
					id:row.id
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/fareTemplate/deleteById",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	if(data.code === 1){
                    		_this.gettemplet()
                    	}
                    	
                    }
                });
		    },
		    // 添加运费模板
			preservation() {
				const _this = this
				let specifyMailStatu = 0
				let check = ''
				if(_this.addfrom.specifyMailStatus === false){
					specifyMailStatu = 0
				}else{
					specifyMailStatu = 1
				}
				const params = {
					name:_this.addfrom.name,
					// areaId:this.isareaId,
					deliverTime:_this.addfrom.deliverTime,
					isFree:_this.addfrom.isFree,
					valuationType:_this.addfrom.valuationType,
					storeId:state.storeId,

					areaId:this.areaId,
					cityId:this.cityId,
					provinceId:this.provinceId,

					province:this.province,
					city:this.city,
					area:this.area,

					specifyMailStatus:specifyMailStatu,

					
					fareCarries:[]
				}





				if(this.checked === true){
					console.log(this.fareCarries)
					const mrobj = {
						mode:0,
						isDefault:0,
						data:"''",
						firstHeavy:this.defaultValue.freight,
						firstPrice:this.defaultValue.element,
						addHeavy:this.defaultValue.zfreight,
						addPrice:this.defaultValue.zelement
					}
					params.fareCarries.push(mrobj)
					for(var i = 0;i<this.fareCarries.length;i++){
						const obj = {
							mode:0,
							isDefault:1,
							data:[]
							// firstHeavy:1,
							// firstPrice:1,
							// addHeavy:1,
							// addPrice:1
						}
						obj.firstHeavy = this.fareCarries[i].firstHeavy
						obj.firstPrice = this.fareCarries[i].firstPrice
						obj.addHeavy = this.fareCarries[i].addHeavy
						obj.addPrice = this.fareCarries[i].addPrice
						obj.data = this.fareCarries[i].id
						// obj.data = JSON.stringify(obj.data)
						params.fareCarries.push(obj)
					}
					// this.defaultValue.data = JSON.stringify(this.defaultValue.data)
					// params.fareCarries.push(this.defaultValue)
					// console.log(params)
				}

				if(this.ems === true){
					console.log(this.fareCarriesEMS)
					const mrobj = {
						mode:1,
						isDefault:0,
						data:'',
						firstHeavy:this.defaultValueEMS.freight,
						firstPrice:this.defaultValueEMS.element,
						addHeavy:this.defaultValueEMS.zfreight,
						addPrice:this.defaultValueEMS.zelement
					}
					params.fareCarries.push(mrobj)
					for(var i = 0;i<this.fareCarriesEMS.length;i++){
						const obj = {
							mode:1,
							isDefault:1,
							data:[]
						}
						obj.firstHeavy = this.fareCarriesEMS[i].firstHeavy
						obj.firstPrice = this.fareCarriesEMS[i].firstPrice
						obj.addHeavy = this.fareCarriesEMS[i].addHeavy
						obj.addPrice = this.fareCarriesEMS[i].addPrice

						obj.data = JSON.stringify(obj.data)
						params.fareCarries.push(obj)
					}
					// this.defaultValueEMS.data = JSON.stringify(this.defaultValueEMS.data)
					// params.fareCarries.push(this.defaultValueEMS)
					// console.log(params)
				}

				if(this.pingyou === true){
					console.log(this.fareCarriesPY)
					const mrobj = {
						mode:2,
						isDefault:0,
						data:'',
						firstHeavy:this.defaultValuePY.freight,
						firstPrice:this.defaultValuePY.element,
						addHeavy:this.defaultValuePY.zfreight,
						addPrice:this.defaultValuePY.zelement
					}
					params.fareCarries.push(mrobj)
					for(var i = 0;i<this.fareCarriesPY.length;i++){
						const obj = {
							mode:2,
							isDefault:1,
							data:[]
						}
						obj.firstHeavy = this.fareCarriesPY[i].firstHeavy
						obj.firstPrice = this.fareCarriesPY[i].firstPrice
						obj.addHeavy = this.fareCarriesPY[i].addHeavy
						obj.addPrice = this.fareCarriesPY[i].addPrice

						obj.data = JSON.stringify(obj.data)
						params.fareCarries.push(obj)
					}
					// this.defaultValuePY.data = JSON.stringify(this.defaultValuePY.data)
					// params.fareCarries.push(this.defaultValuePY)
					// console.log(params)
				}
				




				console.log(params)










				
				// const zobj = {
				// 	data:this.defaultValue.data,
				// 	firstHeavy:this.defaultValue.freight,
				// 	firstPrice:this.defaultValue.element,
				// 	addHeavy:this.defaultValue.zfreight,
				// 	addPrice:this.defaultValue.zelement,
				// 	mode:0,
				// 	isDefault:0
				// }
				// zobj.data = JSON.stringify(zobj.data)
				// params.fareCarries.push(zobj)
				// console.log(params)
				
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/fareTemplate/add",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
                    		_this.addtemplate = false
							_this.template = true
							_this.gettemplet()
                    	}
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
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
			gettemplet() {
				const _this = this
				_this.ceshiarry = []
				const params = {
					storeId:state.storeId,
					pageNum:this.page,
					size:10,
					name:''
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/fareTemplate/selectListByStoreId",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	_this.total = data.data.total
                    	const info = data.data.list
                    	const arry = []
                    	for (var i = 0; i < info.length; i++) {
                    		console.log(info[i])
                    		_this.ceshiarry.push(info[i])
                    		// for (var x = 0; x < info[i].fareCarries.length; x++) {
                    		// 	arry.push(info[i].fareCarries[x])
                    		// }
                    	}
                    	console.log(_this.ceshiarry)
                    	// _this.orderInformation = arry
                    }
                });
			},
			formatter(row,column) {
				if(row.mode === 0){
					return '快递'
				}else if(row.mode === 1) {
					return 'EMS'
				}else if(row.mode === 2) {
					return '平邮'
				}
			}
		},
		mounted() {
			this.gettemplet();
		}
	}

</script>

<style>
	.btncolor{
		color: #20a0ff;
		cursor: pointer;
	}
	.left_temp{
		text-align: right;
		line-height: 30px;
	}
	.top_margin{
		margin-top: 20px;
	}
</style>