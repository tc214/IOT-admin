<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i>用户列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
                <el-select v-model="select_cate" placeholder="筛选单位" class="handle-select mr10">
                    <!--<el-option key="1" label="广东省" value="广东省"></el-option>-->
                    <!--<el-option key="2" label="湖南省" value="湖南省"></el-option>-->
                </el-select>
                <el-input v-model="select_word" placeholder="输入用户名" class="handle-input mr10"></el-input>
                <el-button class="el-icon-search" type="primary" icon="search" @click="search">搜索</el-button>
                <el-button class="el-icon-plus" type="primary" icon="search" @click="doAddUser">新增</el-button>
                <!--<el-col :span="12">-->
                    <!--<div class="el-input" style="width: 200px; float: right;">-->
                        <!--<i class="el-input__icon el-icon-search"></i>-->
                        <!--<input type="text" placeholder="输入用户名称"-->
                               <!--class="el-input__inner">-->
                    <!--</div>-->
                <!--</el-col>-->
            </div>
            <el-table
                v-loading="loading"
                :data="data"
                border style="width: 100%"
                ref="multipleTable"
                @selection-change="handleSelectionChange">

                <el-table-column type="selection" width="55"></el-table-column>

                <el-table-column prop="name" type="text" label="用户名" width="80">
                </el-table-column>
                <el-table-column prop="status" label="状态" width="60">
                </el-table-column>
                <el-table-column prop="name" label="姓名" width="60">
                </el-table-column>
                <el-table-column prop="status" label="手机号" width="60">
                </el-table-column>
                <el-table-column prop="name" label="邮箱" width="120">
                </el-table-column>
                <!--<el-table-column prop="status" label="状态" width="60">-->
                <!--</el-table-column>-->
                <el-table-column prop="status" label="单位名称" width="120">
                </el-table-column>
                <!--<el-table-column prop="status" label="登录时间" width="120">-->
                <!--</el-table-column>-->
                <!--<el-table-column prop="status" label="登录地点" width="120">-->
                <!--</el-table-column>-->
                <el-table-column prop="address" label="注册时间" :formatter="formatter">
                </el-table-column>
                <el-table-column prop="date" label="修改时间" sortable width="120">
                </el-table-column>

                <el-table-column label="操作" width="480">
                    <template slot-scope="scope">
                        <el-button size="small" type="default" @click="handleForbid(scope.$index, scope.row)">禁用</el-button>
                        <el-button size="small" class="el-icon-edit" icon="edit" type="default" @click="handleEdit(scope.$index, scope.row)">修改</el-button>
                        <el-button
                            size="small"
                            type="info"
                            class="el-icon-setting"
                            @click="handleBindRole(scope.$index, scope.row)">绑定角色
                        </el-button>
                        <el-button size="small" class="el-icon-edit" icon="edit" type="default" @click="handleResetPwd(scope.$index, scope.row)">重置密码</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="40px">
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
                <el-form-item label="单位">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
                <el-form-item label="邮箱">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>

                <el-form-item label="日期">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date" value-format="yyyy-MM-dd" style="width: 100%;">
                    </el-date-picker>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 禁用提示框 -->
        <el-dialog title="提示" :visible.sync="disableVisible" width="300px" center>
            <div class="del-dialog-cnt">确定禁用该用户？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="disableVisible = false">取 消</el-button>
                <el-button type="primary" @click="disableRow">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 重置密码提示框 -->
        <el-dialog title="提示" :visible.sync="resetPwdVisible" width="300px" center>
            <div class="del-dialog-cnt">确定重置密码？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="resetPwdVisible = false">取 消</el-button>
                <el-button type="primary" @click="resetPwdRow">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 角色绑定 -->
        <el-dialog title="角色绑定" :visible.sync="bindRoleVisible" width="800px" center>
            <div style="text-align: center">
                <el-transfer
                    style="text-align: left; display: inline-block"
                    v-model="value4"
                    filterable
                    :left-default-checked="[2, 3]"
                    :right-default-checked="[1]"
                    :titles="['未绑定角色', '已绑定角色']"
                    :button-texts="['到左边', '到右边']"
                    :format="{
                        noChecked: '${total}',
                        hasChecked: '${checked}/${total}'
                      }"
                    @change="handleChange"
                    :data="data">
                    <span slot-scope="{ option }">{{ option.key }} - {{ option.label }}</span>
                    <el-button class="transfer-footer" slot="left-footer" size="small">操作</el-button>
                    <el-button class="transfer-footer" slot="right-footer" size="small">操作</el-button>
                </el-transfer>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="bindRoleVisible = false">取 消</el-button>
                <el-button type="primary" @click="bindRole">确 定</el-button>
            </span>

        </el-dialog>
    </div>
