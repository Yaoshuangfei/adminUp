<template>
	<section>
		<!--工具条-->
		<el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<el-button type="primary" v-on:click="addbanner">添加</el-button>
				</el-form-item>
			</el-form>
		</el-col>

		<!--列表-->
		<el-table :data="orderInformation" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
			<el-table-column type="index">
			</el-table-column>
			<el-table-column prop="picture" label="图片">
				<template scope="scope">
					<img class="img" :src="scope.row.picture" alt="">
				</template>
			</el-table-column>
			<el-table-column prop="status"  :formatter='formatterType' label="状态">
			</el-table-column>
			<el-table-column prop="poType"  :formatter='formatterpoType' label="位置">
			</el-table-column>
			<el-table-column prop="createTime" :formatter='formatterTime' label="创建时间">
			</el-table-column>
			<el-table-column label="操作">
				<template scope="scope">
					<el-button type="text" v-if='scope.row.status ===0' size="small" @click="handEnabled(scope.$index, scope.row)">启用</el-button>
					<el-button type="text" v-if='scope.row.status ===1' size="small" @click="handDisabled(scope.$index, scope.row)">禁用</el-button>
					<el-button type="text" v-if='scope.row.status ===0' size="small" @click="handmodify(scope.$index, scope.row)">修改</el-button>
					<el-button type="text" size="small" @click="handleEdit(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>

		<!--工具条-->
		<el-col :span="24" class="toolbar" style="background:#fff;">
			<el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total" style="float:right;">
			</el-pagination>
		</el-col>
		<!--新增banner-->
		<el-dialog title="添加banner" v-model="addbannerdiv" :close-on-click-modal="false">
			<el-form :model="uploadDetails" label-width="60px" :rules="editFormRules" ref="editForm">
				<el-form-item label="链接">
					<el-input v-model="uploadDetails.uploadImgs" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="banner">
					<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
					<button type="button" class="el-button el-button--primary el-button--small">
						<span>点击上传</span>
					</button>
					<button type="button" class="el-button el-button--primary el-button--small" id="btnClear" @click="clear">清空上传</button>
					<span style="display: block;font-size: 12px">{{ imageChange }}</span>
					<!--<button type="button" class="el-button el-button&#45;&#45;primary el-button&#45;&#45;small" id="btnClear" @click="clear">清空上传</button>-->
					<!--<span style="display: block;font-size: 12px">{{ imageChange }}</span>-->
				</el-form-item>
				<el-form-item label="图片位置">
					<el-select v-model="uploadDetails.poType" placeholder="请选择">
				    <el-option
				      v-for="item in options"
				      :key="item.value"
				      :label="item.label"
				      :value="item.value">
				    </el-option>
				  </el-select>
				</el-form-item>
				<el-form-item label="序号" >
					<el-input v-model="uploadDetails.List" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="描述">
					<el-input v-model="uploadDetails.information" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="submitUpload" :loading="editLoading">添加</el-button>
				<el-button type="primary" @click.native="addbannerdiv = false">取消</el-button>
			</div>
		</el-dialog>

		<!--修改banner-->
		<el-dialog title="修改banner" v-model="modifybannerdiv" :close-on-click-modal="false">
			<el-form :model="editForm" label-width="60px" :rules="editFormRules" ref="editForm">
				<el-form-item label="链接">
					<el-input v-model="editForm.link" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="banner">
					<!-- <el-input v-model="editForm.picture" type="text" auto-complete="off"></el-input> -->
					<img style='width: 100px' :src="editForm.picture">
					<input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="modifyload" id="fileInputs">
					<button type="button" class="el-button el-button--primary el-button--small">
						<span>点击上传</span>
					</button>
					<!-- <button type="button" class="el-button el-button--primary el-button--small" id="btnClears" @click="modifyclear">清空上传</button> -->
					<!-- <span style="display: block;font-size: 12px">{{ imageChange }}</span> -->
					<!--<button type="button" class="el-button el-button&#45;&#45;primary el-button&#45;&#45;small" id="btnClear" @click="clear">清空上传</button>-->
					<!--<span style="display: block;font-size: 12px">{{ imageChange }}</span>-->
				</el-form-item>
				<el-form-item label="序号" >
					<el-input v-model="editForm.orderSort" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="描述">
					<el-input v-model="editForm.desc" type="text" auto-complete="off"></el-input>
				</el-form-item>
				<el-col :span='24'></el-col>
			</el-form>
			<div slot="footer" class="dialog-footer" style="text-align: center;">
				<el-button type="primary" @click.native="modifyUpload" :loading="editLoading">修改</el-button>
				<el-button type="primary" @click.native="modifybannerdiv = false">取消</el-button>
			</div>
		</el-dialog>
		<!--编辑界面-->
		<el-dialog title="订单详情" v-model="editFormVisible" :close-on-click-modal="false" >
			<el-form :model="uploadDetails" label-width="160px" :rules="editFormRules" ref="editForm">
				<el-form-item label="订单号">
					<div>{{uploadDetails.uploadImgs }}</div>
					<!-- <el-input v-model="addForm.name" type="text" auto-complete="off"></el-input> -->
				</el-form-item>
				<el-form-item label="用户名">
					<div>{{uploadDetails.List }}</div>
				</el-form-item>
				<el-form-item label="实付金额">
					<div>{{uploadDetails.amountPaid }}</div>
				</el-form-item>
				<el-form-item label="订单总价">
					<div>{{uploadDetails.orderTotal }}</div>
				</el-form-item>
				<el-form-item label="订单状态">
					<div>{{uploadDetails.orderStatus }}</div>
				</el-form-item>
				<el-form-item label="支付方式">
					<div>{{uploadDetails.paymentMethod }}</div>
				</el-form-item>
				<el-form-item label="创建时间">
					<div>{{uploadDetails.creationTime}}</div>
				</el-form-item>
				<el-form-item label="发货时间">
					<div>{{uploadDetails.deliveryTime}}</div>
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
				radio: '0',
				checked: true,
				value:'',
				value1:'',
				value2:'',
				url:'',
				urls:'',
				options: [{
		          value: '1',
		          label: '首页'
		        }, {
		          value: '2',
		          label: '店铺内'
		        }],
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
				editForm:{},

				addbannerdiv: false,//新增界面是否显示
                modifybannerdiv: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				uploadDetails: {
					poType:'',
				},
				//新增界面数据
                modifyDetails: {
				},
				orderInformation:[{
					uploadImg :'145877458784524c',
					courierNumber :'145877458784524c22',
					userName:'吸引力量',
					amountPaid :'300',
					orderTotal :'900',
					orderStatus :'待付款',
					paymentMethod :'微信支付',
					creationTime:'2017-09-08 17:09',
					deliveryTime:'2017-09-08 17:09',
					commodityName:'雨花说'
				}],
                formData: new FormData(),
                fileImg: ''
			}
		},
        computed: {
            // 实时更新上传图片的名字，仅读取，值只须为函数
            imageChange: function () {
                return this.fileImg
            }
        },
		methods: {
//		    清空上传
            clear(){
                let btn = document.getElementById("btnClear");
                let files = document.getElementById("fileInput");
                this.fileImg = '';
                // for IE, Opera, Safari, Chrome
                if (files !== null && files.value) {
                    //     files.outerHTML = files.outerHTML;
                    // } else { // FF(包括3.5)
                    files.value = "";
                    this.formData = new FormData()
                }
            },
		    //图片上传
            upload (event) {
                this.formData = new FormData()
                let file = event.target.files[0]
                // console.log(file)
                const self = this
                // const flag = this.flag
                if (file) {
                    console.log('存在file', file)
                    console.log(file.size)
                    this.fileImg = file.name
                    // console.log(this.formData)
                    this.formData.append('file', file);
                    console.log(this.formData);
                } else {
                    this.fileImg = ''
                    console.log('不存在file', file)
                    this.formData = new FormData()
                }
            },
            //添加
            submitUpload(){
                this.$confirm('确认添加吗？', '提示', {}).then(() => {
                    const _this= this;
                    _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                        progress(event) {
                        }
                    })
                        .then(response => {
                            const info = JSON.parse(response.bodyText);
                            // const info = response.body
							_this.url = info.data[0].baseUri+info.data[0].uri;
                            _this.UploadImg();
                        }, error => _this.$emit('complete', 500, error.message))
                });
            },
