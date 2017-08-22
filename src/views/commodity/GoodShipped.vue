<template>
	<section>
		<el-col :span="24" v-show="next">
			<el-col :span="24" class="Choice_div">
				<el-col :span="6" class="Choice">
					<el-tree :data="data" @node-click="handleNodeClick" :highlight-current="true"></el-tree>
				</el-col>
				<el-col :span="6" class="Choice">
					<el-tree :data="data1" @node-click="handleNodeClick1" :highlight-current="true"></el-tree>
				</el-col>
				<el-col :span="6" class="Choice">
					<el-tree :data="data2" @node-click="handleNodeClick2" :highlight-current="true"></el-tree>
				</el-col>
				<el-col :span="6" class="Choice">
					<el-tree :data="data3"  :highlight-current="true"></el-tree>
				</el-col>
			</el-col>
			<el-col :span="24" class="footer_choice">
				您当前选择的商品类别是：{{commodity}}
				<span  style="display: none;">{{id}}</span>
			</el-col>
			<el-col :span="4" :offset="10">
				<el-button type="primary" style="width: 250px;margin-top: 20px" :disabled="keynext"  v-on:click="NextStep">下一步</el-button>
			</el-col>
		</el-col>
		<!--商品基本信息-->
		<el-col :span="24" v-show="details">
			<el-col :span="24" class="Commodity_information">
				<el-col :span="24" class="head_text">商品基本信息</el-col>
				<el-col :span="24" class="head_text">
					<el-col :span="2" class="leftg_text">商品类目：</el-col>当前分类:     {{commodity}}
				</el-col>
				<el-col :span="24" class="head_text">
					<el-col :span="2" class="leftg_text">商品品牌：</el-col></el-col>
				<el-col :span="24" :offset="2" style="margin-top: 10px">宝贝属性 <span  style="color: #aaa"> 错误填写宝贝属性，可能会引起宝贝下架或搜索流量减少，影响您的正常销售，请确认准确填写！</span></el-col>
				<el-col :span="18" :offset="3" class="body_text">
					<el-col :span="19" :offset="2" style="margin-top: 10px">
						<el-form :model="orderDetails" label-width="80px" :rules="editFormRules" :inline="true" ref="editForm">
							<el-form-item :label="item.name" v-for="item in paramsData" style="margin-right:  200px;">
								<el-input type="text" v-model="item.value"></el-input>
							</el-form-item>
						<el-col :span='24'></el-col>
					</el-form>
					</el-col>
				</el-col>
				<el-col :span="24" class="shangp">
					<el-col :span="24" style=" border-bottom: 1px solid #ddd;">
						<el-col :span="2" style="border-right: 1px solid #ddd;height: 70px;text-align: right;line-height: 60px;">商品名称：</el-col>
						<el-col :span="12" style="margin-left: 10px">
							<el-col :span="12" style="margin-top: 10px"><el-input type="text" v-model="CommodityName"></el-input></el-col>
							<el-col :span="13"  style="margin-top: 5px;color: #aaa;">商品标题名称长度至少3个字符，最长50个汉字</el-col>
						</el-col>
					</el-col>
					<el-col :span="24" style=" border-bottom: 1px solid #ddd;">
						<el-col :span="2" style="border-right: 1px solid #ddd;height: 95px;text-align: right;line-height: 80px;">商品货号：</el-col>
						<el-col :span="12" style="margin-left: 10px">
							<el-col :span="12" style="margin-top: 10px"><el-input type="text" v-model="productCode"></el-input></el-col>
							<el-col :span="13"  style="margin-top: 5px;color: #aaa;">商品货号是指卖家个人管理商品的编号，买家不可见</el-col>
							<el-col :span="13"  style="margin-top: 5px;color: #aaa;">做多可输入20个字符，支持输入中文、字母、数字、_、/、和小数点</el-col>
						</el-col>
					</el-col>
					<el-col :span="24" style=" border-bottom: 1px solid #ddd;">
						<el-col :span="2" style="text-align: right;line-height: 80px;">商品图片：</el-col>
						<el-col :span="12" style="margin-left: -1px;padding-left:10px;border-left: 1px solid #ddd;height: 182px">
							<el-col :span="24" style="margin-top: 10px">
								
							<!-- <button type="button" class="el-button el-button--primary el-button--small" id="btnClear" @click="clear">清空上传</button> -->
							<!-- <span style="display: block;font-size: 12px;margin-bottom: 10px">{{ imageChange }}</span> -->
								<el-col :span="4" v-for="item in CommodityPictures">
									<img style="position: relative;left: 80px;top:30px;" @click="deldetImg(item)" src="../../assets/delet.png">
									<img style="width:100px;border:1px solid #aaa" :src="item">
								</el-col>
								<el-col v-if="CommodityPictures.length < 3" :span="4" style="margin-top: 10px">
									<input type="file" style="position: relative;opacity:0;width:70px;height:40px;margin-right:10px;"  @change="upload" id="fileInput">
									<button type="button" class="el-button el-button--primary el-button--small" style="margin-left: -83px">
										<span>点击上传</span>
									</button>
								</el-col>
							</el-col>
							<el-col :span="19"  style="margin-top: 5px;color: #aaa;">最多上传三张图片</el-col>
						</el-col>
					</el-col>
					<el-col :span="24">
						<el-col :span="24" style="height: 40px;line-height: 30px;">商品交易信息</el-col>
						<el-col :span="24" style="height: 40px;line-height: 30px;border: 1px solid #ddd;">
							<el-col :span="2"  style="height: 30px;line-height: 30px;border-right: 1px solid #ddd;text-align: right">计量单位：</el-col>
							<el-col :span="2"  style=""><el-input type="text" v-model="MeasurementUnit"></el-input></el-col>
						</el-col>
						<el-col :span="24" style="height: 100px;line-height: 30px;border: 1px solid #ddd;" v-for="item in Specifications">
							<el-col :span="2" style="height: 100px;line-height: 30px;border-right: 1px solid #ddd;text-align: right">{{item.name}}</el-col>
							<el-col :span="20"  style="margin-left: 50px">
								<el-checkbox-group v-model="item.checrArry" @change="handleCheckedCitiesChange(item)">
								    <el-col :span="5" v-for="city in item.values">
								    	<el-checkbox :label="city" :key="city">{{city}}</el-checkbox>
								    </el-col>
								</el-checkbox-group>
							</el-col>
						</el-col>
						<el-col :span="24" style="line-height: 30px;border: 1px solid #ddd;">
							<el-col :span="2"  style="height: 150px;line-height: 30px;border-right: 1px solid #ddd;text-align: right">规格报价：</el-col>
							<el-col :span="20"  style="">
								 <el-table :data="tableData" style="width: 100%">
							      <el-table-column prop="specData" label="规格" width="180">
							      	<template scope="scope">
							      		{{scope.row.specData}}
									</template>
							      </el-table-column>
							      <!-- <el-table-column prop="goodsId" label="商品id" width="190">
								      <template scope="scope">
								      		<el-input v-model="scope.row.goodsId" type="text"></el-input>
										</template>
							      </el-table-column> -->
							      <el-table-column prop="costPrice" label="成本价(元)" width="180">
							      	<template scope="scope">
							      		<el-input v-model="scope.row.costPrice" type="text"></el-input>
									</template>
							      </el-table-column>
							      <el-table-column prop="specPrice" label="建议零售价(元)" width="180">
							      	<template scope="scope">
							      		<el-input v-model="scope.row.specPrice" type="text"></el-input>
									</template>
							      </el-table-column>
							      <el-table-column prop="storage" label="商品库存" width="180">
							      	<template scope="scope">
							      		<el-input v-model="scope.row.storage" type="text"></el-input>
									</template>
							      </el-table-column>
							      <el-table-column prop="deductibleImazamox" label="金豆抵扣" width="200">
							      	<template scope="scope">
							      		<el-input v-model="scope.row.deductibleImazamox" type="text"></el-input>
									</template>
							      </el-table-column>
							      <el-table-column prop="specNo" label="规格商家编码" width="200">
							      	<template scope="scope">
							      		<el-input v-model="scope.row.specNo" type="text"></el-input>
									</template>
							      </el-table-column>
							    </el-table>
							</el-col>
						</el-col>
						<el-col :span="24" style="height: 100px;line-height: 30px;border: 1px solid #ddd;">
							<el-col :span="2"  style="height: 100px;line-height: 30px;border-right: 1px solid #ddd;text-align: center">定价模式 </el-col>
							<el-col :span="20">
								<el-col :span="20">
									 <el-radio class="radio" v-model="PricingModel" label="1">模式一</el-radio>
 									 <el-radio class="radio" v-model="PricingModel" label="2">模式二</el-radio>
								</el-col>
								<el-col :span="20">
									 模式一：分销金额 = （定价-成本（人民币））*吸引力平台分佣方式
								</el-col>
								<el-col :span="20">
									 模式二：分销金额 = （定价-成本（金豆））
								</el-col>
							</el-col>
						</el-col>
						<el-col :span="24" style=" border-bottom: 1px solid #ddd;">
							<el-col :span="2" style="border-right: 1px solid #ddd;height: 80px;text-align: left;line-height: 80px;">建议零售价(原价)：</el-col>
							<el-col :span="12" style="margin-left: 10px">
								<el-col :span="12" style="margin-top: 10px"><el-input type="text" v-model="SuggestedRetailRrice"></el-input></el-col>
								<el-col :span="13"  style="margin-top: 5px;color: #aaa;">商品价格必须是0.01~10000000的、之间的数字</el-col>
							</el-col>
						</el-col>
						<el-col :span="24" style=" border-bottom: 1px solid #ddd;">
							<el-col :span="2" style="border-right: 1px solid #ddd;height: 80px;text-align: left;line-height: 80px;">基础服务：</el-col>
							<el-col :span="12" style="margin-left: 10px;margin-top: 20px">
								<el-checkbox-group v-model="checkedCities" :min="0" :max="6" @change="clicka">
								    <el-checkbox v-for="city in cities" :label="city" :key="city">{{city}}</el-checkbox>
								</el-checkbox-group>
							</el-col>
						</el-col>
						<el-col :span="24" style="height: 40px;line-height: 30px;border-bottom: 1px solid #ddd;">商品详情描述</el-col>
						<el-col :span="24" style=" border-bottom: 1px solid #ddd;height: 580px">
							<el-col :span="2" style="text-align: right;line-height: 80px;">商品描述：</el-col>
							<el-col :span="12" style="height: 60px;border-left: 1px solid #ddd;padding-left: 10px">
								<el-col :span="23"  style="margin-top: 5px;color: #aaa;">商品价格必须是0.01~10000000的、之间的数字
								<!-- <el-input type="textarea" v-model="DescriptionGoods" :rows="10"></el-input> -->
								</el-col>
							</el-col>
						</el-col>
						<el-col :span="24" style="height: 40px;line-height: 30px;border-bottom: 1px solid #ddd;">商品物流信息</el-col>
						<el-col :span="24">
							物流设置 为了提升消费者购物体验，要求客户全网商品设置运费模板。
							<el-col :span="24" style="min-height: 100px;margin-top: 40px">
								<el-col :span="2" style="line-height: 40px">运费模板：</el-col>
								<el-col :span="12">
									<el-select v-model="value" placeholder="请选择">
										<el-option v-for="item in options" :label="item.label" :value="item.value" :key="item.value"></el-option>
									</el-select>
								</el-col>
							</el-col>
							<el-col :span="24">
								<el-col :span="1" style="line-height: 40px">重量：</el-col>
								<el-col :span="3">
									<el-input  v-model="weight" ></el-input>
								</el-col>
								<el-col style="margin-top:10px" :span="3">公斤</el-col>
								<!-- <span style="margin-top: 20px">公斤</span> -->
							</el-col>
						</el-col>
					</el-col>
				</el-col>
			<!-- <el-col :span='24' >11111111111111</el-col>	 -->
			<el-col :span='24' style="margin-top: 904px">
				<div id = 'editor-trigger' style="height: 480px"></div>
			</el-col>
			</el-col>
			<el-button type="primary" @click="release" style="margin: 20px;">发布</el-button>
			
		</el-col>
	</section>
