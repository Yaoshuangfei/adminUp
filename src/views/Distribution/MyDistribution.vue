<template>
	<section>
	<el-dialog title="输入密码" v-model="mimayanz" :close-on-click-modal="false" :show-close='false'>
		<el-form label-width="100px">
			<el-form-item label="密码">
				<el-input v-model="password" type="password" auto-complete="off"></el-input>
			</el-form-item>
		</el-form>
		<div slot="footer" class="dialog-footer" style="text-align: center;">
			<el-button type="primary" @click.native="mimaSubmit" :loading="editLoading">确定</el-button>
		</div>
	</el-dialog>
	<el-col v-if="allshow">
		<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px">
			<!-- <el-button type="primary" v-on:click="addIDCard" style="margin-top: 20px" :disabled="this.ruleAll.length===5">新增店铺身份</el-button> -->
		</el-col>
		<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px;margin-bottom: 20px">店铺等级</el-col>
		<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-top: 20px;margin-bottom: 20px">
			<ul class="Grade">
				<li v-for="item in ruleAll">{{item.name}}</li>
			</ul>
		</el-col>
		<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-bottom: 20px;margin-left: 50px">填写可购买的最高级身份，最多填写5个</el-col>
		<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">规则</el-col>
		<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">
			<el-col :xs="4" :sm="4" :md="4" :lg="4" style="margin-bottom: 20px;margin-left: 60px">身份是否升级</el-col>
			<el-col :xs="4" :sm="4" :md="4" :lg="4" style="margin-bottom: 20px">
				<el-switch
					@change='clickrule'
					:disabled='upgradebtn'
				  	v-model="ruleIsUpgrade"
				  	on-color="#13ce66"
				  	off-color="#ff4949">
				</el-switch>
				<!-- <el-select v-model="value" placeholder="请选择">
				    <el-option
				      v-for="item in options"
				      :key="item.value"
				      :label="item.label"
				      :value="item.value">
				    </el-option>
			  </el-select> -->
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3" style="margin-bottom: 20px" v-show="ruleIsUpgrade">邀请人数：{{upgrade.invitedMinNum}}</el-col>
		</el-col>
		<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-bottom: 20px">
			<el-col :xs="3" :sm="3" :md="3" :lg="3" style="margin-bottom: 20px">邀请人身份</el-col>
			<el-col :xs="8" :sm="8" :md="8" :lg="8" style="margin-bottom: 20px">
			  <el-radio-group v-model="roleId" @change = "click">
			    <el-radio-button  v-for="item in ruleAll" :label="item.id">{{item.name}}</el-radio-button>
			  </el-radio-group>
			</el-col>
		</el-col>
		<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-bottom: 20px">
			<el-col :xs="3" :sm="3" :md="3" :lg="3" style="margin-bottom: 20px">被邀请人身份</el-col>
			<el-col :xs="21" :sm="21" :md="21" :lg="21" style="margin-bottom: 20px">
				<el-radio-group v-model="invitedRoleId" @change ="clickB">
				    <el-radio-button  v-for="item in ruleAll" :label="item.id">{{item.name}}</el-radio-button>
				</el-radio-group>
			</el-col>
		</el-col>
		<!--工具条-->
		<!-- <el-col :span="24" class="toolbar" style="background:#fff;"> -->
			<!-- <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button> -->
			<!-- <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;"> -->
			<!-- </el-pagination> -->
		<!-- </el-col> -->
		<!--身份升级-->
		<el-dialog title="身份升级" v-model="ruleIsUpgradeVisible" :close-on-click-modal="false" :show-close='false'>
			<el-form :model="upgrade" label-width="100px">
				<el-form-item label="邀请人数">
					<el-input v-model="upgrade.invitedMinNum" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<!-- <el-form-item label="角色名称">
					<el-input v-model="upgrade.invitedMinNum" type="text" auto-complete="off"></el-input>
				</el-form-item> -->
				<el-col :span='16' :offset="6">被邀请人身份必须同级或高于才有效</el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="upgradetSubmit" :loading="editLoading">确定</el-button>
				<el-button type="primary" @click.native="clerbtn">关闭</el-button>
			</div>
		</el-dialog>
		<!--新增界面-->
		<el-dialog title="新增店铺身份" v-model="addFormVisible" :close-on-click-modal="false">
			<el-form :model="orderDetails" label-width="160px">
				<el-form-item label="角色名称：">
					<el-input v-model="orderDetails.name" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="价格：">
					<el-input v-model="orderDetails.price" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="角色图标：">
					<el-input v-model="orderDetails.icon" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="addSubmit" :loading="editLoading">确定</el-button>
				<el-button type="primary" @click.native="addFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		<!-- //修改分佣表 -->
		<el-col :xs="24" :sm="24" :md="24" :lg="24" class="marginleft">
			<el-col :xs="3" :sm="3" :md="3" :lg="3"></el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3"  v-for="item in ruleAll">{{item.name}}</el-col>
		</el-col>
		<el-col :span='24' style="border:1px solid #ddd">
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arryuser">{{item.name}}
					</li>
				</ul>
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arry1" @click="seeBtn(item.id,arry1,0)">{{item.value}}
					</li>
				</ul>
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arry2" @click="seeBtn(item.id,arry2,1)">{{item.value}}
					</li>		
				</ul>
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arry3" @click="seeBtn(item.id,arry3,2)">{{item.value}}
					</li>					
				</ul>
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arry4" @click="seeBtn(item.id,arry4,3)">{{item.value}}
					</li>					
				</ul>
			</el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3">
				<ul class="roul_ul">
					<li v-for="item in arry5" @click="seeBtn(item.id,arry5,4)">{{item.value}}
					</li>					
				</ul>
			</el-col>
		</el-col>
		<!-- <el-col :xs="24" :sm="24" :md="24" :lg="24" class="marginleft">
			<el-col :xs="3" :sm="3" :md="3" :lg="3"></el-col>
			<el-col :xs="3" :sm="3" :md="3" :lg="3"><el-button type="text" size="small" @click="seeBtn">修改</el-button></el-col>
		</el-col> -->
		<el-col :xs="1" :sm="1" :md="1" :lg="1" :offset="23" style="">
			<el-button type="primary" @click="addruleTable">增加</el-button>
		</el-col>
		<el-dialog title="修改" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form label-width="100px">
				<el-form-item :label="initname">
					<el-input v-model="editValue" type="text"></el-input>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="editSubmit" :loading="editLoading">确定</el-button>
				<el-button type="primary" @click.native="editFormVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		<!-- //新增分佣明细 -->
		<el-dialog title="添加分佣" v-model="ruleIdVisible" :close-on-click-modal="false" >
			<el-form :model="ruleIdFrom" label-width="100px" :rules="editFormRules" ref="editForm">
			<!-- 	<el-form-item v-for="item in ruleAll" :label="item.name">
					<el-input :v-model="ruleIdFrom.Name" type="text" auto-complete="off"></el-input>
				</el-form-item> -->
				<el-form-item :label="arryname[0]"  v-if="ruleAll[0]">
					<el-input v-model="ruleIdFrom.name0" type="text" style="width: 150px"></el-input>%
				</el-form-item>
				<el-form-item :label="arryname[1]" v-if="ruleAll[1]">
					<el-input  v-model="ruleIdFrom.name1" type="text" style="width: 150px"></el-input>%
				</el-form-item>
				<el-form-item :label="arryname[2]" v-if="ruleAll[2]" >
					<el-input v-model="ruleIdFrom.name2" type="text" style="width: 150px"></el-input>%
				</el-form-item>
				<el-form-item :label="arryname[3]" v-if="ruleAll[3]">
					<el-input  v-model="ruleIdFrom.name3" type="text" style="width: 150px"></el-input>%
				</el-form-item>
				<el-form-item :label="arryname[4]" v-if="ruleAll[4]">
					<el-input  v-model="ruleIdFrom.name4" type="text" style="width: 150px"></el-input>%
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="upruleId" :loading="editLoading">确定</el-button>
				<el-button type="primary" @click.native="ruleIdVisible = false">关闭</el-button>
			</div>
		</el-dialog>
		</el-col>
