<template>
    <div class="mainDiv">
        <el-row>
            <span class="title-text">
            <i class="fa fa-user fa-fw"></i>角色管理
            </span>
            <el-divider></el-divider>
        </el-row>
        <el-row>
            <el-col>
                <el-form :inline="true" ref="filtersForm" :model="filtersForm">
                    <el-form-item>
                        <el-input v-model="filtersForm.name" placeholder="姓名" style="width: 200px;"/>
                    </el-form-item>
                    <el-form-item>
                        <el-input v-model="filtersForm.code" placeholder="编号" style="width: 200px;"/>
                    </el-form-item>
                    <el-form-item label="起止时间">
                        <el-col :span="11">
                            <el-date-picker type="date" placeholder="选择开始日期" v-model="filtersForm.dateStart"
                                            style="width: 100%;"></el-date-picker>
                        </el-col>
                        <el-col class="line" :span="2" style="text-align: center;">-</el-col>
                        <el-col :span="11">
                            <el-date-picker type="date" placeholder="选择结束日期" v-model="filtersForm.dateEnd"
                                            style="width: 100%;"></el-date-picker>
                        </el-col>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="blue" @click="handleQuery" icon="el-icon-search">查询</el-button>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="success" @click="handleReset" icon="el-icon-refresh">重置</el-button>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="handleAdd" icon="el-icon-circle-plus-outline">添加</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
        </el-row>

        <!--
         `role_name` varchar(30) DEFAULT NULL COMMENT '角色名称',
  `descpt` varchar(50) DEFAULT NULL COMMENT '角色描述',
  `code` varchar(20) DEFAULT NULL COMMENT '角色编号',
  `insert_uid` int(11) DEFAULT NULL COMMENT '操作用户id',
  `insert_time` datetime DEFAULT NULL COMMENT '添加数据时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
        -->
        <el-table
            :data="page.records"
            style="width: 100%">
            <el-table-column
                label="角色名称"
                align="center">
                <template slot-scope="scope">
                    <i class="el-icon-user"></i>
                    <span style="margin-left: 10px">{{ scope.row.roleName }}</span>
                </template>
            </el-table-column>

            <el-table-column
                label="角色描述"
                align="center">
                <template slot-scope="scope">
                    <i class="el-icon-mobile-phone"></i>
                    <span style="margin-left: 10px">{{ scope.row.descpt }}</span>
                </template>
            </el-table-column>

            <el-table-column
                label="角色编号"
                align="center">
                <template slot-scope="scope">
                    <i class="el-icon-message"></i>
                    <span style="margin-left: 10px">{{ scope.row.code }}</span>
                </template>
            </el-table-column>

            <el-table-column
                label="添加数据时间"
                align="center">
                <template slot-scope="scope">
                    <i class="el-icon-time"></i>
                    <span style="margin-left: 10px">{{ scope.row.insertTime | formatTime}}</span>
                </template>
            </el-table-column>

            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button
                        size="mini"
                        @click="handleEdit(scope.$index, scope.row)">编辑
                    </el-button>
                    <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)">删除
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
            background
            layout="prev, pager, next, jumper,->, total, slot"
            @current-change="handleCurrentChange"
            :page-size="page.size"
            :total="page.total"
            style="float:right;margin-top: 10px;">
        </el-pagination>

        <el-dialog
            title="添加用户"
            :visible.sync="formVisibleAdd"
            :close-on-click-modal="false"
            top="30vh"
            width="55%"
            @close="closeDialogAdd">
            <el-form :model="addUserForm" :rules="rules" ref="addUserForm">
                <el-col :span="12">
                    <el-form-item label="用户姓名" :label-width="formLabelWidth" prop="name">
                        <el-input v-model="addUserForm.name"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="出生日期" :label-width="formLabelWidth" prop="birthDate">
                        <el-date-picker
                            type="date"
                            placeholder="选择出生日期"
                            v-model="addUserForm.birthDate"
                            style="width: 100%;"></el-date-picker>
                    </el-form-item>
                </el-col>

                <el-col :span="12">
                    <el-form-item label="用户编号" :label-width="formLabelWidth" prop="code">
                        <el-input v-model="addUserForm.code"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="用户电话" :label-width="formLabelWidth" prop="phone">
                        <el-input v-model="addUserForm.phone"></el-input>
                    </el-form-item>
                </el-col>

                <el-col :span="12">
                    <el-form-item label="用户邮箱" :label-width="formLabelWidth" prop="email">
                        <el-input v-model="addUserForm.email"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="详细住址" :label-width="formLabelWidth" prop="address">
                        <el-input v-model="addUserForm.address"></el-input>
                    </el-form-item>
                </el-col>

            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button type="info" @click="closeDialogAdd">取 消</el-button>
                <el-button
                    type="success"
                    @click="handleSubmitAdd"
                    :loading="formLoading">确 定
                </el-button>
            </div>
        </el-dialog>

        <el-dialog
            title="修改用户"
            :visible.sync="formVisibleUpdate"
            :close-on-click-modal="false"
            top="30vh"
            width="55%"
            @close="closeDialogUpdate">
            <el-form :model="updateUserForm" :rules="rules" ref="updateUserForm">
                <el-col :span="12">
                    <el-form-item label="用户姓名" :label-width="formLabelWidth" prop="name">
                        <el-input v-model="updateUserForm.name"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="出生日期" :label-width="formLabelWidth" prop="birthDate">
                        <el-date-picker
                            type="date"
                            placeholder="选择出生日期"
                            v-model="updateUserForm.birthDate"
                            style="width: 100%;"></el-date-picker>
                    </el-form-item>
                </el-col>

                <el-col :span="12">
                    <el-form-item label="用户编号" :label-width="formLabelWidth" prop="code">
                        <el-input v-model="updateUserForm.code"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="用户电话" :label-width="formLabelWidth" prop="phone">
                        <el-input v-model="updateUserForm.phone"></el-input>
                    </el-form-item>
                </el-col>

                <el-col :span="12">
                    <el-form-item label="用户邮箱" :label-width="formLabelWidth" prop="email">
                        <el-input v-model="updateUserForm.email"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="详细住址" :label-width="formLabelWidth" prop="address">
                        <el-input v-model="updateUserForm.address"></el-input>
                    </el-form-item>
                </el-col>

            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button type="info" @click="closeDialogUpdate">取 消</el-button>
                <el-button
                    type="success"
                    @click="handleSubmitUpdate"
                    :loading="formLoading">确 定
                </el-button>
            </div>
        </el-dialog>


    </div>
