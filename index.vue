<template>
    <a-card :bordered="false">
        <a-form ref="searchFormRef" name="advanced_search" :model="searchFormState" class="ant-advanced-search-form">
            <a-row :gutter="24">
                <a-col :span="4">
                    <a-form-item label="" name="name">
                        <a-input v-model:value="searchFormState.name" placeholder="设备名称或编号" />
                    </a-form-item>
                </a-col>
                <a-col :span="4">
                    <a-form-item label="" name="team ">
                        <a-select v-model:value="searchFormState.team " placeholder="所有线路站点" :options="formTypeOptions" />
                    </a-form-item>
                </a-col>
                <a-col :span="4">
                    <a-form-item label="" name="type">
                        <a-select v-model:value="searchFormState.type" placeholder="设备类型" :options="MaintenanceType" />
                    </a-form-item>
                </a-col>

                <a-col :span="4">
                    <a-form-item label="" name="major">
                        <a-select v-model:value="searchFormState.major" placeholder="设备状态" :options="MaintenanceMajor" />
                    </a-form-item>
                </a-col>
                <a-col :span="4">
                    <a-form-item label="" name="formStatus">
                        <a-select v-model:value="searchFormState.formStatus" placeholder="运行状态" :options="ApprovalStatus" />
                    </a-form-item>
                </a-col>
                <a-col :span="4">
                    <a-button type="primary" @click="table.refresh(true)">查询</a-button>
                    <a-button style="margin: 0 8px" @click="reset">重置</a-button>
                </a-col>
            </a-row>
        </a-form>
		
		<a-button type="primary" @click="formRef.onOpen()" v-if="hasPerm('genFormTemplateAdd')">
		    <template #icon><plus-outlined /></template>
			新增设备
		</a-button>


        <div class="table-card">
			<!-- <s-table
			    ref="table"
				:rowKey="(r, i) => i"
			    :columns="columns"
			    :data="loadData"
			    :alert="options.alert.show"
			    bordered
			    :row-key="(record) => record.id"
			    :tool-config="toolConfig"
			    :row-selection="options.rowSelection"
				:row-class-name="(_record, index) => (index % 2 === 1 ? 'table-striped' : null)"
			>
			    <template #operator class="table-operator">
			        <a-space>
			           


			        </a-space>
			    </template>
			    <template #bodyCell="{ column, record }">
			       <template v-if="column.dataIndex === 'status'">


						<a-popconfirm title="确定要关闭吗？" @confirm="stateGenFormTemplate(record)">
							<a-switch v-model:checked="state.checked1" checked-children="开起" un-checked-children="关闭" />
						</a-popconfirm>





			        </template>
			        <template v-if="column.dataIndex === 'action'">
			            <a-space>
			               <a @click="viewRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">查看</a>
			                <a @click="formRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">|</a>
			                <a @click="formRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">编辑</a>
			                <a-divider type="vertical" v-if="hasPerm(['genFormTemplateEdit', 'genFormTemplateDelete'], 'and')" />
			     
			            </a-space>
			        </template>
			    </template>
			</s-table> -->
            <a-table :columns="columns" :data-source="data">
                <template #bodyCell="{ column, text }">
                    <template v-if="column.dataIndex === 'action'">
			            <a-space>
			               <a @click="viewRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">查看</a>
			                <a @click="formRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">|</a>
			                <a @click="formRef.onOpen(record)" v-if="hasPerm('genFormTemplateEdit')">编辑</a>
			                <a-divider type="vertical" v-if="hasPerm(['genFormTemplateEdit', 'genFormTemplateDelete'], 'and')" />
			     
			            </a-space>
			        </template>
                </template>
                
            </a-table>
		</div>
    </a-card>



    <View ref="viewRef" @successful="table.refresh(true)" />

    <Form ref="formRef" @successful="table.refresh(true)" />
    <!-- <Inform ref="formRef1" @successful="table.refresh(true)" /> -->
 <!--   <record ref="formRef2" @successful="table.refresh(true)" />

    <maintenance ref="formRef4" @successful="table.refresh(true)" />
    <change ref="formRef5" @successful="table.refresh(true)" />
	 -->

</template>