</template>

<script>
	import { state } from '../../vuex/state'
	import util from '../../common/js/util'
	import {baseUrl , editUser, addUser } from '../../api/api';
	import wangEditor from 'wangEditor'

	export default {
		data() {
			return {
				listBiaoq:[],
				onbiaoqList:[],
				cities:[],
				checkedCities:[],
				test_html:'',
        		value: '',
				keynext:true,
				selectListNameId:'',
				url:'',
                sels:[],
                paramsData:[],
                itemData:[],
                Specifications:[],
                CommodityName:'',// 商品名称
                CommodityPictures:[],// 商品图片
                productCode:'',//商品货号
                MeasurementUnit:'',// 计量单位
                PricingModel:'',// 定价模式
                SuggestedRetailRrice:'',// 建议零售价
                DescriptionGoods:'',// 商品描述
                weight:'',// 重量
				tableData: [],
				PricingModel: '1',
                checkList:[],
				commodity:'',
				id:'',
				next: true,
				details: false,
				data: [],
				data1:[],
				data2:[],
				data3:[],
				options: [],
				filters: {
					name: '',
					status:'',
					type:''
				},
				users: [],
				total: 100,
				page: 1,
				listLoading: false,
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
				addForm:{
					name:''
				},
				addFormVisible: false,//新增界面是否显示
				addLoading: false,
				//新增界面数据
				orderDetails: {
				},
				formData: new FormData(),
                fileImg: '',
                Specifications1:[],
                Specifications2:[],
                Specifications3:[],

                specificatwo:[],
                specificatwo1:[]
			}
		},
		computed: {
            // 实时更新上传图片的名字，仅读取，值只须为函数
            imageChange: function () {
                return this.fileImg
            }
        },
        components: {
            wangEditor
        },
		methods: {
			// /删除图片
			deldetImg(src){
				this.CommodityPictures.splice(jQuery.inArray(src, this.CommodityPictures),1)
			},
			handleCheckedCitiesChange(row) {
				const arry = []
				// console.log(row.name)
				// console.log(row.checrArry)
				// console.log(this.Specifications)
				for(var i = 0;i<this.Specifications.length;i++){
					if(this.Specifications[i].name === row.name){
						if(i === 0){
							this.Specifications1 = []
							for(var x = 0;x<row.checrArry.length;x++){
								const objs = {}
								objs.key = row.name
								objs.value = row.checrArry[x]
								this.Specifications1.push(objs)
							}
							
						}else if(i ===1){
							this.Specifications2 = []
							for(var x = 0;x<row.checrArry.length;x++){
								const objs = {}
								objs.key = row.name
								objs.value = row.checrArry[x]
								this.Specifications2.push(objs)
							}
							
						}else if(i === 2){
							this.Specifications3 = row.checrArry
						}
						
					}
					
				}
				if(this.Specifications.length === 1){
					console.log(this.Specifications1)
					this.tableData = []
					for(var i = 0;i<this.Specifications1.length;i++){
						const obj = {}
						obj.specData = this.Specifications1[i].value
						// obj.goodsId = ''
						obj.specPrice = ''
						obj.costPrice = ''
						obj.storage = '',
						obj.deductibleImazamox = '',
						obj.specNo = '',
						obj.specPicture = this.url,
						obj.id = ''
						this.tableData.push(obj)
					}
				}else if(this.Specifications.length === 2){//specificatwo specificatwo1
					this.specificatwo1 = []
					this.specificatwo = []
					for(var i = 0;i<this.Specifications1.length;i++){
						for(var x = 0;x<this.Specifications2.length;x++){
							this.specificatwo.push(this.Specifications1[i].value+'/'+this.Specifications2[x].value)
							
							const arry = []
							arry.push(this.Specifications1[i],this.Specifications2[x])
							this.specificatwo1.push(arry)
						}
					}
					this.tableData = []
					for(var i = 0;i<this.specificatwo.length;i++){
						const obj = {}
						obj.specData = this.specificatwo[i]
						// obj.goodsId = ''
						obj.specPrice = ''
						obj.costPrice = ''
						obj.storage = '',
						obj.deductibleImazamox = '',
						obj.specNo = '',
						obj.specPicture = this.url,
						obj.id = ''
						this.tableData.push(obj)
					}
						console.log(this.specificatwo)
						console.log(this.specificatwo1)
					// console.log(this.Specifications1)
					// console.log(this.Specifications2)
				}
				
			},
			clickIndex(index){
				console.log(index)
				console.log(index)
			},
			//		    清空上传
            clear(){
                let btn = document.getElementById("btnClear");
                let files = document.getElementById("fileInput");
                this.fileImg = '';
                // for IE, Opera, Safari, Chrome
                if (files !== null && files.value) {
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
                    this.fileImg = file.name
                    // console.log(this.formData)
                    this.formData.append('file', file);
                    console.log(this.formData);
                    this.submitUpload()
                } else {
                    this.fileImg = ''
                    console.log('不存在file', file)
                    this.formData = new FormData()
                }
            },
            submitUpload(){
                // this.$confirm('确认修改吗？', '提示', {}).then(() => {
                    const _this= this;
                    _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                        progress(event) {
                        }
                    })
                        .then(response => {
                            const info = JSON.parse(response.bodyText);
                            console.log(info)
                            // const info = response.body
							_this.url = info.data[0].baseUri+info.data[0].uri;
							console.log(_this.url)
							if(_this.CommodityPictures.length <3){
								_this.CommodityPictures.push(_this.url)
							}
                        }, error => _this.$emit('complete', 500, error.message))
                // });
            },
	     	NextStep() {
	     		this.$confirm('类目一旦选定，编辑商品的时候类目无法更改，请确认清楚再进入编辑?', '提示', {
					type: 'warning'
				}).then(() => {
                    const _this = this;
                    const url   = baseUrl+"/api/goodsClass/selectOne";
                    const params = {
                        id:this.sels[0].id
                    };
                    const data =JSON.stringify(params);
                    $.ajax({
                        type:'POST',
                        dataType:'json',
                        url:url,
                        data:data,
                        contentType:'application/json;charset=utf-8',
                        error: function (XMLHttpRequest, textStatus, errorThrown) {},
                        success:function(data){
                        	console.log(data)
                        	const info = data.data
                        	console.log(info.itemData)
                        	if(info.paramData !== ''){
                        		_this.paramsData = eval('(' + info.paramData + ')')
                        	}else if(info.itemData !== ''){
                        		_this.itemData = eval('(' + info.itemData + ')')
                        	}
                        	_this.itemData = eval('(' + info.itemData + ')')
                        	console.log(_this.itemData)
                        	_this.Specifications = []
                        	for(var i = 0; i<_this.itemData.length;i++){
                        		const obj = {}
                        		obj.name = _this.itemData[i].name
                        		obj.checrArry = []
                        		let jsontry = _this.itemData[i].values
                        		obj.values = []
                        		jsontry=jsontry.split(',');
                        		for (var x = 0; x < jsontry.length; x++) {
                        			obj.values.push(jsontry[x])
                        		}
                        		_this.Specifications.push(obj)
                        		console.log(_this.Specifications)
                        	}
        					_this.next = false;
							_this.details = true;
                        }
                    });
				}).catch(() => {

				});
	     	},
	     	handleNodeClick(data) {
                this.sels=[];
	        	this.commodity =  data.label;
	        	this.id =  data.id;
	        	this.selectListNameId = data.id
	        	this.sels.push(data);
	        	// this.sels.push(this.id);
	        	console.log(this.sels)
	        	this.selectListName1()
	     	},
	     	handleNodeClick1(data) {
                this.sels=[];
	        	this.commodity =  data.label;
	        	this.id =  data.id;
	        	this.selectListNameId = data.id
	        	this.sels.push(data);
	        	// this.sels.push(this.id);
	        	console.log(this.sels)
	        	this.selectListName2()
	     	},
	     	handleNodeClick2(data) {
                this.sels=[];
	        	this.commodity =  data.label;
	        	this.id =  data.id;
	        	this.selectListNameId = data.id
	        	this.sels.push(data);
	        	// this.sels.push(this.id);
	        	console.log(this.sels)
	        	this.selectListName3()
	     	},
			//获取商品分类
            selectListName(){
                const _this = this;
                const url   = baseUrl+"/api/goodsClass/selectListName";
                _this.data = []
                const params = {
                    pid:''
                };
                console.log(params)
                const data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	
                        if(!data.success){
                            alert(data.msg)
                        }else{
                            const _length = data.data;
                            for (const i in _length){
                                const obj = {value:i,label:_length[i].name,id:_length[i].id};
                                _this.data.push(obj);
                            }
                        }
                    }
                });
			},
			//获取商品分类2
            selectListName1(){
                const _this = this;
                _this.data1 = []
                const url   = baseUrl+"/api/goodsClass/selectListName";
                const params = {
                    pid:this.selectListNameId
                };
                console.log(params)
                const data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	if(data.data.length === 0){
                    		_this.keynext = false
                    	}else{
                    		_this.keynext = true
                    	}
                        if(!data.success){
                            alert(data.msg)
                        }else{
                            const _length = data.data;
                            for (const i in _length){
                                const obj = {value:i,label:_length[i].name,id:_length[i].id};
                                _this.data1.push(obj);
                            }
                        }
                    }
                });
			},
			selectListName2(){
                const _this = this;
                _this.data2 = []
                const url   = baseUrl+"/api/goodsClass/selectListName";
                const params = {
                    pid:this.selectListNameId
                };
                console.log(params)
                const data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	if(data.data.length === 0){
                    		_this.keynext = false
                    	}else{
                    		_this.keynext = true
                    	}
                        if(!data.success){
                            alert(data.msg)
                        }else{
                            const _length = data.data;
                            for (const i in _length){
                                const obj = {value:i,label:_length[i].name,id:_length[i].id};
                                _this.data2.push(obj);
                            }
                        }
                    }
                });
			},
			selectListName3(){
                const _this = this;
                _this.data3 = []
                const url   = baseUrl+"/api/goodsClass/selectListName";
                const params = {
                    pid:this.selectListNameId
                };
                console.log(params)
                const data =JSON.stringify(params);
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:data,
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	if(data.data.length === 0){
                    		_this.keynext = false
                    	}else{
                    		_this.keynext = true
                    	}
                        if(!data.success){
                            alert(data.msg)
                        }else{
                            const _length = data.data;
                            for (const i in _length){
                                const obj = {value:i,label:_length[i].name,id:_length[i].id};
                                _this.data3.push(obj);
                            }
                        }
                    }
                });
			},
			// 发布商品
			release() {
				const _this = this
                const params = {
                    name:this.CommodityName,
                    goodsDesc:this.DescriptionGoods,
                    veiw:_this.test_html,
                    price:this.SuggestedRetailRrice,
                    isVirtual:'0',
                    carouselPicture:this.CommodityPictures.toString(),
                    saleState:'1',
                    goodsData:[],
                    catId:this.sels[0].id,
                    storeId:state.storeId,
                    ftId:this.value,
                    pricingModel:this.PricingModel,
                    unit:this.MeasurementUnit,
                    weight:this.weight,
                    goodsNo:this.productCode,
                    goodsSpecs:[],
                    goodsServiceStr:''
                };
                const bqarry = []
                console.log(this.onbiaoqList)
                console.log(this.listBiaoq)
                for (var i = 0; i < this.onbiaoqList.length; i++) {
                	for (var x = 0; x < this.listBiaoq.length; x++) {
                		if(this.onbiaoqList[i] === this.listBiaoq[x].name){
                			bqarry.push(this.listBiaoq[x].id)
                		}
                	}
                }
               
                // console.log(bqarry.toString())
                params.goodsServiceStr = bqarry.toString()
                //商品图片
                // this.CommodityPictures = this.CommodityPictures
                // params.carouselPicture = this.CommodityPictures.toString()
                console.log(this.paramsData)
                //宝贝属性
                const arrysp = []
                for(var i = 0;i<this.paramsData.length;i++){
                	const obj = {}
                	obj.key = this.paramsData[i].name
                	obj.value = this.paramsData[i].value
                	arrysp.push(obj)
                }
                params.goodsData = arrysp
                params.goodsData = JSON.stringify(params.goodsData)
                
                 // console.log(params)
                 params.goodsSpecs
                // 商品规格
                
                if(this.Specifications.length === 1){
                	 params.goodsSpecs = this.tableData
                	 console.log(this.Specifications1)
	                for(var i = 0;i<params.goodsSpecs.length;i++){
	                	params.goodsSpecs[i].specData = []
	                	params.goodsSpecs[i].specData.push(this.Specifications1[i])
	                	params.goodsSpecs[i].specData = JSON.stringify(params.goodsSpecs[i].specData)
	                }
                }
                console.log(this.tableData)
                console.log(this.specificatwo1)
                if(this.Specifications.length === 2){
                	 params.goodsSpecs = this.tableData
	                for(var i = 0;i<params.goodsSpecs.length;i++){
	                	params.goodsSpecs[i].specData = this.specificatwo1[i]
	                	params.goodsSpecs[i].specData = JSON.stringify(params.goodsSpecs[i].specData)

	                }
                }
                console.log(params)
                console.log(this.value)
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goods/add",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                    	console.log(data)
                    	if(data.code === 1){
                    		_this.details = false
                    		_this.next = true
                    		_this.selectListName()
                    		_this.tableData = []
                    	}else{
                    		alert(data.msg)
                    	}
                    	
                    }
                });
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
                    	// _this.total = data.data.total
                    	const info = data.data.list
                    	console.log(data)
                    	if(info.length === 0){
                    		alert('请先添加运费模板')
                    	}
                    	_this.options = []
                    	// const arry = []
                    	for (var i = 0; i < info.length; i++) {
                    		const obj = {}
                    		obj.value = info[i].id
                    		obj.label = info[i].name
                    		_this.options.push(obj)
                    	}
                    	// console.log(_this.ceshiarry)
                    	// _this.orderInformation = arry
                    }
                });
			},
			initEditor(data) {
                const _this = this
                const editor = new wangEditor('editor-trigger')
                editor.config.uploadImgUrl = baseUrl+'/api/attachment/upload'
                editor.config.uploadHeaders = {
                    'contentType' : 'application/json;charset=utf-8'
                }

                editor.config.menus = [
                    'source',
                    '|',
                    'bold',
                    'underline',
                    'italic',
                    'strikethrough',
                    'eraser',
                    'forecolor',
                    'bgcolor',
                    '|',
                    'quote',
                    'fontfamily',
                    'fontsize',
                    'head',
                    'unorderlist',
                    'orderlist',
                    'alignleft',
                    'aligncenter',
                    'alignright',
                    '|',
                    'link',
                    'unlink',
                    'table',
                    // 'emotion',
                    '|',
                    'img',
                    '|',
                    'undo',
                    'redo',
                    'fullscreen'
                ]
                editor.onchange = function () {
                    // 编辑区域内容变化时，实时打印出当前内容
                    _this.test_html = this.$txt.html()
                    console.log(_this.test_html);
                }
                editor.create()
            },
            getPList(){
            	const _this = this
            	_this.cities = []
            	const params = {
            		pageNum:1,
            		size:20
            	}
            	$.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/goodsTag/selectList",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                    	console.log(data)
                    	const info = data.data
                    	for (var i = 0; i < info.list.length; i++) {
                    		_this.cities.push(info.list[i].name)
                    		// info.list[i]
                    	}
                    	_this.listBiaoq = info.list
                    }
                });
            },
            clicka(val){
            	console.log(val)
            	this.onbiaoqList = val
            }
		},
		mounted() {
			 this.initEditor()
			 this.selectListName();
			 this.gettemplet();
			 this.getPList()
		}
	}

</script>

<style>
	.Choice_div{
		width: 1600px;
		height: 500px;
		border: 1px solid #ddd;
		margin-top: 20px;
		margin-left: 20px;
	}
	.Choice{
		width: 350px;
		height: 400px;
		border: 1px solid #ddd;
		margin-left: 40px;
		margin-top: 40px;
		overflow: auto;
	}
	.footer_choice{
		width: 1600px;
		height: 40px;
		border: 1px solid #ddd;
		margin-top: 20px;
		margin-left: 20px;
		line-height: 40px;
		padding-left: 20px;
	}
	.Commodity_information{
		width: 1600px;
		height: 2250px;
		border: 1px solid #ddd;
		margin-top: 20px;
		margin-left: 20px;
	}
	.head_text{
		height: 40px;
		border-bottom: 1px solid #ddd;
		line-height: 40px;
	}
	.leftg_text{
		height:40px;
		border-right: 1px solid #ddd;
		text-align: right;
	}
	.body_text{
		border: 1px solid #ddd;
		min-height: 200px;
		margin-top: 20px;
	}
	.shangp{
		height:140px;
		border-top: 1px solid #ddd;
		margin-top: 10px;
	}
</style>