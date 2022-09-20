<script setup>
import { ref } from 'vue';

// 所有的数据
// 搜索框
const queryInput = ref()

// table表格数据
const tableData = ref([
    {
        id: '1',
        name: '张三',
        age: 18,
        dataTime: '2022-09-19',
        state: '在职',
        address: '广东',
    },
    {
        id: '2',
        name: '李四',
        age: 18,
        dataTime: '2022-09-21',
        state: '在职',
        address: '广东',
    },
    {
        id: '3',
        name: '王五',
        age: 18,
        dataTime: '2022-09-22',
        state: '在职',
        address: '广东',
    },
    {
        id: '4',
        name: '赵七',
        age: 18,
        dataTime: '2022-09-23',
        state: '在职',
        address: '广东',
    },
    {
        id: '5',
        name: '赵小七',
        age: 18,
        dataTime: '2022-09-23',
        state: '在职',
        address: '哈尔滨',
    },
    {
        id: '6',
        name: 'Tom',
        age: 18,
        dataTime: '2022-09-23',
        state: '在职',
        address: '南昌',
    },
])

const copyTableData = Object.assign(tableData.value)

// 表格选中的数据
const multipleSelection = ref([])

// 弹窗默认为false
const dialogFormVisible = ref(false)

// 收集弹窗的数据
const tableForm = ref({
    name: '张三',
    age: 18,
    dataTime: '2022-08-08',
    state: '在职',
    address: '上海'
})

// 弹窗标题
const dialogAdd = ref('add')

// 所有的方法

// 选中
const handleSelectionChange = (val) => {
    // multipleSelection.value = val;
    console.log(val);

    multipleSelection.value = [];
    val.forEach(item => {
        multipleSelection.value.push(item.id)
        console.log(multipleSelection.value);
    });
}

// 新增
const addQuery = () => {
    dialogFormVisible.value = true;
    tableForm.value = {};
    dialogAdd.value = 'add';
}

// 修改
const modifyData = (row) => {
    console.log(row);
    dialogFormVisible.value = true;
    dialogAdd.value = 'edit';
    tableForm.value = { ...row }

}

// 删除一条
const deletedData = ({ id }) => {
    // console.log(row.id);
    // 通过 id 获取条目所对应的索引值
    const index = tableData.value.findIndex(item => item.id === id);
    // 通过索引值删除对应的条目
    tableData.value.splice(index, 1)
}

// 删除多条
const delQueryList = () => {
    multipleSelection.value.forEach(id => {
        deletedData({ id })
    })
    multipleSelection.value = []
}

// 搜索
const queryData = (val) => {
    if (val.length > 0) {
        tableData.value = tableData.value.filter(item => (item.name).toLowerCase().match(val.toLowerCase()))
    } else {
        tableData.value = copyTableData
    }
}

// 弹窗确定按钮
const dialogConfim = () => {
    dialogFormVisible.value = false;
    if (dialogAdd.value === 'add') { //更新操作
        // 1.拿到数据
        // 2.添加到table中
        tableData.value.push({
            id: (tableData.value.length + 1).toString(),
            ...tableForm.value
        })
        console.log(tableData.value);
    } else { //修改操作
        const index = tableData.value.findIndex(item => item.id === tableForm.value.id)
        tableData.value[index] = tableForm.value
    }
}


</script>

<template>
    <el-row>
       <el-col><h3 style="text-align: center;">CRUD</h3></el-col>
    </el-row>
    
    <el-row>
        <el-col :span="12">
            <el-input v-model="queryInput" placeholder="请输入搜索姓名🔍" @input="queryData"/>
        </el-col>
        <el-col :span="12" class="addQuery">
            <el-button type="primary" @click="addQuery">新增</el-button>
            <el-button type="danger" @click="delQueryList" v-if="multipleSelection.length">删除选中</el-button>
        </el-col>
    </el-row>

    <el-table  ref="multipleTableRef"
        :data="tableData"
        @selection-change="handleSelectionChange" 
        border 
        style="width: 100%;margin-top: 20px;">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="name" label="姓名" width="150" />
        <el-table-column prop="age" label="年龄" width="120" />
        <el-table-column prop="dataTime" label="日期" width="120" />
        <el-table-column prop="state" label="状态" width="120" />
        <el-table-column prop="address" label="详细地址" width="200" />
        <el-table-column fixed="right" label="操作" width="120">
            <template #default="scope">
                <el-button link type="primary" size="small" style="color:#F56C6C ;" @click="deletedData(scope.row)">
                    删除
                </el-button>
                <el-button link type="primary" size="small" @click="modifyData(scope.row)">编辑</el-button>
            </template>
        </el-table-column>
  </el-table>

  <el-dialog v-model="dialogFormVisible" :title="dialogAdd === 'add'? '新增':'修改'" width="650px">
    <el-form :model="tableForm">
      <el-form-item label="姓名" :label-width="60">
        <el-input v-model="tableForm.name" placeholder="请输入姓名" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="年龄" :label-width="60">
        <el-input v-model="tableForm.age" placeholder="请输入年龄" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="日期" :label-width="60">
        <el-input v-model="tableForm.dataTime" placeholder="请输入日期" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="状态" :label-width="60">
        <el-input v-model="tableForm.state" placeholder="请输入状态" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="地址" :label-width="60">
        <el-input v-model="tableForm.address" placeholder="请输入地址" autocomplete="off"/>
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取消</el-button>
        <el-button type="primary" @click="dialogConfim"
          >确定
        </el-button>
      </span>
    </template>
  </el-dialog>
</template>

<style lang="css" scoped>
.addQuery {
    text-align: right;
}
</style>