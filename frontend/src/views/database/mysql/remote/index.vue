<template>
    <div v-loading="loading">
        <LayoutContent>
            <template #title>
                <back-button name="MySQL" :header="$t('database.remoteDB')" />
            </template>
            <template #toolbar>
                <el-row>
                    <el-col :xs="24" :sm="20" :md="20" :lg="20" :xl="20">
                        <el-button type="primary" @click="onOpenDialog('create')">
                            {{ $t('database.createRemoteDB') }}
                        </el-button>
                    </el-col>
                    <el-col :xs="24" :sm="4" :md="4" :lg="4" :xl="4">
                        <TableSearch @search="search()" v-model:searchName="searchName" />
                    </el-col>
                </el-row>
            </template>
            <template #main>
                <ComplexTable :pagination-config="paginationConfig" @sort-change="search" @search="search" :data="data">
                    <el-table-column show-overflow-tooltip :label="$t('commons.table.name')" prop="name" sortable />
                    <el-table-column show-overflow-tooltip :label="$t('database.address')" prop="address" />
                    <el-table-column :label="$t('commons.login.username')" prop="username" />
                    <el-table-column :label="$t('commons.login.password')" prop="password">
                        <template #default="{ row }">
                            <div class="flex items-center flex-wrap">
                                <div class="star-center">
                                    <span v-if="!row.showPassword">**********</span>
                                </div>
                                <div>
                                    <span v-if="row.showPassword">
                                        {{ row.password }}
                                    </span>
                                </div>
                                <el-button
                                    v-if="!row.showPassword"
                                    link
                                    @click="row.showPassword = true"
                                    icon="View"
                                    class="ml-1.5"
                                ></el-button>
                                <el-button
                                    v-if="row.showPassword"
                                    link
                                    @click="row.showPassword = false"
                                    icon="Hide"
                                    class="ml-1.5"
                                ></el-button>
                                <div>
                                    <CopyButton :content="row.password" type="icon" />
                                </div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column
                        prop="description"
                        :label="$t('commons.table.description')"
                        show-overflow-tooltip
                    />
                    <el-table-column
                        prop="createdAt"
                        :label="$t('commons.table.date')"
                        :formatter="dateFormat"
                        show-overflow-tooltip
                    />
                    <fu-table-operations
                        width="170px"
                        :buttons="buttons"
                        :ellipsis="10"
                        :label="$t('commons.table.operate')"
                        fix
                    />
                </ComplexTable>
            </template>
        </LayoutContent>

        <AppResources ref="checkRef"></AppResources>
        <OperateDialog ref="dialogRef" @search="search" />
        <DeleteDialog ref="deleteRef" @search="search" />
    </div>
</template>

<script lang="ts" setup>
import { dateFormat } from '@/utils/util';
import { onMounted, reactive, ref } from 'vue';
import { deleteCheckDatabase, searchDatabases } from '@/api/modules/database';
import AppResources from '@/views/database/mysql/check/index.vue';
import OperateDialog from '@/views/database/mysql/remote/operate/index.vue';
import DeleteDialog from '@/views/database/mysql/remote/delete/index.vue';
import i18n from '@/lang';
import { Database } from '@/api/interface/database';

const loading = ref(false);

const dialogRef = ref();
const checkRef = ref();
const deleteRef = ref();

const data = ref();
const paginationConfig = reactive({
    cacheSizeKey: 'mysql-remote-page-size',
    currentPage: 1,
    pageSize: 10,
    total: 0,
    orderBy: 'created_at',
    order: 'null',
});
const searchName = ref();

const search = async (column?: any) => {
    paginationConfig.orderBy = column?.order ? column.prop : paginationConfig.orderBy;
    paginationConfig.order = column?.order ? column.order : paginationConfig.order;
    let params = {
        page: paginationConfig.currentPage,
        pageSize: paginationConfig.pageSize,
        info: searchName.value,
        type: 'mysql,mariadb',
        orderBy: paginationConfig.orderBy,
        order: paginationConfig.order,
    };
    const res = await searchDatabases(params);
    data.value = res.data.items || [];
    paginationConfig.total = res.data.total;
};

const onOpenDialog = async (
    title: string,
    rowData: Partial<Database.DatabaseInfo> = {
        name: '',
        type: 'mysql',
        version: '8.x',
        address: '',
        port: 3306,
        username: 'root',
        password: '',
        description: '',
    },
) => {
    let params = {
        title,
        rowData: { ...rowData },
    };
    dialogRef.value!.acceptParams(params);
};

const onDelete = async (row: Database.DatabaseInfo) => {
    const res = await deleteCheckDatabase(row.id);
    if (res.data && res.data.length > 0) {
        checkRef.value.acceptParams({ items: res.data });
    } else {
        deleteRef.value.acceptParams({
            id: row.id,
            database: row.name,
        });
    }
};

const buttons = [
    {
        label: i18n.global.t('commons.button.edit'),
        click: (row: Database.DatabaseInfo) => {
            onOpenDialog('edit', row);
        },
    },
    {
        label: i18n.global.t('commons.button.delete'),
        click: (row: Database.DatabaseInfo) => {
            onDelete(row);
        },
    },
];

onMounted(() => {
    search();
});
</script>
