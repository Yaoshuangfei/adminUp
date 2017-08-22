<template>
  <section>
    <!--工具条-->
    <el-col :span="24" class="toolbar" style="padding-bottom: 0px;background: #fff">
      <el-form :inline="true" :model="filters">
        <el-form-item>
          <el-button type="primary" v-on:click="addBtn">新增</el-button>
        </el-form-item>
      </el-form>
    </el-col>

    <!--列表-->
    <el-table :data="orderInformation" highlight-current-row v-loading="listLoading" style="width: 100%;min-width: 1080px;">
      <el-table-column prop="indexStyleId" :formatter='formatter' label="位置">
      </el-table-column>
      <el-table-column prop="goodsName" label="商品名称">
      </el-table-column>
      <el-table-column prop="orderSort" label="顺序">
      </el-table-column>
      <el-table-column prop="status" :formatter='formatterStatus' label="状态">
      </el-table-column>
      <el-table-column label="操作">
        <template scope="scope">
           <!-- <el-switch v-model="value1" on-text="" on-color="#13ce66" off-text=""></el-switch> -->
          <el-button type="text" size="small" @click="DelBtn(scope.$index, scope.row)">删除</el-button>
          <el-button type="text" size="small" v-if="scope.row.status===1" @click="handleEnale(scope.$index, scope.row)">禁用</el-button>
          <el-button type="text" size="small" v-else @click="handleDisable(scope.$index, scope.row)">启用</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!--工具条-->
    <el-col :span="24" class="toolbar" style="background:#fff;">
      <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="20" :total="total" style="float:right;">
      </el-pagination>
    </el-col>

    <!--编辑界面-->
    <el-dialog title="新增" v-model="editFormVisible" :close-on-click-modal="false" >
      <el-form :model="orderDetails" label-width="80px" :rules="editFormRules" ref="editForm">
        <el-form-item label="位置">
            <el-select v-model="filters.type" clearable>
              <el-option v-for="item in selectSubjectStatus" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
        </el-form-item>
        <el-form-item label="顺序">
          <el-input v-model="orderDetails.orderNumber" type="text" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="选择商品">
          <el-select v-model="product.type" clearable>
            <el-option v-for="item in options" :label="item.label" :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-col :span='24'></el-col>
      </el-form>
      <div slot="footer" class="dialog-footer" style="text-align: center;">
        <el-button type="primary" @click.native="editSubmit" :loading="editLoading">确定</el-button>
        <el-button type="primary" @click.native="editFormVisible = false">取消</el-button>
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
        value1:true,
        value2:'',
        selectSubjectStatus: [],
        options: [],
        filters: {
          name: '',
          status:'',
          type:''
        },
          product: {
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
        orderInformation:[]
      }
    },
    methods: {
            formatter(row,column){
              let curTime = row.indexStyleId;
              if(curTime === 8){
                  curTime = '模块一'
              }else{
                  curTime = '模块二'
              }
              return curTime
           },
          getlist(){
            const _this = this;
            _this.orderInformation = [];
            const url   = baseUrl+"/api/indexProductConf/page/product?pageNum="+_this.page+"&pageSize=20";
              $.ajax({
                  type:'POST',
                  dataType:'json',
                  url:url,
                  data:{},
                  contentType:'application/json;charset=utf-8',
                  error: function (XMLHttpRequest, textStatus, errorThrown) {},
                  success:function(data){
                    console.log(data)
                    if(data.code === 1){
                      _this.total = data.data.total
                      if(!data.success){
                          // alert(data.msg)
                      }else{
                          var _length   = data.data.list;
                          _this.total   = data.data.total;
                          for (var i = 0; i < _length.length; i++) {
                              _this.orderInformation.push(_length[i]);
                          }
                      }
                    }
                  }
              });
          },
        //判断状态
        formatterStatus(row,column){
            let curTime = row.status;
            if(curTime === 1){
                curTime = '启用'
            }else{
                curTime = '禁用'
            }
            return curTime
        },
        // 获取店铺模块
        box(){
          const _this = this;
          const url   = baseUrl+"/api/indexShop/dropDown/box";
            $.ajax({
                type:'POST',
                dataType:'json',
                url:url,
                data:{},
                contentType:'application/json;charset=utf-8',
                error: function (XMLHttpRequest, textStatus, errorThrown) {},
                success:function(data){
                    console.log(data)
                    if(!data.success){
                        alert(data.msg)
                    }else{
                      if(data.data === null){
                        return
                      }
                        var _lengthOne = data.data.styleOneId;
                        var _lengthTwo = data.data.styleTwoId;
                        _this.selectSubjectStatus=([
                            {value:_lengthOne,label:'模块一'},
                            {value:_lengthTwo,label:'模块二'}
                        ])
                    }
                }
            });
        },
        //获取商品下拉列表
        product_box(){
            const _this = this;
            const url   = baseUrl+"/api/indexProductConf/product/box";
            $.ajax({
                type:'POST',
                dataType:'json',
                url:url,
                data:{},
                contentType:'application/json;charset=utf-8',
                error: function (XMLHttpRequest, textStatus, errorThrown) {},
                success:function(data){
                    if(!data.success){
                        alert(data.msg)
                    }else{
                        var _length = data.data;
                        for (var i in _length){
                             var obj = {value:_length[i].id,label:_length[i].name};
                            _this.options.push(obj);
                        }
                    }
                }
            });
        },
        //添加商品配置
        editSubmit: function () {
            this.$refs.editForm.validate((valid) => {
                if (valid) {
                    this.$confirm('确认提交吗？', '提示', {}).then(() => {
                        const _this  = this;
                        const params = {
                            productId:parseInt(this.product.type),
                            indexStyleId:this.filters.type,
                            orderSort:this.orderDetails.orderNumber,
                        };
                        console.log(params)
                        const url    = baseUrl+"/api/indexProductConf/add";
                        const data   =JSON.stringify(params);
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
                                    _this.$message({
                                        message: '添加成功',
                                        type: 'success'
                                    });
                                    _this.editFormVisible = false;
                                    _this.getlist();
                                }
                            }
                        });
                    });
                }
            });
        },
        //删除商品配置
        DelBtn: function (index, row) {
            this.$confirm('确认删除该记录吗?', '提示', {
                type: 'warning'
            }).then(() => {
                const _this= this;
                const params = {
                    id:row.id
                };
                var url = baseUrl+"/api/indexProductConf/delete/one";
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
            }).catch(() => {

            });
        },
//        禁用
        handleEnale: function (index, row) {
            this.$confirm('确认禁用该商品吗?', '提示', {
                type: 'warning'
            }).then(() => {
                const _this= this;
                const params = {
                    id:row.id
                };
                var url = baseUrl+"/api/indexProductConf/disable";
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
//        启用
        handleDisable: function (index, row) {
      this.$confirm('确认启用该商品吗?', '提示', {
          type: 'warning'
      }).then(() => {
          const _this= this;
          const params = {
              id:row.id
          };
          var url = baseUrl+"/api/indexProductConf/enable";
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

      handleCurrentChange(val) {
        this.page = val;
        this.getlist();
      },
      //显示编辑界面
      addBtn: function (index, row) {
        this.editFormVisible = true;
        this.orderDetails = Object.assign({}, row);
      },

    },
    mounted() {
       this.getlist();
       this.box();
       this.product_box();
    }
  }

</script>

<style>
  
</style>