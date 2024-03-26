## 实现效果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c6fd628003d145c3a0f1bb219c9e38dd.png)


## 核心代码：

```html
<el-table :data="items"
                  style="width: 100%;margin-top: 16px"
                  border
                  :key="randomKey">
          <el-table-column label="计划名称"
                           property="name">
            <template slot-scope="{row}">
              <template v-if="row.edit">
                <el-input v-model="row.name"></el-input>
              </template>
              <span v-else>{{ row.name }}</span>
            </template>
          </el-table-column>
          <el-table-column label="计划完成时间"
                           property="executionDate" width="175">
            <template slot-scope="scope">
              <el-date-picker v-if="scope.row.edit" style="width: 153px;" type="date"
                              v-model="scope.row.timeEnd">
              </el-date-picker>
              <span v-else>{{ parseTime(scope.row.timeEnd) }}</span>
            </template>
          </el-table-column>
          <el-table-column label="协同人"
                           property="leaderList">
            <template slot-scope="scope">
              <template v-if="scope.row.edit">
                <el-select
                  v-model="scope.row.leaderList"
                  clearable filterable multiple>
                  <el-option
                    v-for="item in userList"
                    :key="item.userId"
                    :label="item.nickname"
                    :value="item.userId">
                  </el-option>
                </el-select>
              </template>
              <span v-else>{{ scope.row.leaderName }}</span>
            </template>
          </el-table-column>
          <el-table-column label="执行人" width="80">
            <template>
              <span>{{ $store.getters.name }}</span>
            </template>
          </el-table-column>
          <el-table-column align="center" label="操作" width="100">
            <template slot-scope="{row,column,$index}">
              <el-button
                v-if="row.edit"
                type="success" icon="el-icon-check" circle
                size="small"
                @click="confirmEdit(row)"
              >
              </el-button>
              <el-button
                v-if="row.edit"
                icon="el-icon-close" circle
                size="small"
                @click="cancelEdit(row)"
              >
              </el-button>
              <el-button
                type="primary" icon="el-icon-edit" circle
                v-if="!row.edit"
                size="small"
                @click="row.edit=!row.edit"
              >
              </el-button>
              <el-button
                type="danger" icon="el-icon-delete" circle
                size="small" @click="handleDelete($index)"
                v-if="!row.edit"
              >
              </el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div style="display: flex;margin-top: 28px;justify-content: center;">
        <el-button @click="addSon" size="small" icon="el-icon-plus">添加子计划</el-button>
      </div>
```

## Method：

```javascript
cancelEdit(row) {
      row.name = row.oldName
      row.leaderList = row.oldLeaderList
      row.timeEnd = row.oldTimeEnd
      row.leaderName = row.oldLeaderName
      row.edit = false
      this.$message({
        message: '已恢复原值',
        type: 'warning'
      })
    },
    confirmEdit(row) {
      row.edit = false
      row.oldName = row.name
      row.oldLeaderList = row.leaderList
      row.oldTimeEnd = row.timeEnd
      let arr = []
      row.leaderList.forEach(i => {
        let userName = this.userList.find(({userId}) => userId === i).nickname
        arr.push(userName)
      })
      row.leaderName = arr.join()
      row.oldLeaderName = row.leaderName
      this.$message({
        message: '修改成功',
        type: 'success'
      })
    },
    handleDelete(index) {
      this.items.splice(index, 1)
    },
    addSon() {
      this.items.push({
        name: null,
        timeEnd: null,
        leaderList: [],
        leaderName: null,
        edit: true
      })
    },
```
