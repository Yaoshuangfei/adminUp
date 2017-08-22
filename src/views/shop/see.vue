<template>
  <section>
    <el-form :model="filters" label-width="180px" style="margin-left: 40px;margin-top: 40px" :rules="rules"  ref="filters">
      <el-form-item label="店铺名称" prop="name">
        <el-input v-model="filters.name" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="所属分类">
        <el-input v-model="filters.pertainType" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="主营商品">
        <el-input v-model="filters.marketingGoods"  type="textarea" style="width:400px"></el-input>
      </el-form-item>
      <!-- <img :src="filters.logo"> -->
      <el-form-item label="店铺logo" prop="url">
        <input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
          <button type="button" class="el-button el-button--primary el-button--small">
            <span>点击上传</span>
          </button>
          <el-col :spam="24">
          <img style="width: 100px;" v-if="url !== ''" :src="url"></el-col>
        <!-- <el-input v-model="filters.name" style="width:400px"></el-input> -->
      </el-form-item>
      <el-form-item label="所在地区">
        <el-input v-model="filters.region" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="详细地址" prop="address">
        <el-input v-model="filters.address" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="客服电话" prop="phone">
        <el-input v-model="filters.phone" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="QQ" prop="qq">
        <el-input v-model="filters.qq" style="width:400px"></el-input>
      </el-form-item>
      <el-form-item label="店铺介绍">
        <div id = 'editor-trigger' style="height: 500px"></div>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">保存</el-button>
        <el-button>取消</el-button>
      </el-form-item>
  </el-form>
  </section>
</template>

<script>
  import { state } from '../../vuex/state'
  import util from '../../common/js/util'
  import {baseUrl , editUser, addUser } from '../../api/api';
  import wangEditor from 'wangEditor'

  export default {
    components: {
            wangEditor
        },
    data() {
      return {
        url:'',
         rules: {
          name: [
            { required: true, message: '请输店铺名称', trigger: 'blur' },
            { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '请输入详细地址', trigger: 'change' },
            { min: 3, max: 25, message: '长度在 5 到 25 个字符', trigger: 'blur' }
          ],
          url: [
            {  required: true, message: '请选择图片', trigger: 'change' }
          ],
          phone: [
            {  required: true, message: '请填写客服电话', trigger: 'change' },
            { min: 8, max: 12, message: '长度在 8 到 12 个字符', trigger: 'blur' }
          ],
          qq: [
            { required: true, message: '请填写QQ号', trigger: 'change' },
              // { type:'number', required: true, message: '请填写QQ号', trigger: 'change' },
            { min: 6, max: 11, message: '长度在 6 到 11 个字符', trigger: 'blur' }
          ]
        },
        radio: '0',
        checked: true,
        value:'',
        value1:true,
        value2:'',
        _html:'',
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
                    this.uploadBanner()
                } else {
                    this.fileImg = ''
                    console.log('不存在file', file)
                    this.formData = new FormData()
                }
            },
      uploadBanner(){
              const _this= this;
              _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                  progress(event) {
                  }
              })
                  .then(response => {
                    console.log(response)
                      const info = JSON.parse(response.bodyText);
        _this.url = info.data[0].baseUri+info.data[0].uri;
        console.log(_this.url)
                  }, error => _this.$emit('complete', 500, error.message))
      },
      onSubmit(){
        const _this = this
        const params = {
          id:state.storeId,
          name:this.filters.name,
          logo:this.url,
          pertainType:this.filters.pertainType,
          marketingGoods:this.filters.marketingGoods,
          region:this.filters.region,
          address:this.filters.address,
          phone:this.filters.phone,
          qq:this.filters.qq,
          content:this._html
        }
        console.log(params)
        $.ajax({
              type:'POST',
              dataType:'json',
              url:baseUrl+"/api/store/update/thisStore",
              data:JSON.stringify(params),
              contentType:'application/json;charset=utf-8',
              success:function(data){
                console.log(data)
                if(data.code === 1){
                  _this.$message({
                    message: '添加成功',
                    type: 'success'
                  });
                }else {
                  _this.$message({
                    message: data.msg,
                    type: 'error'
                  });
                }
              }
          });
      },
      getlist(){
        const _this = this
          $.ajax({
              type:'POST',
              dataType:'json',
              url:baseUrl+"/api/store/find/thisStore",
              data:JSON.stringify({}),
              contentType:'application/json;charset=utf-8',
              success:function(data){
                console.log(data)
                _this.filters = data.data
                _this.url = data.data.logo
                _this.initEditor();
              }
          });
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
                    _this._html = this.$txt.html()
                    console.log(_this._html);
                }
                editor.create()
                console.log(_this.filters)
                editor.$txt.append(_this.filters.content)
            }
    },
    mounted() {
      this.getlist();
      
    }
  }

</script>

<style>
  
  .el-form-item__label{
    text-align: left;
  }
</style>