<!--====================================================== 磁疗贴======================================================== -->
		<el-col :span="24" v-if="cltShow">
					<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px">
						<!-- <el-button type="primary" v-on:click="addIDCard" style="margin-top: 20px" :disabled="this.ruleAll.length >= 1">新增店铺身份</el-button> -->
					</el-col>
					<!--新增界面-->
				<el-dialog title="新增店铺身份" v-model="addFormVisible" :close-on-click-modal="false">
					<el-form :model="orderDetails" label-width="160px">
						<el-form-item label="角色名称：">
							<el-input v-model="orderDetails.name" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-form-item label="价格：">
							<el-input v-model="orderDetails.price" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-form-item label="角色图标：">
							<el-input v-model="orderDetails.icon" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-col :span='24'></el-col>
					</el-form>
					<div slot="footer" class="dialog-footer" style="text-align: center;">
						<el-button type="primary" @click.native="addSubmit" :loading="editLoading">确定</el-button>
						<el-button type="primary" @click.native="addFormVisible = false">关闭</el-button>
					</div>
				</el-dialog>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px;margin-bottom: 20px">店铺等级</el-col>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px;margin-bottom: 20px" v-if="commissionLine === 5">小区</el-col>
				<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-top: 20px;margin-bottom: 20px">
					<ul class="Grade">
						<li v-for="item in ruleAll" v-if="item.level !== -100">{{item.name}}</li>
					</ul>
				</el-col>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-top: 20px;margin-bottom: 20px" v-if="commissionLine === 5">大区</el-col>
				<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-top: 20px;margin-bottom: 20px">
					<ul class="Grade">
						<li v-for="item in ruleAll" v-if="item.level === -100">{{item.name}}</li>
					</ul>
				</el-col>
				<el-col :xs="20" :sm="20" :md="20" :lg="20" style="margin-bottom: 20px;margin-left: 50px">填写可购买的最高级身份，最多填写1个</el-col>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">规则</el-col>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">
					<el-col :xs="4" :sm="4" :md="4" :lg="4" style="margin-bottom: 20px;margin-left: 60px">身份是否升级</el-col>
					<el-col :xs="4" :sm="4" :md="4" :lg="4" style="margin-bottom: 20px">
						<el-switch
							@change='clickrule'
							:disabled='upgradebtn'
						  	v-model="ruleIsUpgrade"
						  	on-color="#13ce66"
						  	off-color="#ff4949">
						</el-switch>
						<!-- <el-select v-model="value" placeholder="请选择">
						    <el-option
						      v-for="item in options"
						      :key="item.value"
						      :label="item.label"
						      :value="item.value">
						    </el-option>
					  </el-select> -->
					</el-col>
					<el-col :xs="3" :sm="3" :md="3" :lg="3" style="margin-bottom: 20px" v-show="ruleIsUpgrade">邀请人数：{{upgrade.invitedMinNum}}</el-col>
				</el-col>
				<!--身份升级-->
				<el-dialog title="身份升级" v-model="ruleIsUpgradeVisible" :close-on-click-modal="false" :show-close='false'>
					<el-form :model="upgrade" label-width="100px">
						<el-form-item label="邀请人数">
							<el-input v-model="upgrade.invitedMinNum" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-col :span='16' :offset="6">被邀请人身份必须同级或高于才有效</el-col>
						<el-form-item label="身份名称">
							<el-input v-model="upgrade.name" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-form-item label="角色图标">
							<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
							<button type="button" class="el-button el-button--primary el-button--small">
								<span>点击上传</span>
							</button>
						</el-form-item>
						<img v-if="url !== '' " style="width: 100px;" :src="url">
					</el-form>
					<div slot="footer" class="dialog-footer" style="text-align: center;">
						<el-button type="primary" @click.native="upgradetSubmit" :loading="editLoading">确定</el-button>
						<el-button type="primary" @click.native="clerbtn">关闭</el-button>
					</div>
				</el-dialog>
				<el-dialog title="身份升级" v-model="ruleIsUpgradeVisible_5" :close-on-click-modal="false" :show-close='false'>
					<el-form :model="upgrade" label-width="100px">
						<el-form-item label="邀请人数">
							<el-input v-model="upgrade.invitedMinNum" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-col :span='16' :offset="6">被邀请人身份必须同级或高于才有效</el-col>
						<el-form-item label="身份名称">
							<el-input v-model="upgrade.name" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-form-item label="角色图标">
							<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
							<button type="button" class="el-button el-button--primary el-button--small">
								<span>点击上传</span>
							</button>
						</el-form-item>
						<img v-if="url_5 !== '' " style="width: 100px;" :src="url_5">
						<el-form-item label="身份名称">
							<el-input v-model="upgrade.name1" type="text" auto-complete="off"></el-input>
						</el-form-item>
						<el-form-item label="角色图标">
							<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload1" id="fileInput">
							<button type="button" class="el-button el-button--primary el-button--small">
								<span>点击上传</span>
							</button>
						</el-form-item>
						<img v-if="url_51 !== '' " style="width: 100px;" :src="url_51">
					</el-form>
					<div slot="footer" class="dialog-footer" style="text-align: center;">
						<el-button type="primary" @click.native="upgradetSubmit" :loading="editLoading">确定</el-button>
						<el-button type="primary" @click.native="clerbtn">关闭</el-button>
					</div>
				</el-dialog>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">三级分销</el-col>
					<el-table :data="cltSan" highlight-current-row style="width: 50%;min-width: 580px;margin-left: 40px">
						<el-table-column prop="level" label="等级">
						</el-table-column>
						<el-table-column prop="fybi" label="分佣比">
							<template scope="scope">
								<el-input style="width: 100px" v-model="scope.row.fybi"></el-input>
							</template>
						</el-table-column>
					</el-table>
				<el-col :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">平级奖</el-col>
				<el-table :data="cltPingji" highlight-current-row style="width: 50%;min-width: 580px;margin-left: 40px">
					<el-table-column prop="level" label="等级">
					</el-table-column>
					<el-table-column prop="fybi" label="分佣比">
						<template scope="scope">
							<el-input style="width: 100px" v-model="scope.row.fybi"></el-input>
						</template>
					</el-table-column>
				</el-table>
				<el-col v-if='commissionLine === 5' :xs="14" :sm="14" :md="14" :lg="14" style="margin-bottom: 20px">区域分红</el-col>
				<el-table v-if='commissionLine === 5' :data="qyPingji" highlight-current-row style="width: 50%;min-width: 580px;margin-left: 40px">
					<el-table-column prop="level" label="等级">
					</el-table-column>
					<el-table-column prop="fybi" label="分佣比">
						<template scope="scope">
							<el-input style="width: 100px" v-model="scope.row.fybi"></el-input>
						</template>
					</el-table-column>
				</el-table>
				<el-col :xs="20" :sm="20" :md="20" :lg="20" style="margin-top: 20px;">团队奖
					<el-input style="width: 200px" v-model="cltuanduired"></el-input>
				</el-col>
				<!-- <el-col :xs="20" :sm="20" :md="20" :lg="20" style="margin-top: 20px;" v-if='commissionLine === 3'>区域分红
					<el-input style="width: 200px" v-model="quyufred"></el-input>
				</el-col> -->
				<el-col :xs="10" :sm="10" :md="10" :lg="10" style="margin-top: 20px;">加权分红
					<el-input style="width: 200px" v-model="cltJqfred"></el-input>
				</el-col>
				<el-col :xs="20" :sm="20" :md="20" :lg="20" style="margin-bottom: 120px;">
					<el-button type="primary" v-on:click="cltUpfy" style="margin-top: 20px">提交</el-button>
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
				url_5:'',
				url_51:'',
				ruleIsUpgradeVisible_5:false,
				commissionLine:state.commissionLine,
				quyufred:'',
				mimayanz:true,
				password:'',
				isindex:'',
				eidt_size:'',
				edit_arryinit:[],
				initname:'',
				editValue:'',
				arry1:[],
				arry2:[],
				arry3:[],
				arry4:[],
				arry5:[],
				arryuser:[],
				model_name:[],
				initArry:[],
				ruleAll:[],
				distId:'',
				totalArray:[],
				arryname:[],
				ruleIsUpgrade:false,
				ruleIsUpgradeVisible:false,
				upgradebtn:false,
				upgrade:{},
				roleId:'',
				invitedRoleId:'',
				ruleIdFrom:{
					name0:'0',
					name1:'0',
					name2:'0',
					name3:'0',
					name4:'0'
				},
				options: [{
		          value: '0',
		          label: '否'
		        }, {
		          value: '1',
		          label: '是'
		        }],
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
				editForm: {},
				ruleIdVisible:false,//显示佣金新增页面
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {},
				orderInformation:[],
				//////////////////////////////////////////////////////磁疗贴//////////////////////////////////////////////////////////
				formData: new FormData(),
				url:'',
				allshow:true,
				cltShow:false,
				cltSf:[],
				cltSan:[{
					level:'一代',
					fybi:''
				},{
					level:'二代',
					fybi:''
				},{
					level:'三代',
					fybi:''
				}],
				cltPingji:[{
					level:'一代',
					fybi:''
				},{
					level:'二代',
					fybi:''
				},{
					level:'三代',
					fybi:''
				}],
				qyPingji:[{
					level:'一代',
					fybi:''
				},{
					level:'二代',
					fybi:''
				},{
					level:'三代',
					fybi:''
				}],
				cltJqfred:'',
				cltuanduired:'',
				cltFyBtn:false,
				cltId:''
			}
		},
		methods: {
			// 密码验证
			mimaSubmit(){
				const _this = this
				const params = {
					password :this.password
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/validatePwd",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
							_this.mimayanz = false
                    	}else{
                    		_this.$message.error(data.msg);
                    	}
                    }
                });
			},
			//获取邀请人身份
			click(val) {
				console.log(val)
				this.getSubCommission() 
			},
			//获取被邀请人身份
			clickB(val) {
				console.log(val)
				this.getSubCommission()
			},
			//获取身份分佣表
			getSubCommission() {
				const _this = this
				_this.arry1 = []
				_this.arry2 = []
				_this.arry3 = []
				_this.arry4 = []
				_this.arry5 = []
				// console.log(this.roleId)
				// console.log(this.invitedRoleId)
				for(var i = 0;i<this.totalArray.length;i++){
					if(parseInt(this.roleId) === this.totalArray[i].roleId && parseInt(this.invitedRoleId) === this.totalArray[i].invitedRoleId) {
						// console.log()
						this.distId =  this.totalArray[i].distId
					}
				}
				const params = {
					roleId:this.roleId,
					invitedRoleId:this.invitedRoleId
				}
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectByTwoRuleId",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	console.log(info)
                    	for(var i = 0;i<info.length;i++){
                    		if(info[i].priceData !== ''){
                    			const arrynew = JSON.parse(info[i].priceData)
	                			console.log(arrynew)
	                    		if(_this.ruleAll[0].id ===info[i].enjoyRoleId) {
	                    			_this.arry1 = arrynew
	                    		}else if(_this.ruleAll[1].id ===info[i].enjoyRoleId) {
	                    			_this.arry2 = arrynew
	                    		}else if(_this.ruleAll[2].id ===info[i].enjoyRoleId) {
	                    			_this.arry3 = arrynew
	                    		}else if(_this.ruleAll[3].id ===info[i].enjoyRoleId) {
	                    			_this.arry4 = arrynew
	                    		}else if(_this.ruleAll[4].id ===info[i].enjoyRoleId) {
	                    			_this.arry5 = arrynew
	                    		}
                    		}
                				
                    	}
                    	_this.arryuser = []
                    	console.log(_this.arry1)
                    	for(var i = 0;i<_this.arry1.length;i++){
                    		const obj = {}
                    		if(i === 0){
                    			obj.name = '自己拿'
                    			// obj.model_name = 'my_model'
                    			_this.arryuser.push(obj)
                    		}else{
                    			obj.name = i+'级'
                    			// obj.model_name = i+'level'
                    			_this.arryuser.push(obj)
                    		}
                    	}
                    	console.log(_this.arryuser)
                    }
                });
			},
			getlist(){
				const _this = this
				const params = {
					id:state.storeId
				}
				// console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectRuleAll",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	if(data.data === null){
                    		return
                    	}
                    	console.log(data.data)
                    	const info = data.data.shopRoles
                    	_this.ruleAll = info
                    	console.log(_this.ruleAll)
                    	for(var i = 0;i<info.length;i++){
                    		_this.arryname.push(info[i].name)
                    	}
                    	_this.upgrade.invitedMinNum = data.data.invitedMinNum
                    	if(data.data.ruleIsUpgrade === null){
                    		_this.ruleIsUpgrade = false
                    	}else if(data.data.ruleIsUpgrade === 0){
                    		_this.ruleIsUpgrade = false
                    	}else{
                    		_this.ruleIsUpgrade = true
                    		_this.upgradebtn = true
                    	}
                    }
                });
			},
			getselectRuleDist(){
                const _this = this
				const params = {
					// storeId:state.id
					roleId:this.roleId,
					invitedRoleId:this.invitedRoleId
				}
				// console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectByTwoRuleId",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	// console.log(data)
                    }
                });
            },
			clickrule(val) {
				// console.log(val)
				if(val){
					if(this.commissionLine === 5){
						this.ruleIsUpgradeVisible_5 = true
					}else{
						this.ruleIsUpgradeVisible = true
					}
				}
			},
			upgradetSubmit(){
				const _this = this
				const params = {
					id:state.storeId,
					ruleIsUpgrade:1,
					invitedMinNum:parseInt(_this.upgrade.invitedMinNum),
					shopRoles:[
						{
							name:this.upgrade.name,
							icon:this.url_5,
							level:0
						},{
							name:this.upgrade.name1,
							icon:this.url_51,
							level:-1
						}
					]
				}
				console.log(params)
				if(this.ruleIsUpgrade){
						console.log(1)
				}
				this.$confirm('确认提交吗？', '提示', {}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/shopRole/updateRuleAll",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	console.log(data)
	                    	if(data.code === 1){
		                    	_this.$message({
									message: '提交成功',
									type: 'success'
								});
	                    	}else{
	                    		_this.$message({
									message: data.msg,
									type: 'error'
								});
	                    	}
	                    }
	                });
				});
				this.ruleIsUpgradeVisible = false
				this.upgradebtn = true
			},
			clerbtn(){	
				if(this.commissionLine === 5){
					this.ruleIsUpgradeVisible_5 = false
				}else{
					this.ruleIsUpgradeVisible = false
				}
					this.ruleIsUpgrade = false
			},
			// 显示新增店铺身份页面
			addIDCard() {
				this.addFormVisible = true
			},
			//新增
			addSubmit: function () {
				const _this = this
				const params = {
					storeId:state.storeId,
					level:this.ruleAll.length+1,
					name:this.orderDetails.name,
					price:this.orderDetails.price,
					icon:this.orderDetails.icon
				}
				// console.log(params)
				this.$confirm('确认提交吗？', '提示', {}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/shopRole/addOne",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	// const info = data.data.shopRoles
	                    	console.log(data)
	                    	// _this.identity= info

	                    	_this.$message({
								message: '提交成功',
								type: 'success'
							});
							_this.addFormVisible = false
							_this.getlist()
	                    }
	                });
				});
			},
			//新增分佣数据
			addruleTable() {
				this.ruleIdVisible = true 
			},
			upruleId() {
				console.log(this.ruleAll)
				console.log(this.ruleIdFrom)
				const _this = this
				const _arry1 = this.arry1
				const _arry2 = this.arry2
				const _arry3 = this.arry3
				const _arry4 = this.arry4
				const _arry5 = this.arry5
				let params = {
					distId:this.distId,
					priceProps:[]
				}
				for(var i = 0;i<this.ruleAll.length;i++){
					let objs = {
						enjoyRoleId:this.ruleAll[i].id,
						priceData:[]
					}
					const obj = {}
					if(i === 0){
						obj.value = this.ruleIdFrom.name0
						obj.id = this.arry1.length+1
						_arry1.push(obj)
						objs.priceData = JSON.stringify(this.arry1)
					}else if(i === 1){
						obj.value = this.ruleIdFrom.name1
						obj.id = this.arry2.length+1
						_arry2.push(obj)
						objs.priceData = JSON.stringify(this.arry2)
					}else if(i === 2){
						obj.value = this.ruleIdFrom.name2
						obj.id = this.arry3.length+1
						_arry3.push(obj)
						objs.priceData = JSON.stringify(this.arry3)
					}else if(i === 3){
						obj.value = this.ruleIdFrom.name3
						obj.id = this.arry4.length+1
						_arry4.push(obj)
						objs.priceData = JSON.stringify(this.arry4)
					}else if(i === 4){
						obj.value = this.ruleIdFrom.name4
						obj.id = this.arry5.length+1
						_arry5.push(obj)
						objs.priceData = JSON.stringify(this.arry5)
					}
					params.priceProps.push(objs)
				}
				// params.priceProps[0].priceData = JSON.stringify(params.priceProps[0].priceData) 192.168.0.106
				console.log(params)
				// this.$confirm('确认提交吗？', '提示', {}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/priceProp/insertOrUpdateProp",
	                    // url:"http://192.168.0.106:8080/api/priceProp/insertOrUpdateProp",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	// const info = data.data.shopRoles
	                    	console.log(data)
	                    	// _this.identity= info

	                    	_this.$message({
								message: '提交成功',
								type: 'success'
							});
							_this.ruleIdVisible = false
							_this.click()
	                    }
	                });
				// });
			},
			//显示编辑界面
			seeBtn: function (val,row,index) {
				this.eidt_size = val-1
				console.log(val)
				console.log(row)
				console.log(this.arryuser)
				this.isindex = index
				console.log(this.isindex)
				this.edit_arryinit = row
				this.initname = this.arryuser[val-1].name
				console.log(this.initname)
				this.editFormVisible = true;
				this.editValue = row[val-1].value
			},
			//修改
			editSubmit: function () {
				console.log(this.editValue)
				console.log(this.eidt_size)
				console.log(this.edit_arryinit)
				const _this = this
				let params = {
					distId:this.distId,
					priceProps:[{
						// enjoyRoleId:24,
						enjoyRoleId:this.ruleAll[this.isindex].id,
						priceData:[]
					}]
				}
				this.edit_arryinit[this.eidt_size].value = this.editValue
				params.priceProps[0].priceData = JSON.stringify(this.edit_arryinit)
				console.log(params)
				this.$confirm('确认修改吗？', '提示', {}).then(() => {
					$.ajax({
	                    type:'POST',
	                    dataType:'json',
	                    url:baseUrl+"/api/priceProp/insertOrUpdateProp",
	                    data:JSON.stringify(params),
	                    contentType:'application/json;charset=utf-8',
	                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
	                    success:function(data){
	                    	console.log(data)
	                    	_this.$message({
								message: '修改成功',
								type: 'success'
							});
							_this.editFormVisible = false
							_this.click()
	                    }
	                });
				});
			},
			getselectByTwoRuleId(){
				const _this = this
				const params = {
					roleId:this.roleId,
					invitedRoleId:this.invitedRoleId,
				}
				// console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectByTwoRuleId",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	// console.log(data)
                    }
                });
			},
			//分页
			handleCurrentChange(val) {
				this.page = val;
			},
			selectRuleDist() {
				const _this = this
				const params = {
					storeId:state.storeId
				}
				// console.log(params)  this.initArry 判断当前值 可以做存储  orderInformation 页面展示数据
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/shopRole/selectRuleDist",
                    // url:"http://192.168.0.106:8080/api/shopRole/selectRuleDist",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	const info = data.data
                    	if(info.length === 0){
                    		return
                    	}
                    	console.log(info)
                    	_this.totalArray = info
                    	console.log(data)
                    	console.log(_this.ruleAll)
                    	_this.roleId = info[0].roleId.toString()
                    	_this.invitedRoleId = info[0].invitedRoleId.toString()
                    	_this.distId = info[0].distId
                    	const arrys = info[0].priceProps
                    	console.log(arrys)
                    	for(var i = 0;i<arrys.length;i++){
                    		console.log(arrys[i].priceData)
                    	}
                		const arrynew = JSON.parse(arrys[0].priceData)
                		console.log(arrynew)
                    	_this.arry1 = arrynew
                    	for(var i = 0;i<_this.arry1.length;i++){
                    		const obj = {}
                    		if(i === 0){
                    			obj.name = '自己拿'
                    			_this.arryuser.push(obj)
                    		}else{
                    			obj.name = i+'级'
                    			_this.arryuser.push(obj)
                    		}
                    	}
                    }
                });
			},
			/////////////////////////////// 磁疗贴//////////////////////////////////////////////////////////////////////////
			//图片上传
            upload (event) {
                this.formData = new FormData()
                let file = event.target.files[0]
                // console.log(file)
                const self = this
                // const flag = this.flag
                if (file) {
                    console.log('存在file', file)
                    this.fileImg = file.name
                    // console.log(this.formData)
                    this.formData.append('file', file);
                    console.log(this.formData);
                    this.getImgUrl()
                } else {
                    this.fileImg = ''
                    console.log('不存在file', file)
                    this.formData = new FormData()
                }
            },
            getImgUrl(){
                const _this= this;
                _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                    progress(event) {
                    }
                })
                    .then(response => {
                    	console.log(response)
                        const info = JSON.parse(response.bodyText);
						
						if(_this.commissionLine === 5){
							_this.url_5 = info.data[0].baseUri+info.data[0].uri;
						}else{
							_this.url = info.data[0].baseUri+info.data[0].uri;
						}
						// _this.getUrl()
                    }, error => _this.$emit('complete', 500, error.message))
			},
			upload1 (event) {
                this.formData = new FormData()
                let file = event.target.files[0]
                // console.log(file)
                const self = this
                // const flag = this.flag
                if (file) {
                    console.log('存在file', file)
                    this.fileImg = file.name
                    // console.log(this.formData)
                    this.formData.append('file', file);
                    console.log(this.formData);
                    this.getImgUrl1()
                } else {
                    this.fileImg = ''
                    console.log('不存在file', file)
                    this.formData = new FormData()
                }
            },
            getImgUrl1(){
                const _this= this;
                _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                    progress(event) {
                    }
                })
                    .then(response => {
                    	console.log(response)
                        const info = JSON.parse(response.bodyText);
						
						_this.url_51 = info.data[0].baseUri+info.data[0].uri;
						
						// _this.getUrl()
                    }, error => _this.$emit('complete', 500, error.message))
			},
			getcltList(){
				const _this = this
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+'/api/corePlaConfig/find/proportion/one',
                    data:JSON.stringify({}),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                        const info = data.data
                        if(info === null){
                        	_this.cltFyBtn = true
                        }
                        console.log(info)
                        _this.cltId = info.id
                        // 三级分销
                        _this.cltSan[0].fybi = info.one
                        _this.cltSan[1].fybi = info.two
                        _this.cltSan[2].fybi = info.three
                        // 平级奖
                        _this.cltPingji[0].fybi = info.peersOne
                        _this.cltPingji[1].fybi = info.peersTwo
                        _this.cltPingji[2].fybi = info.peersThree
                        // 与花说区域奖
                        _this.qyPingji[0].fybi = info.specialOne
                        _this.qyPingji[1].fybi = info.specialTwo
                        _this.qyPingji[2].fybi = info.specialThree

                        _this.cltJqfred = info.codePayScale

                        _this.cltuanduired = info.teamOne
                    }
                });
			},
			cltUpfy(){
				const _this = this
				const params = {
					one:_this.cltSan[0].fybi,
					two:_this.cltSan[1].fybi,
					three:_this.cltSan[2].fybi,
					teamOne:_this.cltuanduired,
					peersOne:_this.cltPingji[0].fybi,
					peersTwo:_this.cltPingji[1].fybi,
					peersThree:_this.cltPingji[2].fybi,
					codePayScale:_this.cltJqfred
				}
				let url = ''
				if(_this.cltFyBtn === true){// 添加
					url = '/api/corePlaConfig/add/store/proportion'
				}else{
					params.id = _this.cltId
					url = '/api/corePlaConfig/update/store/proportion'
				}
				if(this.commissionLine === 5){
					params.specialOne = this.qyPingji[0].fybi
					params.specialTwo = this.qyPingji[1].fybi
					params.specialThree = this.qyPingji[2].fybi
				}
				console.log(url)
				console.log(params)
				$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+url,
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
                    		_this.getcltList()
                    	}
                    }
                });
			}
		},
		mounted() {
			if(state.commissionLine === 3 || state.commissionLine === 5){
				this.allshow = false
				this.cltShow = true
				this.getlist();
				this.getcltList()
			}else{
				this.getlist();
				this.getselectRuleDist();
				this.selectRuleDist();
				this.getselectByTwoRuleId();
			}
			
		}
	}

</script>

<style>
	
	.Grade {
		list-style: none;
	}
	.Grade li{
		float: left;
		margin-right: 20px;
		width:200px;
		height: 50px;
		border: 1px solid #ddd;
		text-align: center;
		line-height: 50px;
	}
	.headerfx{
		list-style:none;
	}
	.headerfx li{
		float: left;
		margin-left: 150px
	}
	.leftul{
		list-style:none;
	}
	.leftul li{
		margin-bottom: 40px;
	}
	.roul_ul {
		list-style: none
	}
	.roul_ul li{
		height: 40px;
		line-height: 40px;
		cursor: pointer;
	}
	.marginleft {
		background-color: #eee;
		height: 40px;
		line-height: 40px;

	}
	.marginleft div:nth-child(1){
		width: 235px;
		height: 10px;
	}
</style>