</template>
<style>
    .mainDiv {
        min-height: 700px;
        margin: 30px;
        background-color: #fff;
        box-shadow: 0 5px 20px rgba(220, 222, 231, .65);
        padding: 30px;
    }
</style>
<script>
    import moment from 'moment'
    //form验证规则
    const rules = {
        name: [{
            required: true,
            message: '请输入用户姓名',
            trigger: 'blur'
        }],
        code: [{
            required: true,
            message: '请输入用户编号',
            trigger: 'blur'
        }],
        birthDate: [{
            required: true,
            message: '请选择出生日期',
            trigger: 'blur'
        }],
        phone: [{
            required: true,
            message: '请输入电话号码',
            trigger: 'blur'
        }],
        email: [{
            required: true,
            message: '请输入邮箱地址',
            trigger: 'blur'
        }],
        address: [{
            required: true,
            message: '请输入详细住址',
            trigger: 'blur'
        }],
    }

    //点击查询
    let handleQuery = function () {
        //TODO ajax
        console.log(1111)
    }

    //点击重置
    let handleReset = function () {
        this.filtersForm = {}
    }

    //点击添加按钮
    let handleAdd = function () {
        console.log(11111)
        this.addUserForm = {}
        this.formVisibleAdd = true
    }

    //点击修改按钮
    let handleUpdate = function (index, row) {
        this.updateUserForm = Object.assign({}, row)
        this.formVisibleUpdate = true
    }

    //点击删除按钮
    let handleDelete = function (index, row) {
        if (this.pageLoading)
            return

        this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            this.pageLoading = true
            //TODO ajax
            this.$message({
                message: "删除用户成功",
                type: 'success'
            });
            this.pageLoading = false

        }).catch(e => {
        })
    }

    //点击翻页
    let handleCurrentChange = function (val) {
        //TODO 翻页
    }

    //关闭对话框时的操作（添加）
    let closeDialogAdd = function () {
        this.formVisibleAdd = false;
        this.$refs.addUserForm.resetFields()
    }


    //关闭对话框时的操作（修改）
    let closeDialogUpdate = function () {
        this.formVisibleUpdate = false;
        this.$refs.updateUserForm.resetFields()
    }

    //点击添加提交按钮
    let handleSubmitAdd = function () {
        if (this.formLoading) {
            return
        }
        this.$refs.addUserForm.validate(valid => {
            if (!valid) {
                return
            }
            this.formLoading = true
            this.formVisibleAdd = false

            //TODO ajax
            this.$message({
                message: "添加用户成功",
                type: 'success'
            });
            this.formLoading = false
        })

    }

    //点击修改提交按钮
    let handleSubmitUpdate = function () {
        if (this.formLoading) {
            return
        }
        this.$refs.updateUserForm.validate(valid => {
            if (!valid) {
                return
            }
            this.formLoading = true
            this.formVisibleUpdate = false
            //TODO ajax
            this.$message({
                message: "修改用户成功",
                type: 'success'
            });
            this.formLoading = false
        })

    }

    let list = function () {
        this.$api.post('/role/list', {}, res => {
            let result = res.data;
            if (result.status === '1') {
                this.page = result.data
            }
        })
    }

    export default {
        data() {
            return {
                page: {},
                tableData: [
                    {
                        birthDate: '2016-05-02',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1518 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-04',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1517 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-01',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1519 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-03',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1516 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-03',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1516 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-03',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1516 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }, {
                        birthDate: '2016-05-01',
                        name: '王小虎',
                        address: '上海市普陀区金沙江路 1519 弄',
                        code: '1001001',
                        phone: '18566547896',
                        email: 'test@126.com'
                    }
                ],
                filtersForm: {
                    name: '',
                    code: '',
                    dateStart: '',
                    dateEnd: ''
                },
                addUserForm: {
                    birthDate: '',
                    name: '',
                    address: '',
                    code: '',
                    phone: '',
                    email: ''
                },
                updateUserForm: {
                    birthDate: '',
                    name: '',
                    address: '',
                    code: '',
                    phone: '',
                    email: ''
                },
                formVisibleAdd: false,
                formVisibleUpdate: false,
                formLoading: false,
                formLabelWidth: '100px',
                rules: rules
            }
        },
        methods: {
            list,

            handleUpdate,
            handleDelete,
            handleQuery,
            handleReset,
            handleAdd,
            handleCurrentChange,
            closeDialogAdd,
            closeDialogUpdate,
            handleSubmitAdd,
            handleSubmitUpdate
        },
        mounted:function () {
            this.list()
        },
        filters:{
            formatTime(timestamp){
                // DateUtil.format(timestamp,'yyyy-MM-dd HH:mm:ss')
                return moment(timestamp).format('YYYY-MM-DD HH:mm:ss')
                // return '2019'
            }
        }
    }


</script>
