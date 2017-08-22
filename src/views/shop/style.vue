<template>
	<section>
		<el-row :gutter="10" style="margin-top: 40px">
			<el-col :xs="15" :sm="15" :md="15" :lg="15">
				<el-col :xs="24" :sm="24" :md="24" :lg="24">
					<el-col :xs="24" :sm="24" :md="24" :lg="24">模块一</el-col>
					<el-col :xs="8" :sm="8" :md="8" :lg="8" v-for="item in selectOne">
							<img class="shopstyle" :src="item.picture">
						<div style="margin-left: 60px">
							<el-button type="text" size="small" @click="oneBtn(item)">使用</el-button>
							<!-- <el-button style="margin-left: 40px" type="text" size="small">预览</el-button> -->
						</div>
					</el-col>
				</el-col>
				<el-col :xs="24" :sm="24" :md="24" :lg="24">
					<el-col :xs="24" :sm="24" :md="24" :lg="24">模块二</el-col>
					<el-col :xs="8" :sm="8" :md="8" :lg="8" v-for="item in selectTwo">
						<img class="shopstyle" :src="item.picture">
						<div style="margin-left: 60px">
							<el-button type="text" size="small" @click="twoBtn(item)">使用</el-button>
							<!-- <el-button style="margin-left: 40px" type="text" size="small" @click="seeBtn(scope.$index, scope.row)">预览</el-button> -->
						</div>
					</el-col>
				</el-col>
			</el-col>
		    <el-col :xs="9" :sm="9" :md="9" :lg="9">
		    	<el-col :xs="24" :sm="24" :md="24" :lg="24">预览</el-col>
		    	<el-col :xs="24" :sm="24" :md="24" :lg="24">
		    		<div class="ylback">
		    			<el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-left: 18px;margin-top: 230px">
				    		<img style="width: 256px;height:150px" :src="oneImg">
				    		<img style="width: 256px;height:150px" :src="twoImg">
				    	</el-col>
				    	<!-- <el-col :xs="24" :sm="24" :md="24" :lg="24" style="margin-left: 18px">
				    		<img style="width: 256px;height:150px" :src="twoImg">
				    	</el-col> -->
		    		</div>
		    	</el-col>
		    </el-col>
		</el-row>
		<el-row :gutter="10" style="margin-top: 40px">
			<el-button type="primary"  @click="uploadBtn()">保存</el-button>
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
				oneImg:'',
				twoImg:'',
				selectOne:[],
				selectTwo:[],
				oneId:'',
				twoId:'',
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
				options: [{
		          value: '0',
		          label: '全部'
		        }, {
		          value: '1',
		          label: '订单编号'
		        }, {
		          value: '2',
		          label: '快递单号'
		        }, {
		          value: '3',
		          label: '用户名'
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
			getlist(){
				const _this = this
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+'/api/indexStyle/styleOne/list',
	                data:{},
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	_this.selectOne = data.data
	                  	console.log(_this.selectOne)
	                }
	            })
	            $.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+'/api/indexStyle/styleTwo/list',
	                data:{},
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	_this.selectTwo = data.data
	                  	console.log(_this.selectTwo)
	                }
	            })
			},
			oneBtn(row){
				this.oneId = row.id
				this.oneImg = row.picture
				console.log(row)
			},
			twoBtn(row){
				this.twoId = row.id
				this.twoImg = row.picture
				console.log(row)
			},
			uploadBtn(){
				console.log(this.oneId)
				console.log(this.twoId)
				const _this = this
				const params = {
					styleOneId:this.oneId,
					styleTwoId:this.twoId
				}
				$.ajax({
	                type:'POST',
	                dataType:'json',
	                url:baseUrl+'/api/indexShop/add',
	                data:JSON.stringify(params),
	                contentType:'application/json;charset=utf-8',
	                success:function(data){
	                  	console.log(data)
	                }
	            })
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getUsers();
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
.shopstyle{
	width: 200px;
	height: 200px;
	/*border: 1px solid #ddd;*/
	margin-left: 20px;
	margin-top: 20px;
	margin-bottom: 20px
}
.ylback{
	width: 300px;
	height: 605px;
	background-image: url(../../assets/ipone.png);
	background-size: cover;
	background-repeat: no-repeat;
}
</style>