//			图片上传ajax
			UploadImg(){
                const _this= this;
                const params = {
                    link:this.uploadDetails.uploadImgs,
                    picture:this.url,
                    orderSort:this.uploadDetails.List,
                    poType:this.uploadDetails.poType,
                    desc:this.uploadDetails.information,
                };
                console.log(params)
                var url = baseUrl+"/api/indexAdvert/add";
                var data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                        if(!data.success){
                            alert(data.msg)
						}else{
                            _this.addbannerdiv = false;
                            _this.getlist();
						}
                    }
                });


			},
			getlist(){
				const _this = this;
                _this.orderInformation = [];
				const params = {
                    poType:''
				};
                var url = baseUrl+"/api/indexAdvert/find/page?pageNum="+_this.page+"&pageSize=10";
                var data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                        if(!data.success){
                            alert(data.msg)
                        }else{
                        	console.log(data)
                            var _length 	= data.data.list;
                            _this.total 	= data.data.total;
                            for (var i = 0; i < _length.length; i++) {
                                _this.orderInformation.push(_length[i]);
                            }
                        }
                    }
                });
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlist();
			},
			//删除
            handleEdit: function (index, row) {
				this.$confirm('确认删除该记录吗?', '提示', {
					type: 'warning'
				}).then(() => {
                    const _this= this;
                    const params = {
                        id:row.id
                    };
                    var url = baseUrl+"/api/indexAdvert/delete/one";
                    var data =JSON.stringify(params);
                    $.ajax({
                        type:'POST',
                        dataType:'json',
                        url:url,
                        data:data,
                        contentType:'application/json;charset=utf-8',
                        error: function (XMLHttpRequest, textStatus, errorThrown) {},
                        success:function(data){
                            if(!data.success){
                                alert(data.msg)
                            }else{
                                _this.$message({
                                    message: '删除成功',
                                    type: 'success'
                                });
                                _this.getlist();
                            }
                        }
                    });
//					this.listLoading = true;
					//NProgress.start();
//					let para = { id: row.id };
//					removeUser(para).then((res) => {
//						this.listLoading = false;
//						//NProgress.done();
//						this.$message({
//							message: '删除成功',
//							type: 'success'
//						});
//						this.getUsers();
//					});
				}).catch(() => {

				});
			},
            //启用
            handEnabled: function (index, row) {
                this.$confirm('确认启用该广告位吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    const _this= this;
                    const params = {
                        id:row.id
                    };
                    var url = baseUrl+"/api/indexAdvert/enable";
                    var data =JSON.stringify(params);
                    $.ajax({
                        type:'POST',
                        dataType:'json',
                        url:url,
                        data:data,
                        contentType:'application/json;charset=utf-8',
                        error: function (XMLHttpRequest, textStatus, errorThrown) {},
                        success:function(data){
                            if(!data.success){
                                alert(data.msg)
                            }else{
                                _this.$message({
                                    message: '启用成功',
                                    type: 'success'
                                });
                                _this.getlist();
                            }
                        }
                    });
//					this.listLoading = true;
                    //NProgress.start();
//					let para = { id: row.id };
//					removeUser(para).then((res) => {
//						this.listLoading = false;
//						//NProgress.done();
//						this.$message({
//							message: '删除成功',
//							type: 'success'
//						});
//						this.getUsers();
//					});
                }).catch(() => {

                });
            },
            //禁用
            handDisabled: function (index, row) {
                this.$confirm('确认禁用该广告位吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    const _this= this;
                    const params = {
                        id:row.id
                    };
                    var url = baseUrl+"/api/indexAdvert/disable";
                    var data =JSON.stringify(params);
                    $.ajax({
                        type:'POST',
                        dataType:'json',
                        url:url,
                        data:data,
                        contentType:'application/json;charset=utf-8',
                        error: function (XMLHttpRequest, textStatus, errorThrown) {},
                        success:function(data){
                            if(!data.success){
                                alert(data.msg)
                            }else{
                                _this.$message({
                                    message: '禁用成功',
                                    type: 'success'
                                });
                                _this.getlist();
                            }
                        }
                    });
//					this.listLoading = true;
                    //NProgress.start();
//					let para = { id: row.id };
//					removeUser(para).then((res) => {
//						this.listLoading = false;
//						//NProgress.done();
//						this.$message({
//							message: '删除成功',
//							type: 'success'
//						});
//						this.getUsers();
//					});
                }).catch(() => {

                });
            },