</template>

<script>
    export default {
        data() {
            const generateData = _ => {
                const data = [];
                for (let i = 1; i <= 15; i++) {
                    data.push({
                        key: i,
                        label: `备选项 ${ i }`,
                        disabled: i % 4 === 0
                    });
                }
                return data;
            };

            return {
                data: generateData(),
                value3: [1],
                value4: [1],
                url: './static/vuetable.json',
                tableData: [],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                editVisible: false,
                delVisible: false,
                disableVisible: false,
                resetPwdVisible: false,
                bindRoleVisible: false,

                form: {
                    name: '',
                    date: '',
                    address: ''
                },
                idx: -1,
                idDisable: -1,
                idResetPwd: -1,
                loading: true,
            }
        },
        created() {
            this.getData();
        },
        computed: {
            data() {
                return this.tableData.filter((d) => {
                    let is_del = false;
                    for (let i = 0; i < this.del_list.length; i++) {
                        if (d.name === this.del_list[i].name) {
                            is_del = true;
                            break;
                        }
                    }
                    if (!is_del) {
                        if (d.address.indexOf(this.select_cate) > -1 &&
                            (d.name.indexOf(this.select_word) > -1 ||
                                d.address.indexOf(this.select_word) > -1)
                        ) { console.log("=============d:");
                            console.log(d.address);
                            console.log(d.name);
                            return d;
                        }
                    }
                })
            }
        },
        methods: {
            // 分页导航
            handleCurrentChange(val) {
                this.cur_page = val;
                this.getData();
            },
            // 获取 easy-mock 的模拟数据
            getData() {
                // 开发环境使用 easy-mock 数据，正式环境使用 json 文件
                if (process.env.NODE_ENV === 'development') {
                    this.url = '/ms/table/list';
                }

                this.$axios.post(this.url, {
                    page: this.cur_page
                }).then((res) => {
                    this.tableData = res.data.list;
                    console.log("tableData:");
                    console.log(this.tableData);
                    this.loading = false;
                }).catch(error => {
                    this.loading = false;
                });
            },
            search() {
                this.is_search = true;
            },
            goWaveControll() {
                this.$router.push({path: '/wave'});
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                this.form = {
                    name: item.name,
                    date: item.date,
                    address: item.address
                };
                this.editVisible = true;
                // 页面跳转测试
                // this.$router.push({path: '/wave'});
                // window.location.href = '/wave';
            },
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            handleResetPwd(index, row) {
                this.idResetPwd = index;
                this.resetPwdVisible = true;
            },
            handleForbid(index, row) {
                this.idDisable = index;
                this.disableVisible = true;
            },
            handleBindRole(index, row) {
                // 弹出角色绑定框
                this.bindRoleVisible = true;
            },

            delAll() {
                const length = this.multipleSelection.length;
                let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += this.multipleSelection[i].name + ' ';
                }
                this.$message.error('删除了' + str);
                this.multipleSelection = [];
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 保存修改
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(index, row){
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            },
            // 确定禁用
            disableRow(index, row){
                this.$message.success('禁用成功');
                this.disableVisible = false;
            },
            resetPwdRow(index, row) {
                this.$message.success('重置密码成功');
                this.resetPwdVisible = false;
            },

            bindRole(index, row) {
                this.$message.success('绑定角色成功');
                this.bindRoleVisible = false;

            },

            doAddUser() {

            },

            handleChange(value, direction, movedKeys) {
                console.log(value);
                console.log(direction);
                console.log(movedKeys);
                // console.log(value, direction, movedKeys);


            },

        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }

    .transfer-footer {
        margin-left: 20px;
        padding: 6px 5px;
    }



</style>