<script setup name="formTemplate">
    import tool from '@/utils/tool'



    import Form from './form.vue'
    import View from './view.vue'
    import Inform from './inform.vue'
    import record from './record.vue'
    import failure from './failure.vue'
    import maintenance from './maintenance.vue'
    import change from './change.vue'
    import genFormTemplateApi from '@/api/gen/genFormTemplateApi'
    let searchFormState = reactive({})
    const searchFormRef = ref()



	const viewRef = ref()



    const table = ref()
    const formRef = ref()
    const formRef1 = ref()
    const formRef2 = ref()
    const formRef3 = ref()
    const formRef4 = ref()
    const formRef5 = ref()
    const toolConfig = { refresh: true, height: true, columnSetting: true, striped: false }
    const columns = [{
            title: '序号',
            dataIndex: 'id',
            key: 'id',
            width: 70,
        }, {
            title: '设备编号',
            dataIndex: 'deviceID',
            key: 'deviceID',
            width: 100,
        }, {
            title: '设备名称',
            dataIndex: 'name',
            key: 'name',
            width: 150,
        }, {
            title: '所属线路站点',
            dataIndex: 'point',
            key: 'point',
        }, {
            title: '设备类型',
            dataIndex: 'type',
            key: 'type',
        }, {
            title: '设备状态',
            dataIndex: 'state',
            key: 'state',
        },{
            title: '运行状态',
            dataIndex: 'state1',
            key: 'state1',
        },
        {
            title: '最近检修时间',
            dataIndex: 'time',
            key: 'time',
        }];

        const data = [{
                key: '1',
                id: 1,
                deviceID: 'FT-A-01',
                name:'二号线自动扶梯01',
                point: '二号线-刘家坝站',
                type:'自动扶梯',
                state:'正常',
                state1:'上行',
                time:'2020-11-20 23:26:08',

            }, {
                key: '2',
                id: 2,
                deviceID: 'FT-A-02',
                name:'二号线自动扶梯02',
                point: '二号线-刘家坝站',
                type:'自动扶梯',
                state:'断网',
                state1:'下行',
                time:'2020-11-25 23:26:08',
            }, {
                key: '3',
                id: 3,
                deviceID: 'FT-A-03',
                name:'二号线载人直梯03',
                point: '二号线-刘家坝站',
                type:'载人直梯',
                state:'维保',
                state1:'-',
                time:'2020-11-25 23:26:08',
        },{
                key: '4',
                id: 4,
                deviceID: 'FT-A-04',
                name:'二号线载人直梯04',
                point: '二号线-刘家坝站',
                type:'载人直梯',
                state:'故障',
                state1:'检修',
                time:'2020-11-25 23:26:08',
        }];

	const state = reactive({
	  checked1: true
	});

    // 操作栏通过权限判断是否显示
    if (hasPerm(['genFormTemplateEdit', 'genFormTemplateDelete'])) {
        columns.push({
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            width: '200px'
        })
    }
    const selectedRowKeys = ref([])
    // 列表选择配置
    const options = {
        // columns数字类型字段加入 needTotal: true 可以勾选自动算账
        alert: {
            show: true,
            clear: () => {
                selectedRowKeys.value = ref([])
            }
        },
        rowSelection: {
            onChange: (selectedRowKey, selectedRows) => {
                selectedRowKeys.value = selectedRowKey
            }
        }
    }
    const loadData = (parameter) => {
        const searchFormParam = JSON.parse(JSON.stringify(searchFormState))
        return genFormTemplateApi.genFormTemplatePage(Object.assign(parameter, searchFormParam)).then((data) => {
            return data
        })
    }
    // 重置
    const reset = () => {
        searchFormRef.value.resetFields()
        table.value.refresh(true)
    }

	//启用 禁用
	const stateGenFormTemplate = () =>{
		state.checked1.value = true
	}



    // 删除
    const deleteGenFormTemplate = (record) => {
        let params = [
            {
                id: record.id
            }
        ]
        genFormTemplateApi.genFormTemplateDelete(params).then(() => {
            table.value.refresh(true)
        })
    }
    // 批量删除
    const deleteBatchGenFormTemplate = (params) => {
        genFormTemplateApi.genFormTemplateDelete(params).then(() => {
            table.value.clearRefreshSelected()
        })
    }
    const formTypeOptions = tool.dictList('MODEL_FORM_TYPE')
    const formStatusOptions = tool.dictList('MODEL_STATUS')

    const MaintenanceType = tool.dictList('MaintenanceType')
    const MaintenanceMajor = tool.dictList('MaintenanceMajor')
    const ApprovalStatus = tool.dictList('ApprovalStatus')

















</script>