//			修改
            handmodify: function (index, row) {
			    this.modifybannerdiv = true;
			    this.editForm = row;
            },


//            清空上传
            modifyclear(){
        let btn = document.getElementById("btnClears");
        let files = document.getElementById("fileInputs");
        this.fileImg = '';
        // for IE, Opera, Safari, Chrome
        if (files !== null && files.value) {
            //     files.outerHTML = files.outerHTML;
            // } else { // FF(包括3.5)
            files.value = "";
            this.formData = new FormData()
        }
    },
    //图片上传后修改
            modifyload (event) {
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
        } else {
            this.fileImg = ''
            console.log('不存在file', file)
            this.formData = new FormData()
        }
    },
    //修改
            modifyUpload(){
        this.$confirm('确认修改吗？', '提示', {}).then(() => {
            const _this= this;
            _this.$http.post('http://121.43.178.109:8080/ser/api/attachment/upload', _this.formData, {
                progress(event) {
                }
            })
                .then(response => {
                    const info = JSON.parse(response.bodyText);
                    _this.urls = info.data[0].baseUri+info.data[0].uri;
                    _this.modifyUploads();
                }, error => _this.$emit('complete', 500, error.message))
        });
    },
    //			图片上传ajax
            modifyUploads(){
        const _this= this;
        const params = {
            id:this.editForm.id,
            link:this.editForm.link,
            picture:this.urls,
            orderSort:this.editForm.orderSort,
            desc:this.editForm.desc,
        };
        var url = baseUrl+"/api/indexAdvert/update";
        var data =JSON.stringify(params);
        $.ajax({
            type:'POST',
            dataType:'json',
            url:url,
            data:data,
            contentType:'application/json;charset=utf-8',
            error: function (XMLHttpRequest, textStatus, errorThrown) {},
            success:function(data){
                if(!data.success){
                    alert(data.msg)
                }else{
                    _this.modifybannerdiv = false;
                    _this.getlist();
                }
            }
        });


    },




			//显示编辑界面
			seeBtn: function (index, row) {
				this.editFormVisible = true;
				this.uploadDetails = Object.assign({}, row);
			},
			//显示添加banner页面
			addbanner: function (index, row) {
				this.addbannerdiv = true;
				// this.uploadDetails = Object.assign({}, row);
			},
			//编辑

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
			},
            formatterTime(row,column){
                let curTime = row.createTime;
                curTime = new Date(curTime).toLocaleString()
                return curTime
            },
            formatterType(row,column){
                let type = ''
                if(row.status === 1){
                	type = '启用'
                }else{
                	type = '禁用'
                }
                return type
            },
            formatterpoType(row,column) {
            	return row.poType === 1 ?'首页':'店铺内'
            }
		},
		mounted() {
			 this.getlist();
		}
	}
</script>

<style>
	.el-dialog--small {
    	width: 30%;
    	border-radius: 10px
	}
	.el-form-item__label{
		text-align: left;
	}
	.el-dialog__body{
		margin-left: 0 ! important ;
	}
	.img {
		width:100px;
		height:100px;
	}
</style>