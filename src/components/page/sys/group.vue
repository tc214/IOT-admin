<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i>组织列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
                <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                    <el-option key="1" label="广东省" value="广东省"></el-option>
                    <el-option key="2" label="湖南省" value="湖南省"></el-option>
                </el-select>
                <el-input v-model="select_word" placeholder="输入组织名" class="handle-input mr10"></el-input>
                <el-button class="el-icon-search" type="primary" icon="search" @click="search">搜索</el-button>
                <el-button class="el-icon-plus" type="primary" icon="search" @click="doAddUser">新增</el-button>

            </div>
            <el-table
                v-loading="loading"
                :data="data"
                border style="width: 100%"
                ref="multipleTable"
                @selection-change="handleSelectionChange">

                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column prop="name" label="组织名" width="100">
                </el-table-column>
                <el-table-column prop="status" label="状态" width="60">
                </el-table-column>
                <el-table-column prop="name" label="组织编码" width="100">
                </el-table-column>
                <el-table-column prop="name" label="邮箱" width="120">
                </el-table-column>
                <el-table-column prop="status" label="电话" width="120">
                </el-table-column>
                <el-table-column prop="status" label="单位类型" width="80">
                </el-table-column>
                <el-table-column prop="address" label="地址" :formatter="formatter">
                </el-table-column>
                <el-table-column prop="date" label="注册时间" sortable width="150">
                </el-table-column>
                <!--<el-table-column prop="status" label="登录时间" width="120">-->
                <!--</el-table-column>-->
                <!--<el-table-column prop="status" label="登录地点" width="120">-->
                <!--</el-table-column>-->


                <el-table-column label="操作" width="280">
                    <template slot-scope="scope">
                        <el-button size="small" class="el-icon-edit" icon="edit" type="default"
                                   @click="handleEdit(scope.$index, scope.row)">修改</el-button>
                        <el-button
                            size="small"
                            type="info"
                            class="el-icon-setting"
                            @click="handleRoleConfig(scope.$index, scope.row)">绑定用户
                        </el-button>
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
            <el-form ref="form" :model="form" label-width="50px">
                <el-form-item label="日期">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="登录用户名">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
                <el-form-item label="邮箱">
                    <el-input v-model="form.address"></el-input>
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
    </div>
</template>

<script>
    export default {
        data() {
            return {
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
                form: {
                    name: '',
                    date: '',
                    address: ''
                },
                idx: -1,
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
            // 保存编辑
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(){
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            },
            handleRoleConfig() {

            },
            doAddUser() {

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
</style>
