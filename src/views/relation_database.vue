<template>
    <a-layout style="padding: 24px 24px;">
        <div id="components-form-demo-advanced-search">
            <a-form class="ant-advanced-search-form" :form="form" @submit="handleSearch" >
                <a-row :gutter="24" >
                    <a-col
                            :span="8"
                    >
                        <a-form-item :label="`id`">
                            <a-input
                                    v-decorator="[
                                        `id`,
                                        {
                                          rules: [
                                            {
                                              required: false,
                                              message: 'Input something!',
                                            },
                                          ],
                                        },
                                      ]"
                                    placeholder="请输入id"
                            />
                        </a-form-item>
                    </a-col>
                    <a-col
                            :span="8"
                    >
                        <a-form-item :label="`源数据库id`">
                            <a-input
                                    v-decorator="[
                                        `source_id`,
                                        {
                                          rules: [
                                            {
                                              required: false,
                                              message: 'Input something!',
                                            },
                                          ],
                                        },
                                      ]"
                                    placeholder="请输入源数据库ID"
                            />
                        </a-form-item>
                    </a-col>
                    <a-col
                            :span="8"
                    >
                        <a-form-item :label="`源数据表名`">
                            <a-input
                                    v-decorator="[
                                        `source_form`,
                                        {
                                          rules: [
                                            {
                                              required: false,
                                              message: 'Input something!',
                                            },
                                          ],
                                        },
                                      ]"
                                    placeholder="请输入源数据表名"
                            />
                        </a-form-item>
                    </a-col>
                    <a-col
                            :span="8"
                            :style="{ display: count ? 'block' : 'none' }"
                    >
                        <a-form-item :label="`目标库`">
                            <a-input
                                    v-decorator="[
                                        `database`,
                                        {
                                          rules: [
                                            {
                                              required: false,
                                              message: 'Input something!',
                                            },
                                          ],
                                        },
                                      ]"
                                    placeholder="请输入目标库id"
                            />
                        </a-form-item>
                    </a-col>
                    <a-col
                            :span="8"
                            :style="{ display: count ? 'block' : 'none' }"
                    >
                        <a-form-item :label="`目标表`">
                            <a-input
                                    v-decorator="[
                                        `sourcce_table`,
                                        {
                                          rules: [
                                            {
                                              required: false,
                                              message: 'Input something!',
                                            },
                                          ],
                                        },
                                      ]"
                                    placeholder="请输入目标表名"
                            />
                        </a-form-item>
                    </a-col>
                </a-row>
                <a-row>
                    <a-col :span="24" :style="{ textAlign: 'right' }">
                        <a-button type="primary" html-type="submit">
                            查询
                        </a-button>
                        <a-button :style="{ marginLeft: '8px' }" @click="handleReset">
                            清除
                        </a-button>
                        <a :style="{ marginLeft: '8px', fontSize: '12px' }" @click="toggle">
                            更多 <a-icon :type="expand ? 'up' : 'down'" />
                        </a>
                    </a-col>
                </a-row>
            </a-form>
            <a-layout-content style="margin: 16px 0;padding: 8px 16px;;background-color: #FFFFFF">
                <a-button type="primary">
                    <router-link to="/">添加关联</router-link>
                </a-button>
            </a-layout-content>
            <div class="search-result-list" style="background-color: #FFFFFF">
                <a-table :columns="columns" :data-source="list"
                         :pagination="{
                            defaultPageSize: 10,
                            showSizeChanger: true,
                            pageSizeOptions: ['10', '20', '50', '100'],
                            }">
                    <!--                编辑-->
                    <template slot="operation" slot-scope="text, record, index">
                        <div class="editable-row-operations">
                        <span >
                          <router-link to="/">编辑</router-link>
                        </span>
                        </div>
                    </template>
                    <!--                添加定时任务-->
                    <template slot="edit" slot-scope="text, record, index">
                        <div class="editable-row-operations">
                        <span >
                          <router-link to="/">编辑定时任务</router-link>
                        </span>
                        </div>
                    </template>
                </a-table>
            </div>
        </div>
    </a-layout>
</template>
<script>
    const columns = [
        {
            title: '数据库',
            dataIndex: 'etlTablesId',
            key:"etlTablesId"
        },
        {
            title: '表名',
            dataIndex: 'code',
            key: "code",
            ellipsis: true,
        },
        {
            title: '关联数据库',
            dataIndex: 'jobGroup',
            key: "jobGroup",
            ellipsis: true
        },
        {
            title: '关联表',
            dataIndex: 'jobClassName',
            key: "jobClassName",
            ellipsis: true,
        },
        {
            title: '创建人',
            dataIndex: 'description',
            key: 'description',
        },
        {
            title: '创建时间',
            dataIndex: 'cronExpression',
            key: 'cronExpression',
            ellipsis: true,
        },
        {
            title: '操作',
            dataIndex: 'operation',
            key: 'operation',
            ellipsis: true,
            scopedSlots: { customRender: 'operation' },
        },
        {
            title: ' ',
            dataIndex: 'edit',
            key: 'edit',
            ellipsis: true,
            scopedSlots: { customRender: 'edit' },
        },
    ];
    const list = [];
    import { get } from "axios";
    export default {
        name:"relation_database",
        data() {
            return {
                columns,
                list,
                expand: false,
                form: this.$form.createForm(this, { name: 'advanced_search' }),
            };
        },
        async created() {
            const { data } = await get("/etlTask/jobTask/getAllJobTask");
            console.log(data);
            this.list = data.data;
        },
        computed: {
            count() {
                return !this.expand;
            },
        },
        updated() {
            console.log('updated');
        },
        methods: {
            handleSearch(e) {
                e.preventDefault();
                this.form.validateFields((error, values) => {
                    console.log('error', error);
                    console.log('Received values of form: ', values);
                });
            },

            handleReset() {
                this.form.resetFields();
            },
            toggle() {
                this.expand = !this.expand;
            },
        },
    };
</script>
<style>
    .ant-advanced-search-form {
        padding: 24px;
        background: #fbfbfb;
        border: 1px solid #d9d9d9;
        border-radius: 6px;
    }
    .ant-advanced-search-form {
        border-radius: 0;
        border: none;
        background-color: #FFFFFF;
    }

    .ant-advanced-search-form .ant-form-item {
        display: flex;
    }

    .ant-advanced-search-form .ant-form-item-control-wrapper {
        flex: 1;
    }

    #components-form-demo-advanced-search .ant-form {
        max-width: none;
    }
    #components-form-demo-advanced-search .search-result-list {
        margin-top: 16px;
        border: 1px dashed #e9e9e9;
        border-radius: 6px;
        background-color: #fafafa;
        min-height: 200px;
        text-align: center;
        padding-top: 80px;
    }
    #components-form-demo-advanced-search .search-result-list {
        padding-top: 0;
    }
</style>